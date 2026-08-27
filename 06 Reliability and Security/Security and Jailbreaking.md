---
type: security
layer: reliability
status: evergreen
maturity: established
aliases: [Prompt Security, Jailbreaks]
tags: [ai-engineering, security, prompt-injection, jailbreak]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "06 Reliability and Security/Observability.md"
next: "06 Reliability and Security/Human Oversight and Trust Engineering.md"
last_verified: 2026-08-27
summary: "A jailbreak tries to make a model disregard or reinterpret its governing constraints. Prompt injection is broader: untrusted content attempts to influence instructions..."
---


# Security, jailbreaks, and prompt injection

## Plain-English introduction

A jailbreak is to an AI what lock-picking is to a building — a clever trick that bypasses the locks meant to keep it safe. Someone might try to convince a chatbot to ignore its rules by disguising harmful requests as harmless ones, hiding instructions inside documents the agent reads, or slowly building trust before making a dangerous ask. This note catalogs the main attack families — from fake personas to hidden commands in images — and explains how to defend against them with layered protections: input scanning, output filtering, tool permission walls, and isolated execution environments. The key takeaway is that no single defense is enough; real security comes from stacking multiple safeguards together.

A jailbreak tries to make a model disregard or reinterpret its governing constraints. Prompt injection is broader: untrusted content attempts to influence instructions, tool choice, memory, or authority. The dangerous unit is an agent with tools, credentials, and side effects—not text alone.

## Historical families

- Persona and “DAN”-style role-play: social framing attempts to replace the instruction hierarchy.
- Encoding and obfuscation: hide intent from filters or split it across turns.
- Multi-turn escalation: establish rapport, harmless premises, and progressively stronger requests.
- Adversarial suffixes: optimized token sequences that shift model behavior.
- Prompt leakage: coax system or tool instructions into the response.
- Indirect injection: hostile instructions inside webpages, documents, email, code comments, or search results.
- Tool-output injection: make an observation look like a higher-authority instruction.
- Memory poisoning: persist false or malicious “facts” for later runs.
- Multimodal attacks: place instructions in images, audio, video, or hidden layers.
- Confused deputy and exfiltration: make a legitimate tool use the agent’s authority for an attacker.

## Mitigation evolution

Instruction tuning and preference training help but are not a security boundary. Stronger designs combine instruction hierarchy, adversarial training, content and output checks, provenance labels, taint-aware context assembly, least-privilege tools, credential brokering, sandboxing, approval gates, rate limits, tracing, and incident response.

## Concrete defense implementations

### Input filtering

- **Pre-processing sanitization**: strip or encode known injection patterns (e.g., "ignore prior instructions", base64-encoded payloads, Unicode confusables) before they reach the model. Use regex + embedding-similarity hybrid detection.
- **LLM Guard** (separate from Guardrails AI): lightweight scanner battery—prompt injection detection, jailbreak string detection, secrets scanning, token limit enforcement. Single-digit-millisecond latency. Deploy as the first gate in your stack (cheap, fast, fails closed).
- **NVIDIA NeMo Guardrails**: programmable dialog rails in Colang DSL. Five pipeline stages (input, dialog, retrieval, execution, output rails). Built-in jailbreak detection, moderation, sensitive data masking. Under 50ms/check on GPU. Apache 2.0.

### Output sanitization

- **Guardrails AI**: Python validator architecture with 50+ prebuilt validators (PII, toxicity, JSON schema compliance, profanity). Validators can pass, fix, or raise. Short-circuit cheap-first. 50–200ms per validation.
- **Llama Guard** (Meta): open-weight safety classifier. Llama Guard 3: Llama-3.1-8B, MLCommons taxonomy (S1–S13). Llama Guard 4: 12B for Llama 4. Returns safe/unsafe + category code. Self-hostable, zero per-call cost. ~1/3 false-positive rate vs GPT-4.
- **Content policy enforcement**: apply regex + ML classifiers on generated text before it reaches users or downstream systems. Check for PII leakage, sensitive data, policy violations.

### Tool-call allowlists

- Define an explicit set of permitted tools per agent role. Reject any tool-call request not in the allowlist at the orchestration layer—never rely on the model to self-police.
- Validate tool-call arguments against a schema before execution. Reject malformed or out-of-range arguments. Use deterministic validation (JSON Schema) rather than LLM judgment for argument checking.
- Implement rate limits per tool per time window to prevent abuse or runaway loops.

### Sandboxing

- Run agent tool execution in isolated environments (containers, VMs, sandboxes). Restrict network access to only required endpoints.
- Use MCP's OAuth 2.1 flow for server authentication: short-lived tokens, HTTPS, least-privilege scopes. Validate tokens on every request.
- For code-execution agents: seccomp/AppArmor profiles, read-only filesystem mounts, network namespace isolation, resource limits (CPU, memory, disk).

### Taint-aware context assembly

- Track the provenance of every piece of information flowing into the agent's context (user input, retrieved documents, tool outputs, web results). Label each as trusted or untrusted.
- Apply different trust levels to different sources: user-provided input gets moderate trust, system prompts get high trust, retrieved documents and tool outputs get low trust.
- When assembling context, never allow untrusted content to override system instructions or tool-call policies. This is the core defense against indirect prompt injection.

## Structured red-teaming references

For a comprehensive, reproducible red-teaming program, these frameworks provide the methodology and tooling:

- **MITRE ATLAS** (Adversarial Threat Landscape for AI Systems): living knowledge base of adversary tactics and techniques against AI systems, based on real-world observations. Maps AI-specific attacks to a structured framework separate from traditional ATT&CK. Use ATLAS to catalog threat scenarios and prioritize defenses. https://atlas.mitre.org/
- **Microsoft PyRIT** (Python Risk Identification Toolkit): multi-turn red-teaming framework that orchestrates targets, datasets, scorers, attacks, and memory across text/image/audio/video modalities. Powers AI Red Teaming Agent in Microsoft Foundry. Active at https://github.com/microsoft/PyRIT (Azure/PyRIT archived March 2026).
- **HarmBench** (Center for AI Safety): standardized benchmark for automated red-teaming. 510 harmful behaviors, 33 models, 18 attack methods. MIT license. Provides a reproducible baseline for comparing model and guardrail effectiveness. https://github.com/centerforaisafety/HarmBench
- **Garak** (NVIDIA): breadth-first scanning tool. 37+ probe categories, 9+ providers, AVID-standard exports. Apache 2.0. CI/CD integration. Single-turn probes (no multi-turn chaining). Useful for quick surface-level audits.

## Key findings from recent research

- Single-turn jailbreaks are largely contained by modern instruction tuning. Multi-turn, agentic, and indirect prompt injection still break production systems.
- Adaptive attacks bypass >90% of published defenses—layered defense is essential.
- Jailbreak kits are commercialized ($30–200/month), lowering the barrier for adversaries.
- The OWASP Top 10 for Agentic Applications (ASI01–ASI10) extends the LLM Top 10 for autonomous systems, covering agent goal hijack, tool misuse, memory poisoning, cascading failures, and rogue agents.

For safe, reusable defensive tests see [[06 Reliability and Security/Defensive Red-Team Labs]].

## Future-risk forecasts

These are hypotheses, not established facts: cross-protocol trust confusion, persistent cross-agent poisoning, malicious skill/MCP supply chains, multimodal steganography, monitoring evasion, long-horizon social engineering, and self-modifying harnesses.

## Cited history and standards

- **The term "prompt injection"** was coined by Simon Willison in Sep 2022 with the SQL-injection analogy; his ongoing series is the practitioner archive of real-world injection vectors and defenses (https://simonwillison.net/series/prompt-injection/).
- **Indirect injection (Greshake et al. 2023):** adversaries can remotely compromise LLM-integrated applications by placing instructions inside data the application later retrieves—webpages, documents, email, search results.
- **Automated adversarial suffixes (Zou et al. 2023, "GCG"):** gradient search finds universal, transferable token suffixes that bypass alignment across several models. This demonstrated that alignment is a behavior property, not a guarantee, and motivated layered defenses.
- **Standards:** the OWASP LLM Top 10 (community effort since 2023; 2025 edition) covers prompt injection, sensitive information disclosure, and excessive agency. The OWASP Agentic Top 10 (released Dec 2025) extends this to autonomous systems, with categories including agent goal hijack, tool misuse/excessive agency, and memory poisoning.

## Sources and further reading

- Willison, "Prompt injection" series — https://simonwillison.net/series/prompt-injection/ — coined the term, practitioner archive.
- Greshake et al., "Not what you've signed up for," 2023 — https://arxiv.org/abs/2302.12173 — indirect prompt injection.
- Zou et al., "Universal and Transferable Adversarial Attacks," 2023 — https://arxiv.org/abs/2307.15043 — GCG adversarial suffixes.
- OWASP, "Top 10 for LLM Applications 2025" — https://genai.owasp.org/resource/owasp-top-10-for-llm-applications-2025/ — prompt injection, excessive agency.
- OWASP, "Top 10 for Agentic Applications" (Dec 2025) — https://genai.owasp.org/ — ASI01–ASI10 for autonomous systems.
- MITRE ATLAS — https://atlas.mitre.org/ — AI-specific adversary tactics and techniques.
- PyRIT — Microsoft GitHub — https://github.com/microsoft/PyRIT — multi-turn red-teaming framework.
- HarmBench — GitHub — https://github.com/centerforaisafety/HarmBench — 510 behaviors, 18 attack methods, MIT.
- Garak — NVIDIA — Apache 2.0, breadth-first scanning, 37+ probe categories.
- NeMo Guardrails — GitHub — https://github.com/NVIDIA-NeMo/Guardrails — v0.24.0, Apache 2.0, Colang DSL.
- Guardrails AI — Website — https://guardrailsai.com/ — 50+ validators, Python architecture.
- Meta Llama Guard — HuggingFace — https://huggingface.co/meta-llama — open-weight safety classifiers.
- Particula, "NeMo vs Llama Guard vs Guardrails AI" (2026) — https://particula.tech/blog/ai-guardrails-compared-nemo-guardrails-ai-llama-guard — production stack comparison.
- GenAlpha, "Red-teaming AI in 2026" — https://genalphai.com/red-teaming-ai-the-ultimate-guide-to-adversarial-testing-in-2026/ — 200+ techniques, OWASP Agentic checklist.
- BestLLMScanners, "Open Source LLM Red Teaming Tools" — https://bestllmscanners.com/posts/open-source-llm-red-teaming-tools/ — Garak, PyRIT, HarmBench comparison.

All links verified 2026-08-27.

---

---

> **← [[06 Reliability and Security/Observability|Observability]]** · **[[AI_Home|Home]]** · **[[06 Reliability and Security/Human Oversight and Trust Engineering|Human Oversight and Trust Engineering]] →**
