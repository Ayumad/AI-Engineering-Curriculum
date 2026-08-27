---
type: concept
layer: agents
status: evergreen
maturity: established
aliases: [Agent State, Checkpoints]
tags: [ai-engineering, planning, state, persistence]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "02 Agents and Harnesses/Sandboxing Infrastructure.md"
next: "02 Agents and Harnesses/Vision and Multimodal Input Engineering.md"
summary: "Planning turns a broad request into observable work. State records what is known, what is complete, what failed, and what must happen next."
---


# Planning, state, and persistence

## Plain-English introduction

When you hire a contractor for a big project, you expect them to keep notes: what is done, what is left, what went wrong, and what to try next. If they lost their notes halfway through, you would be starting over from scratch. AI agents face the same problem. Without a way to save their progress, a crash or interruption means losing everything they have figured out. This note explains how agents create plans, save their work at key moments, and recover when things go wrong — whether that means picking up where they left off, undoing a mistake, or starting fresh with everything they have learned.

## Durable work needs durable state

Plans are hypotheses, not authority. Store enough state to resume safely: objective, constraints, completed and pending steps, artifact references, decisions, approvals, idempotency keys, and the last verified observation. Do not persist raw, unbounded conversation merely because it exists; compact it into state that a later run can inspect and correct.

Checkpoint around irreversible or long-running boundaries. On recovery, read the checkpoint and current external state before retrying—never infer that a previous tool call failed just because the process ended. Version state schemas and provide migrations so a deployment does not strand in-flight work.

Planning turns a broad request into observable work. State records what is known, what is complete, what failed, and what must happen next.

## Concrete state schema

A minimal but complete agent state record:

```json
{
  "run_id": "run_a1b2c3",
  "objective": "Deploy staging environment for auth service",
  "constraints": {"max_cost_usd": 5.0, "deadline": "2026-08-28T12:00:00Z"},
  "plan": [
    {"step": 1, "action": "provision_db", "status": "completed", "idempotency_key": "db-prod-v2"},
    {"step": 2, "action": "run_migrations", "status": "completed", "idempotency_key": "mig-0827"},
    {"step": 3, "action": "deploy_service", "status": "in_progress", "idempotency_key": "deploy-staging-42"},
    {"step": 4, "action": "run_smoke_tests", "status": "pending"}
  ],
  "artifacts": {"db_connection": "postgres://staging:5432/auth", "commit_sha": "f3a9c2e"},
  "approvals": [{"step": 3, "approved_by": "ops-bot", "at": "2026-08-27T10:00:00Z"}],
  "errors": [],
  "budget": {"tokens_used": 14200, "tokens_limit": 100000, "cost_usd": 0.87},
  "last_observation": "migrations applied successfully, deploying service...",
  "updated_at": "2026-08-27T10:05:00Z"
}
```

Persist state separately from the transcript so a run can resume, fork, or be audited. Checkpoint before consequential side effects and after meaningful milestones.

## Recovery patterns

When an agent run fails or is interrupted, three recovery strategies apply:

- **Resume (replay from checkpoint):** Load the persisted state, verify external world consistency (did the database migration actually apply?), and continue from the next pending step. Fastest recovery when the checkpoint is recent and side effects are idempotent.
- **Compensate (Saga pattern):** For partially completed work with non-idempotent side effects, execute compensating actions to undo completed steps before retrying. Use try...catch logic per step: if step 3 fails after step 2 succeeded, run the compensating action for step 2 before retrying the whole sequence.
- **Re-execute (fresh run):** Discard partial progress and restart from scratch with the same inputs. Appropriate when the failure mode is unclear or when compensation is more expensive than re-execution.

Temporal's durable execution model captures this spectrum: workflows record every state transition as an immutable event (event sourcing), persisting to Cassandra, PostgreSQL, or MySQL. On failure, the runtime replays the event history to reconstruct the exact workflow state. Activities (individual steps) retry automatically with configurable policies. This model has been in production for 9 years, originating from AWS SQS/SWF and the Uber Cadence team. OpenAI uses Temporal for Codex production agents: crash recovery replays from persisted checkpoints.

## Idempotency keys

Every tool call that mutates external state must carry an idempotency key—a unique identifier that allows the execution layer to detect and deduplicate repeated calls. Combine workflow-level IDs (preventing duplicate workflow execution) with activity-level keys (preventing duplicate side effects within a workflow). Keys should be deterministic functions of the intended outcome, not random UUIDs, so that a retry of the same logical operation produces the same key.

## Backend comparison

| Backend | Durability | Latency | Best fit |
|---|---|---|---|
| SQLite | File-level | Sub-ms | Single-node dev, local agents |
| Redis | Volatile (AOF optional) | Sub-ms | Ephemeral state, pub/sub coordination |
| DynamoDB | Strong (AWS-managed) | 5–15ms | Cloud-native, autoscaling, item-level transactions |
| Temporal | Event-sourced, strongly durable | 10–50ms | Long-running workflows, multi-day tasks, crash recovery |

Choose based on the durability-latency trade-off: in-memory stores are fast but lose state on crash; event-sourced systems survive crashes but add write overhead. For agents that run across minutes or hours, durable persistence is non-negotiable.

## Long-running agents

Long runs need context compaction, heartbeat/timeout handling, durable queues, resumability, human handoff, and explicit expiry. A "goal" is a durable objective with progress and stopping conditions; a normal prompt is a single turn.

## Sources and further reading

- Temporal official, 2026 — https://temporal.io/ — durable execution, event sourcing, workflow recovery.
- Pan, "Agentic Systems Are Distributed Systems," May 2026 — https://tianpan.co/blog/2026-05-05-agentic-systems-distributed-services-microservices-patterns — failure rates, circuit breakers, idempotency patterns.
- Anthropic, "Effective harnesses for long-running agents," Nov 2025 — https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents — initializer/coding agent, progress files, multi-session continuity.

All links verified 2026-08-27.

## Related

[[04 Workflows and Orchestration/Orchestration Hub]] · [[06 Reliability and Security/Reliability Evals and Observability]] · [[09 Playbooks/Mode and Topology Selector]]

---

---

> **← [[02 Agents and Harnesses/Sandboxing Infrastructure|Sandboxing Infrastructure]]** · **[[AI_Home|Home]]** · **[[02 Agents and Harnesses/Vision and Multimodal Input Engineering|Vision and Multimodal Input Engineering]] →**
