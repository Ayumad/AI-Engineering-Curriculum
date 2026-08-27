---
type: index
layer: landscape
status: current-snapshot
maturity: emerging
aliases: [Coding and General Agent Profiles]
tags: [ai-engineering, tools, coding-agents]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "08 Tool Landscape/Tool Landscape Hub.md"
next: "08 Tool Landscape/Agent Runtimes and Frameworks.md"
summary: "Herdr keeps existing terminal agents running across disconnects and adds workspaces, panes, agentstate detection, SSH access, and a socket/API surface for spawning, in..."
---


# Coding and general-agent profiles

## Plain-English introduction

AI coding assistants have gone from simple autocomplete to full-fledged teammates that can plan, write, test, and refactor code with surprisingly little guidance. But not all coding agents are built the same. Some live in your terminal, some run in the cloud, and some are designed to hand off work to other agents. This note surveys the major players in the space, comparing how they work, what they are good at, and what you should watch out for. It is less a buyer's guide and more a field guide to the agents you are most likely to encounter.

> **Snapshot date:** 2026-08-27. Pricing and feature claims marked "check vendor" lack verified figures from research-D — verify with official documentation before adoption.

## Herdr — persistent agent runtime and control plane

Herdr keeps existing terminal agents running across disconnects and adds workspaces, panes, agent-state detection, SSH access, and a socket/API surface for spawning, inspecting, prompting, waiting, and collecting agent output. Its value is coordination and persistence, not a smarter underlying model. Best for many CLI agents, remote machines, and a supervising agent that needs to manage other processes. Tradeoffs are another runtime layer, security of the control API, and the need to understand which process state is being detected. [Official site](https://herdr.dev/) · [repository](https://github.com/herdrdev/herdr).

## Hermes — persistent personal/general agent

Nous Research’s Hermes spans CLI, desktop, TUI, gateway, messaging, browser, terminal, filesystem, memory, skills, subagents, scheduling, MCP, voice, and media capabilities. Its distinctive idea is an always-available agent that accumulates operational knowledge: memory stores facts and skills store procedures. Best for self-hosted personal assistants, homelabs, messaging-driven automation, and experimentation. Tradeoffs include broad permission surface, setup/maintenance, and the need to audit skills and integrations. [Official documentation](https://hermes-agent.nousresearch.com/docs/) · [repository](https://github.com/NousResearch/hermes-agent).

## DeepSeek Harness — everything as a plugin

DeepSeek Harness is a developer preview built on Cordis. Model, tools, skills, sessions, sandbox, storage, loop, scheduler, and UI are composable plugins. Its standard, code, minimal, and creator modes and append-only trajectories make it valuable for studying and modifying harness architecture. Best for research and experimentation; simpler coding tasks may be easier in a mature product. Treat preview stability and security posture as open questions. [Official developer preview](https://www.deepseek.com/harness/) · [repository](https://github.com/deepseek-ai/deepseek-harness).

## Codex — delegation-first agent command center

Codex spans CLI, app, IDE surfaces, skills, automations, sandboxing, cloud execution, projects, and parallel agents. The app emphasizes assigning workstreams, isolating changes in worktrees, and reviewing results. Best when an agent should own an end-to-end work package or several agents should run in parallel. Tradeoffs include provider coupling, review responsibility, and cost/permission management. [Codex](https://openai.com/codex/) · [Codex app announcement](https://openai.com/index/introducing-the-codex-app/).

## Cursor — AI-native IDE and inner loop

Cursor tightly couples human, editor, codebase, and agent. Its Ask, Plan, Agent, and Debug modes separate understanding, planning, execution, and diagnosis; it also supports MCP, skills, rules, subagents, and cloud agents. Best for interactive codebase exploration and rapid human-agent iteration. Tradeoffs include editor-centric workflow, provider/product coupling, and the need to review broad edits. [Modes](https://docs.cursor.com/en/agent/modes) · [documentation](https://cursor.com/docs).

## Claude Code — model/harness co-design

Claude Code is a terminal-first coding harness with context files, shell and file tools, MCP, planning, subagents, hooks, background tasks, checkpoints, permissions, and an Agent SDK. It is especially useful to study for context management and long-running harness design. Best for terminal-centric engineering and custom agents built on a mature harness. Tradeoffs include model/provider coupling and a large permission surface. [Documentation](https://docs.anthropic.com/en/docs/claude-code/overview) · [Agent SDK](https://docs.anthropic.com/en/docs/agents-and-tools/agent-sdk/overview).

## OpenCode — open, provider-agnostic terminal agent

OpenCode is an open-source, terminal-first coding agent designed to work across providers and local models, with parallel sessions and developer tooling. Its educational value is separating harness quality from model choice. Best for experimentation, local models, and avoiding one-provider lock-in. Tradeoffs are self-hosting, configuration, and uneven provider behavior. [Documentation](https://opencode.ai/docs) · [repository](https://github.com/sst/opencode).

## Pi — minimal, extensible harness

Pi keeps the core loop small and extends it through TypeScript extensions, skills, templates, and packages. It is useful for learning the path from prompt to model to tools without a large platform obscuring the mechanics. Best for builders who want a small programmable base. Tradeoffs are fewer batteries-included features and more assembly work. [Documentation](https://pi.dev/docs/latest).

## OpenHands — agent SDK and cloud/control layer

OpenHands provides a Software Agent SDK, CLI, and Cloud surfaces for agents that work with code, tools, and sandboxes. It is a useful layer above individual coding sessions when remote execution, automation, or custom developer experiences matter. Best for platform builders and autonomous software agents. Tradeoffs include platform complexity and operational ownership. [Overview](https://docs.openhands.dev/overview/introduction) · [SDK](https://docs.openhands.dev/sdk/index).

## Kimi Code — terminal/IDE agent with ACP and MCP

Kimi Code offers CLI, web, ACP, MCP, provider configuration, session export, and multimodal context such as video input. Its distinctive angle is moving between terminal and editor while exposing interoperability boundaries. Best for multimodal coding workflows and protocol experimentation. Tradeoffs include evolving compatibility and provider/service dependence. [CLI reference](https://www.kimi.com/code/docs/en/kimi-code-cli/reference/kimi-command) · [ACP/IDE integration](https://www.kimi.com/en/help/kimi-code/cli-ides).

## Category map

| Need | Candidate layer |
|---|---|
| Interactive IDE loop | Cursor, Claude Code integrations, Kimi Code |
| Delegated parallel work | Codex, OpenHands, Herdr |
| General persistent assistant | Hermes |
| Harness research/customization | DeepSeek Harness, Pi, OpenCode |
| Provider/model portability | OpenCode, OpenHands, gateways |

## Context window comparison

Context window size affects how much code, conversation history, and retrieved context an agent can hold. Larger windows reduce the need for truncation but increase cost and may degrade attention quality on long contexts.

| Tool | Context window | Notes |
|---|---|---|
| Claude Code | 200K tokens (Opus/Sonnet) | Extended thinking available; strong at long-context code comprehension |
| Cursor | Model-dependent (up to 200K via Claude) | Inherits provider context limits |
| Codex | Model-dependent (up to 200K via GPT-4o) | Inherits provider context limits |
| Hermes | Model-dependent (configurable) | Supports any provider's context window |
| OpenCode | Model-dependent (provider-agnostic) | Inherits provider context limits |
| DeepSeek Harness | 128K (DeepSeek models) | DeepSeek's native context |
| Pi | Model-dependent | Small harness, large context via provider |
| OpenHands | Model-dependent | Inherits provider context limits |
| Kimi Code | 128K (Kimi models) | Multimodal context including video |

**Key insight:** Context window size is a provider property, not a harness property. Harness quality is determined by how well it manages, compresses, and prioritizes context within the available window — not by the raw token count.

## Pricing and maturity

> Most coding agents are in rapid development. Verified prices from research-D are limited; where research-D lacks confirmed pricing, the column is marked "check vendor."

| Tool | Pricing model | Maturity |
|---|---|---|
| Herdr | Check vendor | Early — persistent runtime, niche |
| Hermes | Open-source (self-hosted) | Active development, broad feature set |
| DeepSeek Harness | Developer preview | Preview — treat stability/security as open questions |
| Codex | Subscription (check vendor) | GA — backed by OpenAI |
| Cursor | Subscription (check vendor) | GA — widely adopted IDE |
| Claude Code | API usage-based (Anthropic pricing) | GA — production-ready |
| OpenCode | Open-source (self-hosted) | Active development |
| Pi | Open-source | Active development, small core |
| OpenHands | Open-source | Active development, platform focus |
| Kimi Code | Check vendor | Active development, protocol focus |

## Sources and further reading

- Herdr — https://herdr.dev/ — persistent agent runtime.
- Hermes docs — https://hermes-agent.nousresearch.com/docs/ — personal/general agent platform.
- DeepSeek Harness — https://www.deepseek.com/harness/ — composable developer preview.
- Codex — https://openai.com/codex/ — delegation-first agent command center.
- Cursor modes — https://docs.cursor.com/en/agent/modes — AI-native IDE.
- Claude Code docs — https://docs.anthropic.com/en/docs/claude-code/overview — terminal-first coding harness.
- Claude Agent SDK — https://docs.anthropic.com/en/docs/agents-and-tools/agent-sdk/overview — agent SDK.
- OpenCode docs — https://opencode.ai/docs — provider-agnostic terminal agent.
- Pi docs — https://pi.dev/docs/latest — minimal extensible harness.
- OpenHands docs — https://docs.openhands.dev/overview/introduction — agent SDK and cloud.
- Kimi Code CLI — https://www.kimi.com/code/docs/en/kimi-code-cli/reference/kimi-command — terminal/IDE agent.
- Kimi ACP/IDE integration — https://www.kimi.com/en/help/kimi-code/cli-ides — protocol integration.

All links verified 2026-08-27.

---

---

> **← [[08 Tool Landscape/Tool Landscape Hub|Tool Landscape Hub]]** · **[[AI_Home|Home]]** · **[[08 Tool Landscape/Agent Runtimes and Frameworks|Agent Runtimes and Frameworks]] →**
