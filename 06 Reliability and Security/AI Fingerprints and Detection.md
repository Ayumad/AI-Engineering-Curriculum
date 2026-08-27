---
type: concept
layer: security
status: current-snapshot
maturity: established
aliases: [AI content detection, Generated-text fingerprints, Text watermarking]
tags: [ai-engineering, security, detection, watermarking]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "06 Reliability and Security/Security and Jailbreaking.md"
next: "06 Reliability and Security/Human Oversight and Trust Engineering.md"
summary: "Detectable traces of AI-generated text: statistical fingerprints (perplexity, burstiness) and embedded watermarks, their failure modes, and why signed provenance metadata beats forensic scoring."
---

# AI fingerprints and generated-text detection

> [!summary] The gist
> Generated text has detectable habits: models pick predictable words, so machine prose reads flatter than human writing. Detection splits into two schools — statistical fingerprints (perplexity, burstiness) and watermarks (signals added at generation). Both are fragile: rewriting erodes them, and detectors badly misfire on careful or non-native human writing. Signed provenance metadata is the more honest lever.

---

## What a fingerprint is

A model generates one token at a time, each chosen from a probability distribution. That process leaves statistical habits in the text: tokens in high-probability ranks, low surprise, even spacing of rare words. Humans write with spikes — sudden rare words, uneven register, idiosyncratic phrasing. Detectability is a distributional difference, not a magic marker. The two approaches exploit it differently: statistical detectors measure the habits after the fact; watermarks deliberately bend the distribution at generation time so a detector can verify the bend later.

## Statistical fingerprints

The classic reference is GLTR (Gehrmann et al., 2019). It colors each token by its rank in a model's predicted distribution: top-10 tokens green, top-100 yellow, top-1000 red, and standalone "wtf" tokens purple. Model text clusters in the green band with low perplexity; human text scatters into the tails. GLTR's tooling raised human detection accuracy from 54% to 72% — the effect of good measurement, not perfect automation.

The concept generalizes: perplexity (average surprise of the text under a language model), burstiness (how evenly rare tokens are spaced), and entropy profiles feed the "AI detector" products (GPTZero and its peers). Each is a proxy. Each breaks when the model changes, because the detector's reference distribution was fitted to the old one.

## Watermarking

Kirchenbauer et al. (2023) made the canonical scheme. At generation, the vocabulary is split into a "green" list and a "red" list using a hash of the preceding tokens as the key. Sampling is biased toward green-list tokens; the soft rule only nudges high-entropy choices, so quality loss is negligible. A detector recomputes the lists from the observed text and counts green tokens; the z-statistic tells you whether the count is too high for chance. Detection needs only a short span of tokens, and running the scheme with a secret key makes removal attacks far harder. The trade-off: longer contexts randomize the hash input, and paraphrase, translation, or insertion attacks degrade the signal.

Production adoption is real but cautious. Google's SynthID modulates token likelihood at generation time — imperceptibly, embedded in the Gemini app and web experience for text, plus Veo for video — and Google publishes a SynthID Detector portal for checking content. The DeepMind team is explicit that SynthID "isn't a silver bullet for identifying AI-generated content."

## Why detection keeps breaking

- **Distribution shift.** Detectors fitted to one model family lose accuracy as models update or new families appear. Nobody detects the current frontier model reliably.
- **Evasion is cheap.** Paraphrasing, translating, or lightly editing text erodes both statistical cues and naive watermark signals; Liang et al. (2023) note that simple prompting can bypass commercial detectors outright.
- **Detectors are biased against people, not just models.** The same study evaluated seven widely used detectors on TOEFL essays: a large share of human-authored non-native English writing — roughly 61% in the reported tests — was flagged as AI-generated. False positives land on real students and real applicants.
- **Vendors hedge.** The companies shipping generation openly warn that detection is a building block, not a verdict. That is the opposite of the third-party detector marketing.

## What holds up

Signed provenance beats forensics. When the generator attaches authenticated metadata at the source — watermark plus content metadata, verified through a portal — you get an answer with a cryptographic claim attached. After-the-fact statistical scoring is inference about a distribution and can only ever be weak evidence. For your own writing, register discipline matters for the same reason the detectors look at: flat, highly predictable prose is the fingerprint. Editing for specificity (rarer-but-right words, varied sentence rhythm) pushes text out of the detection band — not to game a checker, but because that is what readable writing is anyway.

## Sources and further reading

- Kirchenbauer et al., 2023, "A Watermark for Large Language Models" — https://arxiv.org/abs/2301.10226 — the green/red-list soft watermark, z-statistic detection, and attack analysis.
- Gehrmann et al., 2019, "GLTR: Statistical Detection and Visualization of Generated Text" — https://aclanthology.org/P19-3019/ — rank-based visual detection; human accuracy 54% → 72%.
- Liang et al., 2023, "GPT detectors are biased against non-native English writers" — https://arxiv.org/abs/2304.02819 — seven detectors, ~61% false positives on TOEFL essays, prompting bypasses.
- Google DeepMind, 2024, "Watermarking AI-generated text and video with SynthID" — https://deepmind.google/blog/watermarking-ai-generated-text-and-video-with-synthid/ — production token-distribution watermarking in Gemini and Veo.
- SynthID — https://deepmind.google/models/synthid/ — Google's watermarking model page.
- Google, 2026, "SynthID Detector — a new portal to help identify AI-generated content" — https://blog.google/innovation-and-ai/products/google-synthid-ai-content-detector/ — public verification of watermarks and metadata.

All links verified 2026-08-27.

## Related

- [[06 Reliability and Security/Security and Jailbreaking]] — the adversarial view of what models can be made to emit
- [[06 Reliability and Security/Human Oversight and Trust Engineering]] — calibrating trust when the evidence is weak