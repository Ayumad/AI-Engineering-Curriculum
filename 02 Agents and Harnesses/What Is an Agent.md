---
type: concept
layer: agents
status: evergreen
maturity: established
aliases: [Agent Loop, ReAct]
tags: [ai-engineering, agents, react, loop]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "02 Agents and Harnesses/Agents and Harnesses Hub.md"
next: "02 Agents and Harnesses/Agent Harness.md"
summary: "An agent is a model plus a goal, instructions, tools, state, and a loop that continues until a stopping condition."
---


# What is an agent?

## Plain-English introduction

Imagine asking someone to research a question for you. They read a few sources, think about what they found, decide they need one more piece of information, look it up, and then give you an answer. That back-and-forth cycle — look, think, act, repeat — is exactly what an AI agent does. Unlike a calculator that gives one answer to one question, an agent can break a task into steps, try different approaches, and keep going until it reaches a goal or hits a limit. This note explains how that loop works, when it is the right tool for the job, and how to tell if an agent actually performed well or just sounded convincing.

## A bounded control loop

An agent repeatedly observes its environment, selects a next action, receives an observation, and stops under explicit conditions. The loop is useful when the path to an outcome is uncertain; it is not automatically better than deterministic code for a known sequence.

Define the objective, allowed observations and actions, success evidence, failure modes, resource budget, and stopping rules before adding autonomy. A good first agent has a read-only tool, a small task scope, an action limit, and a trace that makes each decision inspectable.

## Evaluate the whole trajectory

An answer can sound correct after the agent selected irrelevant context, used a forbidden tool, or retried a side effect. Evaluate both the final outcome and the path: tool choice, arguments, evidence, cost, latency, recovery, and whether it stopped at the right time.

An agent is a model plus a goal, instructions, tools, state, and a loop that continues until a stopping condition.

```text
observe → reason → decide → act → observe → …
```

The loop stops when the goal is achieved, a human decision is required, a failure/budget limit is reached, or the system cannot safely continue.

## Minimal pseudocode

```python
while not stop(state):
    decision = model(goal, state, available_tools)
    if decision.finished:
        return decision.answer
    observation = execute_and_validate(decision.tool_call)
    state.append(observation)
```

ReAct (“reason and act”) is a historically influential pattern. Modern systems may hide internal reasoning and expose only actions, summaries, and tool results; the architectural loop remains.

## Agent versus workflow

Use an explicit workflow when steps, ordering, and failure handling are known. Use an agent when the path depends on information discovered during execution. Combine them when agents choose within deterministic boundaries.

## Where the agent idea comes from

- **ReAct (Yao et al. 2022):** interleave reasoning traces with actions; the authors showed gains over reasoning-only and acting-only baselines on knowledge-intensive QA and decision-making tasks. The reason→act loop is the direct ancestor of most modern agent loops.
- **Weng's agent anatomy (2023):** planning, memory, tool use—the three subsystems that still organize agent-design discussion.
- **Anthropic (Dec 2024):** production agents succeed with simple, composable patterns; a workflow follows a predefined path, while an agent decides the path at runtime.
- **OpenAI (2025):** an agent is model + tools + orchestration loop. Start simple, add autonomy only where the task demands it, and keep guardrails and evaluation from day one.

## Memory in the loop

Agents that run across multiple steps need memory to avoid re-discovering what they already know. Two architectural patterns dominate: passive extraction (a system automatically stores and retrieves facts without model involvement, predictably token-efficient but unable to judge nuance) and agentic self-editing (the model explicitly decides what to store, overwrite, or forget via tool calls, adaptive but every memory operation costs inference tokens). The MemGPT paper frames this as an OS analogy: the LLM manages its own context window like an OS manages RAM, paging information between a small in-context working set and larger archival storage. Choosing between these patterns depends on whether the agent's tasks require flexible judgment about what is worth remembering or benefit from the consistency of automatic extraction.

## Failure modes

Autonomous agents fail in characteristic, predictable ways. Catalog them explicitly so your harness can detect and recover from each:

1. **Cascading hallucination propagation** — one hallucinated fact enters the context and is cited as evidence by subsequent steps, compounding error silently.
2. **Coordination latency collapse** — each additional agent in a multi-agent system adds 200ms+ of coordination overhead; scaling from 2 to 8 agents can push end-to-end latency from seconds to minutes.
3. **Cascading timeouts** — a slow downstream tool call delays the entire chain; without per-step timeouts, the agent hangs or exhausts its budget on a single blocked step.
4. **Premature completion** — the agent sees partial progress (a file written, a test passing) and declares the task done before verifying the full objective.
5. **One-shot overreach** — the agent tries to complete a multi-step task in a single context window, runs out of tokens mid-implementation, and leaves an inconsistent state.
6. **Side-effect accumulation** — each tool call mutates external state; without idempotency keys or compensating actions, retries duplicate work or create contradictions.
7. **Budget blindness** — the agent optimizes for task completion without tracking token cost, API spend, or wall-clock time, leading to runaway expenses.

Empirical data underscores the urgency: 1,600+ traces across 7 frameworks showed 41–87% failure rates, and Carnegie Mellon studies report 30–35% task completion on multi-step tasks.

## Trajectory evaluation

Evaluating only the final answer misses most failures. Score the entire trajectory: tool choice, arguments, evidence quality, recovery behavior, cost, latency, and whether the agent stopped at the right time. See [[06 Reliability and Security/Evaluation Engineering]] for trajectory metrics and structured eval frameworks.

## Sources and further reading

- Yao et al., "ReAct: Synergizing Reasoning and Acting in Language Models," 2022 — https://arxiv.org/abs/2210.03629 — canonical reason-and-act loop.
- Weng, "LLM Powered Autonomous Agents," Jun 2023 — https://lilianweng.github.io/posts/2023-06-23-agent/ — planning/memory/tool use taxonomy.
- Anthropic, "Building effective agents," Dec 19, 2024 — https://www.anthropic.com/engineering/building-effective-agents — simple composable patterns.
- OpenAI, "A practical guide to building agents," 2025 — https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf — tooling, orchestration, guardrails.
- Huyen, *AI Engineering* (O'Reilly, 2025) — https://www.oreilly.com/library/view/ai-engineering/9781098166298/ — model as one component among many.
- Pan, "Agentic Systems Are Distributed Systems," May 2026 — https://tianpan.co/blog/2026-05-05-agentic-systems-distributed-services-microservices-patterns — failure rates, coordination patterns.
- Anthropic, "Effective harnesses for long-running agents," Nov 2025 — https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents — initializer/coding agent pattern, failure modes.

All links verified 2026-08-27.

## Related

[[02 Agents and Harnesses/Agent Harness]] · [[04 Workflows and Orchestration/Workflow Patterns]] · [[06 Reliability and Security/Reliability Evals and Observability]]

---

---

> **← [[02 Agents and Harnesses/Agents and Harnesses Hub|Agents and Harnesses Hub]]** · **[[AI_Home|Home]]** · **[[02 Agents and Harnesses/Agent Harness|Agent Harness]] →**
