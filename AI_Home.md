---
type: hub
layer: start-here
status: evergreen
maturity: established
aliases: [AI Engineering Home]
tags: [ai-engineering, map-of-content, start-here]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
next: "00 Start Here/AI Engineering Curriculum.md"
summary: "AI engineering is the discipline of turning a probabilistic model into a useful, safe, observable system. The model supplies reasoning; the surrounding system supplies..."
---


# AI Engineering

AI engineering is the discipline of turning a probabilistic model into a useful, safe, observable system. The model supplies reasoning; the surrounding system supplies context, tools, memory, permissions, execution, verification, and recovery.

## Start here

- **Guided tour:** start at [[00 Start Here/AI Engineering Curriculum|AI Engineering Curriculum]] and follow the **← Previous · Home · Next →** bar at the end of every note — all 63 notes in reading order, nothing skipped.
- [[00 Start Here/AI Engineering Curriculum|Follow the curriculum]] from first principles to production.
- [[00 Start Here/Full Table of Contents|Browse the full course table of contents]] by Unit and section.
- [[00 Start Here/How to Use This Vault|Use this vault]] as a course, reference wiki, or visual map.
- [[10 Maps/AI Engineering Atlas|Open the atlas]] for the whole field at a glance.
- [[00 Start Here/Conversation Coverage|Check conversation coverage]] against the original request.
- [[11 Glossary and Sources/Sources|Consult the source registry]] for verified papers, guides, and practitioner references.

## The stack in one sentence

`Model → harness → context + tools + memory → loop → workflow → multi-agent system → operations and governance`

## Core hubs

One-line summaries:

| Area | Hub | One-liner |
|---|---|---|
| Foundations | [[01 Foundations/Foundations Hub]] | Models, tokens, context windows, inference, structured output — the base layer everything else builds on. |
| Agents and harnesses | [[02 Agents and Harnesses/Agents and Harnesses Hub]] | Agent loops, harnesses, permissions, sandboxes — the runtime that wraps a model. |
| Context, knowledge, memory | [[03 Context Knowledge Memory/Context Engineering]] | What the model should know now — retrieval, memory, context compression, RAG. |
| Workflows and orchestration | [[04 Workflows and Orchestration/Orchestration Hub]] | Deterministic and non-deterministic workflows, multi-agent coordination, durable execution. |
| Protocols | [[05 Protocols and Tools/Protocols Hub]] | MCP, A2A, ACP — how agents talk to tools and each other. |
| Reliability and security | [[06 Reliability and Security/Reliability Evals and Observability]] | Eval frameworks, guardrails, red-teaming, observability, incident response. |
| Operations and economics | [[07 Operations and Economics/Operations and Economics Hub]] | Deployment, cost engineering, monitoring, rate limiting, production ops. |
| Tool landscape | [[08 Tool Landscape/Tool Landscape Hub]] | Survey of frameworks, SDKs, platforms, and serving options. |
| Playbooks | [[09 Playbooks/Playbooks Hub]] | Reusable decision frameworks — copy into a project and adapt. |
| Templates | [[12 Templates/Template Library]] | Fill-in templates for prompts, RAG worksheets, and project initialization. |
| Glossary and sources | [[11 Glossary and Sources/Glossary]] · [[11 Glossary and Sources/Sources]] | Definitions, acronyms, and verified source links. |

| Area | Hub |
|---|---|
| Foundations | [[01 Foundations/Foundations Hub]] |
| Agents and harnesses | [[02 Agents and Harnesses/Agents and Harnesses Hub]] |
| Context, knowledge, memory | [[03 Context Knowledge Memory/Context Engineering]] |
| Workflows and orchestration | [[04 Workflows and Orchestration/Orchestration Hub]] |
| Protocols | [[05 Protocols and Tools/Protocols Hub]] |
| Reliability and security | [[06 Reliability and Security/Reliability Evals and Observability]] |
| Operations and economics | [[07 Operations and Economics/Operations and Economics Hub]] |
| Tool landscape | [[08 Tool Landscape/Tool Landscape Hub]] |
| Playbooks | [[09 Playbooks/Playbooks Hub]] |
| Templates | [[12 Templates/Template Library]] |
| Glossary and sources | [[11 Glossary and Sources/Glossary]] · [[11 Glossary and Sources/Sources]] |

## Five ideas to retain

1. A chatbot answers once; an agent observes, decides, acts, and checks until a stopping condition. → [[02 Agents and Harnesses/Agents and Harnesses Hub]]
2. The harness often matters as much as the model: context, tools, permissions, state, retries, and verification determine practical behavior. → [[02 Agents and Harnesses/Agent Harness]]
3. Context engineering is deciding what the model should know now, not merely writing a clever prompt. → [[03 Context Knowledge Memory/Context Engineering]]
4. Multi-agent systems add coordination overhead; use them when specialization, isolation, or parallelism pays for that overhead. → [[04 Workflows and Orchestration/Multi-Agent Systems]]
5. Autonomy must be paired with least privilege, sandboxing, evaluation, and human approval at consequential boundaries. → [[06 Reliability and Security/Reliability Evals and Observability]]

## Current snapshot

## First 15 minutes

New to AI engineering? Start here:

1. **Min 0-3:** Read [[00 Start Here/AI Engineering Curriculum|the curriculum overview]] to understand the scope.
2. **Min 3-6:** Skim [[10 Maps/AI Engineering Atlas|the atlas]] to see the whole field at a glance.
3. **Min 6-9:** Open [[01 Foundations/Foundations Hub|Foundations]] — learn models, tokens, context, and structured output.
4. **Min 9-12:** Read [[02 Agents and Harnesses/Agents and Harnesses Hub|Agents and Harnesses]] — understand the loop, the harness, and permissions.
5. **Min 12-15:** Try [[09 Playbooks/Learning Projects|Learning Project #1]] (Structured Assistant) — hands-on with a schema, validation, and no side effects.

Product and protocol notes are dated snapshots verified on 2026-08-27. Evergreen concepts are intentionally separated from them so the vault can evolve.

---

---

> *← start of tour* · **[[AI_Home|Home]]** · **[[00 Start Here/AI Engineering Curriculum|AI Engineering Curriculum]] →**
