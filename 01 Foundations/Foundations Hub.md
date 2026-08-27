---
type: hub
layer: foundations
status: evergreen
maturity: established
aliases: [Foundation Models Hub]
tags: [ai-engineering, foundations, map-of-content]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "00 Start Here/Conversation Coverage.md"
next: "01 Foundations/What Is an LLM.md"
summary: "The foundation layer explains the model boundary. A model predicts or generates from context; it does not inherently have current knowledge, durable memory, permission..."
---


# Foundations

## Plain-English introduction

Think of a language model as a very powerful prediction engine. You give it some text, and it generates what comes next based on patterns it learned from reading billions of documents. But a model by itself has no live internet access, no memory of past conversations, no ability to open files or take actions. Everything else in AI engineering is about adding those missing pieces around the model. This section lays the groundwork: what a model is, how it thinks, what hardware it needs, and where the boundaries are.

The foundation layer explains the model boundary. A model predicts or generates from context; it does not inherently have current knowledge, durable memory, permissions, a filesystem, or the ability to act.

- [[01 Foundations/What Is an LLM]] — tokens, prediction, model families, multimodality, and limits.
- [[01 Foundations/Context Windows and Inference]] — context as temporary working memory, sampling, reasoning, and cost.
- [[01 Foundations/Local AI Hardware and Inference]] — model fit, VRAM, unified memory, bandwidth, quantization, and platform choice.
- [[01 Foundations/Structured Outputs and Tool Calling]] — turning model decisions into software actions.
- [[01 Foundations/Fine-Tuning Decision Framework]] — when retrieval, tools, prompting, or adapters solve the actual gap.
- [[03 Context Knowledge Memory/Context Engineering]] — the next layer after prompting.

```mermaid
flowchart LR
  Input[Context] --> Model[Foundation model]
  Model --> Text[Text or multimodal output]
  Model --> Schema[Structured decision]
  Schema --> Tool[Tool call]
  Tool --> Environment[Environment result]
  Environment --> Input
```

In plain terms: you send context to the model, it produces either freeform text or a structured decision, that decision can invoke a real tool, the tool's result feeds back into the next round of context, and the loop continues. Everything below this hub is about making that loop faster, safer, and more capable.

## Recommended reading order

Start with the conceptual foundations, then move to the practical hardware and adaptation decisions:

- [[01 Foundations/What Is an LLM]] — what a model is and is not
- [[01 Foundations/Context Windows and Inference]] — how context and sampling work
- [[01 Foundations/Structured Outputs and Tool Calling]] — turning model output into software actions
- [[01 Foundations/Local AI Hardware and Inference]] — where and on what hardware to run models
- [[01 Foundations/Fine-Tuning Decision Framework]] — when training is the right lever vs retrieval or prompting

## Where next

Once the foundations are solid, move to the layer that composes models into systems: [[02 Agents and Harnesses/Agents and Harnesses Hub]].

---

---

> **← [[00 Start Here/Conversation Coverage|Conversation Coverage]]** · **[[AI_Home|Home]]** · **[[01 Foundations/What Is an LLM|What Is an LLM]] →**
