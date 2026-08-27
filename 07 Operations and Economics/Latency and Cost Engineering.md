---
type: concept
layer: operations
status: evergreen
maturity: established
aliases: [Agent Latency, Agent Cost Engineering]
tags: [ai-engineering, latency, cost, caching, routing]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "07 Operations and Economics/Operations and Economics Hub.md"
next: "07 Operations and Economics/Local Subscription API.md"
summary: "Agent responsiveness and unit economics improve by reducing tokens and calls, caching stable prefixes, routing work to the cheapest adequate model, parallelizing independent work, and enforcing budgets."
---


# Latency and cost engineering

> [!summary] The gist
> Latency and cost engineering treats response time and spending as explicit design constraints. An agent's wait time adds up from queueing, prompt construction, model decoding, tool calls, retries, and synthesis — and cost follows the same path. This note covers the controls that matter most: generating fewer tokens, caching stable prefixes, routing by difficulty, parallelizing independent work, and streaming. It walks through budget mechanics, a worked cost model showing 89% savings from prompt caching, gateway routing with LiteLLM and OpenRouter, batch inference discounts, and per-request token budgets that keep autonomous runs from spiraling.

---

## Highest-leverage controls

- **Generate fewer tokens:** set concise output contracts and stop conditions.
- **Send fewer input tokens:** retrieve selectively, compact durable state, and remove duplicate tool output.
- **Make fewer calls:** use deterministic code for deterministic work and avoid gratuitous critique loops.
- **Cache stable prefixes:** place system rules, tool schemas, and examples before volatile conversation/RAG context.
- **Route by difficulty:** use a small/local model or code for easy tasks, reserve frontier reasoning for hard ones.
- **Parallelize safely:** fan out independent reads; join typed results rather than serializing needless waits.
- **Stream:** optimize time-to-first-useful-output, not only final completion time.

## Budget the trajectory

Give every autonomous run limits for cost, tokens, model calls, tool calls, subagents, and wall time. A harness should decide when to stop searching and synthesize the available evidence. Report incomplete work honestly rather than silently spending without bound.

Measure p50/p95 time to first token and completion, cache-hit rate, input/output tokens, tool latency, retry rate, success rate, and cost per successful outcome. Optimize a representative trace; aggregate averages often hide the long, expensive tail that users notice.

## Mechanisms with evidence

- **Speculative decoding (Leviathan et al. 2022):** a draft model proposes tokens and the target verifies them in parallel, raising tokens/s **without changing the output distribution**—a pure latency lever for decode-bound agents.
- **Prompt caching:** stable prefixes (system prompt, tool schemas, examples) are processed once and reused; cacheable prefixes belong *before* volatile conversation and retrieval context (Anthropic 2025; coordinates with [[01 Foundations/Context Windows and Inference]]).
- **Quantization and MoE:** fewer bytes per weight ([[01 Foundations/Local AI Hardware and Inference]]) and routing to active experts cut per-token cost at the platform layer.
- **Route by difficulty:** classification and extraction go to small/cheap models; ambiguous decisions to frontier reasoning—the default pattern in OpenAI's agent guide and Huyen's routing treatment.

## Worked cost model

Example: a RAG system on Sonnet 4.6, 10K context tokens, 100 queries/day.

| Scenario | Calculation | Daily cost |
|---|---|---|
| No caching | 10K × $3/M × 100 queries | $3.00 |
| With prompt caching (1 write + 100 reads) | (10K × $3.75/M × 1) + (10K × $0.30/M × 100) | $0.34 |
| Savings | | 89% |

Anthropic prompt caching: writes at 1.25× base price, reads at 0.10× (90% discount). Minimum 1,024 tokens for Sonnet/Opus, TTL 5 minutes. OpenAI prompt caching: same 1.25× write / 0.10× read ratio, minimum 1,024 tokens, default 30-minute lifetime (extendable to 24h for GPT-5.6+). At 10 requests with a shared prefix: uncached = 10×, cached = 2.15× — a 4.6× cost reduction. Cache strategy now beats model switching for enterprise cost optimization.

## Gateway detail

**LiteLLM** (open-source, Rust core + Python SDK): unified OpenAI-compatible interface to 100+ LLM providers. Routing strategies include simple-shuffle (default), rate-limit-aware (async/Redis-backed), latency-based, and cost-based. Gateway features: virtual keys, per-project spend caps, guardrails, and observability. Benchmarked at 8ms P95 at 1k RPS. Use LiteLLM when you need provider fallbacks, budget tracking, and a single API surface across vendors.

**OpenRouter**: multi-provider routing with unified billing. Default load balancing is price-weighted (inverse-square) with 30-second outage deprioritization. Sorting by price, throughput, or latency. Performance percentiles over 5-minute windows. Enterprise features include regional data residency (EU/US) and zero-data-retention per-request. Use OpenRouter when you want a managed gateway without self-hosting.

Provider header divergence: Anthropic uses `retry-after` as an RFC 3339 timestamp; OpenAI uses `Retry-After` as a duration string (`1s`, `6m0s`). Code assuming one format breaks against the other — always parse both.

## Batch inference

For non-interactive workloads (evaluations, data processing, embeddings generation), batch APIs offer 50% cost discounts from OpenAI and similar savings from other providers. Batch inference is the highest-leverage cost reduction for workloads that tolerate latency. Group requests into batch windows, use provider batch APIs where available, and size batches to balance throughput against queue wait time.

## Per-request token budgets

Every autonomous run needs explicit limits: max input tokens, max output tokens, max tool calls, max subagent depth, max cost per run, and max wall time. The harness should enforce these limits and report incomplete work honestly rather than silently spending without bound. Set per-request budgets proportional to task complexity: simple classification might cap at 2K output tokens; complex multi-step reasoning might allow 32K output tokens but require human approval above a cost threshold.

## Sources and further reading

- Leviathan et al., "Fast Inference from Transformers via Speculative Decoding," 2022 — https://arxiv.org/abs/2211.17192
- Anthropic, "Effective context engineering for AI agents," Sep 2025 — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — token efficiency and caching.
- OpenAI, "A practical guide to building agents," 2025 — https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf — model selection and cost guardrails.
- Huyen, *AI Engineering* (O'Reilly, 2025) — https://www.oreilly.com/library/view/ai-engineering/9781098166298/ — latency, caching, and routing chapters.
- LiteLLM docs, "Routing" — https://docs.litellm.ai/docs/routing — gateway routing strategies and virtual keys.
- LiteLLM GitHub — https://github.com/BerriAI/litellm — open-source LLM gateway.
- OpenRouter, "Provider Selection" — https://openrouter.ai/docs/guides/routing/provider-selection — multi-provider routing and load balancing.
- OpenAI Prompt Caching docs — https://developers.openai.com/api/docs/guides/prompt-caching — prompt caching economics and TTL.
- OpenAI Cookbook 201 — https://developers.openai.com/cookbook/examples/prompt_caching_201 — advanced caching patterns.
- Anthropic caching guide — https://prismix.dev/guides/anthropic-prompt-caching-guide — Anthropic prompt caching mechanics.
- LangSmith cost tracking — https://docs.langchain.com/langsmith/cost-tracking — provider cost recording.
- Langfuse token tracking — https://langfuse.com/docs/observability/features/token-and-cost-tracking — open-source cost tracking.
- GingerLabs pricing — https://www.gingerlabs.ai/resources/cloud-gpu-pricing — GPU pricing snapshots.

All links verified 2026-08-27.

[[01 Foundations/Local AI Hardware and Inference]] · [[02 Agents and Harnesses/Agent Harness]] · [[06 Reliability and Security/Observability]]

---

---

> **← [[07 Operations and Economics/Operations and Economics Hub|Operations and Economics Hub]]** · **[[AI_Home|Home]]** · **[[07 Operations and Economics/Local Subscription API|Local Subscription API]] →**
