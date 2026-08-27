---
type: concept
layer: foundations
status: current-snapshot
maturity: established
aliases: [Local AI Hardware, LLM Hardware Planning]
tags: [ai-engineering, hardware, inference, local-ai, quantization]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "01 Foundations/Structured Outputs and Tool Calling.md"
next: "01 Foundations/Fine-Tuning Decision Framework.md"
summary: "Local model viability is governed first by usable model memory, then by memory bandwidth, runtime support, context/KV-cache headroom, and only then by headline compute."
---


# Local AI hardware and inference

Local AI starts with three separate questions: **can the model load**, **can it run**, and **is it fast enough for the job**. A configuration that barely loads a model may still be unusable after adding a long context, a vision encoder, runtime buffers, or a second user.

## Start with usable memory

Parameter count is a storage problem before it is a compute problem. A dense 27B model needs roughly 54 GB at FP16, 27 GB at 8-bit, and 13.5 GB of raw 4-bit weights. Quantization metadata, the KV cache, runtime buffers, and platform overhead mean the practical Q4 requirement is higher—often roughly 16–20 GB.

| Dense model class | Approximate practical Q4 memory | Comfortable tier |
|---|---:|---|
| 1–3B | 2–4 GB | Modern CPU or small GPU |
| 7–8B | 5–7 GB | 8 GB GPU |
| 12–14B | 8–11 GB | 12–16 GB GPU |
| 20–27B | 14–20 GB | 24 GB preferred |
| 30–32B | 19–24 GB | 24–32 GB |
| 70–72B | 40–50 GB | 48–64 GB |

Treat these as planning ranges, not compatibility guarantees. Architecture, quantizer, context length, batching, and multimodal components change them materially.

## Bandwidth, compute, and memory architecture

Interactive decoding is often memory-bandwidth-bound: each generated token reads a large share of the weights. More compute does not rescue a model that cannot be fed efficiently. Prompt prefill, large batches, image/video generation, and training make matrix compute more important.

Discrete GPUs provide high-bandwidth VRAM but limited capacity. CPU/RAM offload can make a too-large model run, but PCIe is vastly slower than VRAM and usually reduces tokens per second. Unified-memory systems trade some peak speed for a large shared memory pool: they can fit models that a 24–32 GB card cannot. Use a GPU platform for mature runtime support as well as hardware; CUDA, Metal, HIP/ROCm, Vulkan, and CPU backends each change what is practical.

## Quantization, context, and MoE

Lower precision reduces memory use and bandwidth demand, but may affect quality and supported kernels. The KV cache grows with context length, sequence count, and architecture, so reserve headroom instead of comparing model weights to total VRAM.

For mixture-of-experts models, total parameters determine storage while active parameters more closely determine per-token compute. A model can therefore be quick once loaded yet too large to fit on a personal machine.

## Choose hardware from the workload

1. Define the model family, quantization, context, and concurrency target.
2. Budget weights, KV cache, runtime overhead, and growth margin.
3. Choose the memory architecture that fits that budget; use offload only when its latency is acceptable.
4. Verify the runtime and kernels for the intended model, precision, and platform.
5. Measure prompt prefill, decode speed, peak memory, and power on a representative workload.

For inference, capacity and bandwidth lead. For LoRA/QLoRA, add activations, optimizer state, and batches. Full pretraining is a different scale of problem.

## The bandwidth math

For interactive decoding, latency per token is dominated by reading weights, not math:

```text
tokens/s ≈ effective memory bandwidth ÷ (active parameters × bytes per weight)
```

A 70B model at 4-bit reads roughly half the bytes per token of the same model at 8-bit, so quantization directly buys tokens per second on the same hardware. This is the frame behind "inference is memory-bound": compute wins on prefill, batching, and training; bandwidth wins on single-stream chat (Karpathy 2023; Huyen 2025).

## What the evidence says about quantization

QLoRA's authors fine-tuned a 65B model on a single 48 GB GPU using 4-bit base weights while preserving full 16-bit-level task performance (Dettmers et al. 2023). Quantization is therefore a mature memory lever—with two caveats: quality depends on the quantizer and format used, and some low-precision kernels simply are not implemented on every platform/runtime.

## Hardware in practice

Hardware platforms differ not just in raw specs but in software ecosystem maturity, which often matters more than the numbers on the spec sheet.

- **Apple M-series (unified memory):** M2/M3/M4 Ultra with 192–512 GB unified memory can load models that no single consumer GPU fits. Memory bandwidth is high relative to power draw (M4 Ultra ~800 GB/s); the Metal/MPS backend in llama.cpp and MLX is mature. Best for developers and power users who want a single quiet machine for local inference without a GPU server.
- **NVIDIA RTX / H100 / H200:** CUDA + tensor cores remain the most widely supported runtime. An RTX 4090 (24 GB, ~1,007 GB/s) fits 7–14B quantized models interactively. H100/H200 (80 GB, ~3.35 TB/s HBM3e) are the data-center workhorse—multiple can serve 70B+ models or batch many smaller ones. If you need broad kernel support and vendor tooling, NVIDIA is the default.
- **AMD MI300 (ROCm/HIP):** MI300X offers 192 GB HBM3 with ~5.3 TB/s bandwidth—on paper competitive with H200 for large-model inference. ROCm support has improved but kernel coverage still lags CUDA; verify your specific model, quantization, and framework before committing.
- **CPU-only inference** is acceptable when the model is small (≤7–8B quantized), latency tolerance is high (background processing, offline batch), or power/heat budgets are strict. llama.cpp on modern CPUs can sustain 10–30 tokens/s for small models, adequate for non-interactive workloads.
- **Energy note:** A single H100 draws ~700 W under load; an M4 Ultra desktop peaks around 150 W. For always-on local inference, watts-per-useful-token matters more than peak throughput.

## Sources and further reading

- Dettmers et al., "QLoRA: Efficient Finetuning of Quantized LLMs," 2023 — https://arxiv.org/abs/2305.14314
- Karpathy, *Intro to Large Language Models* — https://www.youtube.com/watch?v=zjkBMFhNj_g — the memory-bandwidth-bound decoding intuition.
- Huyen, *AI Engineering* (O'Reilly, 2025) — https://www.oreilly.com/library/view/ai-engineering/9781098166298/ — hardware and inference chapters.

All links verified 2026-08-27.

## Related

[[01 Foundations/Context Windows and Inference]] · [[01 Foundations/Fine-Tuning Decision Framework]] · [[07 Operations and Economics/Latency and Cost Engineering]]

---

---

> **← [[01 Foundations/Structured Outputs and Tool Calling|Structured Outputs and Tool Calling]]** · **[[AI_Home|Home]]** · **[[01 Foundations/Fine-Tuning Decision Framework|Fine-Tuning Decision Framework]] →**
