---
type: hub
layer: landscape
status: current-snapshot
maturity: emerging
aliases: [Agent Tool Landscape]
tags: [ai-engineering, tools, agents, landscape]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "07 Operations and Economics/Deployment and AgentOps.md"
next: "08 Tool Landscape/Coding Agent Profiles.md"
summary: "These products are not all competitors. They occupy different layers: model, coding harness, general agent, terminal runtime, orchestration platform, protocol, sandbox..."
---


# Agent tool landscape — 2026 snapshot

## Plain-English introduction

The AI agent world is crowded with tools that each claim to solve a piece of the puzzle. This section is your map of that landscape. It organizes the major products and projects by what they actually do — which layer of the stack they sit on, what problem they solve, and where they overlap — so you can make sense of the options without getting lost in marketing hype.

These products are not all competitors. They occupy different layers: model, coding harness, general agent, terminal runtime, orchestration platform, protocol, sandbox, gateway, or observability system.

## Layer summary

> Snapshot date: 2026-08-27

| Layer | What it does | Examples |
|---|---|---|
| **Model** | Foundation model providers — the intelligence layer | OpenAI, Anthropic, Google, Meta (Llama), DeepSeek |
| **Coding harness** | IDE-integrated or terminal-first tools that wrap a model with code-editing tools | Cursor, Claude Code, Codex, OpenCode, Pi, Kimi Code |
| **General agent** | Persistent personal assistants with broad tool access and memory | Hermes, DeepSeek Harness |
| **Terminal runtime** | Multi-process coordination and persistence for CLI agents | Herdr, OpenHands |
| **Orchestration framework** | Compose model calls, tools, and state into workflows | LangGraph, CrewAI, AutoGen/AG2, Semantic Kernel, Haystack, PydanticAI |
| **Protocol** | Interoperability standards for agent-to-agent and tool communication | MCP, A2A, ACP |
| **Sandbox** | Isolated execution environments for untrusted agent code | E2B, Daytona, Modal, Docker, microVMs |
| **Gateway** | Multi-provider routing, budgets, fallbacks, and observability | LiteLLM, OpenRouter |
| **Observability** | Tracing, cost tracking, evals, and production monitoring | LangSmith, Langfuse, Braintrust, Arize Phoenix, OpenTelemetry GenAI |

## Profiles

- [[08 Tool Landscape/Coding Agent Profiles]] — Herdr, Hermes, DeepSeek Harness, Codex, Cursor, Claude Code, OpenCode, Pi, OpenHands, and Kimi Code.
- [[08 Tool Landscape/Agent Runtimes and Frameworks]] — LangGraph, CrewAI, AutoGen/AG2, Semantic Kernel, Haystack, PydanticAI, Mastra, Temporal, and control-plane choices.
- [[08 Tool Landscape/Infrastructure and Observability Tools]] — sandboxes, browsers, gateways, guardrails, prompt management, and telemetry.
- [[11 Glossary and Sources/Tool Comparison Index]] — selection aid by layer and job.

## How to read a profile

Ask: Which layer does it occupy? What boundary does it standardize? What does it make easier? What new authority, cost, lock-in, or operational burden does it introduce? Re-check current documentation before adoption.

---

---

> **← [[07 Operations and Economics/Deployment and AgentOps|Deployment and AgentOps]]** · **[[AI_Home|Home]]** · **[[08 Tool Landscape/Coding Agent Profiles|Coding Agent Profiles]] →**
