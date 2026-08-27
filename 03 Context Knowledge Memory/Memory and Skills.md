---
type: concept
layer: context
status: evergreen
maturity: emerging
aliases: [Agent Memory, Procedural Memory, Skills]
tags: [ai-engineering, memory, skills, context-engineering]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "03 Context Knowledge Memory/Prompting for Agents.md"
next: "03 Context Knowledge Memory/Skills, Tools, and Capability Management.md"
summary: "RAG asks "what external information is relevant?" Memory asks "what should this agent retain from experience?""
---


# Memory and skills

> [!summary] The gist
> An agent needs different kinds of memory: working memory for the current task, durable facts across sessions, and reusable procedures. This note maps those kinds and compares two memory architectures — Mem0 (passive extraction) and Letta (agentic self-editing) — plus backend selection and consolidation strategies.

---

## Architecture landscape

Two dominant paradigms exist for agent memory:

### Passive extraction (Mem0)

Mem0 is a pluggable memory layer (not a full agent runtime). It provides an API where `add()` stores facts extracted from conversations and `search()` retrieves via semantic search. Framework-agnostic—works with LangChain, LlamaIndex, or raw API calls.

- **Knowledge graph** (Pro tier, $249/mo): enables multi-hop reasoning across related facts. Graph edges capture relationships that vector similarity alone misses.
- **Conflict resolution:** deduplicates at add time—when a new fact contradicts an existing one, the older entry is updated or removed automatically.
- **Privacy:** SOC 2 Type 1, HIPAA, GDPR compliant. BYOK (bring your own key) and zero-trust architecture. Self-hosted option available.
- **Scale:** ~48K GitHub stars, YC-backed ($24M Series A). Apache 2.0 licensed.

### Agentic self-editing (Letta / MemGPT)

Letta (formerly MemGPT) is a full agent runtime with three-tier memory, inspired by the MemGPT paper (UC Berkeley, 2023) which proposed an LLM-as-OS paradigm where the model manages its own context like an OS manages RAM.

| Tier | Storage | Access pattern |
|---|---|---|
| **Core** | In-context / RAM | Always present; agent reads and writes directly via tool calls |
| **Recall** | Searchable history / disk cache | On-demand retrieval of past conversations |
| **Archival** | Long-term / cold storage | Append-only; agent searches via embeddings |

The agent self-edits memory during reasoning: `core_memory_replace` overwrites Core blocks, Archival is append-only. This is adaptive—quality depends on model judgment—but every memory operation costs inference tokens.

- **Scale:** ~21K GitHub stars, Felis-backed ($10M seed). Apache 2.0 licensed.
- **Conflict resolution:** agent overwrites Core blocks directly; Archival is append-only with no built-in deduplication. Requires explicit agent logic to detect and resolve contradictions.
- **Privacy:** via self-hosting; no third-party compliance certifications.

### Trade-off summary

| Dimension | Mem0 (passive) | Letta (agentic) |
|---|---|---|
| Extraction quality | Predictable, token-efficient | Adaptive, but depends on model judgment |
| Token cost | Lower (extraction runs once) | Higher (every memory op is inference) |
| Multi-hop reasoning | Via knowledge graph (Pro tier) | Via agent's own reasoning over search results |
| Conflict handling | Automatic dedup at add time | Agent-driven; may miss contradictions |
| Longitudinal eval | LongMemEval: 49.0% | Hindsight: 94.6% (with 4 parallel retrieval strategies + reranking) |
| Deployment | Managed SaaS or self-hosted | Self-hosted only |

## Memory backend selection

| Backend | Strengths | Weaknesses | Best for |
|---|---|---|---|
| **SQLite** | Zero-config, embedded, ACID, fast for <100K records | No built-in vector search; single-writer | Prototyping, single-agent, local-first |
| **Redis** | Sub-millisecond latency, pub/sub, TTL support | Volatile (persistence optional), no native vector ops without modules | Real-time working memory, session caches |
| **Vector DB (Pinecone, Qdrant, Weaviate, Chroma)** | Native ANN search, metadata filtering, hybrid queries | Operational overhead, cost scales with index size | Semantic recall, archival, cross-session search |
| **PostgreSQL + pgvector** | ACID, relational joins + vector search, mature ecosystem | Vector performance trails dedicated DBs at scale | Teams already running Postgres; mixed relational+vector workloads |

## Consolidation strategies

Raw conversation history grows indefinitely. Consolidation converts volume into durable knowledge:

1. **Extract-and-store:** After each session, extract key facts, decisions, and user preferences. Store as structured memory entries with source, timestamp, confidence, and expiry. Mem0 does this automatically; Letta requires explicit agent logic.
2. **Summarize-and-replace:** Periodically summarize conversation threads into compact semantic memory. Replace the raw transcript with the summary for retrieval. Risk: nuance loss. Mitigation: keep original in Recall/Archival tier.
3. **Promote procedural knowledge:** Successful multi-step procedures (debugging patterns, deployment checklists, review heuristics) should be extracted into skills—loadable, versionable packages (see [[03 Context Knowledge Memory/Skills, Tools, and Capability Management]]).
4. **Expire and prune:** Set TTL on transient facts (bug status, current sprint goal). Use confidence decay: unverified facts lose confidence over time unless reinforced by new evidence.

## Memory safeguards

- Record source, confidence, owner, timestamp, and expiry where appropriate.
- Separate user preference from security policy.
- Require confirmation before persisting sensitive or surprising facts.
- Treat retrieved memory as data that may be stale or poisoned.
- Provide deletion, correction, and inspection paths.

## A skill is procedural memory

A **skill** is an on-demand package of instructions, examples, scripts, tools, and domain knowledge. Skills are procedural memory made loadable and versionable. Do not preload every skill; select the smallest relevant capability and audit its permissions. For the full lifecycle—definition, registry, versioning, permissions—see [[03 Context Knowledge Memory/Skills, Tools, and Capability Management]].

## Design memory as a governed datastore

Store owner, source, confidence, lifetime, visibility, and correction path with every durable item. Preferences, incident experience, and security policy differ in authority and should not merge into one opaque profile. Retrieve selectively and show provenance when memory changes an important decision.

## Sources and further reading

- Mem0 official (2026) — https://mem0.ai/ — pluggable memory layer, knowledge graph, SOC 2/HIPAA.
- "MemGPT — The Paper That Started Paged Agent Memory" (TokRepo, 2026) — https://tokrepo.com/en/ai-memory/memgpt — LLM-as-OS paradigm, three-tier architecture, paper summary.
- "Mem0 vs Letta (MemGPT)" (Vectorize.io, 2026) — https://vectorize.io/articles/mem0-vs-letta — side-by-side architecture and trade-off comparison.

All links verified 2026-08-27.

## Related

[[03 Context Knowledge Memory/Skills, Tools, and Capability Management]] · [[03 Context Knowledge Memory/Context Engineering]] · [[03 Context Knowledge Memory/RAG]] · [[03 Context Knowledge Memory/Large Project Navigation and Context Scaling]]

---

> **← [[03 Context Knowledge Memory/Prompting for Agents|Prompting for Agents]]** · **[[AI_Home|Home]]** · **[[03 Context Knowledge Memory/Skills, Tools, and Capability Management|Skills, Tools, and Capability Management]] →**
