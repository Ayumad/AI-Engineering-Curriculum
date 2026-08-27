---
type: curriculum
layer: start-here
status: evergreen
maturity: established
aliases: [AI Engineering Course, Zero to Production]
tags: [ai-engineering, curriculum, learning-path]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "AI_Home.md"
next: "00 Start Here/Full Table of Contents.md"
summary: "Each stage has a question, a set of notes, and a small exercise. Do not skip the exercises: agent engineering is learned by observing loops and failure modes, not only..."
---


# AI Engineering from zero to production

**Prerequisites:** Basic Python, familiarity with APIs/HTTP, and access to at least one LLM API key (OpenAI, Anthropic, or a local model via Ollama). No prior ML experience required — the curriculum builds from first principles.

This is the teaching path; [[00 Start Here/Full Table of Contents|the full table of contents]] is the complete clickable index. Each Unit builds a capability and includes an exercise. Do not skip the exercises: AI engineering is learned by observing trajectories and failure modes, not only by reading definitions.

## Unit 1 — Models, inference, and hardware (~8–10h)

Read [[01 Foundations/What Is an LLM]], [[01 Foundations/Context Windows and Inference]], [[01 Foundations/Local AI Hardware and Inference]], and [[01 Foundations/Structured Outputs and Tool Calling]].

Exercise: estimate the memory needs of a 14B and 32B model at two quantizations, then compare free-text output with a strict JSON schema.
> **Skip if:** You already know VRAM math and quantization trade-offs well; jump to Unit 2.

## Unit 2 — Instructions and capabilities (~10–12h)

Read [[03 Context Knowledge Memory/Prompting for Agents]], [[03 Context Knowledge Memory/Project Initialization and Instruction Files]], [[03 Context Knowledge Memory/Behavior and Communication Controls]], [[03 Context Knowledge Memory/Skills, Tools, and Capability Management]], and [[05 Protocols and Tools/MCP]]. Use [[09 Playbooks/Prompt Template]].

Exercise: initialize a small project with a PRD, repository instruction file, and behavior/style profile; then design one typed read-only tool and one skill that teaches when to use it, stating permissions, effort level, output contract, and stop conditions.

## Unit 3 — Context, retrieval, and memory (~10–12h)

Read [[03 Context Knowledge Memory/Context Engineering]], [[03 Context Knowledge Memory/Large Project Navigation and Context Scaling]], [[03 Context Knowledge Memory/RAG]], and [[03 Context Knowledge Memory/Memory and Skills]]. Use [[09 Playbooks/Context Checklist]] and [[09 Playbooks/RAG Design Worksheet]].

Exercise: build a one-page project map and targeted context packet, then create a tiny retrieval set, inspect source coverage and a wrong chunk, and decide whether each durable item belongs in RAG, memory, or a skill.

## Unit 4 — Agents and execution (~10–12h)

Read [[02 Agents and Harnesses/What Is an Agent]], [[02 Agents and Harnesses/Agent Harness]], [[02 Agents and Harnesses/Planning State and Persistence]], [[02 Agents and Harnesses/Sandboxing Infrastructure]], and [[02 Agents and Harnesses/Computer-Use and Browser Agents]].

Exercise: implement or diagram `observe → decide → act → observe` around a read-only calculator, then add an approval gate and a disposable execution boundary.

## Unit 5 — Multimodality (~8–10h)

Read [[02 Agents and Harnesses/Voice and Audio Agents]] and [[02 Agents and Harnesses/Vision and Multimodal Input Engineering]].

Exercise: map a voice or screenshot task into capture, interpretation, verification, and action; mark where noisy input must become a confirmation.
> **Skip if:** Your agents are text-only and will remain so; voice and vision can be deferred.

## Unit 6 — Workflows and agent teams (~10–12h)

Read [[04 Workflows and Orchestration/Workflow Patterns]], [[04 Workflows and Orchestration/Orchestration Hub]], [[04 Workflows and Orchestration/Multi-Agent Systems]], and [[04 Workflows and Orchestration/Agent Democracies]]. Use [[09 Playbooks/Mode and Topology Selector]].

Exercise: express one task as a deterministic workflow, one bounded agent loop, and a supervisor-plus-specialists team; explain why the simplest adequate version wins.

## Unit 7 — Protocols and ecosystem (~8–10h)

Read [[05 Protocols and Tools/Agent Protocols]] and [[08 Tool Landscape/Tool Landscape Hub]].

Exercise: classify an integration as model, tool, skill, MCP server, agent, client, protocol, or orchestration runtime; identify the identity and permission boundary.
> **Skip if:** You are building a single-agent system without external tool servers; revisit when adding integrations.

## Unit 8 — Resource engineering (~8–10h)

Read [[07 Operations and Economics/Local Subscription API]] and [[07 Operations and Economics/Latency and Cost Engineering]].

Exercise: give an agent a token, tool-call, runtime, subagent, and dollar budget; make a routing rule that sends easy work to cheaper execution.
> **Skip if:** You are prototyping on a single API key with no cost concerns yet; return before going to production.

## Unit 9 — Evaluation, operations, and governance (~10–12h)

Read [[06 Reliability and Security/Evaluation Engineering]], [[06 Reliability and Security/Observability]], [[07 Operations and Economics/Deployment and AgentOps]], [[06 Reliability and Security/Security and Jailbreaking]], and [[06 Reliability and Security/Human Oversight and Trust Engineering]].

Exercise: create five trajectory tests, one prompt-injection test, a trace schema, a staged rollout, and a human approval policy for one consequential action.

## Unit 10 — Adaptation and capstone (~12–15h)

Read [[01 Foundations/Fine-Tuning Decision Framework]], then use [[09 Playbooks/Learning Projects]], [[11 Glossary and Sources/Pattern Catalog]], and [[12 Templates/Template Library]].

Build a bounded research or automation agent with evidence, structured outputs, capability scoping, a disposable sandbox, approvals, traces, evaluation cases, cost limits, and a written rollback/incident response plan. Use the template library to document each artifact.

---

---

> **← [[AI_Home|AI_Home]]** · **[[AI_Home|Home]]** · **[[00 Start Here/Full Table of Contents|Full Table of Contents]] →**
