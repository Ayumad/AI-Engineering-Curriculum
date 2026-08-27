---
type: concept
layer: agents
status: evergreen
maturity: emerging
aliases: [Voice Agents, Audio Agents]
tags: [ai-engineering, voice, audio, realtime, multimodal]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "02 Agents and Harnesses/Vision and Multimodal Input Engineering.md"
next: "02 Agents and Harnesses/Computer-Use and Browser Agents.md"
summary: "Voice agents are real-time systems: capture, turn detection, transcription or audio reasoning, response generation, synthesis, interruption handling, and safe actions must work as one loop."
---


# Voice and audio agents

Voice is not simply text chat with speech-to-text and text-to-speech attached. The interface needs low perceived latency, barge-in handling, turn detection, noise tolerance, and clear confirmation before actions that would be consequential in text as well.

## Reference architecture

```text
microphone → VAD/turn detection → transcription or audio model → agent/harness
                                                            ↓
speaker ← synthesis/streaming ← response and action state ←┘
```

Voice activity detection (VAD) determines when someone starts or stops speaking; poor turn detection makes even a capable model feel rude or slow. Stream partial recognition and response where useful, but distinguish provisional text from a final committed action.

## Design decisions

- Use transcription-first when text tools, auditing, and deterministic routing dominate.
- Use native audio reasoning when prosody, interruption, or nonverbal audio is central and the runtime supports it.
- Keep a transcript, timestamps, confidence/uncertainty, and action state for review.
- Let the user interrupt playback; cancel downstream work safely.
- Confirm names, numbers, destinations, and irreversible intent rather than guessing from noisy audio.

Measure time to first audible response, end-to-end turn latency, interruption success, transcription/action accuracy, and escalation rate. For calls or assistants, disclose recording and retention policy and protect audio as sensitive input.

## Latency budget

Conversational voice agents require an end-to-end latency budget of ~300–800ms for responsive interaction. This budget spans the entire pipeline:

- **VAD + turn detection:** <50ms (Silero VAD processes a 30ms audio chunk in <1ms on a single CPU thread).
- **Transcription or audio reasoning:** 100–300ms depending on provider and model.
- **LLM inference:** 100–300ms for short responses.
- **TTS synthesis:** 75–150ms for first audible token (ElevenLabs Flash: 75ms).
- **Network round-trips:** 20–50ms per hop.

If any stage exceeds its budget, the conversation feels sluggish. Synchronous tool calls are the most common bottleneck—OpenAI recommends a spoken preamble ("I'll check that now") to cover the latency while the tool executes.

## Provider landscape

**OpenAI Realtime API** maintains a persistent WebSocket or WebRTC session. Tools are declared in `session.update`; the model outputs `function_call` on `response.done`; the client must reply with `function_call_output` + `response.create`. Forgetting `response.create` after a tool call is the most common bug—the model freezes silently. VAD modes include `server_vad` (silence-based) and `semantic_vad` (with an `eagerness` parameter for interruptibility). Cost is token-based, rising with prompt/context accumulation.

**Deepgram** offers connection-time pricing ($0.05/min on Growth tier), predictable regardless of prompt size. The Flux model is purpose-built for real-time turn-taking and native interruption handling. Unlike token-based pricing, Deepgram's cost is stable across long conversations with large system prompts.

**ElevenLabs Flash TTS** achieves 75ms latency at 50% lower cost than the flagship model, optimized specifically for voice agents. WebSocket streaming provides word-level timestamps and interruption handling, enabling the agent to cut off synthesis mid-word when the user barge-ins.

**Whisper** (OpenAI) is an encoder-decoder Transformer trained on 680K hours of weakly-supervised speech. It processes 30-second chunks via log-Mel spectrogram and achieves 50% fewer errors than specialized models in zero-shot settings. However, Whisper was not designed for streaming—chunking workarounds introduce latency and boundary artifacts. Dictation targets <3s latency; conversational targets <1s.

## Voice activity detection

VAD determines when someone starts or stops speaking; poor turn detection makes even a capable model feel rude or slow.

**Silero VAD** is the dominant open-source option: <1ms processing per 30ms audio chunk on a single CPU thread, ~2MB JIT model, supports 6000+ languages, works at 8kHz and 16kHz sampling rates, MIT licensed with zero telemetry. It is lightweight enough to run on-device alongside the main agent.

OpenAI's Realtime API offers two built-in VAD modes: `server_vad` (silence-duration threshold) and `semantic_vad` (model-based, with an `eagerness` parameter controlling how quickly the model considers a pause as a turn boundary).

## Barge-in and interruption

Users will talk over the agent. Handling this gracefully requires:

- **OpenAI:** `response.cancel` + `conversation.item.truncate` to stop in-flight synthesis and discard the truncated audio.
- **Deepgram Flux:** handles interruption natively—the model detects the user's voice and stops generating.
- **Amazon Nova Sonic:** supports async tool calling, allowing the agent to keep talking while a slow retrieval resolves in the background, then seamlessly incorporate the result.

Without barge-in handling, the agent continues speaking after the user has already moved on, creating a frustrating experience. Stream partial recognition and response where useful, but distinguish provisional text from a final committed action.

## Sources and further reading

- Silero VAD GitHub, 2024 — https://github.com/snakers4/silero-vad — VAD model, benchmarks, MIT license.
- Deepgram, "Deepgram vs OpenAI Realtime API," Jul 2026 — https://deepgram.com/learn/deepgram-voice-agent-api-vs-openai-realtime-api — pricing comparison, Flux model, interruption handling.
- Zylos, "Mid-Conversation Tool Calling in Realtime Voice Agents," Jul 2026 — https://zylos.ai/research/2026-07-18-realtime-voice-agent-tool-calling/ — tool calling patterns, response.create bug, barge-in.
- OpenAI, "Introducing Whisper," Sep 2022 — https://openai.com/index/whisper/ — architecture, training data, streaming limitations.

All links verified 2026-08-27.

[[02 Agents and Harnesses/Vision and Multimodal Input Engineering]] · [[07 Operations and Economics/Latency and Cost Engineering]] · [[06 Reliability and Security/Human Oversight and Trust Engineering]]

---

---

> **← [[02 Agents and Harnesses/Vision and Multimodal Input Engineering|Vision and Multimodal Input Engineering]]** · **[[AI_Home|Home]]** · **[[02 Agents and Harnesses/Computer-Use and Browser Agents|Computer-Use and Browser Agents]] →**
