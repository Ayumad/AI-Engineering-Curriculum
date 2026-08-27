---
type: guide
layer: start-here
status: evergreen
maturity: established
aliases:
  - Gist Collection
  - All the Gists
tags:
  - ai-engineering
  - gist
  - digest
  - reference
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "00 Start Here/Full Table of Contents.md"
next: "00 Start Here/How to Use This Vault.md"
summary: One-paragraph plain-English digest of every content note in the curriculum, in table-of-contents order.
---

# The gist of it

Every content note in the curriculum, one plain-English paragraph each, in the same order as the [[00 Start Here/Full Table of Contents|table of contents]].

## Unit 1 — Models, inference, and local hardware (~8–10h)

1. **[[01 Foundations/What Is an LLM|LLMs and the model/application boundary]]**
   > An LLM is autocomplete at scale. It predicts the next word based on patterns from training data. It doesn't look things up or take actions—those are engineering problems solved around the model. This note covers tokenization, embeddings, model families, and what isn't built in.

2. **[[01 Foundations/Context Windows and Inference|Tokens, context windows, decoding, and inference]]**
   > The context window is a fixed-size stack of papers the model can read at once. Once it's full, older material must be dropped or summarized. More context isn't automatically better—irrelevant material wastes space and dilutes the answer. This note explains how the window works and how to manage it.

3. **[[01 Foundations/Local AI Hardware and Inference|Local AI hardware: VRAM, unified memory, bandwidth, quantization, and model fit]]**
   > Running a model locally means your machine needs enough memory to hold it and enough bandwidth to read it for every word it generates. A setup that barely loads a model may still be too slow once you add context, a vision encoder, or a second user. This note walks through memory, bandwidth, and hardware trade-offs.

4. **[[01 Foundations/Structured Outputs and Tool Calling|Structured outputs and function calling]]**
   > Models produce freeform text, but software needs structured data. Structured outputs force a model to fill out a form instead of writing an essay. Tool calling lets it request specific operations—databases, APIs, other software. Together they bridge the gap between language and code.

## Unit 2 — Instructions and capabilities (~10–12h)

1. **[[03 Context Knowledge Memory/Prompting for Agents|Prompting for agents]]**
   > The best agent prompts describe the outcome and the guardrails, not every step. A rigid script breaks the moment something unexpected happens. This note lays out a goal-constraint-definition-verification framework for writing prompts that work even when the agent takes a different path.

2. **[[03 Context Knowledge Memory/Project Initialization and Instruction Files|Project initialization: PRDs, repository instructions, and project context files]]**
   > Before an agent changes a project, hand it a small, inspectable context package: a PRD for what to build, repo instructions for how to work, and verifiable facts for done. This note covers the artifact map, a safe initialization sequence, and templates for PRDs and instruction files.

3. **[[03 Context Knowledge Memory/Behavior and Communication Controls|Effort levels, writing style, and response contracts]]**
   > An agent has two independent jobs: reach a good decision and explain it well. Configure them separately — deep thinking doesn't require a long answer, and polished prose doesn't prove the work is correct. This note covers the control stack, effort levels, provider-specific routing, and output contracts.

4. **[[03 Context Knowledge Memory/Skills, Tools, and Capability Management|Skills, tool discovery, and capability management]]**
   > Tools are raw instruments. Skills are the procedures for using them well. A capability system decides which ones an agent can access and when. Keeping these three layers distinct makes it safe to add tools, update skills, and control permissions without breaking the rest. This note covers registries, selection policy, and lifecycle management.

5. **[[05 Protocols and Tools/MCP|Model Context Protocol]]**
   > MCP is the standard plug that connects an AI application to external tools and data. It defines three primitives — tools, resources, and prompts — and handles discovery, invocation, and results. Without it, every tool integration is custom-built. This note covers the architecture, transports, auth flow, and error handling.

6. **[[05 Protocols and Tools/Agent Protocols|ACP, A2A, AG-UI, and A2UI]]**
   > AI systems talk to editors, other agents, user interfaces, and tools — each boundary needs its own protocol. The industry has built a family of five: ACP, A2A, AG-UI, A2UI, and MCP. Understanding which protocol fits which boundary is key to building secure, composable systems.

## Unit 3 — Knowledge, retrieval, memory, and context (~10–12h)

1. **[[03 Context Knowledge Memory/Context Engineering|Context engineering]]**
   > Context engineering is the practice of feeding an AI model exactly what it needs to know at the right moment — picking files, decisions, and reminders instead of dumping the entire project on the model. It turns a capable but unfocused model into one that responds as if it's been on the project for months. This note covers the hierarchy of context sources, practical controls, and budgeting.

2. **[[03 Context Knowledge Memory/Large Project Navigation and Context Scaling|Large-project navigation and context scaling]]**
   > When a project exceeds one context window, don't load everything — build a map, pick the right neighborhood, and work in bounded slices. Progressive disclosure loads only the layers needed for the next decision, keeping evidence traceable and the context lean. This note covers the operating loop, navigation layers, and multi-repo patterns.

3. **[[09 Playbooks/Context Checklist|Context checklist]]**
   > Bad context is why agents waste turns: the model guesses, fills blanks, and sometimes gets them wrong. This checklist catches the usual failures — missing files, unclear authority, stale data, missing stop rules — before they cost you time or produce garbage. Use it per message, per task, or per session.

4. **[[03 Context Knowledge Memory/RAG|RAG and retrieval]]**
   > RAG searches a document collection at answer time and hands the most relevant pieces to the model as evidence. It beats fine-tuning when knowledge is private, changes fast, or didn't exist at training time. This note covers the retrieval pipeline: chunking, embeddings, hybrid search, reranking, and deployment patterns.

5. **[[09 Playbooks/RAG Design Worksheet|RAG design worksheet]]**
   > RAG bolts real sources onto a model so answers come with evidence instead of guesses. This worksheet walks every design decision — ingestion, chunking, indexing, retrieval, evaluation — with blanks to fill in and a worked example to check against. Catch the gaps before they become bugs.

6. **[[03 Context Knowledge Memory/Memory and Skills|Memory types and safeguards]]**
   > An agent needs different kinds of memory: working memory for the current task, durable facts across sessions, and reusable procedures. This note maps those kinds and compares two memory architectures — Mem0 (passive extraction) and Letta (agentic self-editing) — plus backend selection and consolidation strategies.

## Unit 4 — Single agents and safe execution (~10–12h)

1. **[[02 Agents and Harnesses/What Is an Agent|Agent loops and stopping conditions]]**
   > An agent can break a task into steps, try different approaches, and keep going until it reaches a goal or hits a limit. Unlike a calculator, it can decide its own path. This note explains how that loop works, when it is the right tool for the job, and how to tell if an agent actually performed well or just sounded convincing.

2. **[[02 Agents and Harnesses/Agent Harness|Harness design]]**
   > The harness is the machinery that makes an agent useful and governable. It translates user intent into context, exposes tools, executes actions, manages state, and decides when to ask for help or stop. Two teams can use the same AI model and get wildly different results depending on how they build the harness around it. This note walks through each part of that machinery.

3. **[[02 Agents and Harnesses/Planning State and Persistence|Planning, checkpoints, and durable state]]**
   > Planning turns a broad request into observable work. State records what is known, what is complete, what failed, and what must happen next. Without a way to save their progress, a crash or interruption means losing everything an agent has figured out. This note explains how agents create plans, save their work at key moments, and recover when things go wrong.

4. **[[02 Agents and Harnesses/Sandboxes and Execution Planes|Execution planes and sandboxing]]**
   > Agents that can run code need a computer they can safely break. A sandbox is a disposable or strongly isolated execution environment with bounded filesystem, CPU, memory, packages, credentials, and network access. This note explains how to design that safe workspace, how to hand the agent only the secrets it needs for one task, and how to bring back only the results you trust.

5. **[[02 Agents and Harnesses/Sandboxing Infrastructure|Process, container, gVisor, microVM, and VM trade-offs]]**
   > Sandbox choice follows the threat model: processes suit trusted code, containers provide moderate isolation, and microVMs or VMs suit generated or arbitrary untrusted execution. This note compares the different types of isolation technology available: from lightweight process separation to full virtual machines. It also covers cloud services that rent you ready-made sandboxes.

6. **[[02 Agents and Harnesses/Computer-Use and Browser Agents|Computer-use and browser agents]]**
   > Computer-use agents operate an untrusted visual environment. Prefer semantic APIs, isolate sessions, ground actions in fresh UI state, and gate consequential clicks at the point of execution. This note explains how these agents work, what dangers to watch for, and how to keep them from causing harm.

## Unit 5 — Voice and multimodal agents (~8–10h)

1. **[[02 Agents and Harnesses/Voice and Audio Agents|Voice and audio agents]]**
   > Voice agents are real-time systems: capture, turn detection, transcription or audio reasoning, response generation, synthesis, interruption handling, and safe actions must work as one loop. This note explains the machinery that makes voice interactions feel smooth and natural, from latency budgets to barge-in handling.

2. **[[02 Agents and Harnesses/Vision and Multimodal Input Engineering|Vision and multimodal input]]**
   > Multimodal systems must preserve provenance and uncertainty across capture, extraction, reasoning, and action; an image is evidence, not an instruction or authority grant. This note covers how agents take in visual information, how to avoid being fooled by bad images or hidden tricks, and how to keep costs down when every image has a price tag.

## Unit 6 — Workflows, orchestration, and teams (~10–12h)

1. **[[04 Workflows and Orchestration/Workflow Patterns|Workflow patterns]]**
   > Workflow patterns are the standard shapes for arranging AI steps — sequential, parallel, routed, looping, and so on. Pick the simplest one that makes the work visible and correct. This note covers the main patterns, how to build each one, and where they break.

2. **[[04 Workflows and Orchestration/Multi-Agent Systems|Multi-agent systems]]**
   > Multiple agents beat a single one when specialization, parallelism, or independent critique matters more than coordination cost. A team of focused agents can outperform a solo generalist — but every handoff is a failure point. This note covers when a team helps, common structures, and the real risks.

3. **[[04 Workflows and Orchestration/Agent Democracies|Debate, voting, reputation, and governance]]**
   > Agent democracies let multiple AI agents independently propose, critique, and vote on a decision before acting — borrowing the structure of human committees and juries. Powerful for high-stakes choices where a single agent's answer is too risky. This note covers the governance mechanics and when the overhead pays off.

4. **[[09 Playbooks/Mode and Topology Selector|Mode and topology selector]]**
   > The first decision with an agent is how to work, not what to build: ask, plan, hand over authority, or run it across sessions. Then whether that's one agent, several in parallel, or a team with roles. This guide makes those choices concrete with tables, a worked example, and cost notes.

## Unit 7 — Resource engineering (~8–10h)

1. **[[07 Operations and Economics/Local Subscription API|Local, subscription, API, and hybrid deployment]]**
   > Running AI models has three main paths: self-host for privacy and control, subscribe for simplicity, or use APIs for scale. Each has different trade-offs in cost, control, and convenience. This guide walks through a decision sequence — from data sensitivity and concurrency needs to pricing and exit plans — with a comparison table covering local, subscription, API, and hybrid approaches. It includes a 2026 pricing snapshot, self-hosting crossover estimates, GDPR considerations for data residency, and a hybrid routing pattern that balances privacy, latency, and cost across providers.

2. **[[07 Operations and Economics/Latency and Cost Engineering|Latency, caching, routing, and cost budgets]]**
   > Latency and cost engineering treats response time and spending as explicit design constraints. An agent's wait time adds up from queueing, prompt construction, model decoding, tool calls, retries, and synthesis — and cost follows the same path. This note covers the controls that matter most: generating fewer tokens, caching stable prefixes, routing by difficulty, parallelizing independent work, and streaming. It walks through budget mechanics, a worked cost model showing 89% savings from prompt caching, gateway routing with LiteLLM and OpenRouter, batch inference discounts, and per-request token budgets that keep autonomous runs from spiraling.

## Unit 8 — Evaluation, observability, and AgentOps (~10–12h)

1. **[[06 Reliability and Security/Evaluation Engineering|Evaluation engineering]]**
   > Evaluation engineering tests the full agent trajectory instead of only the final answer. Like a trial period where you watch how an employee works, you check tool choices, context selection, error recovery, cost, and policy compliance. This note covers building versioned test suites, scoring at different layers, using LLM judges safely, and choosing between open-source and commercial eval frameworks.

2. **[[06 Reliability and Security/Observability|Logs, metrics, traces, and replay]]**
   > Observability records everything your agent does during a request, like a black box recorder on a plane. When something goes wrong, you replay the full trajectory: model calls, tool use, retrievals, retries, and approvals. This note covers the three signals (metrics, logs, traces), how to set alerts, build dashboards, and catch drift before it reaches users.

3. **[[07 Operations and Economics/Deployment and AgentOps|Deployment and AgentOps]]**
   > Deployment and AgentOps is DevOps adapted for probabilistic, tool-using systems. You version and deploy more than code: prompts, skills, tool schemas, retrieval indexes, and evaluation datasets each change real behavior. This guide covers the delivery loop from change to production, operational contracts that assign every run an owner and version, serving stacks compared by throughput, rate limiting patterns for five distinct 429 error types, incident response and rollback procedures, and multi-tenancy isolation with separate control planes and execution workers.

4. **[[09 Playbooks/Evaluation and Security Review|Evaluation and security review]]**
   > Before an AI system ships, it has to be checked for quality and for harm. This is that checklist: define success and failure, run test scenarios from easy to adversarial, verify tools and data access are locked down, and have an incident plan ready. The tool table says what you can automate, and the severity ratings say how fast to respond.

## Unit 9 — Security, oversight, and adaptation (~10–12h)

1. **[[06 Reliability and Security/Security and Jailbreaking|Security and jailbreak resistance]]**
   > Jailbreaking is lock-picking for AI. Attackers try to make models ignore their rules through fake personas, hidden instructions in documents, encoded payloads, or slow trust-building across turns. No single defense holds. This note catalogs the main attack families and the layered countermeasures: input scanning, output filtering, tool allowlists, sandboxing, and taint-aware context assembly.

2. **[[06 Reliability and Security/Defensive Red-Team Labs|Defensive red-team labs]]**
   > Defensive red-team labs are fire drills for AI security. You build a harmless test agent with fake tools, then run realistic attacks against it in a sandbox. This note provides seven lab cases: authority confusion, tool-output injection, memory poisoning, confused deputy, replay attacks, multimodal boundary testing, and cross-agent trust. Only test against toy harnesses or explicitly authorized systems.

3. **[[06 Reliability and Security/Human Oversight and Trust Engineering|Autonomy, approvals, and trust calibration]]**
   > AI agents need an autonomy ladder, like self-driving cars. The real question is what the agent may decide and execute without a person. Authority depends on error cost and evidence quality. This note covers the seven-level autonomy scale, how to separate proposal from execution, and how to build interfaces that keep human judgment at consequential boundaries.

4. **[[01 Foundations/Fine-Tuning Decision Framework|Prompting vs RAG vs tools vs fine-tuning]]**
   > When a model gives weak answers, the instinct is to retrain—but training is expensive and often unnecessary. Better prompts, retrieval, or a tool call usually solve it. This note walks through lighter options first, then fine-tuning as a last resort.

## Unit 10 — Capstone and continuing reference (~12–15h)

1. **[[09 Playbooks/Learning Projects|Learning projects and capstone]]**
   > Nine projects, roughly in difficulty order, that build real agent skills: structured output, tool use, memory, evaluation, multi-agent coordination. Each lists the time, difficulty, and the curriculum note behind it. Do them in order and you end with a governed, production-ready system instead of a demo.

## The rest of the tour

- **[[00 Start Here/AI Engineering Curriculum|AI Engineering Curriculum]]**
  > This curriculum teaches AI engineering as a hands-on craft. It starts with model fundamentals and moves through agents, safety, and production systems. Each unit pairs reading with a practical exercise, because real skill comes from building. You need basic Python and an API key. No ML background required.

- **[[03 Context Knowledge Memory/Vector Search and Embeddings|Vector Search and Embeddings]]**
  > Embeddings turn text into numerical coordinates where similar meanings sit close together. Vector databases search those coordinates at speed. Together they power RAG's retrieval step — but the real pitfalls lie in how embedders are trained, how ANN indexes trade recall for speed, and the gap between similarity and relevance. This note covers the machinery under the hood.

- **[[08 Tool Landscape/Coding Agent Profiles|Coding Agent Profiles]]**
  > AI coding agents range from terminal-first harnesses to cloud-delegated workstreams. This guide profiles the major players — Herdr, Hermes, DeepSeek Harness, Codex, Devin, Cursor, Claude Code, OpenCode, Pi, OpenHands, and Kimi Code — comparing their architecture, strengths, and trade-offs. A category map helps you match needs to candidate tools, and a context window comparison shows that harness quality is about context management, not raw token count.

- **[[08 Tool Landscape/Agent Runtimes and Frameworks|Agent Runtimes and Frameworks]]**
  > Agent runtimes and frameworks provide the scaffolding for chaining model calls, managing state, and recovering from failures. Frameworks compose model calls and tools; orchestration runtimes provide durable execution; agent platforms add user-facing control planes. This guide compares LangGraph, CrewAI, AutoGen/AG2, Haystack, and direct SDK approaches across learning curve, community, and production readiness — and helps you decide when a simple SDK call beats a full framework.

- **[[08 Tool Landscape/Infrastructure and Observability Tools|Infrastructure and Observability Tools]]**
  > Production agents need sandboxes for safe code execution, browser tools for web interaction, gateways for model routing, and observability stacks for tracing failures. This guide covers each category: execution and browser environments, model routing with LiteLLM and OpenRouter, self-hosted vs hosted observability, four-layer guardrails architectures, prompt management, and serving platforms from Replicate to Modal. Each section includes comparison tables and selection heuristics so you can pick the right pieces for your stack.

---

> **← [[00 Start Here/Full Table of Contents|Full Table of Contents]]** · **[[AI_Home|Home]]** · **[[00 Start Here/How to Use This Vault|How to Use This Vault]] →**
