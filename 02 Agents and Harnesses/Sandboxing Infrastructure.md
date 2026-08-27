---
type: concept
layer: agents
status: evergreen
maturity: established
aliases: [Agent Sandbox Architecture]
tags: [ai-engineering, sandbox, containers, microvms, execution]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "02 Agents and Harnesses/Sandboxes and Execution Planes.md"
next: "02 Agents and Harnesses/Planning State and Persistence.md"
summary: "Sandbox choice follows the threat model: processes suit trusted code, containers provide moderate isolation, and microVMs or VMs suit generated or arbitrary untrusted execution."
---


# Sandboxing infrastructure

There is no universally best sandbox. Select an isolation boundary based on what executes, the data and credentials it can reach, the blast radius of compromise, compatibility requirements, and startup/operating cost. For the architectural principle of separating policy from execution, see [[02 Agents and Harnesses/Sandboxes and Execution Planes]].

| Technique | Isolation | Startup | Best fit |
|---|---|---|---|
| Process | Low | Excellent | Trusted internal tools |
| Container | Medium | Excellent | Bounded code execution |
| gVisor-style runtime | Stronger | Good | Untrusted containers |
| MicroVM | Very strong | Good | LLM-generated or user code |
| Full VM | Very strong | Slower | Heavy isolation/compliance |
| Browser sandbox | Scoped | Good | Web interaction only |

## Technology deep dives

**Firecracker (AWS, Rust):** MicroVMs boot in ~100–200ms with <5 MiB overhead per instance. Each microVM runs its own Linux kernel in KVM with only 5 virtio devices (minimal attack surface). ~50K lines of Rust vs. QEMU's ~2M lines of C. Sustains ~150 microVMs/sec per host. Used by AWS Lambda and Fargate. Requires custom orchestration—no built-in Kubernetes integration.

**gVisor (Google, Go):** User-space kernel that intercepts syscalls via Sentry. Supports Systrap, KVM, and Directfs modes. Boot time in milliseconds with lower memory than VM-based alternatives. The trade-off: syscall interception adds overhead on I/O-heavy workloads, and while it reduces the kernel attack surface, it still shares the host kernel (unlike full VM isolation).

**Kata Containers:** An orchestration framework that pairs multiple VMMs (Cloud Hypervisor default, Firecracker, or QEMU) with Kubernetes. Boot ~150–300ms. Handles kernel images, networking, storage, and lifecycle management with native CRI integration. Combines hardware isolation (via the VMM) with container workflows (via Kubernetes), making it the bridge between VM security and container ergonomics.

**Isolation hierarchy (strongest to weakest):** Full VM (KVM) > Firecracker/Kata microVM > gVisor > container > process. For adversarial workloads (LLM-generated code, untrusted user uploads), VM-based isolation is strongest. gVisor's syscall interception is simpler to deploy but weaker against determined attackers.

## Cloud sandbox platforms

Managed services abstract the underlying isolation technology:

- **E2B:** Open-source, Firecracker-based. Cold-start <200ms. Supports Python, JS, Ruby, C++. Full filesystem, terminal, packages, and browser. Sessions up to 24h. SDKs for Python and TypeScript. Integrates with OpenAI, Anthropic, LangChain, and Vercel AI SDK. Freemium tier.
- **Modal:** gVisor-based sandboxes. Supports Python, JS, TS, Go. ~$0.0504/vCPU-hour. Broader platform including inference, training, and batch workloads.
- **Northflank:** Managed container and microVM hosting with configurable network policies. Supports Firecracker-based isolation.

Network egress is configurable across platforms: E2B/Firecracker support network policies at the microVM level; gVisor provides syscall-level filtering for finer-grained control.

## Sandbox-escape risks

No sandbox is absolute. Understand the escape vectors:

- **Docker containers** share the host kernel—a kernel exploit gives full host access. Suitable only for trusted code with no adversarial input.
- **gVisor** reduces the kernel attack surface by intercepting syscalls, but the Sentry process itself runs on the host kernel. A vulnerability in Sentry or a kernel exploit that bypasses interception can escape.
- **Firecracker/Kata** require escaping both the guest kernel and the hypervisor (KVM). This is a significantly harder attack—no known production escapes as of 2026—but the guest kernel is still a real kernel with its own vulnerability surface.
- **Full VMs** provide the strongest boundary but are not immune to hypervisor exploits (e.g., VENOM, though patched).

Defense in depth: combine sandbox isolation with network policies, credential scoping, output validation, and monitoring. Sandboxing complements—rather than replaces—tool allowlists, approval gates, input validation, secrets management, logging, and incident response.

## Resource-limit configuration

Set resource limits outside the model. A typical agent sandbox configuration:

```yaml
sandbox:
  cpu_limit: "2.0"          # vCPUs
  memory_limit: "1Gi"       # RAM
  wall_clock_limit: "300s"  # max runtime
  process_count: 64         # max processes
  disk_limit: "5Gi"         # writable filesystem
  network: "egress-https"   # allow HTTPS only, no internal
  package_install: false    # no pip/npm install
  file_mounts: []           # no host filesystem access
```

Destroy the environment after the job unless durable state is deliberately promoted through a validated boundary (see [[02 Agents and Harnesses/Sandboxes and Execution Planes#Artifact promotion]]).

## Sources and further reading

- Northflank, "Kata Containers vs Firecracker vs gVisor," Jan 2026 — https://northflank.com/blog/kata-containers-vs-firecracker-vs-gvisor — technology comparison, isolation hierarchy, startup costs.
- E2B Documentation, 2026 — https://docs.e2b.dev/ — Firecracker-based cloud sandboxes, SDK, session management.
- E2B API Evangelist profile, 2026 — https://github.com/api-evangelist/e2b — integration examples and architecture.

All links verified 2026-08-27.

## Related

[[02 Agents and Harnesses/Sandboxes and Execution Planes]] · [[02 Agents and Harnesses/Computer-Use and Browser Agents]] · [[06 Reliability and Security/Security and Jailbreaking]]

---

---

> **← [[02 Agents and Harnesses/Sandboxes and Execution Planes|Sandboxes and Execution Planes]]** · **[[AI_Home|Home]]** · **[[02 Agents and Harnesses/Planning State and Persistence|Planning State and Persistence]] →**
