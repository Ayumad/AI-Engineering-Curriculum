---
type: pattern
layer: orchestration
status: evergreen
maturity: established
aliases: [Workflow Topologies]
tags: [ai-engineering, workflows, patterns]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "04 Workflows and Orchestration/Orchestration Hub.md"
next: "04 Workflows and Orchestration/Multi-Agent Systems.md"
summary: "Choose the simplest topology that makes the work observable and correct."
---


# Workflow patterns

> [!summary] The gist
> Workflow patterns are the standard shapes for arranging AI steps — sequential, parallel, routed, looping, and so on. Pick the simplest one that makes the work visible and correct. This note covers the main patterns, how to build each one, and where they break.

---

| Pattern | Shape | Best fit | Main risk |
|---|---|---|---|
| Sequential | A → B → C | Known pipeline | Latency and brittle handoff |
| Branching | Condition → path | Explicit business rules | Uncovered branch |
| Router | Classify → specialist | Distinct task types | Misrouting |
| Parallel | Fan out → join | Independent research or tests | Merge conflicts and cost |
| Map-reduce | Map items → reduce | Many similar records | Weak aggregation |
| Evaluator-optimizer | Draft ↔ critic | Quality improvement | Infinite loops and correlated error |
| Agent loop | Observe → decide → act | Uncertain path | Drift and side effects |
| Event-driven | Event → handler | Reactive systems | Ordering and duplicate events |
| Durable workflow | Persisted steps | Long-running operations | State-version compatibility |

Add deterministic boundaries around model choices: schemas, allowlists, budgets, idempotency, approval gates, and explicit stop conditions.

## Implementation sketches

| Pattern | How to build it |
|---|---|
| Sequential | Chain calls: `a = step_a(input); b = step_b(a); return step_c(b)`. Add try/except per step for independent error handling. |
| Branching | `if/elif/else` on a classifier output or function return. Each branch is its own function with its own retry policy. |
| Router | Call a classifier (LLM or embeddings) to pick a specialist. Use structured output (JSON schema) to force a valid route. |
| Parallel | `asyncio.gather` or thread pool for independent calls. Typed join: each branch returns the same schema. |
| Map-reduce | `map(fn, items)` then reduce with an aggregator LLM call. Chunk items if context window is tight. |
| Evaluator-optimizer | Loop: generate draft → evaluate with rubric → if score below threshold, feed critique back as prompt revision. Cap iterations. |
| Agent loop | ReAct-style: observe state → decide next action → execute tool → check stop condition. Max iterations with escalation. |
| Event-driven | Message queue (SQS, Redis Streams, Temporal signals). Handler subscribes to event type. Deduplicate by event ID. |
| Durable workflow | Use Temporal or LangGraph checkpointing. Each step is a durable activity with idempotency key. State serialized between steps. |

## Cost and latency trade-offs

- **Sequential**: Lowest coordination cost; latency is sum of all steps. Best when steps are cheap and depend on each other.
- **Parallel / Map-reduce**: Latency drops to max of parallel branches; cost is sum of all branches. Only worth it when branches are independent and expensive enough to justify coordination overhead.
- **Router**: One classification call plus one specialist call — two LLM calls total. Fast and cheap if classification is accurate.
- **Evaluator-optimizer**: Cost scales with quality bar. Each optimization round is a full generate + evaluate cycle. Cap at 2–3 rounds to avoid correlated error amplification.
- **Agent loop**: Unpredictable cost. Each iteration is a full LLM call plus tool execution. Hard token and iteration budgets are mandatory.

## Failure modes

| Pattern | Failure mode | Mitigation |
|---|---|---|
| Sequential | Latency compounds; brittle handoff if step N output doesn't match step N+1 input contract | Typed schemas between steps; independent retry per step |
| Branching | Uncovered branch falls through silently | Explicit default/error branch; exhaustive condition coverage |
| Router | Misrouting — classifier picks wrong specialist | Confidence threshold with fallback; log route decisions for eval |
| Parallel | Merge conflicts; one branch fails silently | Typed join with error propagation; timeout per branch |
| Map-reduce | Weak aggregation loses signal from individual items | Chunking strategy; aggregation prompt with explicit instructions to preserve item-level evidence |
| Evaluator-optimizer | Infinite loops; correlated error (evaluator and generator share same model weaknesses) | Hard iteration cap; use different model for evaluator; rubric-based scoring |
| Agent loop | Drift (plan diverges from goal); side effects accumulate | Stop conditions; budget caps; checkpoint and human review at thresholds |
| Event-driven | Ordering violations; duplicate events; lost events | Idempotency keys; exactly-once delivery guarantees; dead-letter queues |
| Durable workflow | State-version compatibility after code changes | Versioned schemas; migration support in durable execution engines |

## Error recovery patterns

- **Exponential backoff with jitter**: Double wait time and add randomness on each retry. Base 1s, max 60s. Default for all LLM API calls. Never retry 400/401/404 — these are permanent failures.
- **Circuit breaker**: Closed → Open → Half-open states. After N consecutive failures, stop calling the service entirely. Periodically probe with half-open requests.
- **Saga / compensation**: When a multi-step workflow has side effects (e.g., API writes), define a compensating action for each step. On failure, execute compensations in reverse order to undo partial work.
- **Checkpoint and resume**: Serialize agent state to durable storage at each step. LangGraph `MemorySaver`/`PostgresSaver`; Temporal workflows. On crash, resume from last checkpoint.
- **Escalation queue**: Structured path for failures needing human resolution. Don't silently retry forever — route to a human after exhausting retries.
- **Common pitfalls**: Retrying non-retriable errors (4xx), losing state on retry, unbounded loops (always cap at ~5), silent fallback that hides the real failure.

## Durable execution

For long-running or multi-hour workflows, durable execution (Temporal, Restate) journals every step *before* executing it. The engine replays from the journal on restart, guaranteeing idempotency. This differs from the saga pattern: sagas require you to write compensation logic; durable execution replays the original logic deterministically. Stripe, Revolut, and Wise use Temporal for production financial workflows.

## Selection method

Start deterministic when steps are known. Add model decisions only where interpretation has measurable upside, parallelism only for independent work with a typed join, and durable state when delays could repeat effects. For every topology, state owner, contract, stop condition, retry/idempotency behavior, budget, and evaluation.

## Provenance and reading

- **The five canonical agent patterns** — prompt chaining, routing, parallelization, orchestrator-workers, evaluator-optimizer — come from Anthropic's "Building effective agents" (Dec 2024), which reports these cover most production designs.
- **ReAct-style agent loop** originates in Yao et al. 2022 (interleaved reasoning and acting).
- **Sequential and branching** are classic software control flow, not LLM-specific; they stay preferred when steps are known.
- **Durable and event-driven workflows** come from durable-execution practice (e.g., Temporal): persisted steps, retries, idempotency, and replay across failures.

## Sources and further reading

- Anthropic, "Building effective agents," Dec 19, 2024 — https://www.anthropic.com/engineering/building-effective-agents — five canonical agent patterns.
- Yao et al., "ReAct: Synergizing Reasoning and Acting in Language Models," 2022 — https://arxiv.org/abs/2210.03629 — canonical reason-and-act loop.
- Temporal documentation — https://docs.temporal.io/ — durable workflows, retries, and idempotency.
- Restate documentation — https://docs.restate.dev/tour/microservice-orchestration — durable execution, journal-based replay.
- OpenAI, "A practical guide to building agents," 2025 — https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf
- AI Agents Blog, "5 Patterns for Production Reliability," 2026 — https://aiagentsblog.com/blog/agent-error-recovery-patterns/ — error recovery patterns for agent workflows.
- Fernel.io, "Durable Execution vs. Saga Pattern," 2026 — https://fernel.io/insights/06-durable-execution-vs-saga — saga vs durable execution trade-offs.
- LangChain, LangGraph Overview — https://docs.langchain.com/oss/python/langgraph/overview — checkpointing, MemorySaver/PostgresSaver.

All links verified 2026-08-27.

---

---

> **← [[04 Workflows and Orchestration/Orchestration Hub|Orchestration Hub]]** · **[[AI_Home|Home]]** · **[[04 Workflows and Orchestration/Multi-Agent Systems|Multi-Agent Systems]] →**
