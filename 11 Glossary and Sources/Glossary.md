---
type: index
layer: glossary
status: evergreen
maturity: established
aliases: [AI Engineering Glossary]
tags: [ai-engineering, glossary, reference]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "11 Glossary and Sources/Sources.md"
next: "11 Glossary and Sources/Pattern Catalog.md"
summary: "| Term | Meaning | ||| | Agent | Modeldirected loop that observes, decides, acts, and stops under conditions | | AgentOps | Operations discipline for deploying, evalua..."
---


# Glossary

| Term | Meaning |
|---|---|
| Agent | Model-directed loop that observes, decides, acts, and stops under conditions |
| AgentOps | Operations discipline for deploying, evaluating, observing, securing, and improving agents |
| ACP | Agent Client Protocol; editor/client ↔ agent communication |
| A2A | Agent2Agent protocol for agent interoperability |
| AG-UI | Agent ↔ application/user-interface event boundary |
| A2UI | Agent-generated declarative user-interface surface |
| AGENTS.md | Repository or directory-scoped working instructions for coding agents; not a security boundary |
| BM25 | Lexical relevance ranking method useful for exact terms |
| Context engineering | Selecting, ordering, retrieving, compressing, and retiring model context |
| Embedding | Vector representation used for semantic similarity search |
| Effort level | A bounded choice of model reasoning, tool, time, or cost budget for a task |
| Harness | Runtime machinery around a model-directed loop |
| MCP | Model Context Protocol for tools, resources, and prompts |
| Memory | Retained facts, experiences, state, or procedures used across turns |
| Progressive disclosure | Loading only the context needed for the next decision, in increasingly detailed layers |
| Project map | A maintained index of subsystems, sources of truth, commands, owners, and navigation hints |
| PRD | Product requirements document defining a problem, goals, requirements, constraints, and acceptance criteria |
| RAG | Retrieval-Augmented Generation |
| ReAct | Reason-and-act loop pattern |
| Reranker | Model or algorithm that reorders retrieved candidates |
| Sandbox | Bounded execution environment for generated or untrusted work |
| Skill | Loadable package of procedural instructions, examples, scripts, and tools |
| Style profile | Explicit audience, tone, voice, terminology, structure, and length guidance for communication |
| Structured output | Model response constrained to a machine-validated schema |
| Tool calling | Model request to invoke a typed external operation |
| Autoregressive | Model that emits one token at a time, conditioning each on all previous tokens |
| Chain-of-thought (CoT) | Prompting technique that elicits intermediate reasoning steps before the answer |
| Constrained decoding | Sampling restricted to tokens valid under a grammar or regex, e.g., for structured output |
| Context rot | Degradation of recall and reasoning as the context window fills (Anthropic 2025) |
| Decoding | Output-generation phase; greedy vs. sampling (temperature, top-p) trade determinism for diversity |
| Golden set | Fixed, versioned evaluation cases used for regression testing of prompts and agents |
| KV cache | Cached key/value vectors for prompt tokens; grows with context length and drives long-context cost |
| LLM-as-judge | Using a model to grade outputs; scalable but biased (position, verbosity, self-preference) |
| Mixture of experts (MoE) | Architecture with sparse active parameters per token; storage ≠ per-token compute |
| Prefill | Processing the input prompt before output generation |
| Prompt caching | Reusing processed stable prefixes (system prompt, tool schemas) across calls |
| Quantization | Reducing numeric precision of weights to cut memory and bandwidth |
|| Speculative decoding | Small-model draft + target-model parallel verification; same output distribution, higher throughput |
|| Compensating action | A transaction that undoes a previous step when a later step in a multi-step workflow fails; in agent workflows, sagas pair each tool call with a compensating action so partial failures roll back cleanly ||
|| Confused deputy | A privileged agent tricked into performing unauthorized actions by prompt injection or adversarial context, misusing tools beyond intended scope ||
|| Circuit breaker | A resilience pattern that monitors failure rates and halts requests to a failing service after a threshold, preventing cascading failures through closed/open/half-open states ||
|| Data drift | Production input distributions shift away from training data (new intents, language, catalog updates), degrading retrieval metrics and grounding quality ||
|| Concept drift | The input-to-output mapping itself changes — altered reasoning paths, different tool choices, changed refusal thresholds — even when inputs look similar ||
|| Drift (concept/data) | Umbrella term for both concept drift (behavioral mapping changes) and data drift (input distribution shifts) that degrade agent reliability over time ||
|| Hallucination (types) | LLM fabrications classified as intrinsic (contradicts provided context), extrinsic (unverifiable claims), input-conflicting, context-conflicting, or fact-conflicting ||
|| Guardrails | Layered safety controls — input filtering, output validation, behavioral policy, and content classification — that constrain LLM behavior across the full request lifecycle ||
|| Idempotency | Producing the same result regardless of how many times an operation is invoked; critical for LLM API calls via idempotency keys to prevent duplicate charges on retries ||
|| LLM firewall | A fast first-pass scanner (e.g., LLM Guard) that blocks prompt injection, jailbreak strings, and secrets at single-digit-millisecond latency before more expensive guardrail layers ||
|| Reasoning effort | A parameter controlling how many internal reasoning tokens the model generates before output — lower is faster and cheaper, higher is more thorough on complex tasks ||
|| RRF (reciprocal rank fusion) | Score aggregation combining multiple ranked retrieval lists using 1/(k + rank); parameter-free and robust to scale differences across retrieval sources ||
|| Taint-aware context | Tracking which parts of an LLM's context came from untrusted sources (user input, retrieved docs, external APIs) and applying stricter validation to tainted content to prevent indirect prompt injection ||

## Sources

Definitions follow the primary references in their topic notes; aggregated, verified links live in [[11 Glossary and Sources/Sources]].

See [[11 Glossary and Sources/Acronyms]] and [[11 Glossary and Sources/Pattern Catalog]].

---

---

> **← [[11 Glossary and Sources/Sources|Sources]]** · **[[AI_Home|Home]]** · **[[11 Glossary and Sources/Pattern Catalog|Pattern Catalog]] →**
