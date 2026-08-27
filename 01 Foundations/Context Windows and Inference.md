---
type: concept
layer: foundations
status: evergreen
maturity: established
aliases: [Context Window, Inference]
tags: [ai-engineering, context, inference]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "01 Foundations/What Is an LLM.md"
next: "01 Foundations/Structured Outputs and Tool Calling.md"
summary: "The context window is the model's temporary working set: instructions, conversation, files, retrieved evidence, tool descriptions, tool results, plans, and memory. It..."
---


# Context windows and inference

> [!summary] The gist
> The context window is a fixed-size stack of papers the model can read at once. Once it's full, older material must be dropped or summarized. More context isn't automatically better—irrelevant material wastes space and dilutes the answer. This note explains how the window works and how to manage it.

---

## The inference budget

The context window is the model's temporary working set: system instructions, conversation, retrieved evidence, tool descriptions/results, and generated tokens all compete for it. It is finite, transient, and not equivalent to long-term storage. Larger context is useful only when the included material is relevant, ordered well, and small enough to preserve headroom for the answer and tool loop.

Inference has two visibly different phases. **Prefill** processes the prompt and grows with input size; **decoding** generates one or more output tokens and often dominates interactive response time. Sampling controls trade determinism for diversity, while structured outputs reduce the recovery work downstream.

## Practice

For a long-running task, record a compact state containing goal, constraints, decisions, open questions, verified evidence, and next steps. Retrieve detail only when it becomes relevant. Measure input tokens, output tokens, cache hits, time to first token, completion time, and answer quality before deciding that a longer prompt improved anything.

## Why it matters

More context is not automatically better. Irrelevant or contradictory material consumes budget and can dilute the evidence needed for the next decision. Treat context like RAM: load the smallest useful set, summarize stale material, and retrieve on demand.

Inference includes the model call, internal computation, decoding, latency, and token usage. Reasoning models may spend more computation before responding; that improves some tasks but affects cost and latency.

## Engineering levers

- Reserve space for tool results and recovery turns.
- Keep durable rules in project context; keep incident details in task context.
- Prefer structured state and summaries over replaying an entire transcript.
- Measure answer quality as context grows; watch for context rot.
- Route cheap models to classification or extraction and stronger models to ambiguous decisions.

## Tokenization, the KV cache, and why long context costs more

- **Tokens are subword units, not words.** Tokenization choices determine context length, cost, and how code, identifiers, and rare terms are represented (Karpathy 2023; Huyen 2025).
- **The KV cache** stores computed key/value vectors for prior tokens; it grows with context length across layers and heads, which is why long context consumes memory and price even when the output is short.
- **KV cache size formula:** For a transformer with `n_layers`, `n_kv_heads`, and `d_head` dimensions, the KV cache for a sequence of length `seq_len` at FP16 uses approximately `2 × n_layers × n_kv_heads × d_head × seq_len` bytes. For example, Llama-3 70B (80 layers, 8 KV heads, 128 d_head) at 128K context: `2 × 80 × 8 × 128 × 128,000 ≈ 21 GB`—a substantial memory cost that must be reserved alongside model weights.
- **Attention is pairwise:** n tokens create O(n²) relationships, and recall precision degrades as the window fills—"context rot." A big window is capacity, not automatically quality (Anthropic 2025).

## Provider context-window sizes (snapshot 2026-08-27)

| Provider / Model | Context window | Notes |
|---|---|---|
| OpenAI GPT-4.1 | 1M tokens | Flagship large-context model |
| OpenAI GPT-4o | 128K tokens | Multimodal |
| Anthropic Claude 4 Opus | 200K tokens | Strong long-context recall |
| Anthropic Claude Sonnet | 200K tokens | Cost-effective |
| Google Gemini 2.5 Pro | 1M+ tokens | Largest public window |
| Meta Llama 4 Maverick | 1M tokens | Open-weight MoE |
| Meta Llama 3.1 | 128K tokens | Open-weight dense |
| DeepSeek V3 | 128K tokens | Open-weight MoE |

## Decoding modes and speedups

- **Sampling controls** (temperature, top-p) trade determinism for diversity; reasoning models spend extra tokens thinking before answering, which changes both quality and cost.
- **Speculative decoding:** a small draft model proposes several tokens and the target model verifies them in parallel. The output distribution is unchanged, making it a pure latency lever (Leviathan et al. 2022).
- **Prompt caching:** stable prefixes (system prompt, tool schemas, examples) are processed once and reused across turns, cutting both latency and cost on repeated calls.

## Context-rot mitigations

Anthropic (2025) identifies several practical strategies for managing context quality as windows grow:

- **Altitude management:** Put the most important instructions and information near the top and bottom of the context (the "primacy/recency" effect). Middle content gets less attention.
- **Minimal tool sets:** Only expose tools relevant to the current task. Unused tool schemas consume context budget without contributing to performance.
- **Progressive summarization:** As conversations grow, replace earlier turns with compressed summaries rather than replaying full transcripts.
- **Context budgeting:** Reserve explicit token budgets for each section (system prompt, retrieved docs, conversation, tool results) and enforce them before sending to the model.
- **Structured state over transcripts:** Maintain a running state object (goal, decisions, evidence) rather than relying on the model to extract relevant facts from a growing conversation history.

## Sources and further reading

- Karpathy, *Intro to Large Language Models* — https://www.youtube.com/watch?v=zjkBMFhNj_g — tokenization and the training/inference distinction.
- Leviathan et al., "Fast Inference from Transformers via Speculative Decoding," 2022 — https://arxiv.org/abs/2211.17192
- Anthropic, "Effective context engineering for AI agents," Sep 2025 — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — context rot and attention-budget analysis.
- Huyen, *AI Engineering* (O'Reilly, 2025) — https://www.oreilly.com/library/view/ai-engineering/9781098166298/

All links verified 2026-08-27.

## Related

[[03 Context Knowledge Memory/Context Engineering]] · [[03 Context Knowledge Memory/Memory and Skills]] · [[07 Operations and Economics/Local Subscription API]]

---

---

> **← [[01 Foundations/What Is an LLM|What Is an LLM]]** · **[[AI_Home|Home]]** · **[[01 Foundations/Structured Outputs and Tool Calling|Structured Outputs and Tool Calling]] →**
