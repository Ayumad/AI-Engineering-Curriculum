---
type: concept
layer: context
status: evergreen
maturity: established
aliases: [Vector Databases, Embeddings for Retrieval, ANN Search, Vector Search]
tags: [ai-engineering, retrieval, embeddings, vector-search, ann]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "03 Context Knowledge Memory/RAG.md"
next: "03 Context Knowledge Memory/Large Project Navigation and Context Scaling.md"
summary: "How retrieval embeddings are actually trained (contrastive objectives, SimCSE, task-prefix conditioning), what ANN indexes do under the hood (HNSW/IVF/PQ mechanics and trade-offs), why similarity is not relevance, and field notes for local Ollama + ChromaDB stacks."
---

# Vector search and embeddings

> [!summary] The gist
> Embeddings turn text into numerical coordinates where similar meanings sit close together. Vector databases search those coordinates at speed. Together they power RAG's retrieval step — but the real pitfalls lie in how embedders are trained, how ANN indexes trade recall for speed, and the gap between similarity and relevance. This note covers the machinery under the hood.

---

## What an embedding model actually learns

Retrieval embedders are trained with **contrastive objectives**: pull "positive" pairs (texts that mean the same thing) together in vector space, push "negative" pairs apart. Distance then encodes meaning — within the distribution the model saw in training.

- **SimCSE (Gao et al. 2021)** is the reference simple recipe: an unsupervised contrastive objective where a sentence predicts itself, using standard dropout as the only noise. Dropout acts as minimal data augmentation — remove it and the model undergoes **representation collapse**. The supervised variant uses NLI entailment pairs as positives and contradiction pairs as hard negatives and reaches 81.6% Spearman correlation on STS with BERT-base.
- The same paper shows contrastive training **regularizes the anisotropic space** that raw pretrained embeddings collapse into — one reason vanilla BERT/GPT hidden states are poor retrieval vectors without a task-specific stage.
- **Modern embedders stack stages**: nomic-embed-text-v1 starts from a long-context BERT and runs an unsupervised contrastive stage on weakly related pairs (forum Q&A, title–body pairs), then a finetuning stage on labeled search-query/answer data with hard-example mining. It is a text encoder only as an embedding model — 8,192-token context, MTEB 62.39 — tuned per the official model card.

**Task prefixes are the secret sauce.** nomic (and several peers) require a small instruction prefix so one model can serve many geometries: documents embed as `search_document: <text>`, queries as `search_query: <text>`. Mixing modes without prefixes measurably degrades retrieval — a cheap, often-missed lever.

**Dimension guidance:** retrieval embedders commonly run 300–3,000+ dimensions (BGE at 1,024, ada-002 at 3,072 — see [[01 Foundations/What Is an LLM]] for the full lineage). Every dimension costs ~4 bytes (float32) per vector and slows brute-force scans, and more dimensions do not automatically mean better retrieval for a personal corpus. Size the model to the corpus, not the shiny leaderboard.

## ANN index internals: why vector DBs aren't just arrays

Brute-force kNN is exact but O(n·d) per query. Vector DBs use **approximate nearest neighbor (ANN)** structures that trade a little recall for orders of magnitude less search time:

| Index | Idea | Strengths | Costs / trade-offs |
|---|---|---|---|
| **HNSW** | Multi-layer navigable small-world graph; search starts at the sparsest top layer and works down, logarithmic complexity (Malkov & Yashunin) | Best recall-latency in memory; robust to clustered data; the paper reports strongly outperforming prior open-source vector-only approaches | Memory-hungry (raw vectors **plus** the link graph); slower bulk inserts; must fit RAM |
| **IVF** | Cluster vectors (k-means, `nlist` clusters); probe only the `nprobe` nearest clusters at query time | Low memory; fast inserts; simple | Recall drops when a query straddles cluster boundaries; needs a training pass over the data |
| **PQ / OPQ** | Compress vectors into short product-quantized codes | Big memory savings — what makes billion-scale in-RAM possible | Distances are approximate (code residual error); exact rescoring needs the original vectors at the end |

Counterintuitive but true for personal scale: **at ~10K–1M chunks the index choice moves retrieval quality far less than embeddings and chunking do.** ANN-Benchmarks (Aumüller, Bernhardsson & Faithfull) compares implementations on a precision-performance frontier precisely because, tuned properly, they all sit on the same curve. Pick the index the store ships with; spend the tuning budget on chunking and metadata filters instead.

## Similarity is not relevance

Top-k cosine returns *related* chunks, not necessarily *answers*. Embedding recall finds the candidate set; reranking and prompt construction decide whether the answer is right. Two mitigations, both built out in [[03 Context Knowledge Memory/RAG]]:

1. **Rerankers** — cross-encoders or late-interaction models (e.g. ColBERT) that re-score the shortlist with full token-level comparison.
2. **Query-side care** — hybrid keyword + vector fusion (RRF), query rewriting, and the fact that exact identifiers, error codes, and numbers are terrible embedding queries (they want BM25).

## Local-stack field notes (Ollama + ChromaDB)

- **Embed:** `ollama pull nomic-embed-text` (requires Ollama 0.1.26+; embedding-only model). Talk to it through the embed endpoint, `POST /api/embed`. Remember the `search_document:` / `search_query:` prefixes in ingestion and query paths respectively.
- **Index:** ChromaDB — open-source, zero-config embedded store for local or single-node workloads; collections hold `id + embedding + metadata + document`, with metadata filtering and dense-plus-sparse search. Its architecture page documents local / single-node (sub-10M records) / distributed deployment modes; for this corpus the embedded mode is right.
- **Pin the embed model.** Re-embedding with a different model or checkpoint produces vectors on a different geometry — old and new vectors in one collection become incomparable. Version-pin the model + prefix scheme in the ingestion script and reindex wholesale on any upgrade.
- **Chunking and filters beat index tuning** — and the stale-index is the silent killer: re-index on document change (hashing/version tracking), not on a calendar.

## Sources and further reading

- Malkov & Yashunin, "Efficient and robust approximate nearest neighbor search using Hierarchical Navigable Small World graphs," 2016–2018 — https://arxiv.org/abs/1603.09320 — HNSW algorithm: multi-layer graph, log-complexity search, neighbor-selection heuristic (verified 2026-08-27).
- Gao et al., "SimCSE: Simple Contrastive Learning of Sentence Embeddings," 2021 — https://arxiv.org/abs/2104.08821 — contrastive sentence-embedding recipe; dropout-as-noise; anisotropy regularization (verified 2026-08-27).
- Aumüller, Bernhardsson & Faithfull, "ANN-Benchmarks: A Benchmarking Tool for Approximate Nearest Neighbor Algorithms," Information Systems, 2019 — https://github.com/erikbern/ann-benchmarks — empirical recall/latency frontier across ANN libraries (repo, verified 2026-08-27; the project notes VIBE as its successor).
- Nomic Embed model card (`nomic-embed-text-v1`) — https://huggingface.co/nomic-ai/nomic-embed-text-v1 — 8,192 context, MTEB numbers, task-prefix requirement, two-stage contrastive training; technical report arXiv:2402.01613 (verified 2026-08-27).
- Chroma documentation — https://docs.trychroma.com/ — open-source retrieval store: collections model, metadata filtering, dense+sparse search, deployment modes (verified 2026-08-27).
- Ollama, "Generate embeddings" API reference — https://docs.ollama.com/api/embed — local embedding endpoint (verified 2026-08-27).
- Khattab & Zaharia, "ColBERT," 2020 — https://arxiv.org/abs/2004.12832 — late-interaction reranking for the "similarity ≠ relevance" mitigation (verified 2026-08-27).

All links verified 2026-08-27.

## Related

- [[03 Context Knowledge Memory/RAG]] — end-to-end retrieval pipeline, hybrid search, rerankers, vector-store comparison table.
- [[01 Foundations/What Is an LLM]] — embedding fundamentals, static vs contextual embeddings, cosine-similarity math.
- [[03 Context Knowledge Memory/Memory and Skills]] — embeddings as agent archival recall.
- [[03 Context Knowledge Memory/Context Engineering]] — what to do with retrieved context once you have it.
- [[09 Playbooks/RAG Design Worksheet]] — worked embedding-model and vector-store decisions.

---

> **← [[03 Context Knowledge Memory/RAG|RAG]]** · **[[AI_Home|Home]]** · **[[03 Context Knowledge Memory/Large Project Navigation and Context Scaling|Large Project Navigation and Context Scaling]] →**
