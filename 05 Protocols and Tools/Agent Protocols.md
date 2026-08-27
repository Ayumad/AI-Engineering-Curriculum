---
type: protocol
layer: protocols
status: current-snapshot
maturity: emerging
aliases: [ACP A2A AG-UI A2UI]
tags: [ai-engineering, protocols, interoperability]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "05 Protocols and Tools/MCP.md"
next: "06 Reliability and Security/Reliability Evals and Observability.md"
summary: "| Protocol | Boundary | Typical concern | |||| | ACP | Editor/client ↔ agent | Sessions, updates, permissions, cancellation | | A2A | Agent ↔ agent | Discovery, messag..."
---


# Agent protocol family

## Plain-English introduction

As AI systems grow more complex, they need to talk to many different things: user interfaces, coding editors, other agents, and external tools. No single communication standard handles all of those connections well, so the industry has developed a family of specialized protocols — each designed for a particular boundary. One handles how an editor talks to an agent; another handles how agents talk to each other; a third streams live updates to a user's screen. Think of them like the different types of plugs and cables inside a computer — USB for peripherals, HDMI for displays, Ethernet for networking. Each serves a specific purpose, and understanding which protocol fits which boundary is the key to building systems that are secure and composable.

| Protocol | Boundary | Typical concern |
|---|---|---|
| ACP | Editor/client ↔ agent | Sessions, updates, permissions, cancellation |
| A2A | Agent ↔ agent | Discovery, messages, tasks, async collaboration |
| AG-UI | Agent ↔ application/user interface | Streaming state and interactive UI events |
| A2UI | Agent → generated UI | Declarative, model-driven interface surfaces |
| MCP | Agent application ↔ tools/data | Capabilities and context |

Adjacent standards may also cover commerce, identity, payments, and agent authorization. Treat these as separate trust and liability boundaries: an agent-to-agent message is not proof of identity, authority, consent, payment settlement, or user intent.

Protocols compose, but every boundary adds identity, trust, schema, and replay questions. A2A should not be treated as permission to act; ACP should not imply a tool is safe; MCP discovery should not silently grant credentials.

## Per-protocol internals

### ACP (Agent Client Protocol)

- **What it does**: Defines how an IDE, editor, or client application communicates with an agent. Manages sessions, sends user messages, receives agent responses, and handles cancellation.
- **Transport**: WebSocket or HTTP. Session-scoped — the client opens a session, sends messages within it, and closes when done.
- **When to use**: IDE integrations (VS Code, JetBrains), CLI tools, any scenario where a human-facing application needs to talk to an agent.
- **v2 overview**: Supports bidirectional streaming, structured updates (tool calls, progress), and permission requests (agent asks for access to files, APIs). The client controls what the agent can do.
- **Key design**: The agent is a service; the client is the authority. The agent cannot act without client approval on sensitive operations.

### A2A (Agent-to-Agent)

- **What it does**: Enables discovery, messaging, and task delegation between agents. Agents advertise capabilities via "agent cards" (JSON metadata). One agent can send a task to another, stream partial results, and receive a final response.
- **Transport**: HTTP/HTTPS with SSE for streaming. Discovery via well-known URLs (`/.well-known/agent.json`).
- **When to use**: Multi-agent systems where agents are owned by different parties or run in different environments. Cross-organization agent collaboration.
- **Backed by**: Linux Foundation, Apache 2.0 license. Google, Salesforce, SAP, and others are contributors.
- **Key design**: Asynchronous tasks with status updates (submitted, working, input-required, completed, failed). Supports push notifications for long-running tasks.

### AG-UI (Agent-User Interface)

- **What it does**: Streams agent state (text, tool calls, status) to a user interface in real-time. Enables interactive, conversational UIs where the user sees the agent working.
- **Transport**: WebSocket or SSE. Bidirectional — user can interrupt, provide input, or modify the agent's plan mid-stream.
- **When to use**: Chat-based interfaces, co-pilot experiences, any scenario where the user needs to see and interact with the agent's reasoning process.

### A2UI (Agent → Generated UI)

- **What it does**: Agent generates a declarative UI description (forms, dashboards, data visualizations) that the application renders. The agent decides *what* to show; the application handles *how* to render it.
- **Transport**: JSON or structured markup embedded in agent responses.
- **When to use**: Dynamic dashboards, data exploration tools, scenarios where the agent needs to present complex data in interactive formats without hard-coding UI components.

## Security considerations

- **ACP**: Client controls permissions. Encrypt transport (WSS/HTTPS). Validate session tokens. Enforce cancellation server-side — don't trust the client to stop sending.
- **A2A**: Agent identity is a claim, not proof. Verify via signed agent cards and mutual TLS. Apply rate limits per-agent. Don't assume an agent card's claimed capabilities are accurate — verify at runtime.
- **AG-UI**: Streaming state can leak sensitive data (tool outputs, intermediate reasoning). Enforce auth on connection establishment, not per-message. Sanitize state before rendering.
- **A2UI**: Generated UI descriptions can contain injection attacks (XSS via dynamic forms). Validate and sanitize all agent-generated UI schemas before rendering. Render in sandboxed iframes where possible.
- **Cross-protocol**: When composing protocols (e.g., ACP + MCP), each boundary has its own trust model. Don't assume auth from one protocol covers the other.

## Interop and composition

Protocols are complementary, not competing:

| Composition | Pattern | Trust model |
|---|---|---|
| MCP + A2A | Agent uses MCP for tools, A2A for peer coordination | MCP: tool trust; A2A: agent trust |
| ACP + MCP | IDE talks to agent via ACP; agent uses MCP for tools | ACP: user-facing session; MCP: tool-facing permissions |
| AG-UI + A2UI | App streams agent state via AG-UI; agent generates UI via A2UI | AG-UI: connection auth; A2UI: content sanitization |

Every composition boundary adds latency, failure modes, and trust questions. Document the trust model at each crossing. Minimize unnecessary boundaries.

## Sources and further reading

- ACP overview (v2) — https://github.com/agentclientprotocol/agentclientprotocol/blob/main/docs/protocol/v2/overview.mdx — agent-client protocol for IDE integration.
- A2A protocol — https://a2a-protocol.org/ — agent-to-agent protocol, discovery, messaging, backed by Linux Foundation.
- AgentProtocol.ai — https://agentprotocol.ai/ — earlier agent protocol standard.
- MCP specification — https://modelcontextprotocol.io/specification/2025-06-18/server/index — see [[05 Protocols and Tools/MCP]] for detailed MCP internals (transports, OAuth, error handling).
- MCP Transports — https://modelcontextprotocol.io/specification/2025-06-18/basic/transports — stdio and Streamable HTTP.

All links verified 2026-08-27.

---

---

> **← [[05 Protocols and Tools/MCP|MCP]]** · **[[AI_Home|Home]]** · **[[06 Reliability and Security/Reliability Evals and Observability|Reliability Evals and Observability]] →**
