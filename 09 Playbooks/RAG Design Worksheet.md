---
type: worksheet
layer: playbooks
status: evergreen
maturity: established
aliases: [RAG Worksheet]
tags: [ai-engineering, rag, retrieval, design]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "09 Playbooks/Context Checklist.md"
next: "09 Playbooks/Evaluation and Security Review.md"
summary: "Question and users:"
---


# RAG design worksheet

> [!summary] The gist
> RAG bolts real sources onto a model so answers come with evidence instead of guesses. This worksheet walks every design decision — ingestion, chunking, indexing, retrieval, evaluation — with blanks to fill in and a worked example to check against. Catch the gaps before they become bugs.

---

**Question and users:**

**Sources and authority:**

**Freshness and ACL requirements:**

**Ingestion:** formats, parsing, OCR, normalization, metadata, deduplication.

**Chunking:** semantic boundaries, overlap, maximum size, parent/child relationships.

**Indexing:** embedding model, keyword index, vector store, metadata filters.

**Querying:** rewrite, decomposition, hybrid retrieval, candidate count, reranking.

**Context assembly:** ordering, citations, conflict handling, token budget, untrusted-data labels.

**Failure cases:** missing evidence, stale evidence, contradictory sources, poisoned content, ACL leak, prompt injection.

**Evaluation:** retrieval recall/precision, groundedness, citation correctness, answer quality, latency, cost.

**Operations:** re-index schedule, deletion, access revocation, drift monitoring, rollback.

## Worked example: internal support KB

**Question and users:** Front-line support agents need answers from a 50K-doc internal knowledge base (confluence + Jira tickets). Users are non-technical support reps; answers must be cited.

**Sources and authority:** Confluence (authoritative), Jira tickets (supplementary, stale after 6 months), Slack threads (untrusted, never cite directly). Authority hierarchy: Confluence > Jira > Slack.

**Freshness and ACL:** Confluence pages re-indexed hourly. Jira tickets indexed on close. ACL: support team sees everything; engineering-only pages restricted via metadata filter.

**Ingestion:** Confluence export (HTML → markdown via pandoc). Jira: API export to JSON, extract description + comments. Slack: excluded from index. Deduplication via URL hash.

**Chunking:** Recursive splitter, 512 tokens, 15% overlap. FAQ pages: 256 tokens. Long architecture docs: 1024 tokens with parent/child (parent = section header, child = chunk).

**Indexing:** Embedding model: `text-embedding-3-small`. Vector store: Pinecone (managed, ACL filters). Keyword index: BM25 via Elasticsearch. Metadata filters: `source`, `acl_group`, `last_updated`.

**Querying:** Rewrite: expand acronyms ("KB" → "knowledge base"). Decomposition: complex questions split into sub-questions. Hybrid retrieval: BM25 + dense via RRF (k=60). Candidate count: top-20 per retrieval method. Reranking: Cohere rerank-v3.0.

**Context assembly:** Top-5 reranked chunks. Citations inline. Conflict handling: prefer Confluence over Jira. Token budget: 4K tokens for context. Unttrusted data labels on Jira chunks.

**Failure cases:** Missing evidence → "I don't have enough information." Stale evidence → flag last-updated date. Poisoned content → ACL filter blocks. Prompt injection → input guardrails.

**Evaluation:** Retrieval recall@10 > 85%. Groundedness > 90%. Citation correctness > 95%. Latency p50 < 2s. Cost < $0.01/query.

**Operations:** Re-index Confluence hourly. Jira on close. Weekly retrieval drift benchmark. Schema hashing on MCP tools. Rollback: revert to previous index snapshot.

> **Canonical source:** The fill-in version of this worksheet lives in [[12 Templates/Template Library]]. This note is the explanation and worked example.

---

> **← [[09 Playbooks/Context Checklist|Context Checklist]]** · **[[AI_Home|Home]]** · **[[09 Playbooks/Evaluation and Security Review|Evaluation and Security Review]] →**
