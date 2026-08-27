---
type: hub
layer: reliability
status: evergreen
maturity: established
aliases: [Agent Reliability, Agent Evaluation]
tags: [ai-engineering, reliability, evals, observability, agentops]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "05 Protocols and Tools/Agent Protocols.md"
next: "06 Reliability and Security/Evaluation Engineering.md"
last_verified: 2026-08-27
summary: "Agent quality is more than the final answer. Evaluate the trajectory: context selected, tools called, arguments, permissions, retries, latency, cost, evidence, and fin..."
---


# Reliability, evaluation, and observability

## Plain-English introduction

Think of this note as the map connecting two essential practices: testing whether your AI agent actually works well, and watching it in real time to catch problems early. Just as a factory needs both a quality-control inspector and a live dashboard showing machine health, AI systems need rigorous tests before deployment and constant monitoring afterward. This hub ties those pieces together and points you to the deeper notes on each.

Agent quality is more than the final answer. Evaluate the trajectory: context selected, tools called, arguments, permissions, retries, latency, cost, evidence, and final outcome.

## Reliability controls

- Typed schemas and deterministic validation.
- Allowlisted tools and explicit authority boundaries.
- Timeouts, retry budgets, backoff, and idempotency keys.
- Checkpoints, resumability, compensating actions, and human approval.
- Fallback models/providers and graceful degradation.
- Evidence requirements and honest uncertainty reporting.

## Evaluation layers — canonical source

The detailed evaluation-layer table (Unit, Tool-use, Retrieval, Trajectory, End-to-end, Human) and guidance on LLM-as-judge calibration, versioned datasets, and deterministic assertions lives in [[06 Reliability and Security/Evaluation Engineering]]. That note is the canonical reference for building and running eval systems. Use it when you need to design test cases, select metrics by task type, or choose between open-source and commercial eval frameworks.

## Observability and tracing — canonical source

Trace fields (correlation IDs, model/prompt/tool versions, token usage, latency, cost, approvals), OTel GenAI instrumentation, alerting thresholds, dashboard patterns, and drift monitoring all live in [[06 Reliability and Security/Observability]]. That note is the canonical reference for making agent trajectories diagnosable and replayable. Use it when you need to set up tracing, configure alerting, or build dashboards.

## Sources and further reading

- OpenTelemetry GenAI semantic conventions — https://opentelemetry.io/docs/specs/semconv/gen-ai/ — the shared attribute vocabulary for LLM traces (verified 2026-08-27).
- OpenAI, "A practical guide to building agents" — https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf — evaluation and production guidance (verified 2026-08-27).

All links verified 2026-08-27.

## Study sequence

Use [[06 Reliability and Security/Evaluation Engineering]] to turn acceptance criteria into versioned tests, [[06 Reliability and Security/Observability]] to make trajectories diagnosable, and [[06 Reliability and Security/Human Oversight and Trust Engineering]] to place human authority at consequential boundaries.

---

---

> **← [[05 Protocols and Tools/Agent Protocols|Agent Protocols]]** · **[[AI_Home|Home]]** · **[[06 Reliability and Security/Evaluation Engineering|Evaluation Engineering]] →**
