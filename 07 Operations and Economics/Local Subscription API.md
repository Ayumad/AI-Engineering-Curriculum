---
type: decision
layer: operations
status: evergreen
maturity: established
aliases: [Local vs Subscription vs API]
tags: [ai-engineering, economics, deployment, models]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "07 Operations and Economics/Latency and Cost Engineering.md"
next: "07 Operations and Economics/Deployment and AgentOps.md"
summary: "| Model | Strengths | Costs and limits | |||| | Local | Privacy, control, predictable marginal cost, offline use | Hardware, setup, maintenance, model quality, heat, u..."
---


# Local models, subscriptions, APIs, and hybrid designs

## Plain-English introduction

Choosing how to run an AI model is a bit like choosing how to get your electricity. You can install solar panels on your roof and be self-sufficient, pay a utility company every month and never think about it, or buy power on the open market when you need it. Each option has trade-offs in cost, control, and convenience. This note walks through the practical differences between running models locally, subscribing to a hosted service, calling APIs, and mixing all three together — so you can pick the approach that fits your budget, your privacy needs, and your tolerance for maintenance.

| Model | Strengths | Costs and limits |
|---|---|---|
| Local | Privacy, control, predictable marginal cost, offline use | Hardware, setup, maintenance, model quality, heat, upgrades |
| Subscription | Simple UX, bundled limits, hosted capability, predictable personal spend | Provider limits, less control, account dependence, data policy |
| API | Programmatic scale, model choice, usage-based economics, routing | Variable spend, latency, secrets, quotas, vendor and network dependence |
| Hybrid | Route sensitive/cheap/fast work locally and difficult work to hosted models | More operational complexity and policy design |

## Decision sequence

1. Classify data sensitivity and residency requirements.
2. Estimate concurrency, context size, latency, and monthly volume.
3. Price hardware plus maintenance against API usage and engineering time.
4. Compare capability, reliability, licensing, provider lock-in, and fallback options.
5. Decide what can be cached, batched, downgraded, or routed to a smaller model.
6. Add budgets, quotas, alerts, redaction, and an exit plan.

There is no universally cheapest option: utilization, task mix, and operational labor dominate. Use a gateway such as LiteLLM or OpenRouter when routing, observability, and fallback value exceed their added complexity.

## Pricing snapshot (2026-08-27)

> **Note:** These are representative figures from research-D snapshots. Actual prices change frequently — verify with vendors before making commitments.

| Model/Provider | Type | Cost per 1M tokens | Notes |
|---|---|---|---|
| GPT-4o (OpenAI) | API | ~$4.38/M | Frontier, pay-per-use |
| Llama 70B fp16 (H100/Lambda) | Self-hosted | ~$0.49/M | Requires H100 (~$2.49/hr on-demand) |
| Llama 8B fp16 (A10G/AWS) | Self-hosted | ~$0.14/M | Smaller model, commodity GPU |
| Llama 70B int4 (A100/Lambda) | Self-hosted | ~$0.92/M | Quantized, fits single A100 |
| Lambda Labs A100 80GB | On-demand GPU | ~$1.99/hr | ~60% cheaper than AWS |
| AWS A100 80GB | On-demand GPU | ~$5.12/hr | Hyperscaler premium |
| Lambda Labs H100 SXM | On-demand GPU | ~$2.49/hr | ~80% cheaper than AWS/GCP |
| Spot/preemptible A100 | Spot GPU | ~$1.49–1.99/hr | 50–80% discount, batch/dev only |

**Self-hosting crossover** (2026-08-27): ~3–5B tokens/month on a flagship model. Below that, API pricing is cheaper after accounting for GPU rental, ops labor, and maintenance.

## Data residency and GDPR

API providers store prompts and completions on their infrastructure by default. For GDPR-regulated data, consider: (1) providers with explicit EU data residency (OpenRouter enterprise, Azure OpenAI), (2) self-hosting on EU-located GPUs (Lambda EU, Hetzner), (3) hybrid routing where sensitive queries stay local and non-sensitive queries go to hosted models. Anthropic and OpenAI both offer zero-data-retention options for enterprise tiers. Always verify data processing agreements (DPAs) and sub-processor lists before routing sensitive data through any provider.

## Hybrid routing pattern

The hybrid pattern routes work across local and hosted models based on data sensitivity, latency requirements, and cost thresholds:

1. **Classify the request** — data sensitivity (PII, financial, health), latency requirement (interactive vs batch), complexity (simple classification vs complex reasoning).
2. **Route by policy** — sensitive data stays on self-hosted models; simple tasks go to cheap/small models (Llama 8B locally); complex reasoning goes to frontier models via API; batch work goes to the cheapest provider with acceptable latency.
3. **Enforce budgets** — per-tenant token limits, per-query cost caps, daily spend ceilings enforced at the gateway layer (LiteLLM virtual keys or OpenRouter per-key budgets).
4. **Monitor and adjust** — track cost per task type, cache hit rates, and quality scores; rebalance routing thresholds monthly based on actual usage patterns.

## Sources and further reading

- GingerLabs, "Cloud GPU Pricing" — https://www.gingerlabs.ai/resources/cloud-gpu-pricing — on-demand and spot GPU pricing snapshots.
- GPU Cloud Prices — https://gpucloudprices.com/ — cross-provider GPU price comparison.
- GetDeploying — https://getdeploying.com/gpus — GPU availability and pricing tracker.
- LiteLLM docs — https://docs.litellm.ai/docs/routing — gateway routing and virtual keys for hybrid deployments.
- OpenRouter docs — https://openrouter.ai/docs — managed multi-provider routing.

All links verified 2026-08-27.

## Related

[[08 Tool Landscape/Tool Landscape Hub|Tool landscape]] · [[07 Operations and Economics/Latency and Cost Engineering]]

---

---

> **← [[07 Operations and Economics/Latency and Cost Engineering|Latency and Cost Engineering]]** · **[[AI_Home|Home]]** · **[[07 Operations and Economics/Deployment and AgentOps|Deployment and AgentOps]] →**
