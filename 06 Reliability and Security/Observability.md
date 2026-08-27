---
type: concept
layer: reliability
status: evergreen
maturity: established
aliases: [Agent Observability, Agent Tracing]
tags: [ai-engineering, observability, tracing, logs, metrics]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "06 Reliability and Security/Evaluation Engineering.md"
next: "06 Reliability and Security/Security and Jailbreaking.md"
last_verified: 2026-08-27
summary: "Metrics tell what changed, logs capture events, and traces reveal the full agent trajectory; together they make probabilistic systems diagnosable and replayable."
---


# Observability

## Plain-English introduction

Think of an airplane's black box recorder — it captures everything that happened during a flight so investigators can understand exactly what went wrong. Observability gives your AI agent the same capability. When a user asks a question and gets a strange answer, you need a complete record of what the agent searched, which tools it called, how many times it retried, what it decided, and how long everything took. This note explains how to capture that record (using metrics, logs, and traces), how to set up alerts when things drift off course, and how to build dashboards that turn raw data into actionable understanding. Without observability, diagnosing agent failures is like fixing a car blindfolded.

An agent trace represents one request as a connected operation: model calls, retrieval, tool use, subagent work, approvals, retries, and final result. It is the record needed to understand why a plausible response failed—or why a cost/latency regression appeared after a configuration change.

## Three complementary signals

| Signal | Answers | Example |
|---|---|---|
| Metrics | What is changing? | p95 latency, success rate, cost/run |
| Logs | What event occurred? | tool timeout, validation failure |
| Traces | How did this request unfold? | planner → search → retry → synthesis |

Attach correlation IDs and capture model/provider, prompt and skill versions, context/retrieval identifiers, tool name and status, state transition, token/cache use, latency, cost, error, approval, and redaction state. Record arguments carefully: retain enough to debug but redact secrets and sensitive content.

## Use traces to improve safely

Connect production traces to evaluations. A representative failed trajectory can become a regression case; replay it against a proposed version to compare result, cost, latency, and policy behavior. Dashboards should expose outcome quality and safety signals alongside throughput—not merely model-call counts.

## Standard signals and tooling

- **OpenTelemetry GenAI semantic conventions** standardize span and event attributes for LLM calls, retrieval, and agents, making traces portable across backends — see the [specification](https://opentelemetry.io/docs/specs/semconv/gen-ai/) (verified 2026-08-27).
- Managed platforms (LangSmith, Langfuse, Braintrust, Arize Phoenix) implement traces, evals, and replay out of the box; see [[08 Tool Landscape/Infrastructure and Observability Tools]].
- Instrument before you need it: capture model/prompt/tool/context versions on every span so a production failure can be replayed identically in evaluation (OpenAI 2025; OTel GenAI conventions).

## OpenTelemetry GenAI: span walkthrough

A minimal OTel GenAI trace for an agent request creates these spans in order:

1. **`gen_ai.system`** (top-level span): attributes `gen_ai.operation.name = "chat"`, `gen_ai.request.model`, `gen_ai.request.max_tokens`.
2. **`gen_ai.chat`** (child span): captures `gen_ai.response.model`, `gen_ai.usage.input_tokens`, `gen_ai.usage.output_tokens`, `gen_ai.usage.total_tokens`.
3. **`gen_ai.tool_call`** (child span per tool invocation): `gen_ai.tool.name`, `gen_ai.tool.call.id`, `gen_ai.tool.call.arguments` (truncated/sanitized).
4. **`gen_ai.retrieval`** (if RAG): `gen_ai.retrieval.query`, `gen_ai.retrieval.results.count`, `gen_ai.retrieval.results.score` (top-k).

Each span carries a `trace_id` that correlates model calls, tool invocations, and retrieval into a single request trajectory. The GenAI semantic conventions at https://opentelemetry.io/docs/specs/semconv/gen-ai/ define these attributes portably across backends (Langfuse, Braintrust, Arize Phoenix, Grafana Tempo).

**Practical instrumentation**: wrap your LLM client and tool executor with OTel spans. Most frameworks (LangChain, OpenAI Agents SDK, Mastra) emit GenAI-compatible traces when OTel exporters are configured. Set `OTEL_EXPORTER_OTLP_ENDPOINT` to your collector and enable the GenAI semantic conventions.

## Alerting and threshold guidance

Set alerts at three levels:

- **Latency**: p95 latency > 2× baseline for 10+ minutes → warning. p99 > 5× baseline → critical. Agent trajectories should be measured end-to-end, not just per-model-call.
- **Error rate**: tool failure rate > 5% over a 5-minute window → warning. > 15% → critical (likely upstream API issue or tool schema change).
- **Cost**: per-request cost > 3× the rolling 7-day average → investigate. Daily aggregate cost > 120% of budget → alert.
- **Safety**: jailbreak detection trigger rate spike (> 2× baseline) → investigate immediately. PII leak rate > 0 → P0.

Use anomaly detection (rolling z-score or percent change) rather than fixed thresholds where baselines vary by time-of-day or workload. Log all alerts with the same correlation ID as the trace they reference.

## Dashboard patterns

An effective agent observability dashboard groups signals into four panels:

1. **Outcome quality**: success rate, human approval rate, LLM-judge scores over time. This is the primary signal—everything else is diagnostic.
2. **Trajectory health**: p50/p95/p99 latency, tool call count per request, retry rate, escalation rate. Break down by agent type/prompt version.
3. **Cost and throughput**: tokens consumed, cost per request, requests per minute. Track both aggregate and per-model-provider.
4. **Safety and errors**: guardrail trigger rates, jailbreak detection, error categories (tool failure, timeout, schema violation, permission denied).

Pin model/prompt/tool version alongside every metric so you can correlate regressions with deployments.

## Drift monitoring

Agent systems drift in four dimensions:

- **Reasoning drift**: LLM produces different plans for same input (model updates, context truncation). Detection: golden prompt regression runs, token-level plan diffing.
- **Retrieval drift**: vector embeddings, chunking, or index health change → different grounding. Detection: retrieval accuracy benchmarks (hash returned chunk IDs vs baseline), embedding distribution shifts.
- **Tool drift**: MCP upgrades, schema changes, API differences alter tool behavior. Detection: schema hashing (detect silent MCP tool schema changes), output shape consistency checks.
- **User/workflow drift**: usage pattern changes, intent distribution shifts. Detection: cohort analyses, output shape consistency, latency drift tracking.

**Minimal monitoring stack** (catches ~80% of real-world drift events): (1) daily golden prompt regression runs, (2) weekly top-k retrieval benchmarks, (3) continuous tool schema hashing.

**Cadence**: daily golden prompt runs, weekly retrieval benchmarks, continuous schema hashing, real-time anomaly alerts on tool latency/error rates. Map behavior with schemas and traceability → Measure with component-wise and end-to-end evals → Manage with observability, gateway governance, and data curation (NIST AI RMF).

## Sources and further reading

- OpenTelemetry, "GenAI semantic conventions" — https://opentelemetry.io/docs/specs/semconv/gen-ai/ — standardized span attributes for LLM calls.
- OTel GenAI GitHub — https://github.com/open-telemetry/semantic-conventions-genai — reference implementations and examples.
- APML Substack, "Why AI Agents Rot: The 4 Hidden Drifts" (2026) — https://apml.substack.com/p/why-ai-agents-rot-the-4-hidden-drifts — four drift types, detection methods, minimal monitoring stack.
- Dev.to/Kuldeep Paul, "Managing AI Agent Drift Over Time" (2026) — https://dev.to/kuldeep_paul/managing-ai-agent-drift-over-time-a-practical-framework-for-reliability-evals-and-observability-1fk8 — NIST AI RMF mapping, RAG eval metrics.
- APXML, "Monitor RAG Retrieval Drift" — https://apxml.com/courses/optimizing-rag-for-production/chapter-6-advanced-rag-evaluation-monitoring/monitoring-retrieval-drift-rag — retrieval accuracy benchmarks.
- OpenAI, "A practical guide to building agents," 2025 — https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf — observability as a design requirement.
- Tool docs indexed in [[11 Glossary and Sources/Sources]] and [[08 Tool Landscape/Infrastructure and Observability Tools]].

All links verified 2026-08-27.

[[06 Reliability and Security/Evaluation Engineering]] · [[07 Operations and Economics/Deployment and AgentOps]] · [[07 Operations and Economics/Latency and Cost Engineering]]

---

---

> **← [[06 Reliability and Security/Evaluation Engineering|Evaluation Engineering]]** · **[[AI_Home|Home]]** · **[[06 Reliability and Security/Security and Jailbreaking|Security and Jailbreaking]] →**
