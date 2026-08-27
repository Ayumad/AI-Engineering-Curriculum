---
type: playbook
layer: context
status: evergreen
maturity: emerging
aliases: [Project Bootstrap, Repository Instructions, PRD and AGENTS.md]
tags: [ai-engineering, project-initialization, prd, instruction-files, agents-md, specifications]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "03 Context Knowledge Memory/Large Project Navigation and Context Scaling.md"
next: "03 Context Knowledge Memory/Behavior and Communication Controls.md"
summary: "A practical method for turning an idea into a discoverable project context package: a PRD for what to build, repository instructions for how to work, and executable project facts for verification."
---


# Project initialization and instruction files

> [!summary] The gist
> Before an agent changes a project, hand it a small, inspectable context package: a PRD for what to build, repo instructions for how to work, and verifiable facts for done. This note covers the artifact map, a safe initialization sequence, and templates for PRDs and instruction files.

---

## The artifact map

Use the smallest set of files that gives the project a reliable shared memory. Names vary by ecosystem; purpose matters more than filename.

| Artifact | Answers | Keep here | Do not use it for |
|---|---|---|---|
| PRD or feature specification | Why are we building this, for whom, and what counts as success? | Problem, goals, non-goals, requirements, acceptance criteria, risks, rollout | Shell commands, temporary task state, or hidden policy |
| `AGENTS.md` / `CLAUDE.md` / `GEMINI.md` | How should an agent work in this repository or directory? | Architecture map, commands, conventions, safety rules, verification, reporting | The complete product plan or a security boundary |
| `README.md` | How does a human understand and use this project? | Purpose, setup, basic usage, links to deeper docs | Agent-only policy or a copy of every implementation detail |
| `CONTRIBUTING.md` | How do people propose, test, review, and merge changes? | Contribution flow, style checks, review expectations, release rules | Runtime authorization |
| Architecture decision record | Why was a consequential design choice made? | Decision, alternatives, consequences, date, status | Current task instructions that will expire soon |
| Project manifest and config | What is executable truth? | Dependencies, scripts, tool versions, build and test configuration | Aspirational requirements that are not implemented |
| `PLAN.md` / task record | What is the current work state? | Steps, decisions, evidence, blockers, next action | Durable repository policy |
| `STYLE.md` or behavior profile | How should user-facing communication be written? | Audience, tone, terminology, structure, examples, exclusions | Safety policy or product requirements |

The same repository may combine some of these. Preserve the distinctions even when the files are combined: requirements, working instructions, executable facts, and temporary state have different owners and lifetimes.

## A safe initialization sequence

1. **Inventory the project.** Identify the repository root, existing instruction files, manifests, test commands, deployment boundaries, generated files, and sensitive paths. Read existing guidance before adding a competing version.
2. **Write the PRD before the implementation plan.** Agree on the user problem, desired outcome, non-goals, constraints, risks, and observable acceptance criteria. A PRD is a decision record, not a list of imagined implementation steps.
3. **Map the sources of truth.** Point to the code, schema, API contract, design, or operational dashboard that can verify each important requirement. Mark assumptions and unresolved questions.
4. **Write repository instructions.** Put stable architecture, commands, conventions, safety rules, and definition-of-done guidance in the nearest applicable instruction file. Keep instructions short enough to load and specific enough to test.
5. **Create the human entry point.** Update the README with purpose, setup, first successful run, and links to the PRD, architecture decisions, contribution guidance, and agent instructions.
6. **Record consequential decisions.** Use an ADR when a choice affects interfaces, data, security, deployment, cost, or reversibility. Link it from the relevant PRD or README.
7. **Create current task state.** Turn the approved PRD into a bounded plan with dependencies, evidence, approvals, and a next action. Keep it separate from permanent instructions.
8. **Run a discovery test.** Ask a fresh agent to locate the project instructions, explain the architecture, identify the right verification command, and list what it is not authorized to do. Correct ambiguity before granting write access.

## PRD template

```text
# <Feature or product name>

Status: draft | approved | in progress | shipped | retired
Owner: <person or team>
Last updated: <date>

## Problem
Who has what problem, in what situation, and what evidence supports it?

## Goal
What user-visible or operational outcome should exist?

## Non-goals
What related work is explicitly out of scope for this version?

## Users and workflows
- Primary user:
- Trigger:
- Main flow:
- Important alternate or failure flows:

## Requirements
- Functional:
- Data and interfaces:
- Performance and cost:
- Privacy and security:
- Accessibility or localization:

## Constraints and dependencies
- Must remain true:
- Dependencies:
- Authority or approval boundaries:
- Compatibility requirements:

## Definition of done
- Observable acceptance criterion:
- Observable acceptance criterion:

## Verification
- Tests or checks:
- Evidence to retain:
- Reviewers or approval gates:

## Rollout and recovery
- Release strategy:
- Monitoring:
- Rollback or migration path:

## Open questions and risks
- Question or risk:
- Owner and next decision date:
```

Write acceptance criteria as observable facts. “Improve search” is a goal; “for the test corpus, the top five relevant documents contain the answer in at least four cases, with citations” is testable.

## Repository instruction template

```text
# Agent instructions for <repository or directory>

## Scope and precedence
These instructions apply to <path>. More specific instructions may add constraints; task-specific instructions may define the current objective but cannot override safety policy or authorization.

## Project map
- <path>: <responsibility>
- <path>: <responsibility>

## Setup and commands
- Install:
- Run locally:
- Test:
- Lint/typecheck:
- Build:

## Working conventions
- Follow:
- Preserve:
- Generated files:
- Naming or dependency rules:

## Safety boundaries
- Read-only by default:
- Ask before:
- Never:
- Sensitive paths or data:

## Verification and reporting
- Definition of done:
- Required checks:
- Report changed files, evidence, assumptions, failures, and unresolved risks.
```

Do not claim a hierarchy that the harness does not implement. If multiple instruction files are supported, document the actual discovery and precedence rules and test them with a harmless conflicting example. A nearer file should narrow scope, not silently weaken a broader safety rule.

## Instruction-file precedence and ecosystem differences

When multiple instruction files exist in a project, the agent must know which one wins. Precedence rules vary by tool:

| File | Tool ecosystem | Scope | Precedence rule |
|---|---|---|---|
| `AGENTS.md` | Community standard (Linux Foundation stewardship). Works across Claude Code, Gemini CLI, Cursor, Codex, Jules, Amp, Factory. | Repository-wide and per-directory | Nested files override parent: closest `AGENTS.md` to the working directory wins. Root-level file is the fallback. |
| `CLAUDE.md` | Claude Code | Project root (primary context mechanism) | Single file at project root; Claude Code reads it automatically on startup. Directory-level variants are supported. |
| `GEMINI.md` | Gemini CLI | Project-level and directory-level | Technical hierarchy: project-level → directory-level. Gemini CLI also reads `AGENTS.md` for compatibility, so both can coexist. |

**Key ecosystem differences:**

- **AGENTS.md** has no required fields. Sections typically cover: project overview, build/test commands, code style, testing instructions, security. Can be updated anytime. Symlink migration for repos with existing `AGENT.md`: `mv AGENT.md AGENTS.md && ln -s AGENTS.md AGENT.md`.
- **CLAUDE.md** is Claude Code's primary context mechanism. It is read at project root and provides stable instructions for how Claude Code should operate in the repository.
- **GEMINI.md** is Gemini CLI's context file. Gemini CLI is open-source (Apache 2.0), supports 60 req/min free, 1M token context, MCP support, checkpointing, headless mode, sandboxing, subagents, and token caching. It reads both `GEMINI.md` and `AGENTS.md`.

**Precedence principle:** when a nearer file narrows scope, it should not silently weaken a broader safety rule. Document the actual precedence and test it with a conflicting example before granting write access.

## Quality checks

- Every requirement has an owner, source of truth, and verification path.
- The PRD distinguishes goals from non-goals and stable requirements from open questions.
- Instructions describe the project as it exists, not an imagined architecture.
- Commands are copied from executable project configuration and have been run successfully.
- Safety rules identify approval boundaries and do not rely on the model merely obeying prose.
- Temporary plans, incidents, and bugs are not promoted into permanent repository instructions without review.
- A fresh agent can discover the right files and explain what it may and may not do.
- Changes to the PRD or instruction files are reviewed like code because they change future agent behavior.

## Common failure modes

- **The mega-instruction file:** everything from product strategy to a one-off bug is loaded on every task. Split by scope and lifetime.
- **The stale command list:** instructions name scripts that the manifest no longer provides. Verify commands continuously.
- **Policy by suggestion:** “never deploy” in prose does not replace deployment credentials, environment separation, or approval gates.
- **Requirements without tests:** a polished PRD that cannot produce evidence is only an aspiration.
- **Instruction drift:** architecture changes while `AGENTS.md` remains authoritative-looking. Assign ownership and review dates.
- **Conflicting authorities:** multiple files give different commands or rules. Define precedence, remove duplicates, and add a discovery test.

## Sources and further reading

- AGENTS.md official (Linux Foundation, 2026) — https://agents.md/ — community standard, nested file precedence, no required fields, symlink migration.
- Gemini CLI GitHub (Google, 2026) — https://github.com/google-gemini/gemini-cli — GEMINI.md mechanism, AGENTS.md compatibility, open-source Apache 2.0.
- Gemini CLI Documentation — https://geminicli.com/docs/ — technical hierarchy, directory-level overrides, token caching.
- Anthropic, "Effective harnesses for long-running agents," Nov 2025 — https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents — initializer+coding-agent pattern, progress files for multi-session continuity.

All links verified 2026-08-27.

## Related

[[03 Context Knowledge Memory/Context Engineering]] · [[03 Context Knowledge Memory/Large Project Navigation and Context Scaling]] · [[03 Context Knowledge Memory/Prompting for Agents]] · [[02 Agents and Harnesses/Planning State and Persistence]] · [[02 Agents and Harnesses/Agent Harness]] · [[09 Playbooks/Prompt Template]] · [[12 Templates/Template Library]]

---

---

> **← [[03 Context Knowledge Memory/Large Project Navigation and Context Scaling|Large Project Navigation and Context Scaling]]** · **[[AI_Home|Home]]** · **[[03 Context Knowledge Memory/Behavior and Communication Controls|Behavior and Communication Controls]] →**
