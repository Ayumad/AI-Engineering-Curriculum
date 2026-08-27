---
type: playbook
layer: context
status: evergreen
maturity: emerging
aliases: [Large Project Context, Progressive Disclosure, Context Scaling]
tags: [ai-engineering, context-engineering, large-projects, navigation, retrieval, progressive-disclosure, project-maps]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "03 Context Knowledge Memory/Vector Search and Embeddings.md"
next: "03 Context Knowledge Memory/Project Initialization and Instruction Files.md"
summary: "A practical method for working in projects too large to parse manually: build a navigation layer, select context progressively, work in bounded slices, and preserve durable state."
---


# Large project navigation and context scaling

When a project is too large to parse manually, do not respond by placing the whole repository or document collection into one prompt. Use progressive disclosure, durable project state, and targeted retrieval. The objective is not to know everything at once; it is to make the next good decision with traceable evidence.

## The operating loop

```text
project map
    ↓
task brief and acceptance criteria
    ↓
targeted search or retrieval
    ↓
small bounded work slice
    ↓
verification
    ↓
checkpoint or handoff
```

Each cycle should reduce uncertainty, change a bounded part of the system, or produce evidence. If it does none of these, stop and re-scope.

## Separate information by purpose and lifetime

| Need | Preferred artifact or system | Lifetime |
|---|---|---|
| Understand the project | `README.md`, `docs/INDEX.md`, architecture map, manifests | Stable, reviewed |
| Stable agent rules | `AGENTS.md` and scoped instruction files | Stable, reviewed |
| Product intent | `PRD.md` or feature specification | Versioned, changes by decision |
| Design rationale | ADRs | Durable until superseded |
| Current work | `PLAN.md` or task record | Until task completion |
| Transfer ownership | `HANDOFF.md` | Until received or expired |
| User-facing communication | `STYLE.md` or behavior profile | Stable but versioned |
| Large changing knowledge | Search index or RAG system | Refreshable |
| Durable facts and procedures | Governed memory and skills | Explicitly promoted and versioned |
| Runtime progress | Structured checkpoints or state store | Resumable and auditable |

Do not use a stable instruction file for temporary incident details. Do not use a raw transcript as durable memory. Do not use a human-readable Markdown handoff as the only source of truth for machine recovery.

## Progressive disclosure

Load context in layers, stopping when the next decision is supported.

### Layer 0 — Orientation

Read the project map, repository instructions, manifest, and relevant top-level README. Identify the likely subsystem, owner, commands, protected paths, and source-of-truth documents.

### Layer 1 — Task context

Load the task brief, PRD slice, acceptance criteria, current plan, recent decisions, and authority boundaries. State what is explicitly out of scope.

### Layer 2 — Targeted evidence

Retrieve the relevant files, symbols, tests, logs, schemas, API contracts, and recent changes. Prefer sections and small windows over entire files. Preserve path, source, timestamp, scope, and confidence.

### Layer 3 — Runtime context

Add tool results, approvals, failures, traces, checkpoints, and handoff reports only as they become relevant. Summarize completed work while retaining decisions, evidence, and unresolved risks.

The context window is working memory, not the project database. Reserve room for tool output, errors, recovery, and the final answer.

## Build a navigation layer

Large projects need a map that makes the territory searchable.

- **Directory and subsystem index:** describe what each major path owns and link to its entry point.
- **Dependency or architecture map:** show important data flows, service boundaries, and callers/callees.
- **Symbol and code search:** locate definitions, references, routes, schemas, tests, and configuration by name.
- **Test and ownership index:** connect subsystems to the tests that verify them and the people or teams who own them.
- **Document metadata:** record scope, owner, freshness, authority, sensitivity, and source for important documents.
- **Source-of-truth map:** connect each important requirement or decision to the code, schema, contract, test, dashboard, or operational record that can verify it.
- **Retrieval index:** combine exact search for identifiers with semantic retrieval for concepts, then rerank and filter by scope, ACL, freshness, and task relevance.

Generate maps from executable facts where possible. A manually maintained index is useful, but a stale map creates false confidence; review it after structural, dependency, or ownership changes.

## Code-intelligence tools

Progressive disclosure works best when the retrieval layer can answer structural questions without loading entire files:

| Tool | What it provides | When to use |
|---|---|---|
| **LSP (Language Server Protocol)** | Go-to-definition, find-references, diagnostics, hover info, symbol outline. Runs locally per language. | Understanding call chains, type relationships, and code structure without reading entire files. |
| **Sourcegraph / code search** | Cross-repository semantic and regex search, dependency graphs, batch change previews. | Finding every call site or configuration pattern across a large or multi-repo codebase. |
| **AST / symbol index** | Structural tree of code: classes, functions, imports, exports. Often exposed via `find_symbol` or `list_symbols` in agent harnesses. | Building a navigation layer from executable facts rather than manual documentation. |
| **Incremental indexing** | Re-index only changed files on each commit or save. Combine file-watching with hash-based invalidation to keep the index fresh without full rebuilds. | Large codebases where full re-indexing is slow (minutes to hours) and freshness matters for retrieval accuracy. |

These tools turn the "symbol and code search" item in the navigation layer from a description into an implementation. When building a project map, prefer generating it from LSP or AST data over hand-writing it—machines produce maps that stay current as code changes.

## Multi-repo and monorepo patterns

Large organizations often span multiple repositories. The progressive-disclosure framework extends cleanly:

- **Shared context index.** Maintain a single cross-repo map that lists each repository's purpose, ownership, API contracts, and dependency edges. This is the Layer 0 orientation for any cross-repo task.
- **Boundary-first retrieval.** When a task crosses repos, identify the API boundary (contract, schema, protobuf, OpenAPI spec) before diving into implementation details of either side. Contracts are the smallest authoritative context.
- **Nested instruction files.** Per AGENTS.md convention (Linux Foundation 2026), nested `AGENTS.md` files in monorepos take precedence over root-level ones—the closest file wins. This lets each subsystem declare its own conventions without polluting the parent.
- **Shared skill and tool registry.** Skills and tool definitions that apply across repos should live in a shared registry rather than being duplicated. Cross-link to the canonical definition (see [[03 Context Knowledge Memory/Skills, Tools, and Capability Management]]).

## A concrete working routine

1. **Orient.** Read the project map and instructions. If no map exists, create a one-page map before attempting a broad change.
2. **Scope.** State the task outcome, acceptance criteria, authority, non-goals, and likely subsystem.
3. **Find.** Search for symbols, requirements, tests, configuration, recent changes, and source-of-truth records. Ask for a targeted retrieval set rather than “all relevant files.”
4. **Assemble.** Build a context packet with authoritative instructions, task facts, relevant evidence, untrusted content, available capabilities, and budgets.
5. **Bound.** Choose one small work slice with a clear input, output, stop condition, and verification path.
6. **Act.** Make the smallest reversible change that can test the hypothesis. Keep unrelated discoveries in the plan or issue rather than expanding scope silently.
7. **Verify.** Run the narrowest useful tests first, then broader checks as the change warrants. Record the exact evidence and any uncertainty.
8. **Checkpoint.** Save completed work, decisions, artifacts, approvals, errors, budget remaining, last verified observation, and next action.
9. **Handoff or continue.** Write a handoff when ownership, session, or operating conditions change. Continue only if the next action remains within scope and budget.

For agent harnesses, expose bounded navigation capabilities such as `list_files`, `search`, `find_symbol`, `read_sections`, `run_tests`, `inspect_state`, and `record_checkpoint`. Each capability still needs authorization, input validation, limits, and traces.

## Retrieval and context selection rules

- Search by the task’s nouns, verbs, symbols, paths, requirements, and failure symptoms.
- Start with exact matches for identifiers, then use semantic retrieval for concepts and related terminology.
- Retrieve parent context for a selected section when local meaning depends on surrounding definitions.
- Filter by scope, access control, freshness, and source authority before ranking for relevance.
- Keep citations or links back to the source of every important retrieved fact.
- Label external, generated, or user-provided material as untrusted data rather than instructions.
- Remove duplicate, stale, contradictory, or already-completed material from the active context.
- When summaries conflict, return to the authoritative source instead of averaging the summaries.
- Measure retrieval quality and downstream answer quality; a plausible answer does not prove the right context was selected.

## Escalation signals

Pause, re-scope, or change the operating mode when:

- the task requires more context than the available budget can safely hold;
- the agent needs several large files merely to identify where to begin;
- no authoritative source can be named for a critical fact;
- retrieved summaries or instructions conflict;
- the task crosses multiple subsystems without a plan and ownership map;
- the work will continue across sessions or ownership changes;
- the agent is looping, repeating side effects, or consuming budget without new evidence;
- verification is unavailable or the consequence of an unverified change is high.

The response may be a smaller slice, a new index, a human question, a stronger retrieval pass, a higher-effort route, a checkpoint, or a handoff. More context is not always the answer.

## Failure modes to avoid

- **Whole-repository prompting:** context cost rises while relevant evidence becomes harder to notice.
- **The mega-index:** one map becomes a second unsearchable repository. Keep entries short and link outward.
- **Stale summaries:** a summary without freshness and source metadata becomes misleading project memory.
- **Unbounded retrieval:** adding more chunks hides the decision-relevant evidence and consumes recovery budget.
- **Transcript hoarding:** replaying every prior turn preserves noise instead of durable decisions.
- **Scope drift:** discoveries silently become new requirements. Record them as questions or follow-up tasks.
- **Authority blending:** project rules, task instructions, retrieved content, and tool output are treated as equally authoritative.
- **False completion:** a plausible answer or successful tool call is reported without a validator or observed outcome.

## Minimal large-project starter set

```text
README.md       Human orientation and quick start
docs/INDEX.md   Subsystem map and source-of-truth links
AGENTS.md       Stable repository instructions and safety boundaries
PRD.md          Product intent, requirements, and acceptance criteria
PLAN.md         Current bounded work and evidence
HANDOFF.md      Ownership or session transfer
STYLE.md        User-facing communication profile
docs/adr/       Durable architectural decisions
```

Not every project needs every file. Start with the map, rules, intent, current state, and verification path; add retrieval, memory, skills, and runtime state as the project’s size and lifetime require.

## Sources and further reading

- Anthropic, "Effective context engineering for AI agents," Sep 2025 — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — context rot, attention budget, progressive disclosure patterns.
- Anthropic, "Effective harnesses for long-running agents," Nov 2025 — https://www.anthropic.com/engineering/effective-harnesses-for-long-running-agents — initializer+coding-agent pattern, progress files for multi-session continuity.
- AGENTS.md official (Linux Foundation, 2026) — https://agents.md/ — nested file precedence in monorepos, community standard for agent instructions.
- Gemini CLI GitHub (Google, 2026) — https://github.com/google-gemini/gemini-cli — GEMINI.md context mechanism, nested directory support.

All links verified 2026-08-27.

## Related

[[03 Context Knowledge Memory/Context Engineering]] · [[03 Context Knowledge Memory/RAG]] · [[03 Context Knowledge Memory/Memory and Skills]] · [[03 Context Knowledge Memory/Project Initialization and Instruction Files]] · [[02 Agents and Harnesses/Planning State and Persistence]] · [[02 Agents and Harnesses/Agent Harness]] · [[12 Templates/Template Library]]

---

---

> **← [[03 Context Knowledge Memory/Vector Search and Embeddings|Vector Search and Embeddings]]** · **[[AI_Home|Home]]** · **[[03 Context Knowledge Memory/Project Initialization and Instruction Files|Project Initialization and Instruction Files]] →**
