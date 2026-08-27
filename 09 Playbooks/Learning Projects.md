---
type: curriculum
layer: playbooks
status: evergreen
maturity: established
aliases: [Agent Engineering Projects]
tags: [ai-engineering, projects, practice]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "09 Playbooks/Evaluation and Security Review.md"
next: "10 Maps/AI Engineering Atlas.md"
summary: "1. Structured assistant: one model, one schema, no side effects. Test validation and malformed output recovery. 2. Toolcalling agent: add a readonly calculator or weat..."
---


# Staged learning projects

## Plain-English introduction

Learning AI engineering is like learning to fly: you start in a simulator with no real consequences, move to a small plane with an instructor beside you, and only later take the controls of something complex in rough weather. These nine projects follow that same path. Each one adds a new capability — structured output, tool use, memory, evaluation, multi-agent coordination — on top of what you built before. Every project includes a time estimate, a difficulty level, and a pointer to the curriculum note where the underlying concept is explained. Work through them in order and you'll build from a simple helper that follows a schema all the way to a governed, production-ready system with rollback, budgets, and human oversight.

1. **Structured assistant** (~2 hrs, easy): one model, one schema, no side effects. Test validation and malformed output recovery. See [[01 Foundations/Structured Outputs and Tool Calling]].
2. **Tool-calling agent** (~3 hrs, easy-moderate): add a read-only calculator or weather tool, typed arguments, timeout, and trace. See [[01 Foundations/Structured Outputs and Tool Calling]].
3. **Sandboxed coding loop** (~4 hrs, moderate): add temporary files, tests, and a disposable execution plane; require approval before writes outside it. See [[02 Agents and Harnesses/Sandboxes and Execution Planes]].
4. **Evidence-based RAG agent** (~5 hrs, moderate): index a small corpus, show citations, test missing and contradictory evidence. See [[03 Context Knowledge Memory/RAG]].
5. **Persistent assistant** (~4 hrs, moderate): add inspectable semantic and procedural memory with confirmation and deletion. See [[03 Context Knowledge Memory/Memory and Skills]].
6. **Orchestrated workflow** (~6 hrs, moderate-hard): add queues, retries, checkpoints, and a human handoff around a deterministic pipeline. See [[04 Workflows and Orchestration/Orchestration Hub]].
7. **Multi-agent team** (~8 hrs, hard): add a supervisor and two bounded specialists with typed handoffs and independent evaluations. See [[04 Workflows and Orchestration/Multi-Agent Systems]].
8. **Governed agent democracy** (~10 hrs, hard): add proposals, critics, quorum, dissent, reputation decay, audit logs, and a human veto. See [[06 Reliability and Security/Reliability Evals and Observability]].
9. **Production capstone** (~12 hrs, expert): combine routing, RAG, sandbox, approvals, telemetry, regression tests, budgets, incident response, and a documented rollback. See [[07 Operations and Economics/Deployment and AgentOps]].

For every project, record the model, harness, context, tools, state, authority, stop conditions, evaluation set, traces, cost, and known failure modes.

---

---

> **← [[09 Playbooks/Evaluation and Security Review|Evaluation and Security Review]]** · **[[AI_Home|Home]]** · **[[10 Maps/AI Engineering Atlas|AI Engineering Atlas]] →**
