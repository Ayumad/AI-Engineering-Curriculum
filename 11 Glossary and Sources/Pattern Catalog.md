---
type: index
layer: glossary
status: evergreen
maturity: established
aliases: [Agent Pattern Catalog]
tags: [ai-engineering, patterns, reference]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "11 Glossary and Sources/Glossary.md"
next: "11 Glossary and Sources/Acronyms.md"
summary: "02 Agents and Harnesses/What Is an Agent · 02 Agents and Harnesses/Agent Harness · ReAct · plannerexecutor · criticreviewer · humaninloop · skill loading · checkpoint/..."
---


# Agent pattern catalog

## Plain-English introduction

When you build an AI agent, you face a design choice: how should it think, plan, and act? A pattern is a proven recipe for arranging those pieces. This catalog collects the most useful ones—like having a toolbox of blueprints—and helps you pick the right recipe based on your task's complexity, how much verification you need, and your budget.

Choose a pattern from the task's uncertainty, decomposability, verification method, and cost—not from novelty. Every pattern needs a stop condition, failure behavior, ownership of state, and an evaluation strategy.

| Pattern | Shape | Use when | Primary failure mode |
|---|---|---|---|
| Single call | model → answer | Fixed transformation | Unsupported confidence |
| Prompt chain | A → B → C | Known stages | Brittle handoff / latency |
| Router | classify → specialist | Clear task classes | Misrouting |
| Parallel fan-out | A → [B,C,D] | Independent work | Cost and merge conflict |
| Map-reduce | map items → combine | Many similar records | Weak aggregation |
| ReAct | reason ↔ act | Tool-driven uncertainty | Drift or repeated action |
| Planner-executor | plan → execute | Long, decomposable work | Stale plan |
| Evaluator-optimizer | draft ↔ critic | Checkable quality | Infinite loop / correlated judge |
| Supervisor-workers | manager → specialists | Real specialization | Coordination overhead |
| Handoff | agent → agent | Conversation ownership changes | Lost context / authority confusion |
| Blackboard | agents ↔ shared state | Many collaborators | Stale/conflicting state |
| Debate or voting | candidates → judge/vote | Independent views add value | False consensus |
| Human gate | agent → approval → action | Consequential execution | Friction or rubber-stamping |
| Skill/tool search | task → retrieve capability | Large catalog | Discovery mistaken for permission |
| Agentic RAG | search ↔ evaluate ↔ search | Retrieval uncertainty | Endless search / weak evidence |

For each selected pattern, document: the problem, topology, input/output contract, when not to use it, permissions, costs, stop condition, expected failures, and evaluation cases. See [[04 Workflows and Orchestration/Workflow Patterns]] and [[09 Playbooks/Mode and Topology Selector]].

> **Cross-reference:** Workflow patterns are detailed in [[04 Workflows and Orchestration/Workflow Patterns]] — this catalog is the reference index (no duplication of pattern details).

## Agent patterns

[[02 Agents and Harnesses/What Is an Agent]] · [[02 Agents and Harnesses/Agent Harness]] · ReAct · planner-executor · critic-reviewer · human-in-loop · skill loading · checkpoint/resume.

## Workflow patterns

[[04 Workflows and Orchestration/Workflow Patterns]] · sequence · router · branch · parallel fan-out/fan-in · map-reduce · evaluator-optimizer · event-driven · durable workflow.

## Multi-agent patterns

[[04 Workflows and Orchestration/Multi-Agent Systems]] · supervisor-workers · specialist handoff · hierarchy · swarm · blackboard · debate · jury · quorum.

## Knowledge patterns

[[03 Context Knowledge Memory/RAG]] · semantic retrieval · BM25 · hybrid retrieval · reranking · query decomposition · agentic RAG · knowledge graph.

## Safety patterns

[[06 Reliability and Security/Security and Jailbreaking]] · least privilege · approval gate · sandbox · provenance/taint labels · credential broker · kill switch · audit trail.

## Provenance and further reading

| Pattern | Canonical reference |
|---|---|
| Single call · prompt chain · router · parallel fan-out · evaluator-optimizer · supervisor-workers | Anthropic, "Building effective agents" (Dec 2024) — https://www.anthropic.com/engineering/building-effective-agents |
| ReAct | Yao et al., 2022 — https://arxiv.org/abs/2210.03629 |
| Planner-executor | Weng, "LLM Powered Autonomous Agents" (2023) — https://lilianweng.github.io/posts/2023-06-23-agent/ — planning and reflection lineage |
| Debate or voting | Song et al., "More Agents Is All You Need" (2024) — https://arxiv.org/abs/2402.05120 |
| Human gate · handoff | OpenAI, "A practical guide to building agents" (2025) — https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf — guardrails and handoffs |
| Agentic RAG | Lewis et al. (2020) — https://arxiv.org/abs/2005.11401; Anthropic, "Introducing Contextual Retrieval" (2024) — https://www.anthropic.com/engineering/contextual-retrieval |
| Skill/tool search | Anthropic, "Effective context engineering for AI agents" (2025) — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — minimal viable tool sets |

All links verified 2026-08-27.

---

---

> **← [[11 Glossary and Sources/Glossary|Glossary]]** · **[[AI_Home|Home]]** · **[[11 Glossary and Sources/Acronyms|Acronyms]] →**
