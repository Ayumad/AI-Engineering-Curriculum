---
type: decision-framework
layer: foundations
status: evergreen
maturity: established
aliases: [When to Fine-Tune]
tags: [ai-engineering, fine-tuning, lora, rag, prompting]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "01 Foundations/Local AI Hardware and Inference.md"
next: "02 Agents and Harnesses/Agents and Harnesses Hub.md"
summary: "Fine-tuning is for persistent, repeatable behavioral gaps—not current knowledge, missing capabilities, or instructions that can be supplied at runtime."
---


# Fine-tuning decision framework

## Plain-English introduction

When a language model gives weak answers, the instinct is to retrain it—but training is expensive, slow, and not always the right fix. Often the model already knows enough; it just needs better instructions, a reference document, or a tool to look things up. This note is a decision guide that helps you figure out which lever to pull. It walks through lighter options first—improved prompts, retrieval of relevant documents, connecting a tool—before recommending fine-tuning, which rewrites part of the model's internal wiring to permanently change how it behaves.

Do not treat every weak answer as a training problem. Diagnose the missing layer first:

```text
model fails task
  ├─ missing/changing information → retrieval (RAG)
  ├─ unclear or temporary instruction → prompt/context design
  ├─ missing action → typed tool or skill
  └─ stable behavior is inconsistent across many examples → fine-tuning or adapter
```

## When adaptation helps

Fine-tuning is a good fit for stable classification, repeated structured transformations, domain conventions, predictable style, and specialized tool-selection behavior. It can also let a smaller model serve a narrow task at lower cost and latency.

It is a poor fit for daily-changing facts, private documents that must remain current, authorization logic, or an operation the model simply cannot perform. Those require retrieval, policy, or tools.

## Prefer the lightest adaptation

Start with a strong prompt, examples, schemas, and an evaluation set. If a stable gap remains, consider parameter-efficient fine-tuning such as LoRA/PEFT: freeze the base model and train a small adapter. Keep separate adapters for separate domains rather than baking every behavior into one opaque model.

Version the base model, dataset, data filters, training configuration, adapter, and evaluation results. Test regressions against the baseline before deployment; a locally improved behavior can degrade safety, formatting, or unrelated tasks.

## Practical decision checklist

- Can the missing information be retrieved at request time?
- Can a prompt, example, or validated tool call solve it at lower risk?
- Is the target behavior stable enough that the training data will remain valid?
- Do you have representative positives, negatives, edge cases, and holdout evaluations?
- Will the cost of data curation, training, serving, and rollback beat a prompt/RAG/tool solution?

## Methods in practice

**Parameter-efficient fine-tuning (PEFT):** LoRA injects trainable low-rank adapter matrices while freezing all base weights—typically tuning only ~0.1–1% of parameters. This reduces VRAM by roughly 3–4× compared to full fine-tuning and delivers 90–95% of full-fine-tuning quality on most tasks (Dettmers et al. 2023). QLoRA pushes this further: a 4-bit quantized base (NormalFloat NF4 + double quantization) plus LoRA adapters lets you fine-tune a 65B model on a single 48 GB GPU with no measurable quality loss on standard benchmarks. Full fine-tuning updates every parameter and reaches the highest possible accuracy, but requires multi-GPU clusters (7B ≈ 60–80 GB VRAM; 70B ≈ 500+ GB VRAM).

**RLHF vs DPO:** Reinforcement Learning from Human Feedback (RLHF) trains a reward model on human preference rankings, then optimizes the policy via PPO—it is the most expressive alignment method but complex, unstable, and costs 3–10× a supervised fine-tuning run (Ouyang et al. 2022). Direct Preference Optimization (DPO) bypasses the reward model entirely: a simple classification loss on preference pairs matches or exceeds PPO on summarization, dialogue, and sentiment tasks at ~1.5–2× SFT cost (Rafailov et al. 2023). Choose RLHF when you need maximum control and have the infrastructure; choose DPO when you have preference data and want simplicity.

**Cost/time estimate (2026-08-27 snapshot):**

| Scale | Full fine-tune | LoRA | QLoRA | RLHF (PPO) | DPO |
|---|---|---|---|---|---|
| 7B | $50–200 | $5–20 | $1–5 | $150–600 | $10–40 |
| 70B | $1,000–5,000 | $100–400 | $20–80 | $3,000–15,000 | $200–800 |

Ranges assume cloud GPU pricing as of mid-2026 and a moderate-size training dataset (10K–100K examples). Actual cost scales with data volume, sequence length, number of epochs, and checkpoint frequency.

Before any fine-tuning, build an evaluation suite and run it against the base model as a regression baseline. After training, re-run the same evals: a locally improved behavior can silently degrade safety, formatting, or unrelated tasks. [[06 Reliability and Security/Evaluation Engineering]] covers eval design in detail.

## Sources and further reading

- Dettmers et al., "QLoRA: Efficient Finetuning of Quantized LLMs," 2023 — https://arxiv.org/abs/2305.14314 — 4-bit fine-tuning, 65B on a single 48 GB GPU.
- Ouyang et al., "Training language models to follow instructions with human feedback" (OpenAI, 2022) — https://arxiv.org/abs/2203.02155 — RLHF pipeline (InstructGPT).
- Rafailov et al., "Direct Preference Optimization" (Stanford, 2023) — https://arxiv.org/abs/2305.18290 — closed-form DPO as RLHF alternative.
- Hoffmann et al., "Training Compute-Optimal Large Language Models" (Chinchilla, 2022) — https://arxiv.org/abs/2203.15556 — compute-optimal training scale.
- Huyen, *AI Engineering* (O'Reilly, 2025) — https://www.oreilly.com/library/view/ai-engineering/9781098166298/ — fine-tuning decision guidance.

All links verified 2026-08-27.

## Related

[[03 Context Knowledge Memory/RAG]] · [[03 Context Knowledge Memory/Skills, Tools, and Capability Management]] · [[06 Reliability and Security/Evaluation Engineering]]

---

---

> **← [[01 Foundations/Local AI Hardware and Inference|Local AI Hardware and Inference]]** · **[[AI_Home|Home]]** · **[[02 Agents and Harnesses/Agents and Harnesses Hub|Agents and Harnesses Hub]] →**
