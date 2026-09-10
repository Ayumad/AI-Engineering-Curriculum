---
type: concept
layer: foundations
status: evergreen
maturity: established
aliases: [KV cache, KV cache memory, long-context cost, context rot sizing]
tags: [ai-engineering, inference, kv-cache, long-context]
visibility: personal
created: 2026-09-09
updated: 2026-09-09
prev: "01 Foundations/Inference Engines and Serving.md"
next: "01 Foundations/Test-Time Compute and Reasoning Models.md"
summary: "The KV cache is a linear-in-context memory bill that decides whether long-context inference fits on your GPU, and it is separate from model weights."
---

# KV Cache and Long-Context Costs

> [!summary] The gist
> Long context costs memory, and the memory is the bill you actually pay.
> Every token you feed a model adds a fixed chunk of cache. It stays put until the request ends.
> Llama-3-70B at 128K tokens wants about 40GB of cache. The weights are a separate 140GB.
> Your GPU holds both, so the cache is what decides how many users you can serve.
> Architecture and precision fixes shrink it 4-40x. But a bigger window never meant better recall.

---

## What the KV cache is and why it exists

Transformers generate one token at a time. Each new token attends to every token before it. Without help, that means recomputing key and value vectors for the whole sequence at every step, so the cost grows quadratically.

The KV cache removes that redundancy. You compute each token's key and value once, store them, and reuse them. Only the newest token's K and V get calculated per step.

The trade is that the cache lives in the same GPU HBM pool as the model weights, and it grows for the whole life of a request. It is not a temporary scratch buffer you can reclaim mid-generation.

## The formula

The standard form, published by NVIDIA and used across the industry:

```
KV cache bytes = batch_size x seq_len x 2 x num_layers x hidden_size x bytes_per_param
```

The 2 is for keys and values. `bytes_per_param` is 2 for FP16/BF16, 1 for FP8 or INT8, 0.5 for 4-bit.

One correction matters. With grouped-query or multi-query attention you substitute `num_kv_heads x head_dim` for `hidden_size`, since only the KV projections get cached. Getting this wrong overestimates the bill by 4x or more on modern models.

NVIDIA's own worked example: Llama-2-7B, batch 1, 4,096 tokens, FP16 gives 1 x 4096 x 2 x 32 x 4096 x 2 bytes, roughly 2GB for a single request.

## The 70B example

Apply it to Llama-3-70B with GQA at 128K tokens and you land on about 40GB. Hugging Face's published table lists it precisely as 39.06GB, and NVIDIA independently reports "about 40GB per user" for that configuration. Two primary sources, two methods, same answer.

Now the part people miss: that 40GB sits on top of roughly 140GB just to hold the FP16 weights. A single 80GB H100 cannot do it. A single 141GB H200 cannot do it either, since the weights alone nearly fill it. That deployment needs multi-GPU tensor parallelism, which is why AWS bundles 8 H100s per P5 instance for 640GB of combined HBM.

Tensor parallelism has a useful side effect for budgeting. Sharding weights across more GPUs leaves more of each GPU's memory free for the cache.

## Why it scales twice

The cache scales linearly with context length, and linearly again with batch size or concurrent users. Ten users at 128K each does not add 40GB once. It adds roughly 400GB, a multiple of what any single accelerator provides.

This is why KV head count and layer depth dominate the bill, not total parameter count. Qwen2-7B uses 4 KV heads against Llama-3-8B's 8 at roughly the same parameter class, which halves its per-token cache cost. When you pick a model for long-context work, treat published KV head count as a cost parameter, not only an accuracy parameter.

For reference on the hardware side of the ceiling: 80GB on an H100, 141GB on an H200, and 372GB per Grace Blackwell Superchip in a GB200 NVL72 rack. That 372GB covers two GPUs, so roughly 186GB each.

## The architectural fixes

**Grouped-query attention.** Between multi-head and multi-query. More than one KV head but far fewer than the query head count. Meta's Llama-2 paper says plainly that MHA memory costs "grow significantly" with context and batch, which is why the 34B and 70B variants use 8 KV projections instead of one per head. Every Llama 3 model from 8B to 70B uses GQA. Reported compression is 4-8x at sub-0.5 point quality regression.

**DeepSeek MLA.** Multi-head latent attention stores a compressed latent vector, typically 512-dim, instead of full K and V tensors, with decoupled RoPE-encoded keys handling position. DeepSeek-V2's paper reports it reduces the KV cache by 93.3% versus DeepSeek 67B while boosting max generation throughput 5.76x. That is the >90% reduction claim, from the primary source. Reported compression is 7-14x at under 0.2 point regression.

**FP8 KV quantization.** Halves bytes per element with no change to allocation logic. NVIDIA's TensorRT-LLM documentation reports it enables 2-3x larger batch sizes on H100s, and recommends FP8 over INT8 for lower accuracy impact. The regression numbers are 0.3-0.7 points on multi-needle long-context retrieval for FP8, versus 1.5-3 points for INT8. Batch headroom is not purely linear with cache savings, because freed memory also absorbs activation growth.

**PagedAttention.** Treats cache memory like OS virtual memory: fixed-size blocks, allocated on demand, reached through a block table. No contiguity requirement. The original vLLM paper found existing systems wasted 60-80% of allocated memory to fragmentation and over-reservation, because they pre-allocated a contiguous block sized for a request's maximum possible length. Paged allocation cut that waste to under 4% and improved throughput 2-4x at the same latency versus FasterTransformer and Orca. The cost is roughly 2-5% per-token compute overhead for 95%+ effective utilization.

**Prefix caching.** Reuses the KV state of a shared prompt prefix across calls. If two calls share 200K tokens of system prompt and docs and differ only in the last 5K, you compute once and read thereafter. vLLM hashes prefixes automatically from 0.4+; SGLang's RadixAttention organizes the cache as a radix tree for branching workloads. Reported hit rates of 60-85% on agent loops and repo Q&A drop per-call cost 5-12x, with 85-95% savings on cache hits. This is the one optimization that gets cheaper the longer your context is.

**Sliding windows and eviction.** Mistral's 4K sliding window caps cache growth past the window at the cost of long-range attention. Token eviction and attention-sink methods like H2O and StreamingLLM drop less useful entries. Windows are right when most reasoning is local and wrong for long-document Q&A or code search.

## How they compound

None of these are mutually exclusive. Production stacks combine them.

Start at a naive MHA FP16 baseline for a 70B-class model at 1M context: about 135GB of cache, which already exceeds the 140GB of FP16 weights and cannot fit alongside them on one GPU. Apply MLA and FP8 together and that drops to roughly 8-10GB, a 17x reduction. Add paged allocation so you stop wasting 60-80% of what remains, plus prefix caching on top of a stable prompt structure, and the reported range is 4-40x total cost reduction on long-context inference.

The useful framing: the model picks the floor, the serving stack picks the ceiling. Architecture (GQA vs MLA) is pinned at model selection. Paged attention, prefix cache, and FP8 KV are runtime knobs you can turn afterward.

The right starting point is all of them together, then back off anything that breaks your eval.

## Capacity is not recall

Here is the uncomfortable part. You can buy the memory and still not get the answers.

RULER, an independent long-context benchmark, tested 17 models (15 open plus Gemini-1.5-Pro and GPT-4) against their own claimed windows, which ranged from 32K to 1M tokens. Its headline finding: only about half maintained satisfactory performance even at 32K, far short of what vendors advertised.

Anthropic names this degradation "context rot." As token count in the window grows, recall accuracy falls even when the tokens technically fit. They frame it as an attention budget: transformers model n-squared pairwise relationships for n tokens, and training data skews toward shorter sequences, so models have fewer specialized parameters for context-wide dependencies. The result is a performance gradient rather than a hard cliff.

For cost purposes this matters directly. A KV cache sized for 128K or 1M tokens is a real, billable memory allocation whether or not the model can use that much context effectively. Provisioning for advertised length without validating usable length means paying for capacity that never turns into better answers. Measure usable context before you buy for maximum context.

## Sizing on one GPU

Say you have 12GB of VRAM and a 7-8B quantized model. The weights at 4-bit take about 4-5GB, leaving roughly 7-8GB for cache plus activations and overhead.

The rough rule at FP16: about 1-2K tokens per GB of KV cache for a GQA model in this class. Worked out for Llama-3-8B (32 layers, 8 KV heads, 128 head dim), 8,000 tokens costs about 1.05GB at FP16 and roughly 0.52GB at FP8. Sixteen thousand tokens is about 2.1GB at FP16, 1.05GB at FP8.

So on 12GB you can realistically serve 8-16K tokens of context at FP16, and push toward 32K with FP8 KV. Those are single-request numbers; concurrency divides them. GQA is doing the heavy lifting here, since the same context on a full-MHA model would cost 4x more.

Practical read for local hardware: your context ceiling is set by the cache budget, and that budget is small. A model that advertises 128K will happily accept it and then fall over, or thrash into CPU offload. Set max context to what your VRAM actually supports, and prefer models with low KV head counts.

Also note what TensorRT-LLM does by default: it allocates 90% of whatever GPU memory remains after weights and activations to the KV cache. That is a knob, and on a tight card you may want to turn it down rather than let the scheduler admit requests it cannot serve.

## Sources and further reading

- IntuitionLabs (Adrien Laurent) / 2026, "KV Cache Memory: The Real Cost of Long-Context Inference" — https://intuitionlabs.ai/articles/kv-cache-memory-long-context-inference-cost — grounds the formula, the 40GB Llama-3-70B figure, the 140GB FP16 weights, GPU memory pools and pricing, FP8 batch benefit, RULER, MLA >90%, and PagedAttention waste numbers.
- Digital Applied / 2026, "KV Cache Optimization for LLMs 2026: Engineering Guide" — https://www.digitalapplied.com/blog/kv-cache-optimization-techniques-2026-engineering-guide — grounds paged attention as substrate, prefix caching leverage, GQA/MLA compression ratios, FP8 accuracy regressions, and the 4-40x compounding claim.
- Kwon et al. / 2023, "Efficient Memory Management for Large Language Model Serving with PagedAttention" — https://arxiv.org/abs/2309.06180 — grounds the 60-80% waste to under 4% figure and the 2-4x throughput gain.
- DeepSeek-AI / 2024, "DeepSeek-V2: A Strong, Economical, and Efficient Mixture-of-Experts Language Model" — https://arxiv.org/abs/2405.04434 — grounds MLA and the 93.3% KV cache reduction with 5.76x throughput.
- Anthropic (Rajasekaran, Dixon, Ryan, Hadfield) / 2025, "Effective context engineering for AI agents" — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — grounds context rot, the attention budget framing, and n-squared attention scaling.
- / 2026, "KV Cache Optimization Strategies for Scalable and Efficient LLM Inference" — https://arxiv.org/abs/2603.20397 — systematic review organizing techniques into eviction, compression, hybrid memory, novel attention, and combination strategies; grounds the linear-in-context bottleneck framing and the point that no single technique dominates.

All links verified 2026-09-09.

## Related

- [[01 Foundations/Context Windows and Inference]]
- [[01 Foundations/Inference Engines and Serving]]
- [[01 Foundations/Local AI Hardware and Inference]]
- [[07 Operations and Economics/Latency and Cost Engineering]]
- [[03 Context Knowledge Memory/Context Engineering]]

---

> **← [[01 Foundations/Inference Engines and Serving|Inference Engines and Serving]]** · **[[AI_Home|Home]]** · **[[01 Foundations/Test-Time Compute and Reasoning Models|Test-Time Compute and Reasoning Models]] →**
