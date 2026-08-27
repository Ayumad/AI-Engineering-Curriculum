---
type: concept
layer: context
status: evergreen
maturity: established
aliases: [Context Management]
tags: [ai-engineering, context-engineering, prompting]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "02 Agents and Harnesses/Computer-Use and Browser Agents.md"
next: "03 Context Knowledge Memory/Prompting for Agents.md"
summary: "Prompt engineering asks “what should I say?” Context engineering asks “what should the model know at this exact moment?” It includes retrieval, memory, file selection,..."
---


# Context engineering

Prompt engineering asks “what should I say?” Context engineering asks “what should the model know at this exact moment?” It includes retrieval, memory, file selection, tool descriptions, summaries, plans, runtime evidence, and removal of stale material.

## Context hierarchy

```text
GLOBAL       personal preferences and universal safety rules
PROJECT      architecture, commands, conventions, stable constraints
TASK         objective, acceptance criteria, current decisions
RETRIEVED    relevant files, documents, records, evidence
RUNTIME      tool results, test failures, approvals, subagent reports
```

The ideal context is the smallest set needed for the next good decision. Keep stable information in project instructions; keep yesterday’s incident in task context. Load skills and large references on demand.

## Practical controls

- Rank sources by authority and freshness.
- Label untrusted content as data, not instructions.
- Preserve provenance for retrieved evidence.
- Summarize completed work while retaining decisions and unresolved risks.
- Reserve tokens for tool output and recovery.
- Test retrieval and compression as part of the agent, not as a preprocessing detail.

For large projects, use progressive disclosure: begin with a project map and task brief, retrieve only the relevant sections and tests, work in a bounded slice, then checkpoint the verified result. See [[03 Context Knowledge Memory/Large Project Navigation and Context Scaling]].

## Project context files

Repository-level files such as `AGENTS.md`, `CLAUDE.md`, and `GEMINI.md` are useful for stable architecture, commands, conventions, and safety rules. Keep incident-specific details, temporary experiments, and current bugs in task context instead of turning them into permanent instructions.

See [[03 Context Knowledge Memory/Prompting for Agents]], [[03 Context Knowledge Memory/Large Project Navigation and Context Scaling]], [[03 Context Knowledge Memory/RAG]], and [[03 Context Knowledge Memory/Memory and Skills]].

## Apply it deliberately

Select context by relevance, authority, freshness, and sensitivity. Keep a compact state of goal, decisions, evidence, and open questions; retrieve detail only for the next decision. Log selection so a failure can be reproduced, and treat retrieved text as evidence—not instructions that outrank policy.

## What the field says

- **Context rot is real:** recall precision drops as token count rises across models. Attention is O(n²) over n tokens, models train mostly on shorter sequences, and every token depletes a finite "attention budget"—so context is a resource with diminishing returns, like working memory (Anthropic 2025).
- **Target the smallest set of high-signal tokens** that maximizes the chance of the desired outcome. Minimal does not mean short: give enough stable instruction up front, then test and add only on observed failure modes (Anthropic 2025).
- **System-prompt altitude:** specific enough to steer behavior, flexible enough not to hardcode brittle logic. Organize prompts into sections (`<background_information>`, `<instructions>`, tool guidance, output description) with XML tags or Markdown headers (Anthropic 2025).
- **Curate the tool set:** minimal viable tools, token-efficient returns, unambiguous parameters. Few-shot examples should be *diverse and canonical*—not exhaustive edge-case laundry lists (Anthropic 2025).
- **Sub-agents condense context:** a lead agent keeps a clean window while subagents explore with large budgets and return distilled summaries (often ~1–2k tokens). This is the architecture behind Anthropic's research system (Anthropic 2025).

## Context-window budgeting

Context is a finite resource with a power-law cost curve: every token depletes attention budget, and the O(n²) pairwise-relationship cost in the transformer means the last tokens added cost more than the first. Budget context in three buckets:

| Bucket | Allocate | Notes |
|---|---|---|
| Stable instructions | 10–20% of window | System prompt, project rules, tool definitions |
| Working evidence | 50–65% of window | Retrieved chunks, task brief, code under edit |
| Recovery reserve | 15–30% of window | Tool outputs, error traces, final answer space |

Average prompt tokens grew ~4× from early 2024 (1,500) to late 2025 (6,000) per Anthropic's data. Prompt caching (OpenAI automatic longest-prefix cache; Anthropic explicit breakpoints at 1.25× write cost) can save 40–60% on repeated-context workloads, making larger stable prefixes affordable. Dynamic tool injection (5–10 relevant tools per request vs all 64) saves ~735 tokens per omitted tool definition.

## Compression techniques

When context exceeds the budget, three strategies apply—each with different quality trade-offs:

| Strategy | When to use | Trade-off |
|---|---|---|
| **Summarization** | Long completed work, conversation history, retrieved documents | Preserves gist; loses nuance, exact values, edge-case details. Quality depends on the summarizer model. |
| **Extraction** | Need specific facts, code fragments, or structured data from source material | Keeps exact fragments without paraphrasing; loses surrounding context that gives fragments meaning. |
| **Eviction** | Old tool outputs, superseded plans, stale error messages, low-signal tokens | Lowest cost; permanently removes information. Use only when the fact is durable elsewhere or no longer relevant. |

Teams report 50–70% token reduction with minimal quality loss when compressed context retains key information. Anthropic's compaction mechanism (in the Claude Agent SDK) enables long-running agents to avoid exhausting context but is not sufficient alone—agents still try to one-shot tasks or declare premature completion, requiring additional harness-level guardrails.

**Best practice:** compress bottom-up—evict stale runtime output first, extract key facts from completed work second, and summarize only what cannot be reduced further. Always preserve decisions, open risks, and the current goal verbatim.

## Contamination and poisoning defenses

Retrieved content, tool outputs, and user-provided material can carry adversarial instructions (see [[06 Reliability and Security/Security and Jailbreaking]]). Defense-in-depth:

- **Label untrusted content as data, not instructions.** Wrap retrieved text in explicit markers (e.g., `<external_content>…</external_content>`) and instruct the model to treat it as evidence, never as directives.
- **Taint-aware handling.** Track the trust level of every context element. Untrusted-sourced text must not override safety policy or authorization rules regardless of how confidently it is phrased.
- **Provenance on every fact.** Record source, timestamp, confidence, and access policy with each retrieved chunk. When a decision depends on external evidence, surface the provenance to the model and the human reviewer.
- **Separate authority layers.** Security policy, user instructions, and retrieved data occupy different slots in the context hierarchy. A style or task request must never silently weaken a safety rule.
- **Red-team retrieval.** Test whether adversarial documents in the corpus can cause the agent to follow injected instructions. OWASP Top 10 for LLM Applications (2025) lists indirect prompt injection as a top risk.

## Sources and further reading

- Anthropic, "Effective context engineering for AI agents," Sep 29, 2025 — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — the primary source for the fields above.
- Anthropic, "Building effective agents," Dec 19, 2024 — https://www.anthropic.com/engineering/building-effective-agents
- Anthropic, "Introducing Contextual Retrieval," Sep 2024 — https://www.anthropic.com/engineering/contextual-retrieval — retrieval-side context curation.
- Huyen, *AI Engineering* (O'Reilly, 2025) — https://www.oreilly.com/library/view/ai-engineering/9781098166298/
- Anthropic, "Effective harnesses for long-running agents," Nov 2025 — https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents — compaction limitations, initializer+coding-agent pattern.
- OWASP, "Top 10 for LLM Applications," 2025 — https://genai.owasp.org/resource/owasp-top-10-for-llm-applications-2025/ — indirect prompt injection as a top risk.

All links verified 2026-08-27.

---

---

> **← [[02 Agents and Harnesses/Computer-Use and Browser Agents|Computer-Use and Browser Agents]]** · **[[AI_Home|Home]]** · **[[03 Context Knowledge Memory/Prompting for Agents|Prompting for Agents]] →**
