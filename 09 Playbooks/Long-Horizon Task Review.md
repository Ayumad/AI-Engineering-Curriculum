---
type: playbook
layer: playbooks
status: evergreen
maturity: established
aliases: [Long-Horizon Task Review]
tags: [ai-engineering, playbooks, long-horizon, review]
visibility: personal
created: 2026-09-09
updated: 2026-09-09
prev: "09 Playbooks/Evaluation and Security Review.md"
next: "09 Playbooks/Learning Projects.md"
summary: "A worksheet-style playbook for running long agent jobs (dozens of steps, hours of work) with a task contract, checkpoint and compaction cadence, drift alarms, and a post-run trajectory review plus release gate."
---

# Long-Horizon Task Review

> [!summary] The gist
> Long runs fail quietly. You can't watch every tool call, so you watch the shape instead. Contract before the run, checkpoints during, trajectory review after. Errors compound: a small per-step mistake turns into systematic failure by step 40. Like a road trip — you check the map at rest stops, not at every mile marker.

---

Long-horizon tasks are the ones that run dozens of steps over hours: multi-file refactors, data pipelines, research sweeps, benchmark rollouts. Surge AI's long-horizon training environments treat 25–40+ tool-calling turns and 80K–100K tokens as the normal size of "long." The HORIZON paper measured 3100+ trajectories and found the failure mix shifts as horizons grow: planning errors and memory failures (catastrophic forgetting) start dominating, and per-step error rates compound across dependent steps. This playbook is how I run and review those jobs without babysitting them.

## Before the run — the task contract

Write one page. If the contract doesn't fit on one page, the task isn't ready.

- [ ] **Goal**: one sentence. What done looks like, in outcome terms, not activity terms.
- [ ] **Success criteria**: explicit and checkable. A test that passes, a file that exists, a diff that matches. Vague criteria produce vague completions.
- [ ] **Constraints**: what it must not touch. Prod, main branch, other people's files, budget.
- [ ] **Stop conditions**: when to bail instead of grinding. N consecutive failed retries, error classes it can't fix, signs the environment is broken rather than the plan.
- [ ] **Budget**: max steps, max tokens, max wall-clock time. Write the numbers down. A run without a budget has no failure definition.
- [ ] **Approval gates**: which actions pause for a human. Deploys, deletes, payments, external posts, anything irreversible.
- [ ] **Grading plan**: decide up front how you'll judge the trajectory, not just the output. Outcome-only grading misses a lot — Claw-Eval found final-answer scoring missed 44% of safety violations that trajectory grading caught. Define per-criterion checks (like Surge's graders: 8 of 10 criteria met) so partial credit is visible.

Two questions to answer before launch: what does a good trajectory look like step by step, and what deviation would make me kill the run?

## During the run — checkpoints, compaction, drift checks

### Checkpoint cadence

- [ ] State written to disk after each milestone, not just at the end. Anthropic's agents restart from their own notes after context resets and keep going.
- [ ] Idempotency keys on anything that mutates external state (API writes, DB rows, sends). A retried step must not double-apply.
- [ ] Compensation plan per milestone: if step 12 fails after step 8 committed something, what undoes or resumes it?
- [ ] Resume test: could I kill the process right now and restart from the checkpoint without human archaeology? If no, the checkpoint isn't real.

### Compaction schedule

Context rots — as token count grows, recall degrades (Anthropic). Don't let the agent re-read a growing transcript.

- [ ] Compact at fixed intervals or context thresholds. The agent should re-read a small state: goal, constraints, evidence so far, next steps. Not the raw history.
- [ ] Keep just-in-time references (file paths, queries, IDs) instead of loading full objects into context.
- [ ] After compaction, verify the compacted state still contains the success criteria. Summaries that drop the goal are how agents drift.

### Drift checks and alarms

- [ ] Every K steps (I use 5–10): goal vs current subplan. Is the agent still solving the contracted task, or its own reinterpretation of it?
- [ ] Alarm triggers that pause or notify: budget burn rate spiking, same tool error repeating 3x, agent editing files outside the contract scope, agent declaring success while criteria remain unchecked.
- [ ] Watch for shortcut behavior under optimization pressure. Arize catalogs agents that monkey-patch graders, copy answers from git history, or weaponize error traces. If the agent can see the checker, assume it might game the checker.

## After the run — trajectory review

Replay the trace before reading the summary. The summary is the agent's story; the trace is what happened.

- [ ] **Plan adherence**: did it follow the planned sequence? List each deviation and whether the deviation was justified.
- [ ] **Verification results**: run the success criteria checks myself. Never accept the agent's claim of "done" without re-executing the checks.
- [ ] **Milestone timestamps**: where did time actually go? Long stalls usually mean loops.
- [ ] **Failure attribution** (HORIZON categories):
  - [ ] Planning — wrong decomposition, bad subgoal ordering, missed dependency
  - [ ] Memory — forgot a constraint, lost earlier findings after compaction, contradicted itself
  - [ ] Tooling — bad tool output, wrong tool choice, API failure
  - [ ] Environment — broken sandbox, stale data, network, permissions
- [ ] **Partial credit**: which per-criterion checks passed? Dense scoring tells you how close it got, binary scoring doesn't.
- [ ] **Log for next run**: one paragraph per failure — attribution, evidence (trace snippet), and the fix that goes into the contract or harness next time. Failures that don't change the next contract were reviewed for nothing.

## Gate for release

What must be true before I trust a long run's output:

- [ ] Evidence exists, not just claims. Test outputs, file diffs, screenshots, logs — things I can check independently.
- [ ] Tests re-run clean on my side, in my environment, after the agent's last edit.
- [ ] No constraint violations in the trace (out-of-scope writes, skipped approval gates, budget blown).
- [ ] Trajectory graded, not just outcome. A right answer via a wrong path is a broken process that got lucky — reliability research shows single-run success hides inconsistent behavior.
- [ ] Human sign-off at every consequential boundary the contract named. The agent proposes, a person approves deploys, deletes, and sends.
- [ ] Rollback path is real and tested, not theoretical.

If any box is unchecked, the run isn't done. It's a draft.

## Sources and further reading

- [The Long-Horizon Task Mirage? Diagnosing Where and Why Agentic Systems Break](https://arxiv.org/html/2604.11978v1) (HORIZON) — cross-domain failure benchmark; planning vs memory vs tooling vs environment attribution; compounding per-step errors.
- [Long-horizon agent benchmarks are fragmenting](https://arize.com/blog/long-horizon-agent-benchmarks-field-guide/) (Arize AI) — trajectory vs outcome grading, the 44% missed-violations stat, reward hacking and sandbagging in benchmark runs.
- [Effective context engineering for AI agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) (Anthropic) — context rot, compaction, just-in-time retrieval, notes that survive context resets.
- [Training on Long-Horizon Agent Tasks](https://surgehq.ai/blog/cross-benchmark-generalization-for-long-horizon-agentic-tasks) (Surge AI) — 25–40+ turn / 80K–100K token task scale, per-criterion dense graders, task-closure failures.
- [A Practical Guide to Building Agents](https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf) (OpenAI) — guardrails and human-in-the-loop patterns for agent deployment.

All links verified 2026-09-09.

## Related

- [[02 Agents and Harnesses/Long-Horizon Tasks and Failure Modes]]
- [[02 Agents and Harnesses/Planning State and Persistence]]
- [[06 Reliability and Security/Long-Horizon Evaluation and Benchmarks]]
- [[09 Playbooks/Evaluation and Security Review]]
- [[03 Context Knowledge Memory/Large Project Navigation and Context Scaling]]
- [[06 Reliability and Security/Human Oversight and Trust Engineering]]

---

> **← [[09 Playbooks/Evaluation and Security Review|Evaluation and Security Review]]** · **[[AI_Home|Home]]** · **[[09 Playbooks/Learning Projects|Learning Projects]] →**
