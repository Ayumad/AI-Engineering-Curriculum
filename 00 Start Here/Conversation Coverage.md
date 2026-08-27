---
type: index
layer: start-here
status: evergreen
maturity: established
aliases: [Conversation Coverage Checklist]
tags: [ai-engineering, coverage, provenance]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "00 Start Here/How to Use This Vault.md"
next: "01 Foundations/Foundations Hub.md"
summary: "This checklist maps the supplied conversation to vault notes. Check each item when its note has been reviewed and refreshed."
---

# Conversation coverage

## Original scope

- [x] Explain AI engineering from first principles.
- [x] Cover agents, harnesses, orchestration, agent democracies, MCP, RAG, memory, reliability, evaluation, security, deployment, and AgentOps.
- [x] Explain Herdr, Hermes, DeepSeek Harness, Codex, Cursor, and adjacent tools.
- [x] Explain prompt structure, context engineering, context selection, Plan/Goal modes, and single versus multiple agents.
- [x] Compare local AI, subscriptions, APIs, and hybrid routing.
- [x] Explain jailbreak history, mitigations, future risks, and safe defensive testing.

## Coverage map

| Conversation theme | Primary notes |
|---|---|
| Models and tokens | [[01 Foundations/What Is an LLM]] · [[01 Foundations/Context Windows and Inference]] |
| Local AI hardware and inference | [[01 Foundations/Local AI Hardware and Inference]] |
| Agent definition and loop | [[02 Agents and Harnesses/What Is an Agent]] |
| Harnesses and sandboxes | [[02 Agents and Harnesses/Agent Harness]] · [[02 Agents and Harnesses/Sandboxes and Execution Planes]] |
| Prompting and context | [[03 Context Knowledge Memory/Prompting for Agents]] · [[03 Context Knowledge Memory/Context Engineering]] |
| RAG and embeddings | [[03 Context Knowledge Memory/RAG]] · [[03 Context Knowledge Memory/Vector Search and Embeddings]] |
| Memory and skills | [[03 Context Knowledge Memory/Memory and Skills]] |
| Skills, tools, and capability lifecycle | [[03 Context Knowledge Memory/Skills, Tools, and Capability Management]] |
| Workflows | [[04 Workflows and Orchestration/Workflow Patterns]] |
| Orchestration | [[04 Workflows and Orchestration/Orchestration Hub]] |
| Multi-agent systems | [[04 Workflows and Orchestration/Multi-Agent Systems]] |
| Agent democracies | [[04 Workflows and Orchestration/Agent Democracies]] |
| MCP and protocols | [[05 Protocols and Tools/MCP]] · [[05 Protocols and Tools/Agent Protocols]] |
| Reliability and evals | [[06 Reliability and Security/Reliability Evals and Observability]] |
| Jailbreaking and security | [[06 Reliability and Security/Security and Jailbreaking]] · [[06 Reliability and Security/Defensive Red-Team Labs]] |
| Local/subscription/API economics | [[07 Operations and Economics/Local Subscription API]] |
| Deployment and AgentOps | [[07 Operations and Economics/Deployment and AgentOps]] |
| Latency and cost engineering | [[07 Operations and Economics/Latency and Cost Engineering]] |
| Evaluation engineering and observability | [[06 Reliability and Security/Evaluation Engineering]] · [[06 Reliability and Security/Observability]] |
| Voice, vision, and computer use | [[02 Agents and Harnesses/Voice and Audio Agents]] · [[02 Agents and Harnesses/Vision and Multimodal Input Engineering]] · [[02 Agents and Harnesses/Computer-Use and Browser Agents]] |
| Sandboxing infrastructure | [[02 Agents and Harnesses/Sandboxing Infrastructure]] |
| Human oversight and trust | [[06 Reliability and Security/Human Oversight and Trust Engineering]] |
| Fine-tuning decision framework | [[01 Foundations/Fine-Tuning Decision Framework]] |
| Agent pattern catalog | [[11 Glossary and Sources/Pattern Catalog]] |
| Tool landscape | [[08 Tool Landscape/Coding Agent Profiles]] · [[08 Tool Landscape/Agent Runtimes and Frameworks]] · [[08 Tool Landscape/Infrastructure and Observability Tools]] |
| Prompt and architecture playbooks | [[09 Playbooks/Playbooks Hub]] |

## Provenance

The source conversation is titled “Explain AI Engineering” and has conversation ID `6a902da4-02a0-83e8-9dd2-aa825032fe3c`. This vault is a structured, source-checked synthesis rather than a verbatim transcript.

## Source and depth pass — 2026-08-27

- [x] Add verified expert sources and "Sources and further reading" sections across Foundations, Agents, Context, Workflows, Reliability, Operations, Glossary, and Pattern Catalog (15 papers + Anthropic/OpenAI engineering guides + OWASP frameworks; all links live-checked 2026-08-27).
- [x] Expand [[11 Glossary and Sources/Sources]] with foundational papers, practitioner writing (Karpathy, Huyen, Weng, Willison), and security frameworks (OWASP LLM Top 10 2025, Agentic Top 10).
- [x] Document the source convention in [[00 Start Here/How to Use This Vault]] and add the copyable source-block template to [[12 Templates/Template Library]].
- [x] Flesh out thin notes: model-building/scaling, tokenization

## Full audit and expansion pass — 2026-08-27

- [x] Full vault audit identifying ~35 gap clusters across glossary, patterns, tools, sources, templates, and start-here notes.
- [x] 8-writer research wave with live-verified sources (research-A through research-D) covering guardrails, eval frameworks, serving stacks, gateways, drift, compliance, and incident response.
- [x] 8-writer expansion adding new terms, tools, patterns, templates, and source registrations across all 10 glossary/registry/template/start-here notes.
- [x] Redundancy cleanup: cross-reference notes added, canonical-source links established, no content duplication between catalog and workflow notes.

 + KV cache + context rot, bandwidth math + quantization evidence, constrained decoding, agent lineage (ReAct/Weng/BEA/OpenAI), harness-as-product, context-engineering field guidance, RAG evidence base, workflow provenance, multi-agent evidence, debate evidence, LLM-judge biases, OTel GenAI observability, security history + standards, latency mechanisms, glossary terms, pattern provenance.

## Vector search and embeddings note — 2026-08-27

- [x] New concept note [[03 Context Knowledge Memory/Vector Search and Embeddings]]: embedding training (contrastive objectives, SimCSE recipe + numbers, nomic task-prefix requirement), ANN index internals (HNSW/IVF/PQ mechanics + trade-offs), similarity ≠ relevance, local Ollama + ChromaDB field notes (model pinning, chunking > index tuning).
- [x] Sources live-verified 2026-08-27: HNSW (arXiv 1603.09320), SimCSE (arXiv 2104.08821), ANN-Benchmarks, nomic HF model card, Chroma docs, Ollama embed API. Registered in [[11 Glossary and Sources/Sources]].
- [x] Wired into the nav chain (RAG → Vector Search and Embeddings → Large Project Navigation), cross-linked from [[03 Context Knowledge Memory/RAG]], added to the coverage map.

## Plain-English introductions pass — 2026-08-27

- [x] All 64 tour notes gained a `## Plain-English introduction` as the first body section (right after the H1): analogy-first, ~8th-grade reading level, no jargon/URLs/wikilinks; 40–80 words on hubs/maps/glossaries/indexes, 60–180 on concepts/patterns/guides.
- [x] Convention baked into the `ai-engineering-vault-update` skill (v1.3.0) and documented in [[00 Start Here/How to Use This Vault]].
- [x] Verified: 64/64 intros, exactly one per note, placed after H1, nav bars intact; validator at the pre-existing baseline (476 notes / 4 invalid / 3 unresolved). AI_Home "Guided tour" count corrected 63 → 64.
- [x] **Welcome.md removed by Ayush (2026-08-27)** — deliberate; byte-identical policy retired (skill v1.4.0). Validator baseline now 475 / 3 / 2 / 0.

## The gist pass — 2026-08-27

- [x] The `## Plain-English introduction` H2 is abolished. Content notes (concepts, patterns, guides, protocols, security, worksheets, checklists, playbooks — 44 notes) now open with a **`> [!summary] The gist` callout** right under the title, rewritten in Ayush's voice (STE100 × voice spec: short sentences, plain words, dry understatement, one concrete analogy max, banned AI-vocab list), followed by a `---` divider.
- [x] Index/hub/reference notes (section hubs, AI_Home, How to Use This Vault, ToC, Coverage, Atlas, Glossary, Sources, Pattern Catalog, Acronyms, Tool Comparison Index, Prompt Template, Template Library — 20 notes) carry **no intro at all** per Ayush ("reserve the gist to content notes") — H1 goes straight to content.
- [x] Convention encoded in `ai-engineering-vault-update` skill v1.5.1 (verification: grep == 44 callouts, zero legacy headers, no-gist files have none); How to Use This Vault bullet updated.
- [x] 9-writer wave: 39/44 converted by subagents; the 09 Playbooks group stalled at the header stub, so Context Checklist, Evaluation and Security Review, Learning Projects, Mode and Topology Selector, and RAG Design Worksheet were written by hand. Two gists micro-fixed for banned patterns (`not just`, `highest-leverage`); `harness` hits in Agent Harness / Coding Agent Profiles are the topic noun, kept.

## The Gist of It note — 2026-08-27

- [x] New note `00 Start Here/The Gist of It.md`: every content note's gist (44/44), one paragraph each, in table-of-contents order — Units 1–10 headings mirrored verbatim from Full Table of Contents, links deduped across units, plus a "The rest of the tour" appendix for the 5 notes the ToC doesn't link (AI Engineering Curriculum, Vector Search and Embeddings, Coding Agent Profiles, Agent Runtimes and Frameworks, Infrastructure and Observability Tools).
- [x] Reference note — no gist on it, per the content-only rule. Chain 64 → 65: `add_nav.py` CHAIN updated (inserted after Full ToC), bars regenerated for all 65 notes, prev/next verified. AI_Home guided-tour count 64 → 65 + short-version pointer; How to Use This Vault nav bullet 64 → 65; ToC fast-path link. Doubled pre-nav dividers (from repeated `add_nav.py` runs) collapsed across all notes — 0 remaining. Validator: 476 / 3 / 2 / 0 — zero new issues.

## Plain Reading companion — 2026-08-27

- [x] Generated `00 Start Here/The Gist of It — Plain Reading.md` from the collection note: 44 link-free entries, normal paragraphs, ToC order, no nav bar, deliberately outside the 65-note tour. Regenerated after catching and fixing a one-character extraction bug; verified first paragraph begins `An LLM` and all 44 entries are present.

---

> **← [[00 Start Here/How to Use This Vault|How to Use This Vault]]** · **[[AI_Home|Home]]** · **[[01 Foundations/Foundations Hub|Foundations Hub]] →**
