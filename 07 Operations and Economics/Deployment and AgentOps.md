---
type: concept
layer: operations
status: evergreen
maturity: established
aliases: [AgentOps, Production Agents]
tags: [ai-engineering, deployment, agentops, operations]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "07 Operations and Economics/Local Subscription API.md"
next: "08 Tool Landscape/Tool Landscape Hub.md"
summary: "Separate a control plane from disposable execution workers. Put policy, identity, queues, durable state, model routing, and telemetry in the control plane; run code an..."
---


# Deployment and AgentOps

## Plain-English introduction

Getting an AI agent to work on your laptop is one thing. Getting it to work reliably for hundreds of users, every day, without surprises — that is a different challenge entirely. Deployment and AgentOps covers the practices borrowed from traditional software operations and adapted for the unique quirks of AI systems: versioning not just code but prompts and configurations, rolling out changes safely, and keeping an eye on things when they go sideways. Think of it as the owner's manual for keeping your agent factory running after it opens for business.

AgentOps is DevOps for probabilistic, tool-using systems. Version and deploy more than code: the agent configuration, model and provider, prompts, skills, tool schemas, retrieval/index configuration, policies, and evaluation datasets can each change real behavior.

## Delivery loop

```text
change → static checks → evals → security tests → staging → canary → production
  ↑                                                               │
  └────────── traces, feedback, production evals, rollback ──────┘
```

Run a baseline before each change and compare quality, safety, cost, latency, handoffs, and error rate. Roll out a small cohort first, keep the prior artifact set available, and roll back the full compatible configuration—not only application code.

## Operational contract

Every production run needs an owner, a versioned configuration, a bounded resource policy, traces with redaction, alert thresholds, and an escalation path. A system that cannot state which prompt, model, skill, retriever, and tool version handled a failure is not yet operable.

See [[06 Reliability and Security/Evaluation Engineering]], [[06 Reliability and Security/Observability]], and [[07 Operations and Economics/Latency and Cost Engineering]].

Separate a control plane from disposable execution workers. Put policy, identity, queues, durable state, model routing, and telemetry in the control plane; run code and untrusted transformations in isolated workers.

The deployable surface commonly includes an API or UI, stateless workers, a queue, a state database, an artifact store, a vector/keyword index, sandbox or container workers, secrets/identity services, and tracing. CI/CD should promote prompts, skills, schemas, retrieval indexes, and code together with regression evidence.

## Production checklist

- Version prompts, skills, tool schemas, models, and retrieval indexes.
- Use a queue with timeouts, retries, dead-letter handling, and backpressure.
- Store checkpoints and artifacts with retention and deletion policies.
- Broker short-lived credentials and redact secrets from traces.
- Monitor success, tool errors, approval rate, cost, latency, token use, drift, and safety incidents.
- Run offline regression and trajectory evaluations before promotion.
- Keep a kill switch, human escalation path, and incident runbook.

AgentOps is the operational discipline around these controls: release management, evaluation, observability, cost governance, security review, and continuous improvement.

## Serving stacks

| Stack | Approach | Throughput | Best for |
|---|---|---|---|
| vLLM | PagedAttention (eliminates KV cache fragmentation 60–80% waste → <4%), continuous batching (iteration-level scheduling) | 793 tok/s (Red Hat benchmark, 19× Ollama); 85–92% GPU utilization; Stripe: 73% cost reduction on 50M daily calls, 1/3 GPU fleet | High-throughput production serving; used by Meta, Mistral, Cohere, IBM |
| TGI (HuggingFace) | Rust router, Prometheus+OpenTelemetry built-in | 13× faster than vLLM on 200k+ token prompts | Ultra-long-context serving — **maintenance mode since Dec 11, 2025** |
| Ollama | Built on llama.cpp, GGUF only, sequential FIFO queue | 41 tok/s; P99=24.7s at 50 concurrent users (vs vLLM 2.8s) | Local dev, Apple Silicon, single-user demos — not concurrent production |
| llama.cpp | C/C++, CPU/GPU, GGUF format | Varies by hardware | Edge, CPU-only, resource-constrained deployments |

**When self-hosting beats API** (2026-08-27 snapshot): crossover at ~3–5B tokens/month on a flagship model. A 70B model on H100 costs ~$0.49/M tokens vs GPT-4o at $4.38/M. Key advantage: data sovereignty. Key disadvantage: ops burden (model updates, GPU management, failover). Quantization support varies: vLLM and llama.cpp handle GGUF/GPTQ/AWQ; Ollama is GGUF-only. Always benchmark your workload — throughput claims vary by batch size, sequence length, and model.

**Sources:** GingerLabs comparison — https://gingerlabs.ai/blog/vllm-vs-ollama-vs-tgi · Anyscale continuous batching — https://www.anyscale.com/blog/continuous-batching-llm-inference · LearnOpenCV vLLM — https://learnopencv.com/vllm-deploy-llms-at-scale-paged-attention/

## Rate limiting, backoff, and jitter

Production agents hit five distinct 429 error types: (1) per-minute cap (wait), (2) spend/credit cap (never clears), (3) acceleration limit (gradual ramp), (4) free-tier daily cap, (5) upstream capacity (fail over, don't retry same route). Backoff pattern: 1s, 2s, 4s, 8s, 16s with jitter ±30%, cap 5 attempts; use the Retry-After value when present. Note that Anthropic uses an RFC 3339 timestamp while OpenAI uses a duration string — code assuming one shape breaks against the other.

Five surfaces to monitor: LLM provider, embedding provider, destination tools, database/vector store, and your own concurrency caps. Production pattern: cap concurrency with a semaphore, persist queues (Redis/SQS), per-item retry budgets with a dead-letter queue, and alert on queue depth, 429 rate, retry rate, dead-letter size, and p95 latency. Anthropic's token bucket is continuously replenished (not fixed-window); OpenAI SDK v2.53.0 defaults to 2 retries and refuses retry >120s.

**Sources:** Ofox 429 guide — https://ofox.io/blog/429-too-many-requests-rate-limit-exceeded-when-to-try-2026/ · Gravity Fast — https://gravity.fast/blog/how-to-handle-agent-rate-limits/ · Ofox 429 types — https://ofox.io/blog/429-too-many-requests-rate-limit-exceeded-when-to-retry-2026/

## Incident response and rollback

Every production agent needs an incident runbook: a kill switch to halt autonomous execution, an escalation path (human approval gate), and a rollback procedure. Roll back the full compatible configuration — not only application code — including prompts, skills, tool schemas, retrieval indexes, and model selection. Keep the prior artifact set available for instant rollback. A system that cannot state which prompt, model, skill, retriever, and tool version handled a failure is not yet operable.

## Multi-tenancy and isolation

Separate a control plane (policy, identity, queues, durable state, model routing, telemetry) from disposable execution workers (code and untrusted transformations). Run each tenant's agent execution in isolated workers with scoped permissions. Shared control-plane resources (routing, budget tracking, observability) must enforce per-tenant limits on concurrency, token spend, and tool access. Tenants should not be able to observe or influence each other's traces, queues, or state.

## Sources and further reading

- GingerLabs, "vLLM vs Ollama vs TGI," 2026 — https://gingerlabs.ai/blog/vllm-vs-ollama-vs-tgi — throughput comparison across serving stacks.
- Anyscale, "Continuous Batching for LLM Inference" — https://www.anyscale.com/blog/continuous-batching-llm-inference — iteration-level scheduling mechanics.
- LearnOpenCV, "vLLM: Deploy LLMs at Scale" — https://learnopencv.com/vllm-deploy-llms-at-scale-paged-attention/ — PagedAttention memory management.
- Ofox, "429 Too Many Requests — When to Retry in 2026" — https://ofox.io/blog/429-too-many-requests-rate-limit-exceeded-when-to-retry-2026/ — five 429 error types and retry strategy.
- Gravity Fast, "How to Handle Agent Rate Limits" — https://gravity.fast/blog/how-to-handle-agent-rate-limits/ — backoff patterns and jitter.

All links verified 2026-08-27.

---

---

> **← [[07 Operations and Economics/Local Subscription API|Local Subscription API]]** · **[[AI_Home|Home]]** · **[[08 Tool Landscape/Tool Landscape Hub|Tool Landscape Hub]] →**
