---
type: concept
layer: foundations
status: evergreen
maturity: established
aliases: [Inference Engines, LLM Serving, Serving Stack, Model Serving]
tags: [ai-engineering, inference, serving]
visibility: personal
created: 2026-09-09
updated: 2026-09-09
prev: "01 Foundations/Local AI Hardware and Inference.md"
next: "01 Foundations/KV Cache and Long-Context Costs.md"
summary: "An inference engine turns model weights into a service; the levers are batching, KV memory management, caching, and quantization."
---

# Inference Engines and Serving

> [!summary] The gist
> A trained model is just weights. An inference engine turns it into a service.
> Prefill is compute-bound, decode is memory-bandwidth-bound. That split drives everything.
> Batching, paging, caching, and quantization are the throughput and cost levers.
> TTFT is how long you wait for token one. TPOT is the gap between the rest.

---

## The serving stack

Between a checkpoint on disk and a user reading text sits a serving stack. The engine loads weights onto a GPU (or CPU), manages the KV cache, schedules requests, and exposes an API, usually OpenAI-compatible.

The main players:

- **vLLM** — the default datacenter choice. PagedAttention, continuous batching, chunked prefill, prefix caching, FP8/INT4/GPTQ/AWQ quantization, speculative decoding, and an OpenAI-compatible server, all in one stack ([vLLM docs](https://docs.vllm.ai/en/latest/)).
- **SGLang** — same tier as vLLM, strongest at branching workloads. Its RadixAttention organizes the KV cache as a radix tree for prefix reuse across calls, and the paper reports up to 6.4x throughput over prior systems on agent, few-shot, and RAG workloads.
- **TensorRT-LLM / NVIDIA NIM** — compiled, NVIDIA-only, highest peak performance for locked-in workloads, more setup friction.
- **Hugging Face TGI** — one of the first to productionize continuous batching (Orca-style iteration-level scheduling) in a Rust/Python server.
- **llama.cpp** — GGUF quantized inference on CPUs, Macs, and consumer GPUs. No datacenter batching tricks, but it runs a 7B Q4 model on a laptop.
- **Ollama** — llama.cpp plus an easy local API and model manager. The "docker of local LLMs."

Rough map: vLLM/SGLang/TRT-LLM for serving many users, llama.cpp/Ollama for serving yourself.

## Prefill vs decode

Every request runs in two phases, and they bottleneck differently (NVIDIA inference-optimization guide):

- **Prefill** processes the whole prompt at once to build the KV cache and emit the first token. It's matrix-matrix math, highly parallel, and effectively saturates GPU compute. Compute-bound.
- **Decode** generates one token per forward pass, each conditioned on all previous ones. It's matrix-vector math that underuses the GPU's compute; the time goes into moving weights and KV tensors from memory to the compute cores. Memory-bandwidth-bound.

This is why long prompts hurt TTFT but barely touch per-token speed, and why decode throughput depends on how many requests you can batch together.

## Continuous batching

Decode wastes the GPU at batch size 1: you stream all the model weights from HBM to generate one token. Batching amortizes that weight load across many requests, which is the cheapest throughput lever there is (Anyscale).

Static batching breaks on LLMs because requests finish at different times. The whole batch waits for the longest one, and idle slots can't take new work. Orca (OSDI '22) fixed this with iteration-level scheduling: reschedule the batch after every single forward pass, so finished requests leave and new ones enter immediately. Orca showed 36.9x throughput over FasterTransformer on GPT-3 175B. Anyscale's benchmarks showed up to 8x from continuous batching alone, and 23x with vLLM's memory optimizations on top.

**Worked example.** Take Anyscale's envelope math for a 13B model: roughly 1 MB of KV state per token. On a 40 GB A100, weights eat ~26 GB, leaving ~14 GB, so about 14k tokens of KV fit at once. At 512-token sequences that's ~28 concurrent requests; at 2048 tokens it drops to ~7. Each decode step loads the weights once no matter what, so a batch of 28 produces ~28x the tokens per second of a batch of 1 (until KV or latency headroom caps you). Sequence length is the killer: 4x longer contexts mean 4x smaller batches.

The real cap on batch size is KV cache memory, which is why the next three sections exist.

## PagedAttention and vLLM's founding numbers

The KV cache is the second big GPU memory consumer after weights. Per NVIDIA's formula: 2 x layers x KV-heads x head-dim x sequence length x batch size x bytes-per-element. For Llama 2 7B at 4096 tokens in FP16 that's ~2 GB per request; for Llama 3 70B at 128K tokens it's ~40 GB (IntuitionLabs).

Before vLLM, engines reserved one contiguous KV block per request at max length. Kwon et al. measured the result: 60-80% of KV memory wasted on fragmentation and over-reservation, which strangled batch size. PagedAttention borrows OS virtual memory: KV lives in fixed-size blocks, scattered in physical memory, addressed through a block table. Waste drops to under 4%, and vLLM improves throughput 2-4x over FasterTransformer and Orca at the same latency. Digital Applied's 2026 framing: paged attention is the substrate now, not an optimization. Without it you lose 30-50% of VRAM to fragmentation.

## TTFT vs TPOT

Two latency metrics, mapped to the two phases (NVIDIA NIM benchmarking docs):

- **TTFT (time to first token)** — submission to first token. Includes queuing, prefill, and network. Long prompts raise it because prefill must process the whole input before generation starts.
- **TPOT (time per output token)** — also called inter-token latency (ITL): the average gap between consecutive tokens during decode. It determines the perceived typing speed.

They trade off. Bigger batches raise throughput and TPOT but queue requests longer, hurting TTFT. Chat UX cares about both; batch jobs care about neither, just tokens/second.

## Speculative decoding

Decode is serial: K tokens need K forward passes. Speculative decoding (Leviathan et al.) breaks the serial chain. A small, cheap draft model proposes several tokens; the big target model verifies them in parallel in one pass. Accepted tokens stay, the first mismatch and everything after it gets thrown out.

The key property: the sampling method keeps the output distribution identical to the target model alone. It's a pure latency lever with no quality cost. The original paper showed 2-3x acceleration on T5-XXL with identical outputs. vLLM ships it today with n-gram, EAGLE, and other draft strategies. It pays off when the draft model guesses well and you're latency-bound at low batch size; at big batches the GPU is already busy and gains shrink.

## Prompt and prefix caching

If two calls share a long prefix (system prompt, reference docs, tool history), you can reuse its KV state instead of re-prefilling it. vLLM hashes incoming prefixes and reuses KV blocks automatically; SGLang's RadixAttention does it tree-shaped for branching workloads. On cache hits, Digital Applied measures 85-95% cost savings, and hit rates of 60-85% on agent loops and repo Q&A drop per-call cost 5-12x. API providers sell the same thing as "prompt caching" at a discount rate.

Design consequence: put stable content first in your prompts. A changing preamble invalidates the whole prefix match.

## Quantized serving

Two separate levers:

- **Weight quantization (INT4/GPTQ/AWQ, FP8)** — smaller weights mean less memory and less bandwidth per decode step. Halving precision roughly doubles the batch you can fit (Anyscale). It's what lets a 7B model run in 4-5 GB instead of 14 GB.
- **KV cache quantization (FP8, INT8)** — FP8 KV halves cache memory at 0.3-0.7 points regression on long-context retrieval, within noise for most workloads; INT8 costs 1.5-3 points, so prefer FP8 on hardware that supports it. The 50% memory saving turns into 30-50% throughput via bigger batches (Digital Applied).

Trade-off pattern: quality regressions show up first in long-context retrieval and fine reasoning, not in everyday chat. Benchmark on your own evals before shipping INT4.

## Free vs paid serving

You can run inference on hardware you already own, or rent it per token.

- **Local** — llama.cpp/Ollama on a consumer GPU. A used RTX 3060 12GB runs 7B-8B models at Q4 comfortably, and 12-14B tight. Zero marginal cost, private, works offline. But: no continuous batching worth speaking of, one user at a time, and long contexts eat VRAM fast (1 MB/token ballpark on a 13B model means 12K tokens of KV fills the card).
- **API** — you pay per token and get frontier models, datacenter batching, prefix caching, and someone else's on-call. H100s rent for roughly $4/GPU-hour, H200s ~$6 (IntuitionLabs, Sept 2026), and providers price tokens above that to cover the serving overhead.

The break-even is volume and model size. Occasional local use of a 7B model is effectively free; heavy use of frontier models is cheaper via API than buying an H100 rig, until your token spend exceeds the hardware cost.

## Sources and further reading

- NVIDIA, 2025 — "Mastering LLM Techniques: Inference Optimization" — https://developer.nvidia.com/blog/mastering-llm-techniques-inference-optimization/ — prefill vs decode bottlenecks, KV cache formula, batching and speculative decoding mechanics.
- Yu et al., 2022 — "Orca: A Distributed Serving System for Transformer-Based Generative Models" — https://www.usenix.org/conference/osdi22/presentation/yu — iteration-level scheduling origin, 36.9x throughput over FasterTransformer.
- Anyscale (Cade Daniel et al.), 2023 — "How continuous batching enables 23x throughput in LLM inference" — https://www.anyscale.com/blog/continuous-batching-llm-inference — static vs continuous batching, 8x/23x numbers, ~1 MB KV per token for a 13B model.
- Kwon et al., 2023 — "Efficient Memory Management for Large Language Model Serving with PagedAttention" — https://arxiv.org/abs/2309.06180 — PagedAttention, vLLM's 2-4x throughput and near-zero KV waste.
- Leviathan et al., 2022 — "Fast Inference from Transformers via Speculative Decoding" — https://arxiv.org/abs/2211.17192 — draft-and-verify scheme, 2-3x speedup, distribution unchanged.
- Zheng et al., 2023 — "SGLang: Efficient Execution of Structured Language Model Programs" — https://arxiv.org/abs/2312.07104 — RadixAttention, up to 6.4x throughput on branching workloads.
- vLLM project, 2026 — "vLLM documentation" — https://docs.vllm.ai/en/latest/ — feature list grounding the engine comparison (paged attention, chunked prefill, quantization formats, speculative decoding).
- NVIDIA, 2026 — "Metrics — NVIDIA NIM LLMs Benchmarking" — https://docs.nvidia.com/nim/benchmarking/llm/latest/metrics.html — TTFT, ITL/TPOT, and tokens-per-second definitions.
- IntuitionLabs (Adrien Laurent), 2026 — "KV Cache Memory: The Real Cost of Long-Context Inference" — https://intuitionlabs.ai/articles/kv-cache-memory-long-context-inference-cost — KV formula worked examples, Llama-3-70B ~40 GB at 128K, H100/H200 memory and rental pricing, the 60-80% to <4% waste figure.
- Digital Applied, 2026 — "KV Cache Optimization for LLMs 2026: Engineering Guide" — https://www.digitalapplied.com/blog/kv-cache-optimization-techniques-2026-engineering-guide — paged attention as substrate, prefix caching savings, FP8 vs INT8 KV trade-offs, 4-40x compounded.
- (Survey, for depth) 2026 — "KV Cache Optimization Strategies for Scalable and Efficient LLM Inference" — https://arxiv.org/abs/2603.20397 — systematic review of eviction, compression, hybrid memory, and attention-mechanism approaches.

All links verified 2026-09-09.

## Related

- [[01 Foundations/Context Windows and Inference]]
- [[01 Foundations/Local AI Hardware and Inference]]
- [[07 Operations and Economics/Latency and Cost Engineering]]

---

> **← [[01 Foundations/Local AI Hardware and Inference|Local AI Hardware and Inference]]** · **[[AI_Home|Home]]** · **[[01 Foundations/KV Cache and Long-Context Costs|KV Cache and Long-Context Costs]] →**
