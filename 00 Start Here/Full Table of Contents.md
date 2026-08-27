---
type: curriculum-index
layer: start-here
status: evergreen
maturity: established
aliases:
  - AI Engineering Table of Contents
  - AI Engineering Course Index
tags:
  - ai-engineering
  - curriculum
  - toc
  - map-of-content
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "00 Start Here/AI Engineering Curriculum.md"
next: "00 Start Here/The Gist of It.md"
summary: The course-first navigation index for the full AI Engineering vault, arranged as sequential units with linked sections and practice outcomes.
---

# AI Engineering — full table of contents

**Fast path:** [[00 Start Here/The Gist of It|The Gist of It]] collects every content note's plain-English gist in this exact order.

## Unit 1 — Models, inference, and local hardware (~8–10h)

**Question:** What can a model do, and what determines whether it can run well?

1. [[01 Foundations/What Is an LLM|LLMs and the model/application boundary]]
2. [[01 Foundations/Context Windows and Inference|Tokens, context windows, decoding, and inference]]
3. [[01 Foundations/Local AI Hardware and Inference|Local AI hardware: VRAM, unified memory, bandwidth, quantization, and model fit]]
4. [[01 Foundations/Structured Outputs and Tool Calling|Structured outputs and function calling]]

Outcome: estimate whether a workload fits a machine, and turn uncertain model output into validated program input.

## Unit 2 — Instructions and capabilities (~10–12h)

**Question:** How does a model receive a task and gain controlled ways to act?

1. [[03 Context Knowledge Memory/Prompting for Agents|Prompting for agents]]
2. [[09 Playbooks/Prompt Template|Prompt template]]
3. [[03 Context Knowledge Memory/Project Initialization and Instruction Files|Project initialization: PRDs, repository instructions, and project context files]]
4. [[03 Context Knowledge Memory/Behavior and Communication Controls|Effort levels, writing style, and response contracts]]
5. [[01 Foundations/Structured Outputs and Tool Calling|Tool calling]]
6. [[03 Context Knowledge Memory/Skills, Tools, and Capability Management|Skills, tool discovery, and capability management]]
7. [[05 Protocols and Tools/MCP|Model Context Protocol]]
8. [[05 Protocols and Tools/Agent Protocols|ACP, A2A, AG-UI, and A2UI]]

Outcome: design a minimal, typed, permission-aware capability surface rather than a giant prompt with every tool preloaded.

## Unit 3 — Knowledge, retrieval, memory, and context (~10–12h)

**Question:** What should the system know now, remember later, or retrieve on demand?

1. [[03 Context Knowledge Memory/Context Engineering|Context engineering]]
2. [[03 Context Knowledge Memory/Large Project Navigation and Context Scaling|Large-project navigation and context scaling]]
3. [[09 Playbooks/Context Checklist|Context checklist]]
4. [[03 Context Knowledge Memory/RAG|RAG and retrieval]]
5. [[09 Playbooks/RAG Design Worksheet|RAG design worksheet]]
6. [[03 Context Knowledge Memory/Memory and Skills|Memory types and safeguards]]

Outcome: distinguish current facts (retrieval), durable facts (memory), and procedures (skills), while defending against stale or unsafe context.

## Unit 4 — Single agents and safe execution (~10–12h)

**Question:** What turns a model call into a bounded, recoverable agent?

1. [[02 Agents and Harnesses/What Is an Agent|Agent loops and stopping conditions]]
2. [[02 Agents and Harnesses/Agent Harness|Harness design]]
3. [[02 Agents and Harnesses/Planning State and Persistence|Planning, checkpoints, and durable state]]
4. [[02 Agents and Harnesses/Sandboxes and Execution Planes|Execution planes and sandboxing]]
5. [[02 Agents and Harnesses/Sandboxing Infrastructure|Process, container, gVisor, microVM, and VM trade-offs]]
6. [[02 Agents and Harnesses/Computer-Use and Browser Agents|Computer-use and browser agents]]

Outcome: build an agent that can observe, act, recover, and stop without granting it ambient authority.

## Unit 5 — Voice and multimodal agents (~8–10h)

**Question:** How do audio, images, and interfaces change the agent architecture?

1. [[02 Agents and Harnesses/Voice and Audio Agents|Voice and audio agents]]
2. [[02 Agents and Harnesses/Vision and Multimodal Input Engineering|Vision and multimodal input]]
3. [[02 Agents and Harnesses/Computer-Use and Browser Agents|Computer-use interaction]]

Outcome: separate capture, interpretation, interaction, and action; select the right confirmation and isolation boundary for each.

## Unit 6 — Workflows, orchestration, and teams (~10–12h)

**Question:** When should work be deterministic, agentic, parallel, or delegated?

1. [[04 Workflows and Orchestration/Workflow Patterns|Workflow patterns]]
2. [[04 Workflows and Orchestration/Orchestration Hub|Orchestration and durable control flow]]
3. [[04 Workflows and Orchestration/Multi-Agent Systems|Multi-agent systems]]
4. [[04 Workflows and Orchestration/Agent Democracies|Debate, voting, reputation, and governance]]
5. [[11 Glossary and Sources/Pattern Catalog|Agent pattern catalog]]
6. [[09 Playbooks/Mode and Topology Selector|Mode and topology selector]]

Outcome: choose the simplest topology that has measurable upside over one well-instrumented agent.

## Unit 7 — Resource engineering (~8–10h)

**Question:** How do you make an agent responsive and economically bounded?

1. [[07 Operations and Economics/Local Subscription API|Local, subscription, API, and hybrid deployment]]
2. [[07 Operations and Economics/Latency and Cost Engineering|Latency, caching, routing, and cost budgets]]
3. [[01 Foundations/Local AI Hardware and Inference|Hardware and inference constraints]]

Outcome: reason from a task’s quality target to model tier, tokens, calls, cacheable context, and resource budget.

## Unit 8 — Evaluation, observability, and AgentOps (~10–12h)

**Question:** How do you prove an agent works, diagnose failures, and ship safer changes?

1. [[06 Reliability and Security/Reliability Evals and Observability|Reliability foundations]]
2. [[06 Reliability and Security/Evaluation Engineering|Evaluation engineering]]
3. [[06 Reliability and Security/Observability|Logs, metrics, traces, and replay]]
4. [[07 Operations and Economics/Deployment and AgentOps|Deployment and AgentOps]]
5. [[09 Playbooks/Evaluation and Security Review|Evaluation and security review]]

Outcome: version the agent’s model, prompt, skills, tools, retrieval, policy, and tests; measure trajectories as well as final answers.

## Unit 9 — Security, oversight, and adaptation (~10–12h)

**Question:** Who has authority, what can fail, and when should the model itself be adapted?

1. [[06 Reliability and Security/Security and Jailbreaking|Security and jailbreak resistance]]
2. [[06 Reliability and Security/AI Fingerprints and Detection|AI fingerprints and generated-text detection]]
3. [[06 Reliability and Security/Defensive Red-Team Labs|Defensive red-team labs]]
4. [[06 Reliability and Security/Human Oversight and Trust Engineering|Autonomy, approvals, and trust calibration]]
5. [[01 Foundations/Fine-Tuning Decision Framework|Prompting vs RAG vs tools vs fine-tuning]]

Outcome: place approvals where errors are consequential and choose fine-tuning only for stable behavioral gaps.

## Unit 10 — Capstone and continuing reference (~12–15h)

1. [[09 Playbooks/Learning Projects|Learning projects and capstone]]
2. [[08 Tool Landscape/Tool Landscape Hub|Tool landscape]]
3. [[11 Glossary and Sources/Glossary|Glossary]] · [[11 Glossary and Sources/Sources|Sources]] · [[11 Glossary and Sources/Tool Comparison Index|Tool comparison index]]
4. [[10 Maps/AI Engineering Atlas|AI Engineering Atlas]]
5. [[12 Templates/Template Library|Template library]]

Outcome: deliver a bounded research or automation agent with evidence, sandboxing, structured outputs, approvals, traces, evals, and a rollback story.

## Templates

The [[12 Templates/Template Library|AI Engineering template library]] collects copyable templates for PRDs, repository instructions, plans, handoffs, style profiles, prompts, context packets, skills, tools, agents, workflows, evaluations, security reviews, operations, and adaptation decisions.

---

> **← [[00 Start Here/AI Engineering Curriculum|AI Engineering Curriculum]]** · **[[AI_Home|Home]]** · **[[00 Start Here/The Gist of It|The Gist of It]] →**
