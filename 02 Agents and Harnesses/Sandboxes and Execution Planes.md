---
type: concept
layer: agents
status: evergreen
maturity: established
aliases: [Agent Sandbox, Execution Plane]
tags: [ai-engineering, security, sandbox, infrastructure]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "02 Agents and Harnesses/Agent Harness.md"
next: "02 Agents and Harnesses/Sandboxing Infrastructure.md"
summary: "Agents that can run code need a computer they can safely break. A sandbox is a disposable or strongly isolated execution environment with bounded filesystem, CPU, memo..."
---


# Sandboxes and execution planes

## Plain-English introduction

When an AI agent writes or runs code, you would not hand it the keys to your whole computer. Instead, you give it a disposable workspace — like a sandbox at a playground, where kids can dig and build without damaging anything outside the box. In the AI world, a sandbox is a temporary, isolated environment where the agent can run programs, access files, and use tools without risking your real data or systems. This note explains how to design that safe workspace, how to hand the agent only the secrets it needs for one task, and how to bring back only the results you trust.

## Control plane versus execution plane

Keep policy, credentials, approvals, and durable state in a trusted control plane. Run generated code, untrusted files, browser sessions, and transient dependencies in an execution plane with a small, explicit interface back to the controller. The controller decides what may be mounted, reached, exported, or retained.

Make the execution environment disposable by default and set resource/time/network limits outside the model. Inspect generated artifacts before promotion. See [[02 Agents and Harnesses/Sandboxing Infrastructure]] for choosing the actual isolation technology.

Agents that can run code need a computer they can safely break. A sandbox is a disposable or strongly isolated execution environment with bounded filesystem, CPU, memory, packages, credentials, and network access.

## Reference architecture

```text
CONTROL PLANE                         EXECUTION PLANE
agent loop · policy · state DB        disposable container/VM
credentials broker · traces    →      files · shell · packages · tests
```

Common primitives include containers, VMs, microVMs, namespaces, seccomp, temporary filesystems, network policies, and short-lived credentials. Isolation is defense in depth; it does not make arbitrary code automatically safe.

## Secret brokering and credential lifecycle

The execution plane should never hold long-lived credentials. Instead, the control plane brokers secrets on a per-operation basis:

1. **Request phase:** the agent declares which secret it needs and for what purpose (e.g., "read-only access to the users table for this query").
2. **Grant phase:** the control plane validates the request against policy, provisions a short-lived credential (time-boxed, scope-limited), and injects it into the sandbox environment.
3. **Execute phase:** the agent uses the credential within the sandbox. The credential is invisible to the model's context—it appears as an environment variable, not as text the model can echo or exfiltrate.
4. **Revoke phase:** on completion or timeout, the credential is revoked. The sandbox is destroyed, taking any cached credentials with it.

This lifecycle prevents ambient credential accumulation: an agent that runs 100 tool calls never holds more than one active credential at a time, and each credential is scoped to the minimum required permissions.

## Artifact promotion

Not everything an agent produces should leave the sandbox. Artifact promotion is the controlled process of extracting validated results:

- **Ephemeral artifacts** (logs, intermediate files, test output) stay in the sandbox and are destroyed on teardown.
- **Promoted artifacts** (code, documents, data exports) are explicitly extracted through a validated boundary: the control plane inspects, scans, or lints the artifact before accepting it into durable storage.
- **Rejected artifacts** (failed validation, policy violations) are discarded with an audit record.

The promotion boundary is where human review, automated testing, or policy checks happen. Never promote raw stdout or model output as authority—always validate against a schema or test suite first.

## Isolation overhead

Isolation is not free. Each layer adds startup latency, memory overhead, and I/O cost:

- **Process isolation:** near-zero overhead, but shares the host kernel—an exploit in the agent's code can reach the host.
- **Container isolation:** minimal overhead (~10ms startup), but shares the host kernel syscall surface.
- **gVisor:** user-space kernel adds syscall interception overhead; fast boot (milliseconds) but degraded I/O throughput.
- **MicroVM (Firecracker):** ~100–200ms boot, <5 MiB overhead, full kernel isolation—but requires custom orchestration.
- **Full VM:** strongest isolation but slowest startup (seconds to minutes).

The trade-off is linear: more isolation buys more safety at the cost of more latency and resource consumption. Choose based on the threat model—trusted internal tools may need only process isolation, while LLM-generated code executing arbitrary operations warrants microVM or stronger boundaries. See [[02 Agents and Harnesses/Sandboxing Infrastructure]] for technology-specific trade-offs.

## Boundaries to specify

- What files can be read or written?
- Can the process reach the internet, metadata services, or internal networks?
- Which secrets are brokered, and for which exact operation?
- What survives teardown?
- Which actions require human approval?

Related infrastructure appears in [[08 Tool Landscape/Infrastructure and Observability Tools]] and the security review in [[09 Playbooks/Evaluation and Security Review]].

## Sources and further reading

- E2B Documentation, 2026 — https://docs.e2b.dev/ — Firecracker-based cloud sandboxes, SDK, cold-start <200ms.
- Northflank, "Kata Containers vs Firecracker vs gVisor," Jan 2026 — https://northflank.com/blog/kata-containers-vs-firecracker-vs-gvisor — isolation hierarchy, startup costs, escape risks.
- Anthropic, "Effective context engineering for AI agents," Sep 2025 — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — context management, tool injection patterns.

All links verified 2026-08-27.

---

---

> **← [[02 Agents and Harnesses/Agent Harness|Agent Harness]]** · **[[AI_Home|Home]]** · **[[02 Agents and Harnesses/Sandboxing Infrastructure|Sandboxing Infrastructure]] →**
