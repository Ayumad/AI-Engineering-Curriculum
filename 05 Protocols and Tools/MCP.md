---
type: protocol
layer: protocols
status: current-snapshot
maturity: emerging
aliases: [Model Context Protocol]
tags: [ai-engineering, mcp, tools, protocols]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "05 Protocols and Tools/Protocols Hub.md"
next: "05 Protocols and Tools/Agent Protocols.md"
summary: "MCP is an open protocol for connecting an AI application to external data sources and tools. Its primitives are:"
---


# Model Context Protocol (MCP)

MCP is an open protocol for connecting an AI application to external data sources and tools. Its primitives are:

- **Tools:** model-controlled actions such as `query_database` or `create_issue`.
- **Resources:** structured context such as documents, records, or files.
- **Prompts:** reusable templates or interaction patterns.

## Architecture

```text
Host application
  └─ MCP client ↔ MCP server
                    ├─ tools
                    ├─ resources
                    └─ prompts
```

MCP is not an agent. A server may be a thin adapter around an API. The host still owns user consent, authorization, context policy, and execution boundaries.

## Transports (2025-06-18 spec)

| Transport | How it works | When to use |
|---|---|---|
| **stdio** | Server runs as a subprocess. JSON-RPC messages travel over stdin/stdout. | Local tools, CLI integrations, development. Simple, low-latency, no network config. |
| **Streamable HTTP** | HTTP POST for client→server requests. Server responds with SSE (Server-Sent Events) for streaming. Resumability via `Last-Event-ID` header. Session tracking via `Mcp-Session-Id`. | Remote tools, cloud-hosted servers, multi-client scenarios. Requires HTTPS. |

Key design points:
- **stdio** is fire-and-forget: the host spawns the server process and communicates via pipes. No authentication needed (the host controls the process).
- **Streamable HTTP** supports resumability: if a connection drops, the client replays from the last event ID. This is critical for long-running tool calls.
- Servers declare supported transports in their configuration. The client picks the best available.

## Capability negotiation

During initialization, client and server exchange capability declarations:
- **Server capabilities**: which primitives it supports (tools, resources, prompts), whether it supports sampling, and notification types.
- **Client capabilities**: whether it supports roots (file system access), sampling (server-initiated LLM calls), and notifications.
- Neither side should invoke capabilities the other hasn't declared. This prevents runtime errors and security violations.

## Versioning

The 2025-06-18 spec defines a versioned protocol (`protocolVersion` field in the initialize request). Clients must send their supported version; servers respond with their own. If versions are incompatible, the server returns an error. This prevents protocol mismatches when either side upgrades independently.

## OAuth 2.1 security (2025-06-18 spec)

The full auth flow for remote MCP servers:

1. Client sends a request → server returns `401 Unauthorized` with `WWW-Authenticate: Bearer resource_metadata="..."`.
2. Client fetches the Protected Resource Metadata (PRM) to discover the authorization server.
3. Client fetches the Auth Server's OAuth metadata (RFC 8414).
4. Client registers (or reuses) a client ID via dynamic client registration.
5. User authenticates via browser redirect or device code flow.
6. Client receives a short-lived access token.
7. All subsequent requests include `Authorization: Bearer <token>`.

**Key security rules**: Always validate tokens server-side. Use short-lived tokens (minutes, not hours). Enforce HTTPS. Apply least-privilege scopes — a tool that reads database records should not also be able to delete them. Server descriptions and tool output are untrusted input; validate and sanitize.

## Typical server design

A minimal MCP server:
1. **Declare capabilities**: tools, resources, prompts. Each tool has a name, description, and JSON Schema input.
2. **Handle `tools/list`**: return available tools with schemas.
3. **Handle `tools/call`**: validate arguments against schema, execute the underlying operation, return structured result.
4. **Handle `resources/read`**: fetch and return resource content.
5. **Handle `prompts/get`**: return a pre-built prompt template.
6. **Error handling**: return JSON-RPC error objects with `code`, `message`, and optional `data`. Use standard codes (`-32600` for invalid request, `-32601` for method not found).

## Error handling

MCP uses JSON-RPC error codes:
| Code | Meaning | When to use |
|---|---|---|
| `-32700` | Parse error | Malformed JSON-RPC message |
| `-32600` | Invalid request | Missing required fields, wrong protocol version |
| `-32601` | Method not found | Client calls a method the server doesn't implement |
| `-32602` | Invalid params | Tool arguments don't match the JSON Schema |
| `-32603` | Internal error | Server-side failure (API down, timeout) |

Servers should also implement timeouts (per-tool and global), rate limiting, and circuit breakers. Never let a tool call run indefinitely — set a maximum execution time and return a timeout error if exceeded.

## Security checklist

- Display what a tool does and what data it can access.
- Separate read and write tools; use least privilege and scoped credentials.
- Validate arguments and results; impose timeouts and budgets.
- Treat server descriptions, resources, and tool output as untrusted data.
- Pin versions, review dependencies, and maintain an allowlist of servers.
- Log actor, tool, arguments, approval, result, and redaction status.

## Sources and further reading

- Model Context Protocol specification, 2025-06-18 — https://modelcontextprotocol.io/specification/2025-06-18/server/index — transports, lifecycle, and OAuth 2.1 authorization semantics (verified 2026-08-27).
- [Anthropic announcement of MCP](https://www.anthropic.com/news/model-context-protocol) — origin and intent of the standard (verified 2026-08-27).
- For protocol boundaries and comparisons: [[05 Protocols and Tools/Agent Protocols]] and the [Protocols canvas](10 Maps/Protocols.canvas).

All links verified 2026-08-27.

---

---

> **← [[05 Protocols and Tools/Protocols Hub|Protocols Hub]]** · **[[AI_Home|Home]]** · **[[05 Protocols and Tools/Agent Protocols|Agent Protocols]] →**
