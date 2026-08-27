---
type: concept
layer: orchestration
status: evergreen
maturity: emerging
aliases: [Multi-Agent Architecture]
tags: [ai-engineering, multi-agent, orchestration]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "04 Workflows and Orchestration/Workflow Patterns.md"
next: "04 Workflows and Orchestration/Agent Democracies.md"
summary: "Multiple agents are useful when specialization, isolation, parallelism, or independent critique outweighs communication and coordination cost."
---


# Multi-agent systems

> [!summary] The gist
> Multiple agents beat a single one when specialization, parallelism, or independent critique matters more than coordination cost. A team of focused agents can outperform a solo generalist — but every handoff is a failure point. This note covers when a team helps, common structures, and the real risks.

---

## Common topologies

- **Supervisor → workers:** one coordinator delegates bounded tasks.
- **Router → specialists:** classify once, then hand off to a domain agent.
- **Handoffs:** an agent transfers ownership with a typed context package.
- **Hierarchical team:** managers coordinate subteams.
- **Swarm:** peers discover and delegate dynamically.
- **Blackboard:** agents publish artifacts to shared state rather than chatting endlessly.
- **Debate or jury:** independent answers are compared, criticized, or voted on.

## Single versus multiple

Start with one agent. Add dynamic skills when the problem is capability loading rather than coordination. Add subagents for bounded parallel work or isolation. Use a full team only when roles, contracts, budgets, and failure ownership are explicit.

Every handoff should include goal, evidence, assumptions, open questions, output schema, and authority. Never treat another agent’s text as automatically trusted.

See [[04 Workflows and Orchestration/Agent Democracies]] and [[09 Playbooks/Mode and Topology Selector]].

## Practical rule

Use a team only to gain specialization, isolation, or independent parallelism. Give workers bounded tools and typed outputs, and make the supervisor accountable for integration, cost, and stopping. Compare the system with a strong single-agent baseline on quality, latency, cost, and safety.

## Framework topology coverage

| Topology | LangGraph | CrewAI | OpenAI Agents SDK | AutoGen/MS AF | Mastra |
|---|---|---|---|---|---|
| Supervisor → workers | ✅ graph of sub-graphs | ✅ crew with manager | ✅ orchestrator agent | ✅ GroupChat manager | ✅ agent delegation |
| Router → specialists | ✅ conditional edges | ✅ routing tasks | ✅ handoffs | ✅ selector | ✅ conditional routing |
| Handoffs | ✅ dynamic graph edges | ✅ task delegation | ✅ first-class handoff primitive | ✅ handoff messages | ✅ agent-to-agent calls |
| Hierarchical team | ✅ nested sub-graphs | ✅ manager crews | ✅ nested handoffs | ✅ nested GroupChat | ❌ not built-in |
| Swarm | ✅ dynamic routing | ❌ not built-in | ✅ swarm handoff | ✅ swarm mode | ❌ not built-in |
| Debate/jury | ✅ custom graph | ❌ not built-in | ❌ not built-in | ✅ GroupChat debate | ❌ not built-in |

## Failure modes in detail

- **Agent drift**: Each subagent has its own prompt, tool set, and context window. Without shared schemas and periodic alignment checks, agents can diverge — producing plans that conflict or contradict each other. Detection: compare reasoning traces across agents; flag when tool call patterns diverge from baseline.
- **Coordination bugs**: Handoffs are the most fragile point. If agent A's output schema doesn't match agent B's expected input, the pipeline silently degrades. Mitigation: typed output contracts with validation at each boundary; integration tests that exercise the full multi-agent path.
- **Shared-state contention**: When multiple agents read/write shared state (blackboard, shared memory), race conditions and stale reads are common. Mitigation: append-only shared state with versioning; read-write locks; or avoid shared state entirely by using message-passing.
- **Cost amplification**: Multi-agent systems consume tokens across all agents. A 3-agent pipeline at 5k tokens each per iteration hits 15k tokens per cycle. Budget per-agent and per-run; detect runaway costs early.
- **Lost context across handoffs**: Evidence, assumptions, and open questions must travel with every handoff. Without structured context packages, downstream agents make decisions on incomplete information (OpenAI 2025).

## Debate/jury as a pattern

The debate or jury topology (independent answers compared, criticized, or voted on) is a common multi-agent pattern. For governance-oriented implementations of this topology — with proposers, critics, quorum rules, reputation tracking, and formal voting mechanics — see [[04 Workflows and Orchestration/Agent Democracies]].

## Evidence and trade-offs

- **Anthropic's research system (Jun 2025)** is an orchestrator-worker design: a lead agent decomposes the query, parallel subagents explore (often spending tens of thousands of tokens each), and each returns a condensed summary (~1–2k tokens) that the lead synthesizes with citations. Anthropic reports substantial improvement over single-agent on complex research tasks, while being explicit that multi-agent engineering overhead and new failure modes (agent drift, coordination bugs) are real costs.
- **Sampling and voting (Song et al. 2024):** scaling up the *number of agents* on an inference task improves accuracy, but gains are asymptotic and effectiveness depends on task difficulty—cheap parallelism is not a substitute for design.
- **Handoffs carry risk:** pass goal, evidence, assumptions, open questions, output schema, and authority; do not trust another agent's text implicitly (OpenAI 2025).

## Sources and further reading

- Anthropic, "How we built our multi-agent research system," Jun 13, 2025 — https://www.anthropic.com/engineering/multi-agent-research-system
- Anthropic, "Building effective agents," Dec 19, 2024 — https://www.anthropic.com/engineering/building-effective-agents — the orchestrator-workers pattern.
- Song et al., "More Agents Is All You Need," 2024 — https://arxiv.org/abs/2402.05120
- OpenAI, "A practical guide to building agents," 2025 — https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf

All links verified 2026-08-27.

---

> **← [[04 Workflows and Orchestration/Workflow Patterns|Workflow Patterns]]** · **[[AI_Home|Home]]** · **[[04 Workflows and Orchestration/Agent Democracies|Agent Democracies]] →**
