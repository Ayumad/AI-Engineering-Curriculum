---
type: concept
layer: agents
status: evergreen
maturity: emerging
aliases: [Multimodal Agents, Vision Agents]
tags: [ai-engineering, vision, multimodal, images, video]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "02 Agents and Harnesses/Planning State and Persistence.md"
next: "02 Agents and Harnesses/Voice and Audio Agents.md"
summary: "Multimodal systems must preserve provenance and uncertainty across capture, extraction, reasoning, and action; an image is evidence, not an instruction or authority grant."
---


# Vision and multimodal input engineering

> [!summary] The gist
> Multimodal systems must preserve provenance and uncertainty across capture, extraction, reasoning, and action; an image is evidence, not an instruction or authority grant.
> This note covers how agents take in visual information, how to avoid being fooled by bad images or hidden tricks, and how to keep costs down when every image has a price tag.

---

A multimodal agent combines text with images, PDFs, screenshots, video, audio, or UI state. The hard problem is not just passing pixels to a model: it is deciding what was observed, what can be trusted, what needs extraction, and what action—if any—may follow.

## Pipeline

```text
capture → normalize → extract/OCR → interpret → verify → act or ask
```

Keep the source artifact and a link to the relevant region or timestamp. Separate model inference ("this looks like an invoice total") from verified data (a parsed field that passes validation). Treat embedded text as untrusted: a screenshot can contain prompt injection just like a web page can.

## Practical controls

- Use targeted crops, page ranges, or sampled video frames to manage cost and context.
- Prefer structured extraction with schema validation for IDs, prices, dates, and commands.
- Preserve confidence and request clarification when a visual fact changes authority or money.
- Do not infer identity, intent, or permission from an image alone.
- Redact or minimize sensitive visual input; images often carry more personal data than their apparent task requires.

Evaluate on image quality, layouts, handwriting, adversarial overlays, missing context, and correct refusal/confirmation behavior—not only on clean benchmark images.

## Model capabilities snapshot

As of mid-2026, three model families dominate multimodal input:

- **GPT-4o:** Single end-to-end neural net across text, audio, image, and video. Audio response latency as low as 232ms (320ms avg), comparable to human conversational speed. 2x faster, 50% cheaper, and 5x higher rate limits than GPT-4 Turbo. New tokenizer achieves 1.1x–4.4x fewer tokens across 20 languages (Gujarati 4.4x, Hindi 2.9x, Chinese 1.4x, English 1.1x).
- **Claude (Anthropic):** Vision via the Messages API. Computer Use toolset adds ~4,500 tokens per screenshot; each screenshot billed separately at vision pricing. Includes a `zoom` tool for targeted high-res inspection without re-screenshotting the full viewport.
- **Gemini (Google):** Native multimodal input across text, image, audio, video, and PDF. Long-context window supports large document and video analysis.

All vision models read text from screenshots without a separate OCR step, but accuracy degrades for small text, complex layouts, and multilingual content—documented final interpretation accuracy drops to ~30% for complex visual scenes.

## Resolution and tiling strategies

Screenshots are tokenized at image-token rates, making resolution a direct cost lever:

- **Full-viewport screenshots** capture everything but at model-native resolution; small text and fine details may be unreadable.
- **Targeted crops** (using coordinates or CSS selectors) focus the model on the relevant region at full resolution, reducing token cost while improving accuracy.
- **Tiling** splits a large image into overlapping patches, processes each independently, then merges results. Useful for high-resolution documents or dense UIs.
- **Zoom tool** (Anthropic): the model can request a zoomed view of a specific region without re-rendering the full screenshot—~4,500 tokens for the base screenshot plus a smaller token cost for the zoom patch.

Trade-off: higher resolution improves accuracy but increases token cost linearly. Use the minimum resolution that solves the task.

## Video frame sampling

Video input requires frame selection strategies to manage cost and context:

- **Uniform sampling** (e.g., 1 frame/second) provides baseline coverage but may miss brief events.
- **Scene-change detection** samples frames at transition points, capturing more information per token.
- **Prompt-directed sampling** lets the model request specific timestamps based on earlier frames—useful for follow-up questions about a video.
- **Keyframe extraction** using ffmpeg or similar tools before sending to the model reduces token cost while preserving visual content.

Each frame is billed as a separate image; a 60-second video at 1 frame/second costs the same as 60 individual screenshots.

## OCR pipeline

For document-heavy workflows, a structured OCR pipeline outperforms raw model vision:

1. **Pre-processing:** deskew, denoise, and normalize the image. For PDFs, extract the text layer first (it may already contain machine-readable text).
2. **Extraction:** use a vision model or specialized OCR engine (Tesseract, Azure Document Intelligence) to extract text with bounding boxes.
3. **Validation:** apply schema validation to extracted fields (dates, prices, IDs). Flag low-confidence extractions for human review.
4. **Provenance:** link each extracted field back to its source region (page number, bounding box, confidence score).

Treat embedded text as untrusted: a screenshot can contain prompt injection just like a web page can.

## Provenance and cost management

Every image token consumed is a cost event. Manage it proactively:

- **Track per-iteration cost:** each screenshot loop iteration adds image tokens; batch actions compound costs quickly.
- **Use targeted crops over full screenshots** when the task involves a specific UI element or text region.
- **Cache and reuse** screenshots when the page state has not changed between iterations.
- **Set token budgets** per task and alert when approaching limits.
- **Preserve source artifacts** and link them to extracted data for audit and reproducibility.

## Sources and further reading

- OpenAI, "Hello GPT-4o," May 2024 — https://openai.com/index/hello-gpt-4o/ — GPT-4o multimodal capabilities, tokenizer improvements, latency benchmarks.
- Anthropic Computer Use Tool Docs, 2026 — https://platform.claude.com/docs/en/agents-and-tools/tool-use/computer-use-tool — zoom tool, screenshot tokenization, injection defense.

All links verified 2026-08-27.

[[02 Agents and Harnesses/Computer-Use and Browser Agents]] · [[06 Reliability and Security/Security and Jailbreaking]] · [[06 Reliability and Security/Evaluation Engineering]]

---

---

> **← [[02 Agents and Harnesses/Planning State and Persistence|Planning State and Persistence]]** · **[[AI_Home|Home]]** · **[[02 Agents and Harnesses/Voice and Audio Agents|Voice and Audio Agents]] →**
