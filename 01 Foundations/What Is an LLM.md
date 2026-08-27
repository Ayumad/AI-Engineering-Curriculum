---
type: concept
layer: foundations
status: evergreen
maturity: established
aliases: [Large Language Model]
tags: [ai-engineering, models, llm]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "01 Foundations/Foundations Hub.md"
next: "01 Foundations/Context Windows and Inference.md"
summary: "An LLM is a learned function that maps a sequence of tokens and other inputs to a probability distribution over possible next tokens or structured outputs. In practice..."
---


# What is an LLM?

An LLM is a learned function that maps a sequence of tokens and other inputs to a probability distribution over possible next tokens or structured outputs. In practice, foundation models may also process images, audio, video, or tool results.

## Mental model

`context → model inference → candidate continuation → decoding`

Tokens are not identical to words. Tokenization affects context length, cost, and how identifiers or code are represented. Decoding chooses an output from probabilities; temperature, top-p, seed behavior, and model-specific controls affect variability.

## Tokenization

Tokenization converts raw text into the integer sequences models actually process. The choice of tokenizer determines context length, cost, and how well code, rare words, and multilingual text are handled.

**BPE (Byte Pair Encoding):** Merges the most frequent byte pairs iteratively (Sennrich 2016). Handles rare and unknown words via subword decomposition. +1.1–1.3 BLEU over dictionary baselines on WMT 15.

**WordPiece:** Used in BERT (Google). Longest-match-first strategy with a trie. Fast WordPiece achieves O(n) tokenization via Aho-Corasick—8.2× faster than HuggingFace Tokenizers (Wu et al. 2021).

**SentencePiece:** Language-independent tokenizer (Kudo & Richardson 2018). Trains directly from raw sentences without pre-tokenization. Implements BPE + Unigram LM. Used by T5, Llama, and Gemma.

**Why tokenization matters for cost and context:** APIs charge per token. English averages ~0.75 words per token (~4 characters). CJK languages average ~1 token per character (2–3× more expensive for the same content). Code, agglutinative languages, and URLs also tokenize poorly. A 128K-token context window ≈ 96K English words but ≈ 40K CJK characters. Petrov et al. (NeurIPS 2023) showed tokenized lengths differ by >10× between best- and worst-served languages for the same content.

## Embeddings

After tokenization, each token (or sequence) is mapped to a dense vector—an embedding—that captures semantic and syntactic relationships.

**Static embeddings** (Word2Vec, GloVe): One fixed vector per word regardless of context. Word2Vec (Mikolov 2013) learns from local co-occurrence via skip-gram or CBOW. GloVe (Pennington 2014) factorizes log co-occurrence matrices. Dimensions: 100–300. Fast and interpretable, but cannot distinguish polysemous uses (e.g., "bank" as riverbank vs. financial institution).

**Contextual embeddings** (ELMo, BERT, GPT): Representation varies by sentence. ELMo (Peters 2018) extracts deep bidirectional LM internals; upper layers become more context-specific (Ethayarajh 2019). Transformer hidden dimensions range from 768 (BERT-base) to 12,288 (GPT-4 class). Retrieval-specific embeddings: OpenAI ada-002 uses 3,072 dimensions; BGE uses 1,024.

**Cosine similarity** measures the angle between two vectors: 1 = identical direction, 0 = orthogonal, −1 = opposite. It is the primary metric for retrieval and nearest-neighbor search. Inner product and L2 distance are alternatives with different geometric interpretations.

**Trade-off:** Static embeddings are fast and cheap but ignore context. Contextual embeddings capture polysemy and nuance but require the full model forward pass.

## Model families snapshot

The landscape as of 2026-08-27:

| Family | Provider | Key models | Approx. params | Strengths | Access |
|---|---|---|---|---|---|
| GPT | OpenAI | GPT-4o, GPT-4.1 | ~1.8T MoE (rumored) | Broad reasoning, multimodal | Closed API |
| Claude | Anthropic | Claude 4 Opus, Sonnet | Undisclosed | Constitutional AI alignment, 100K+ context | Closed API |
| Llama | Meta | Llama 4 Scout/Maverick | 8B–405B | Open weights, commercial license, 128K context | Open |
| Gemini | Google | Gemini 2.5 Pro | Undisclosed | Multimodal native, 1M+ context | API |
| DeepSeek | DeepSeek | V3 (671B/37B active), R1 | 671B MoE | Extremely cost-efficient training, open weights | Open |
| Mistral | Mistral AI | Mixtral 8x7B | 47B/13B active | Efficiency-focused, strong small models, Apache 2.0 | Open |

Parameter counts are approximate; MoE models store more parameters than they activate per token (see [[01 Foundations/Local AI Hardware and Inference]]).

## Evaluation benchmarks

Common benchmarks and why single numbers mislead:

| Benchmark | What it tests | Status (2026) |
|---|---|---|
| MMLU (57 subjects, multiple-choice) | Broad knowledge | Saturated (>90% by frontier) |
| MMLU-Pro (10 choices, reasoning-focused) | Harder reasoning, less trivia | Better discriminator; drops accuracy 16–33% vs MMLU |
| GSM8K (grade school math) | Multi-step reasoning | Largely saturated (>95%) |
| HumanEval (164 Python problems) | Code generation with unit tests | Saturated (>95%); spawned SWE-bench |
| Chatbot Arena (LMSYS) | Crowdsourced pairwise preference, Elo rating | Most referenced live leaderboard; rankings shift by task type |

**Why single numbers mislead:** (a) Saturation—top models cluster near ceiling. (b) Prompt sensitivity—same model scores vary 4–5% across formats. (c) No single benchmark covers all capabilities. (d) Contamination—training data may contain benchmark questions. (e) Arena shows rankings shift by task type.

## What is not built in

An LLM does not automatically possess current web access, private files, durable memory, a todo list, authentication, safe execution, or a goal that persists after the request. Engineers add these around the model through a [[02 Agents and Harnesses/Agent Harness]].

## Useful distinctions

| Thing | Primary responsibility |
|---|---|
| Model | Pattern completion and reasoning from supplied context |
| Application | User experience and domain logic |
| Agent | Model-directed loop that can choose actions |
| Workflow | Explicit sequence and control flow |
| Harness | Runtime that gives an agent context, tools, state, and guardrails |

## How to use this mental model

When a system fails, locate the missing layer before changing the model. A wrong current fact suggests retrieval; an invalid JSON field suggests a schema/validator; an action the model cannot take suggests a tool; repeated unsafe behavior suggests a harness policy. This prevents the common mistake of trying to solve every problem with a longer prompt or a larger model.

Build a small exercise around one request: record the exact input context, model output distribution/decoding settings where available, validator result, and final user-facing answer. Change one variable at a time. You will quickly see that the system surrounding inference controls much of real-world reliability.

## Common misconceptions

- Fluent output is not evidence of current knowledge, authorization, or tool completion.
- A context window is not durable memory; it is a limited working set that must be selected and managed.
- Reasoning capability does not confer safe execution. Authority comes from the application and its policies.

## How a model is built and why that matters

Karpathy's "two-file" mental model: an LLM is **parameters** (the learned weights) plus **the code** that runs them. Everything a model "knows" was selected during training—so a capability gap is usually a training fact, not a prompt bug.

- **Pretraining:** predict the next token over web-scale text. This yields broad language and world knowledge but no instruction-following. Loss falls as a power law with model size, data, and compute (Kaplan et al. 2020), and compute-optimal training shifts the balance toward more data per parameter (Hoffmann et al. 2022, "Chinchilla").
- **Post-training:** supervised fine-tuning shapes output format, and preference/RL alignment tunes behavior against human judgments. Alignment changes behavior; it is **not** a security boundary (see [[06 Reliability and Security/Security and Jailbreaking]]).
- **Architecture:** the transformer's attention mechanism (Vaswani et al. 2017) lets every token attend to every other token; most chat models are autoregressive decoder-only transformers that emit one token at a time. Mixture-of-experts models store more parameters than they activate per token (see [[01 Foundations/Local AI Hardware and Inference]]). Chain-of-thought-style reasoning emerges at sufficient scale (Wei et al. 2022; see [[03 Context Knowledge Memory/Prompting for Agents]]).

## Scaling laws: practical implications

Kaplan et al. (2020) showed loss falls as a power law with model size, dataset size, and compute. The key practical implication: doubling parameters without proportionally more data yields diminishing returns. Chinchilla (Hoffmann et al. 2022) showed compute-optimal training shifts the balance toward more tokens per parameter—many "large" models were actually undertrained. This means a well-trained 70B model can outperform a poorly-trained 200B model. For practitioners, scaling laws predict the minimum data and compute needed for a target performance level, and they explain why simply making a model bigger without more data rarely helps.

## Sources and further reading

- Sennrich, Haddow & Birch, "Neural Machine Translation of Rare Words with Subword Units," 2016 — https://arxiv.org/abs/1508.07909 — BPE algorithm and NMT results.
- Kudo & Richardson, "SentencePiece," 2018 — https://arxiv.org/abs/1808.06226 — language-independent tokenization.
- Wu et al., "Fast WordPiece Tokenization," 2021 — https://arxiv.org/abs/2012.15524 — O(n) tokenization via Aho-Corasick.
- Mikolov et al., "Efficient Estimation of Word Representations in Vector Space," 2013 — https://arxiv.org/abs/1301.3781 — Word2Vec skip-gram/CBOW.
- Pennington, Socher & Manning, "GloVe: Global Vectors for Word Representation," 2014 — https://aclanthology.org/D14-1162/ — log co-occurrence factorization.
- Peters et al., "Deep contextualized word representations," 2018 — https://arxiv.org/abs/1802.05365 — ELMo contextual embeddings.
- Ethayarajh, "How Contextual are Contextualized Word Representations?," 2019 — https://arxiv.org/abs/1909.00512 — anisotropy in contextual embeddings.
- Multigrid, "How Many Tokens Is a Word?," 2026 — https://multigrid.ai/learn/tokens-per-word — token-per-word ratios across languages.
- Karpathy, *Intro to Large Language Models* — https://www.youtube.com/watch?v=zjkBMFhNj_g — the two-file view, training, tokenization, and scale intuition.
- Vaswani et al., "Attention Is All You Need," 2017 — https://arxiv.org/abs/1706.03762
- Kaplan et al., "Scaling Laws for Neural Language Models," 2020 — https://arxiv.org/abs/2001.08361
- Hoffmann et al., "Training Compute-Optimal Large Language Models" (Chinchilla), 2022 — https://arxiv.org/abs/2203.15556
- Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models," 2022 — https://arxiv.org/abs/2201.11903
- OpenAI, "GPT-4 Technical Report," 2023 — https://arxiv.org/abs/2303.08774
- Meta AI, "The Llama 3 Herd of Models," 2024 — https://arxiv.org/abs/2407.21783
- DeepSeek AI, "DeepSeek-V3 Technical Report," 2024 — https://arxiv.org/abs/2412.19437
- Jiang et al., "Mistral 7B," 2023 — https://arxiv.org/abs/2310.06825
- Jiang et al., "Mixtral of Experts," 2024 — https://arxiv.org/abs/2401.04088
- Hendrycks et al., "Measuring Massive Multitask Language Understanding," 2021 — https://arxiv.org/abs/2009.03300
- Wang et al., "MMLU-Pro," 2024 — https://arxiv.org/abs/2406.01574
- Cobbe et al., "Training Verifiers to Solve Math Word Problems," 2021 — https://arxiv.org/abs/2110.14168
- Chen et al., "Evaluating Large Language Models Trained on Code," 2021 — https://arxiv.org/abs/2107.03374
- Zheng et al., "Chatbot Arena," 2024 — https://arxiv.org/abs/2403.04132
- Huyen, *AI Engineering* (O'Reilly, 2025) — https://www.oreilly.com/library/view/ai-engineering/9781098166298/ — practitioner reference for the whole stack.

All links verified 2026-08-27.

## Related

[[01 Foundations/Context Windows and Inference]] · [[01 Foundations/Structured Outputs and Tool Calling]] · [[02 Agents and Harnesses/What Is an Agent]]

---

---

> **← [[01 Foundations/Foundations Hub|Foundations Hub]]** · **[[AI_Home|Home]]** · **[[01 Foundations/Context Windows and Inference|Context Windows and Inference]] →**
