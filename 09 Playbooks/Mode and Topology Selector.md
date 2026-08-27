---
type: decision
layer: playbooks
status: evergreen
maturity: established
aliases: [Agent Mode Selector]
tags: [ai-engineering, planning, multi-agent, decision]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "09 Playbooks/Playbooks Hub.md"
next: "09 Playbooks/Prompt Template.md"
summary: "| Mode | Use when | Expected behavior | |||| | Ask | You need understanding | Explore and explain; do not change state | | Plan | The work is ambiguous or broad | Insp..."
---


# Mode and topology selector

## Interaction mode

| Mode | Use when | Expected behavior |
|---|---|---|
| Ask | You need understanding | Explore and explain; do not change state |
| Plan | The work is ambiguous or broad | Inspect, decompose, identify risks, propose steps |
| Agent | The outcome is clear and authority is granted | Execute, verify, and report |
| Debug | Something is failing | Gather runtime evidence before changing code |
| Goal | Work must persist across turns or time | Track durable progress, checkpoints, and stopping conditions |

## Agent topology

| Choice | Use when | Avoid when |
|---|---|---|
| One agent | Problem is coherent and sequential | Independent domains can proceed in parallel |
| One agent + skills | Expertise can be loaded on demand | Roles need independent trust boundaries |
| Subagents | Work is bounded, parallel, or needs isolation | Handoffs would cost more than the work |
| Full multi-agent team | Specialization, debate, or ownership is essential | A single verified loop is sufficient |

## Worked choice example

**Scenario:** You need to migrate a Python service from PostgreSQL to MongoDB, update queries, and deploy to staging.

1. **Ask first** — "What files touch the database layer?" → explore, don't change state.
2. **Plan** — Decompose into: schema mapping, query rewriting, migration script, tests, deployment. Identify risks: data loss, query incompatibility, staging drift.
3. **Agent** — Execute each step as a bounded task: rewrite queries, generate migration, run tests. One agent + skills is sufficient; no parallel work needed.
4. **Goal** — Track progress across multiple sessions (the migration may span days). Checkpoint at each verified step.

**Cost implications:**
- Ask/Plan: low cost — few tokens, no tool calls, no state mutations.
- Agent: moderate — tool calls, file reads/writes, tests. Budget per step.
- Subagents: higher — parallel execution multiplies token cost; justified only if subtasks are truly independent.
- Multi-agent team: highest — coordination overhead (orchestrator + specialists), 200ms × 2 agents → 4+ seconds × 8 agents. Reserve for specialized debate or trust-boundary needs.

## Mid-task switching

You can switch modes mid-task. Common pattern: Ask → Plan → Agent. If an Agent step reveals ambiguity, drop back to Plan. If Goal work hits a blocker, drop to Debug. The mode selector is a decision aid, not a lock-in.

Default: Ask/Plan first for uncertainty; Agent for a bounded task; Goal for durable work; one agent until a concrete reason justifies more coordination.

For deeper agent harness and topology guidance, see [[04 Workflows and Orchestration/Orchestration Hub]] and [[02 Agents and Harnesses/Agent Harness]].

---

---

> **← [[09 Playbooks/Playbooks Hub|Playbooks Hub]]** · **[[AI_Home|Home]]** · **[[09 Playbooks/Prompt Template|Prompt Template]] →**
