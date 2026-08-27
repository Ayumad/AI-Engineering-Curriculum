---
type: concept
layer: context
status: evergreen
maturity: established
aliases: [Retrieval-Augmented Generation]
tags: [ai-engineering, rag, retrieval, embeddings]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "03 Context Knowledge Memory/Skills, Tools, and Capability Management.md"
next: "03 Context Knowledge Memory/Vector Search and Embeddings.md"
summary: "RAG retrieves relevant external evidence and places it into model context at answer time. It is usually preferable to finetuning when knowledge changes or is private."
---


# Retrieval-augmented generation (RAG)

## Plain-English introduction

A good librarian doesn't memorize every book — they know how to find the right one and open it to the right page. RAG gives AI that same superpower. Instead of relying only on what the model learned during training, it searches a collection of documents at answer time and hands the most relevant pieces to the model as evidence. This is especially useful when knowledge is private, changes frequently, or simply didn't exist when the model was first trained. This note walks through how that search-and-supply pipeline works.


RAG retrieves relevant external evidence and places it into model context at answer time. It is usually preferable to fine-tuning when knowledge changes or is private.

```text
documents → chunk → embed/index → retrieve → rerank → build context → generate with evidence
```

## Retrieval toolbox

- **Embeddings:** semantic vectors for meaning-based search.
- **Keyword/BM25:** strong for exact identifiers, error codes, and names.
- **Metadata filters:** tenant, date, ACL, source, language, or document type.
- **Hybrid retrieval:** combine lexical and semantic candidates.
- **Reranking:** spend more compute to order a smaller candidate set.
- **Query rewriting/decomposition:** turn vague questions into retrievable subqueries.
- **Agentic RAG:** let the agent inspect evidence gaps, search again, compare sources, and resolve contradictions.
- **Knowledge graphs:** preserve entities and relations when connections matter more than passages.

Chunking must preserve enough local meaning without making every result too broad. Store provenance such as document, section, page, timestamp, and access policy. Retrieval quality, context construction, and citation behavior all require evaluation.

## Chunking strategies

| Strategy | How it works | Trade-offs |
|---|---|---|
| **Fixed** | Split every N tokens (e.g., 512) | Fastest; cuts mid-sentence, losing local context |
| **Recursive** | Split by separator hierarchy (paragraph → sentence → token), cuts at largest natural boundary that fits | Sensible default; preserves more semantic boundaries |
| **Semantic** | Embed each sentence, break on topic shift | Best coherence; costs one embedding call per sentence |

**Practical defaults:** start at ~512 tokens with 10–20% overlap using recursive splitting. Adjust by content type: FAQ entries ~256 tokens, articles ~512, legal/technical documents ~1024, tables/page-aligned.

**Overlap:** 10–20% token overlap helps boundary-spanning concepts in dense retrieval. However, a Jan 2026 study found no measurable benefit with sparse (SPLADE) retrieval while raising indexing cost—keep overlap for dense retrieval, omit for sparse-only pipelines.

**Benchmark caveat:** public chunking benchmarks conflict because they ran on different documents with different embedders. Do not copy someone else's leaderboard; evaluate on your own corpus.

**Contextual Retrieval (Anthropic 2024):** prepend 50–100 tokens of chunk-specific context to each chunk before embedding AND BM25 indexing. This reduces failed retrievals by 49% alone, 67% with reranking. Uses prompt caching for indexing efficiency. This is the single highest-leverage chunking improvement in current practice.

## Hybrid search (BM25 + vector)

Combine dense (semantic) and sparse (lexical) retrieval via **Reciprocal Rank Fusion (RRF)**:

```python
# RRF combines rank positions, not raw scores — calibration-free
def rrf_score(rank, k=60):
    return 1 / (k + rank)

# Merge ranked lists from BM25 and vector search
# k=60 smoothing constant works well empirically
```

RRF is calibration-free across different score scales (BM25 scores vs cosine similarity). Expect 5–15% higher recall@10 vs the stronger individual method.

| Method | Excels at | Fails at |
|---|---|---|
| BM25 (sparse) | Exact matches, rare terms, product names, acronyms, error codes | Vocabulary gaps, paraphrases, semantic similarity |
| Vector (dense) | Semantic matching, paraphrase handling, conceptual queries | Exact string matches, rare terminology |

Their failures are complementary—this is why hybrid outperforms either alone.

## Rerankers

After initial retrieval, rerankers spend more compute to improve ordering of a smaller candidate set:

| Reranker | Approach | Latency | Quality | Notes |
|---|---|---|---|---|
| **Cross-encoder** (general) | Jointly attend to [query; doc] pair | ~100ms+ | +5–15 NDCG@10 over bi-encoder | Gold standard for quality; too slow for full corpus |
| **BGE-Reranker-v2-m3** | Multilingual cross-encoder, 568M params | ~50–100ms | 60.4 MTEB | Default choice; MIT licensed |
| **Cohere rerank-v3.0** | Proprietary cross-encoder | ~100ms p50 | Strong | $1/1K queries; managed service |
| **ColBERT** | Late interaction: token-level matching | 5–50ms | +3–8 NDCG | Faster than cross-encoder; token embeddings cached |

## GraphRAG

Microsoft's GraphRAG extracts a knowledge graph from text, then uses hierarchical clustering (Leiden algorithm) to create community summaries for retrieval:

| Search mode | How it works | When to use |
|---|---|---|
| **Global Search** | Holistic question answered from community summaries | "What are the main themes across this corpus?" |
| **Local Search** | Entity fan-out: find entity → retrieve related entities and communities | "What does X know about Y?" |
| **DRIFT Search** | Entity + community with dynamic retrieval | Complex multi-hop questions requiring traversal |

GraphRAG is strong on questions requiring traversing disparate information that pure vector search would miss. Note: now in maintenance mode. See https://microsoft.github.io/graphrag/ for current status.

## Vector database selection

| Database | Strengths | Weaknesses | Best for |
|---|---|---|---|
| **Chroma** | Zero-config, embedded, quick start | Limited scale, basic features | Prototyping, local dev |
| **Qdrant** | Rust performance, rich filtering, hybrid search | Younger ecosystem | Production vector search with metadata filtering |
| **Weaviate** | GraphQL API, built-in vectorization modules | Operational complexity | Multi-modal search, managed cloud option |
| **Pinecone** | Fully managed, serverless, low ops | Vendor lock-in, cost at scale | Teams wanting zero-ops managed service |
| **pgvector** | PostgreSQL extension, relational + vector | Vector perf trails dedicated DBs at scale | Teams already on Postgres |

**Selection criteria:** evaluate on your corpus size, query latency requirements, filtering needs (metadata, ACL, tenant), operational budget, and whether you need hybrid (vector + keyword) search natively.

## Production deployment patterns

1. **Indexing pipeline:** chunk → enrich (contextual metadata) → embed → store with provenance. Run on every document change. Use prompt caching for contextual enrichment cost.
2. **Query pipeline:** rewrite query → hybrid retrieve (BM25 + vector) → rerank → build context → generate with citations. Measure hit@K, MRR, citation correctness, and faithfulness.
3. **Monitoring:** track retrieval precision, answer faithfulness, latency p50/p99, and cost per query. Alert on precision drops—stale indexes are the silent killer.
4. **Lost-in-the-middle mitigation:** models ignore information in the middle of long context. Use smaller context windows (2–4K tokens), place strongest chunks at start AND end, query after context presentation.
5. **Incremental updates:** re-index only changed documents. Use document hashing and version tracking to avoid full rebuilds.

## RAG versus fine-tuning

`Changing knowledge → RAG` · `Changing behavior/style → fine-tuning` · `Both → combine them deliberately`.

Related: [[03 Context Knowledge Memory/Memory and Skills]] · [[03 Context Knowledge Memory/Context Engineering]] · [[06 Reliability and Security/Reliability Evals and Observability]].

## Practice and evaluation

Start with a small authoritative corpus and inspect actual queries before tuning a complex pipeline. Evaluate hit@K/MRR, source coverage, citation correctness, faithfulness, access control, and correct abstention. RAG serves changing facts; memory serves retained experience; skills serve reusable procedures.

For the machinery under this pipeline — how embedding models are trained, ANN index internals (HNSW/IVF/PQ), similarity-vs-relevance, and the local Ollama + ChromaDB stack — see [[03 Context Knowledge Memory/Vector Search and Embeddings]].

## Evidence base

- **RAG's origin (Lewis et al. 2020):** combine parametric memory (the model's weights) with non-parametric memory (retrievable documents). The paper's recipe produced strong gains on knowledge-intensive NLP tasks and is the canonical citation for the pattern.
- **ColBERT (Khattab & Zaharia 2020):** late-interaction, token-level search delivers better retrieval quality than single-vector embedding search at practical cost—the basis of many modern dense and hybrid designs.
- **Contextual retrieval (Anthropic 2024):** prepending a short context to each chunk (explaining what the chunk is about and where it fits) and combining BM25 with embeddings substantially reduced retrieval failures in Anthropic's public evaluations. Chunk design matters as much as the retriever.
- **Book treatment (Huyen 2025):** retrieval is a design space—chunking, embedding vs. keyword, hybrid, reranking, evals—and RAG beats fine-tuning when knowledge changes or is private, matching this note's core claim.

## Sources and further reading

- Lewis et al., "Retrieval-Augmented Generation for Knowledge-Intensive NLP Tasks," 2020 — https://arxiv.org/abs/2005.11401
- Khattab & Zaharia, "ColBERT: Efficient and Effective Passage Search via Contextualized Late Interaction over BERT," 2020 — https://arxiv.org/abs/2004.12832
- Anthropic, "Introducing Contextual Retrieval," Sep 2024 — https://www.anthropic.com/engineering/contextual-retrieval
- Huyen, *AI Engineering* (O'Reilly, 2025) — https://www.oreilly.com/library/view/ai-engineering/9781098166298/ — retrieval and evals chapters.
- "RAG Chunking Strategies" (The Agent Ecosystem, Jul 2026) — https://www.theagentecosystem.com/blog/rag-chunking-strategies — fixed/recursive/semantic comparison, practical defaults.
- "Hybrid Search for RAG" (Adaptive Recall) — https://www.adaptiverecall.com/rag-pipelines/hybrid-search-rag.php — RRF fusion, BM25+vector complementarity.
- GraphRAG official docs (Microsoft) — https://microsoft.github.io/graphrag/ — entity extraction, Leiden clustering, Global/Local/DRIFT search.
- GraphRAG GitHub (Microsoft) — https://github.com/microsoft/graphrag — open-source implementation, maintenance status.
- "Reranking & Cross-Encoders for RAG" (LocalAIMaster, 2026) — https://localaimaster.com/blog/reranking-cross-encoders-guide — cross-encoder vs bi-encoder benchmarks.
- "Rerankers & context engineering" (Masst Docs) — https://docs.masst.dev/ai/retrieval/rerankers-context — ColBERT latency, Cohere pricing.
- "RAG vs Fine-tuning" (The Agent Ecosystem, Jul 2026) — https://www.theagentecosystem.com/blog/rag-chunking-strategies — chunking benchmark caveats.

All links verified 2026-08-27.

---

---

> **← [[03 Context Knowledge Memory/Skills, Tools, and Capability Management|Skills, Tools, and Capability Management]]** · **[[AI_Home|Home]]** · **[[03 Context Knowledge Memory/Vector Search and Embeddings|Vector Search and Embeddings]] →**
