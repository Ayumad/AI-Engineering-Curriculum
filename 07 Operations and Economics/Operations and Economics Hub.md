---
type: hub
layer: operations
status: evergreen
maturity: established
aliases: [Agent Operations and Economics]
tags: [ai-engineering, operations, economics, agentops]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "06 Reliability and Security/Defensive Red-Team Labs.md"
next: "07 Operations and Economics/Latency and Cost Engineering.md"
summary: "Production agents are software systems with variable compute, probabilistic behavior, external dependencies, and human approval points."
---


# Operations and economics

Operations and economics is the discipline of running production AI agents reliably, cost-effectively, and safely. It spans deployment mechanics (routing, queues, sandboxes, telemetry), cost engineering (caching, batching, token budgets), resource management (GPU economics, self-hosted vs API trade-offs), and operational governance (rate limiting, incident response, multi-tenancy). The goal is predictable behavior from probabilistic systems: every autonomous run must have a budget, a version, an owner, and a rollback path.

Production agents are software systems with variable compute, probabilistic behavior, external dependencies, and human approval points. They compound failure modes across model calls, tool invocations, retrieval, and human approval gates — making operational discipline a first-class engineering concern, not an afterthought.

### Section overview

This section covers the full lifecycle of production agent operations:

| Topic | What it covers |
|---|---|
| [[07 Operations and Economics/Deployment and AgentOps]] | Delivery loops, serving stacks, rate limiting, rollback, multi-tenancy |
| [[07 Operations and Economics/Latency and Cost Engineering]] | Token budgets, caching economics, routing, batch inference |
| [[07 Operations and Economics/Local Subscription API]] | Local vs API vs hybrid economics, pricing, data residency |
| [[06 Reliability and Security/Reliability Evals and Observability]] | The signals operations must measure |

- [[07 Operations and Economics/Local Subscription API]] — deployment and purchasing models.
- [[07 Operations and Economics/Deployment and AgentOps]] — routing, queues, sandboxes, telemetry, and continuous evaluation.
- [[07 Operations and Economics/Latency and Cost Engineering]] — budgets, caching, routing, streaming, and resource-aware topology.
- [[06 Reliability and Security/Reliability Evals and Observability]] — the signals operations must measure.

---

---

> **← [[06 Reliability and Security/Defensive Red-Team Labs|Defensive Red-Team Labs]]** · **[[AI_Home|Home]]** · **[[07 Operations and Economics/Latency and Cost Engineering|Latency and Cost Engineering]] →**
