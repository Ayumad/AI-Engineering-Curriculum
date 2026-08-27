---
type: concept
layer: agents
status: evergreen
maturity: established
aliases: [Agent Runtime, Harness]
tags: [ai-engineering, harness, runtime]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "02 Agents and Harnesses/What Is an Agent.md"
next: "02 Agents and Harnesses/Sandboxes and Execution Planes.md"
summary: "The harness is the machinery that makes an agent useful and governable. It translates user intent into context, exposes tools, executes actions, manages state, and dec..."
---


# Agent harness

## Plain-English introduction

An agent's brain is impressive, but it cannot do anything useful without a body. The harness is that body: the system that feeds the agent the right information, lets it use tools, remembers what happened, and steps in when things go wrong. Think of it like the difference between a talented chef and a fully equipped restaurant kitchen — the chef provides the skill, but the kitchen provides the stove, ingredients, recipe book, and cleanup crew that make the meal possible. This note walks through each part of that machinery and explains why two teams using the same AI model can get wildly different results depending on how they build the harness around it.

The harness is the machinery that makes an agent useful and governable. It translates user intent into context, exposes tools, executes actions, manages state, and decides when to ask for help or stop.

## Typical components

- **Context manager:** selects, orders, summarizes, and removes information.
- **Tool registry/router:** describes tools, validates arguments, and dispatches calls.
- **Planner and task state:** turns outcomes into steps, dependencies, and progress.
- **Permissions and approvals:** enforce authority outside the model.
- **Memory and skills:** retrieve durable facts and procedural capabilities on demand.
- **Retries and checkpoints:** recover from transient failure without duplicating side effects.
- **Sandbox:** provides disposable files, packages, commands, and network policy.
- **Tracing and evaluation:** records trajectories, cost, latency, tool calls, and outcomes.
- **Subagent manager:** delegates bounded work and reunites typed results.

```text
user → harness → context → model → decision → tool/sandbox → observation → harness
```

The same model can behave very differently under two harnesses. Harness quality is therefore a first-class engineering variable, not an afterthought.

## How components cooperate

The components above are not independent layers; they form a pipeline where each stage constrains the next:

1. **Context manager feeds the model.** It selects which files, prior tool results, skill definitions, and user messages reach the context window. Tool descriptions alone cost ~735 tokens each; Anthropic recommends dynamic injection of 5–10 relevant tools per request rather than loading all 64 at once. The context manager's choices directly determine what the model can see and therefore what it can decide.
2. **Model emits a tool call.** The tool registry validates the call name and argument schema, checks the allowlist, and dispatches to the appropriate executor. A subtly broken JSON schema can cause the model to call the tool incorrectly without any error—schema design is a first-class concern.
3. **Planner updates task state.** After each tool result returns, the planner inspects the outcome and decides: mark the step complete, retry with adjusted arguments, escalate to a human, or reprioritize remaining steps. The planner's decision is itself a function of what the context manager chose to include.
4. **Retry loop handles transient failures.** Circuit breakers (error-rate threshold → open/half-open), per-step timeouts, and idempotency keys prevent cascading failures. Without them, a single slow API call can stall the entire agent.
5. **Sandbox executes.** The execution plane runs generated code with bounded resources. The harness reads back only validated artifacts—never raw stdout as authority.

```text
user message
  → context manager (select, compress, inject tools)
    → model inference
      → tool registry (validate, route)
        → sandbox / API (execute)
          → observation (validated result)
            → planner (update state, decide next)
              → retry loop (if transient failure)
                → context manager (append observation)
                  → model inference (next step)
```

## Trace fields

Every harness iteration should emit a structured trace record for debugging and evaluation:

```json
{
  "step": 3,
  "timestamp": "2026-08-27T10:15:03Z",
  "tool_call": {"name": "bash", "arguments": {"command": "pytest tests/ -x"}},
  "tool_result": {"exit_code": 1, "stdout": "...", "stderr": "FAILED test_parse..."},
  "planner_decision": "retry_with_fix",
  "context_tokens_before": 12400,
  "context_tokens_after": 14100,
  "latency_ms": 820,
  "cost_usd": 0.0042,
  "observation": "test_parse failed; retrying after fixing import"
}
```

Traces make every decision inspectable. They are the foundation of trajectory evaluation (see [[06 Reliability and Security/Evaluation Engineering]]).

## Harness design patterns

- **Thin harness:** minimal orchestration—model selects tools, harness validates and executes, context manager compacts. Best for simple, well-scoped tasks where the model's judgment is sufficient. Lower engineering cost, less control over failure modes.
- **Framework harness:** opinionated runtime with planner, state machine, approval gates, checkpointing, and sub-agent delegation. Necessary for long-running, multi-step, or high-stakes tasks where the harness must enforce policy the model cannot be trusted to follow.
- **Hybrid:** thin harness for the core loop, framework features (approval gates, checkpoints) added only where the threat model demands them. Anthropic's production guidance recommends starting thin and adding structure only where task complexity or risk justifies it.

## Control versus execution

Keep orchestration, credentials, policy, and durable state in a control plane; run generated code and untrusted data in an execution plane. See [[02 Agents and Harnesses/Sandboxes and Execution Planes]].

## Why the harness is the product

- Anthropic's survey of dozens of production teams found the most successful agents use **simple, composable patterns**—with the complexity concentrated in the harness (context, tools, guardrails), not the prompt (Anthropic 2024).
- OpenAI's agent guide treats the loop as an engineering artifact: tools are the primary interface, guardrails (allowlists, human-in-the-loop, budgets) live outside the model, and observability plus evaluation drive iteration (OpenAI 2025).
- Huyen's *AI Engineering* frames the model as one component among many: most reliability, cost, and safety outcomes come from the surrounding system—which is exactly what the harness owns (Huyen 2025).

## Sources and further reading

- Anthropic, "Building effective agents," Dec 19, 2024 — https://www.anthropic.com/engineering/building-effective-agents — five workflow patterns.
- OpenAI, "A practical guide to building agents," 2025 — https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf — tooling, orchestration, guardrails.
- Huyen, *AI Engineering* (O'Reilly, 2025) — https://www.oreilly.com/library/view/ai-engineering/9781098166298/ — model as one component among many.
- Anthropic, "Effective context engineering for AI agents," Sep 2025 — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — context rot, dynamic tool injection, token budget.
- Anthropic, "Effective harnesses for long-running agents," Nov 2025 — https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents — initializer/coding agent, progress files.
- Pan, "Agentic Systems Are Distributed Systems," May 2026 — https://tianpan.co/blog/2026-05-05-agentic-systems-distributed-services-microservices-patterns — circuit breakers, bulkheads, failure rates.
- MCP Tools Specification, Jun 2025 — https://modelcontextprotocol.io/specification/2025-06-18/server/tools — tool schema design, structured results.

All links verified 2026-08-27.

## Related

[[02 Agents and Harnesses/What Is an Agent]] · [[02 Agents and Harnesses/Planning State and Persistence]] · [[02 Agents and Harnesses/Sandboxes and Execution Planes]]

---

---

> **← [[02 Agents and Harnesses/What Is an Agent|What Is an Agent]]** · **[[AI_Home|Home]]** · **[[02 Agents and Harnesses/Sandboxes and Execution Planes|Sandboxes and Execution Planes]] →**
