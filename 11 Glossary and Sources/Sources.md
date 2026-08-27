---
type: source-registry
layer: glossary
status: current-snapshot
maturity: established
aliases: [AI Engineering Sources]
tags: [ai-engineering, sources, citations]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "10 Maps/AI Engineering Atlas.md"
next: "11 Glossary and Sources/Glossary.md"
summary: "Current product and protocol claims in this vault were checked on 20260827. Recheck dated notes before acting on pricing, availability, feature support, security postu..."
---


# Sources and verification policy

Current product and protocol claims in this vault were checked on 2026-08-27. Updated 2026-08-27 (research wave). Re-check dated notes before acting on pricing, availability, feature support, security posture, or maturity. Add new sources here when a note's "Sources and further reading" section grows; keep one bullet per source with the claim it grounds.

## Foundational papers

- [Vaswani et al., "Attention Is All You Need," 2017](https://arxiv.org/abs/1706.03762) — the transformer architecture behind modern LLMs.
- [Kaplan et al., "Scaling Laws for Neural Language Models," 2020](https://arxiv.org/abs/2001.08361) — loss scales as a power law with model size, data, and compute.
- [Hoffmann et al., "Training Compute-Optimal Large Language Models" (Chinchilla), 2022](https://arxiv.org/abs/2203.15556) — compute-optimal balance of model size and training data.
- [Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models," 2022](https://arxiv.org/abs/2201.11903) — reasoning emerges with scale via intermediate reasoning steps.
- [Yao et al., "ReAct: Synergizing Reasoning and Acting in Language Models," 2022](https://arxiv.org/abs/2210.03629) — the canonical reason-and-act agent loop.
- [Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks," 2020](https://arxiv.org/abs/2005.11401) — RAG origin: parametric + non-parametric memory.
- [Khattab & Zaharia, "ColBERT," 2020](https://arxiv.org/abs/2004.12832) — late-interaction token-level passage search.
- [Zheng et al., "Judging LLM-as-a-Judge with MT-Bench and Chatbot Arena," 2023](https://arxiv.org/abs/2306.05685) — LLM judge quality and known biases.
- [Leviathan et al., "Fast Inference from Transformers via Speculative Decoding," 2022](https://arxiv.org/abs/2211.17192) — parallel draft-and-verify decoding speedup.
- [Dettmers et al., "QLoRA: Efficient Finetuning of Quantized LLMs," 2023](https://arxiv.org/abs/2305.14314) — 4-bit quantization enables 65B fine-tuning on a single 48 GB GPU.
- [Radford et al., "Robust Speech Recognition via Large-Scale Weak Supervision" (Whisper), 2022](https://arxiv.org/abs/2212.04356) — weak-supervision speech model at scale.
- [Willard & Louf, "Efficient Guided Generation for Large Language Models," 2023](https://arxiv.org/abs/2307.09702) — grammar/regex-constrained decoding (Outlines).
- [Greshake et al., "Not what you've signed up for," 2023](https://arxiv.org/abs/2302.12173) — indirect prompt injection in real LLM-integrated apps.
- [Zou et al., "Universal and Transferable Adversarial Attacks on Aligned Language Models," 2023](https://arxiv.org/abs/2307.15043) — automated adversarial suffixes (GCG).
- [Song et al., "More Agents Is All You Need," 2024](https://arxiv.org/abs/2402.05120) — sampling-and-voting: more agents help, gains asymptotic.
- [Malkov & Yashunin, "Efficient and robust approximate nearest neighbor search using HNSW graphs," 2016–2018](https://arxiv.org/abs/1603.09320) — hierarchical navigable small-world graphs; logarithmic-complexity ANN search; the default index family in many vector stores.
- [Gao et al., "SimCSE: Simple Contrastive Learning of Sentence Embeddings," 2021](https://arxiv.org/abs/2104.08821) — contrastive sentence-embedding training; dropout-as-noise; anisotropy regularization.
- [Aumüller, Bernhardsson & Faithfull, "ANN-Benchmarks," Information Systems 2019](https://github.com/erikbern/ann-benchmarks) — empirical recall/latency comparison of approximate-nearest-neighbor libraries.
- [Nussbaum et al., "Nomic Embed: Training a Reproducible Long Context Text Embedder," 2024](https://arxiv.org/abs/2402.01613) — two-stage contrastive 8K-context embedder; see the [official model card](https://huggingface.co/nomic-ai/nomic-embed-text-v1) for task-prefix usage.

## Protocols and standards

- [Model Context Protocol specification](https://modelcontextprotocol.io/specification/2025-06-18/server/index)
- [Agent Client Protocol overview](https://github.com/agentclientprotocol/agentclientprotocol/blob/main/docs/protocol/v2/overview.mdx)
- [A2A protocol](https://a2a-protocol.org/)
- [OpenTelemetry GenAI semantic conventions](https://opentelemetry.io/docs/specs/semconv/gen-ai/)

## Agent products

- [Herdr](https://herdr.dev/) · [Herdr repository](https://github.com/herdrdev/herdr)
- [Hermes Agent documentation](https://hermes-agent.nousresearch.com/docs/) · [repository](https://github.com/NousResearch/hermes-agent)
- [DeepSeek Harness preview](https://www.deepseek.com/harness/) · [repository](https://github.com/deepseek-ai/deepseek-harness)
- [OpenAI Codex](https://openai.com/codex/) · [Codex app announcement](https://openai.com/index/introducing-the-codex-app/)
- [Cursor modes](https://docs.cursor.com/en/agent/modes) · [Cursor docs](https://cursor.com/docs)
- [Claude Code docs](https://docs.anthropic.com/en/docs/claude-code/overview) · [Agent SDK](https://docs.anthropic.com/en/docs/agents-and-tools/agent-sdk/overview)
- [OpenCode docs](https://opencode.ai/docs) · [repository](https://github.com/sst/opencode)
- [Pi docs](https://pi.dev/docs/latest)
- [OpenHands overview](https://docs.openhands.dev/overview/introduction) · [SDK](https://docs.openhands.dev/sdk/index)
- [Kimi Code CLI](https://www.kimi.com/code/docs/en/kimi-code-cli/reference/kimi-command) · [ACP/IDE integration](https://www.kimi.com/en/help/kimi-code/cli-ides)

## Frameworks and infrastructure

- [LangGraph](https://docs.langchain.com/oss/python/langgraph/overview) · [PydanticAI](https://ai.pydantic.dev/) · [Mastra](https://mastra.ai/docs) · [Temporal](https://docs.temporal.io/)
- [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/) · [LangChain](https://docs.langchain.com/oss/python/langchain/overview)
- [E2B](https://e2b.dev/docs) · [Daytona](https://www.daytona.io/docs) · [Modal](https://modal.com/docs)
- [Playwright](https://playwright.dev/docs/intro) · [Browser Use](https://docs.browser-use.com/) · [Stagehand](https://docs.stagehand.dev/) · [Firecrawl](https://docs.firecrawl.dev/)
- [LiteLLM](https://docs.litellm.ai/) · [OpenRouter](https://openrouter.ai/docs)
- [Chroma docs](https://docs.trychroma.com/) — open-source retrieval store: local/single-node/distributed modes, collections (id + embedding + metadata + document), dense+sparse search, metadata filters.
- [Ollama embeddings API](https://docs.ollama.com/api/embed) · [nomic-embed-text](https://ollama.com/library/nomic-embed-text) — local embedding serving; embedding-only model, 8K context, beats ada-002 / text-embedding-3-small on short and long context tasks.
- [LangSmith](https://docs.langchain.com/langsmith/home) · [Langfuse](https://langfuse.com/docs) · [Braintrust](https://www.braintrust.dev/docs) · [Arize Phoenix](https://phoenix.arize.com/)

## Experts and practitioner writing

- [Karpathy](https://karpathy.ai/) · [Intro to Large Language Models (1-hour talk)](https://www.youtube.com/watch?v=zjkBMFhNj_g) — the two-file model, training, tokenization, and memory-bandwidth-bound decoding intuition.
- [Huyen, *AI Engineering* (O'Reilly, 2025)](https://www.oreilly.com/library/view/ai-engineering/9781098166298/) · [companion repo `chiphuyen/aie-book`](https://github.com/chiphuyen/aie-book) — the standard practitioner text on building applications with foundation models.
- [Weng, "LLM Powered Autonomous Agents" (2023)](https://lilianweng.github.io/posts/2023-06-23-agent/) — planning, memory, tool use; her [entire Lil'Log archive](https://lilianweng.github.io/) covers prompting and evaluation essays.
- [Willison's prompt-injection series](https://simonwillison.net/series/prompt-injection/) — coined the term (Sep 2022); the practitioner archive of real-world injection and defense.
- [Anthropic engineering blog](https://www.anthropic.com/engineering) — "Building effective agents" (Dec 2024), "Introducing Contextual Retrieval" (Sep 2024), "How we built our multi-agent research system" (Jun 2025), "Effective context engineering for AI agents" (Sep 2025).
- [OpenAI, "A practical guide to building agents" (2025)](https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf) — tooling, orchestration, guardrails, and evaluation patterns from deployed agents.

## Security and risk frameworks

- [OWASP Top 10 for LLM Applications 2025](https://genai.owasp.org/resource/owasp-top-10-for-llm-applications-2025/) — community risk framework for LLM apps (started 2023); covers prompt injection, sensitive information disclosure, excessive agency.
- [OWASP Top 10 for Agentic Applications (Dec 2025)](https://genai.owasp.org/) — first edition for autonomous systems; categories include agent goal hijack, tool misuse/excessive agency, and memory poisoning.

## Serving, routing, and evaluation tooling

- [LiteLLM docs](https://docs.litellm.ai/docs/routing) — unified OpenAI-compatible interface to 100+ LLM providers; gateway routing strategies (rate-limit, latency, cost-based); 8ms P95 at 1k RPS.
- [LiteLLM GitHub](https://github.com/BerriAI/litellm) — open-source gateway with virtual keys, per-project spend, and observability.
- [OpenRouter routing](https://openrouter.ai/docs/guides/routing/provider-selection) — multi-provider routing with price-weighted load balancing and outage deprioritization.
- [vLLM — Anyscale continuous batching](https://www.anyscale.com/blog/continuous-batching-llm-inference) — PagedAttention and iteration-level scheduling; 793 tok/s vs Ollama 41 tok/s.
- [GingerLabs — vLLM vs Ollama vs TGI](https://gingerlabs.ai/blog/vllm-vs-ollama-vs-tgi) — production benchmarking of self-hosted serving stacks.
- [promptfoo docs](https://www.promptfoo.dev/docs/intro/) — open-source eval CLI/library; YAML test cases, red-teaming, CI integration. MIT.
- [DeepEval website](https://deepeval.com/) — pytest-native eval with 50+ metrics (hallucination, faithfulness, toxicity). Apache-2.0.
- [Braintrust website](https://www.braintrust.dev/) — hosted eval and observability platform; SOC 2 Type II, quality gates.
- [lm-eval-harness GitHub](https://github.com/EleutherAI/lm-evaluation-harness) — EleutherAI academic benchmark framework; 60+ benchmarks.
- [Arize Phoenix](https://phoenix.arize.com/) — open-source observability and eval; OpenTelemetry-native.
- [Langfuse docs](https://langfuse.com/docs) — open-source LLM observability; MIT; acquired by ClickHouse 2026.
- [MITRE ATLAS](https://atlas.mitre.org/) — living knowledge base of adversary tactics and techniques against AI systems.
- [PyRIT — Microsoft GitHub](https://github.com/microsoft/PyRIT) — multi-turn red-teaming framework; text/image/audio/video modalities.
- [HarmBench GitHub](https://github.com/centerforaisafety/HarmBench) — standardized benchmark for automated red teaming; 510 harmful behaviors, 18 attack methods.
- [NVIDIA NeMo Guardrails GitHub](https://github.com/NVIDIA-NeMo/Guardrails) — programmable dialog rails in Colang DSL; 5 pipeline stages; Apache 2.0.
- [Guardrails AI website](https://guardrailsai.com/) — Python validators with 50+ Hub validators (PII, toxicity, schema).
- [Meta Llama on HuggingFace](https://huggingface.co/meta-llama) — Llama Guard open-weight safety classifiers (Llama Guard 3 and 4).
- [Ofox 429 guide](https://ofox.io/blog/429-too-many-requests-rate-limit-exceeded-when-to-retry-2026/) — five 429 error types and production backoff patterns.
- [Gravity Fast — agent incident response](https://gravity.fast/blog/ai-agent-incident-response/) — P0-P3 severity tiers, kill switch procedures, prevention stack.
- [APML Substack — AI agent drift](https://apml.substack.com/p/why-ai-agents-rot-the-4-hidden-drifts) — four drift types, detection methods, minimal monitoring stack.
- [Dev.to / Kuldeep Paul — agent drift framework](https://dev.to/kuldeep_paul/managing-ai-agent-drift-over-time-a-practical-framework-for-reliability-evals-and-observability-1fk8) — NIST AI RMF mapping, RAG eval metrics.
- [Anomity — EU AI Act for agents](https://anomity.ai/blog/eu-ai-act-ai-agents-guide/) — risk tiers, article mapping to agents/MCP, practical compliance steps.

## Research and security reading

Use original papers, model/system cards, and provider security documentation for jailbreak, prompt-injection, RAG, agent-evaluation, and multi-agent claims. The vault records forecasts as forecasts and does not present them as established findings.

---

---

> **← [[10 Maps/AI Engineering Atlas|AI Engineering Atlas]]** · **[[AI_Home|Home]]** · **[[11 Glossary and Sources/Glossary|Glossary]] →**
