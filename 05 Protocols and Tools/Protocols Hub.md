---
type: hub
layer: protocols
status: current-snapshot
maturity: emerging
aliases: [Agent Protocols Hub]
tags: [ai-engineering, protocols, mcp, a2a, acp]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "04 Workflows and Orchestration/Agent Democracies.md"
next: "05 Protocols and Tools/MCP.md"
summary: "Protocols standardize boundaries; they do not create intelligence. The useful map is:"
---

# Protocols and tools

```text
Human ↔ application       AG-UI
Editor ↔ agent             ACP
Agent ↔ tools and data     MCP
Agent ↔ agent              A2A
Agent → generated UI       A2UI
```

Protocols compose, but every boundary adds identity, trust, schema, and replay questions. An agent-to-agent message is not proof of identity, authority, consent, or user intent. MCP discovery should not silently grant credentials; ACP should not imply a tool is safe; A2A should not be treated as permission to act.

See [Protocols canvas](10 Maps/Protocols.canvas) for a visual overview of the protocol landscape.

## Protocol security digest

| Protocol | Authentication | Key security concerns |
|---|---|---|
| MCP | OAuth 2.1 (2025-06-18 spec): 401 → PRM discovery → auth server discovery → client registration → user auth → authenticated requests | Short-lived tokens, validate always, HTTPS, least-privilege scopes. Server descriptions and tool output are untrusted input. |
| A2A | Agent cards with identity claims; OAuth/Bearer tokens | Agent identity is a claim, not proof. Verify via signed agent cards. |
| ACP | Session-level auth; IDE-managed credentials | Session hijacking if transport is not encrypted. Cancel semantics must be enforced server-side. |
| AG-UI | Application-level auth (WebSocket/HTTP) | Streaming state can leak sensitive data; enforce auth on connection establishment. |

**Rate limits**: Apply per-client and per-tool rate limits at the protocol boundary. MCP servers should enforce tool-level budgets; A2A servers should enforce per-agent quotas. Circuit breakers protect against cascading failures across protocol boundaries.

## Interop and composition

Protocols compose when an agent acts as both client and server across boundaries. Common compositions:
- **MCP + A2A**: An agent uses MCP to access tools, then coordinates with peer agents via A2A. The MCP boundary is tool trust; the A2A boundary is agent trust — different security models.
- **ACP + MCP**: An IDE uses ACP to talk to an agent, which in turn uses MCP to access external tools. ACP controls user-facing session; MCP controls tool-facing permissions.
- **AG-UI + A2UI**: An application uses AG-UI for streaming state to the user, while the agent generates UI via A2UI for complex data visualization.

Every additional protocol boundary adds latency (serialization/deserialization), failure modes (timeout cascading), and trust questions (who authorized this cross-boundary call?). Minimize boundaries where possible; document the trust model at each crossing.

## Sources and further reading

- MCP specification — https://modelcontextprotocol.io/specification/2025-06-18/server/index — agent-to-tool protocol, capabilities, security.
- MCP Transports — https://modelcontextprotocol.io/specification/2025-06-18/basic/transports — stdio and Streamable HTTP.
- A2A protocol — https://a2a-protocol.org/ — agent-to-agent protocol, discovery, messaging.
- ACP overview — https://github.com/agentclientprotocol/agentclientprotocol/blob/main/docs/protocol/v2/overview.mdx — agent-client protocol for IDE integration.
- OWASP Top 10 for LLM Applications 2025 — https://genai.owasp.org/resource/owasp-top-10-for-llm-applications-2025/ — security framework for AI systems.

All links verified 2026-08-27.

---

> **← [[04 Workflows and Orchestration/Agent Democracies|Agent Democracies]]** · **[[AI_Home|Home]]** · **[[05 Protocols and Tools/MCP|MCP]] →**
