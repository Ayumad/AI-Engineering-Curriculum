---
type: index
layer: landscape
status: current-snapshot
maturity: emerging
aliases: [Agent Infrastructure]
tags: [ai-engineering, sandboxes, browsers, gateways, observability]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "08 Tool Landscape/Agent Runtimes and Frameworks.md"
next: "09 Playbooks/Playbooks Hub.md"
summary: "| Category | Examples | Design question | |||| | Sandboxes | E2B, Daytona, Modal, Docker, microVMs | What can an agent destroy or reach? | | Browser automation | Playw..."
---


# Infrastructure and observability tools

> [!summary] The gist
> Production agents need sandboxes for safe code execution, browser tools for web interaction, gateways for model routing, and observability stacks for tracing failures. This guide covers each category: execution and browser environments, model routing with LiteLLM and OpenRouter, self-hosted vs hosted observability, four-layer guardrails architectures, prompt management, and serving platforms from Replicate to Modal. Each section includes comparison tables and selection heuristics so you can pick the right pieces for your stack.

---

## Execution and browser environments

| Category | Examples | Design question |
|---|---|---|
| Sandboxes | E2B, Daytona, Modal, Docker, microVMs | What can an agent destroy or reach? |
| Browser automation | Playwright, Browser Use, Stagehand | Is the page data or an instruction? |
| Web extraction | Firecrawl | How are provenance, robots, and freshness handled? |

## Model routing

LiteLLM and OpenRouter can centralize provider access, model routing, fallbacks, budgets, and usage accounting. They add another policy and dependency boundary, so keep provider identity, data handling, and failure behavior visible.

## Observability

LangSmith, Langfuse, Braintrust, Arize Phoenix, and OpenTelemetry-oriented instrumentation help trace model calls, retrieval, tools, state transitions, latency, cost, and evaluations. A chat transcript alone cannot explain a long-running agent failure.

### Self-hosted vs hosted observability

| Dimension | Hosted (LangSmith, Braintrust) | Self-hosted (Langfuse, OTel) |
|---|---|---|
| Setup effort | Low — SaaS, managed infrastructure | Higher — deploy, scale, maintain |
| Cost model | Seat-based + trace-based pricing | No license cost; infra cost only |
| Data control | Provider holds traces (verify DPA) | Full data sovereignty |
| Vendor lock-in | Medium — proprietary trace formats | Low — OTel portable, MIT license |
| Feature depth | Managed evals, dashboards, team features | BYO dashboards, more flexibility |
| Best for | Teams wanting fast onboarding, managed evals | Regulated industries, cost-sensitive teams at scale |

Langfuse (MIT, acquired by ClickHouse Jan 2026 for $400M Series D) tracks every LLM call cost and ships with OpenAI/Anthropic/Google model prices. Unit-based pricing with unlimited users; self-hosted has no license cost. LangSmith (proprietary) auto-records token usage/costs for major providers; seat-based + trace-based pricing; caveat: evaluators auto-upgrade traces to extended retention.

## Guardrails tools

| Tool | Approach | Latency | Best for |
|---|---|---|---|
| NVIDIA NeMo Guardrails (Apache 2.0) | Colang DSL for dialog rails across 5 pipeline stages | <50ms/GPU check | Conversation flow governance, tool-use allowlists |
| Guardrails AI | Python validators, 50+ Hub validators (PII, toxicity, JSON schema) | 50–200ms/validation | Structured output validation, re-ask loops |
| Llama Guard 3/4 (Meta, Apache 2.0) | Open-weight safety classifier; Llama Guard 4 (12B, Apr 2025) multimodal | Fast (model inference) | Content classification, ~1/3 false-positive rate of GPT-4 |
| LLM Guard (open-source) | Fast first-layer scanner for injection/jailbreak/secrets | Single-digit ms | Cheap gate before expensive guardrail layers |

**Four-layer guardrails architecture:** (1) Input guardrails (5–50ms: Prompt Guard 2, Azure Prompt Shields, Presidio PII), (2) Output guardrails (toxicity, PII, schema validation, grounding), (3) Behavioral/dialog guardrails (NeMo Colang DSL, tool-use allowlists), (4) Retrieval/tool guardrails (document trust scoring, sandboxed execution). Layer 1 (LLM Guard, single-digit ms) acts as a cheap gate before expensive layers. Regulatory context: EU AI Act GPAI obligations active Aug 2, 2025; high-risk obligations Aug 2, 2026; NIST AI 600-1 enterprise reference; OWASP Top 10 for LLM Applications 2025.

**Sources:** NeMo vs Llama Guard vs Guardrails AI — https://particula.tech/blog/ai-guardrails-compared-nemo-guardrails-ai-llama-guard · AI Guardrails implementation — https://bigdataboutique.com/blog/ai-guardrails-implementing-safety-production-llm-apps

## Prompt management tools

| Tool | What it does | Best for |
|---|---|---|
| LangSmith Hub | Version-controlled prompt repository, A/B testing, community templates | Teams using LangChain ecosystem |
| PromptLayer | Prompt versioning, usage analytics, collaborative editing | Small teams wanting prompt observability |

Prompt management belongs in the control plane alongside model selection and tool schemas. Version-control prompts, run regression evals before promotion, and track which prompt version handled which request.

## Serving and deployment platforms

| Platform | Approach | Best for | Limitations |
|---|---|---|---|
| Replicate | Cog containers, model marketplace | Lowest-friction model-to-API (predict.py + cog push) | No gateway/eval/guardrails |
| BentoML | Python-first packaging (Bento format), BentoCloud or self-host | Full runtime control, OSS without lock-in | More setup than Replicate |
| Ray Serve (2.58.0) | Framework-agnostic, model composition, dynamic batching | Multi-node/multi-GPU, fractional GPUs | Operational complexity |
| Modal | Serverless GPU FaaS, Python-native | Batch/custom compute, second-level cold starts | No native gateway/observability/guardrails; per-second billing compounds |
| Together AI | Managed open-model inference | Production OSS model serving | Provider dependency |
| Fireworks | Sub-100ms TTFT optimization | Lowest-latency inference | Provider dependency |

**Selection heuristic:** Together AI for managed open-model inference; Fireworks for lowest-latency; Anyscale for Ray-native enterprise; Replicate for one-click; BentoML for OSS-first; Modal for custom compute.

**Sources:** Ray Serve docs — https://docs.ray.io/en/latest/serve/index.html · Modal alternatives — https://futureagi.com/blog/best-modal-llm-serving-alternatives-2026/

## Sources and further reading

- E2B docs — https://e2b.dev/docs — sandbox execution for agents.
- Daytona docs — https://www.daytona.io/docs — development environment management.
- Modal docs — https://modal.com/docs — serverless GPU compute.
- Playwright docs — https://playwright.dev/docs/intro — browser automation.
- Browser Use docs — https://docs.browser-use.com/ — AI-driven browser interaction.
- Stagehand docs — https://docs.stagehand.dev/ — web automation.
- Firecrawl docs — https://docs.firecrawl.dev/ — web extraction.
- LiteLLM docs — https://docs.litellm.ai/ — multi-provider gateway.
- OpenRouter docs — https://openrouter.ai/docs — managed routing.
- LangSmith docs — https://docs.langchain.com/langsmith/home — hosted observability.
- Langfuse docs — https://langfuse.com/docs — open-source observability.
- Braintrust docs — https://www.braintrust.dev/docs — eval and observability platform.
- Arize Phoenix — https://phoenix.arize.com/ — LLM observability.
- OpenTelemetry GenAI conventions — https://opentelemetry.io/docs/specs/semconv/gen-ai/ — vendor-neutral tracing.
- NeMo vs Llama Guard vs Guardrails AI — https://particula.tech/blog/ai-guardrails-compared-nemo-guardrails-ai-llama-guard — guardrails comparison.
- AI Guardrails implementation — https://bigdataboutique.com/blog/ai-guardrails-implementing-safety-production-llm-apps — production guardrails patterns.
- Ray Serve docs — https://docs.ray.io/en/latest/serve/index.html — distributed serving.
- Modal alternatives — https://futureagi.com/blog/best-modal-llm-serving-alternatives-2026/ — serving platform comparison.

All links verified 2026-08-27.

---

---

> **← [[08 Tool Landscape/Agent Runtimes and Frameworks|Agent Runtimes and Frameworks]]** · **[[AI_Home|Home]]** · **[[09 Playbooks/Playbooks Hub|Playbooks Hub]] →**
