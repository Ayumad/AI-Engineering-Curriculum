---
type: concept
layer: context
status: evergreen
maturity: established
aliases: [Skill Management, Capability Management]
tags: [ai-engineering, skills, tools, permissions, context]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "03 Context Knowledge Memory/Memory and Skills.md"
next: "03 Context Knowledge Memory/RAG.md"
summary: "Capabilities should be discoverable, minimally loaded, permission-scoped, versioned, tested, and observable rather than placed wholesale in every model context."
---


# Skills, tools, and capability management

## Plain-English introduction

A toolbox is useful, but a skilled craftsperson knows not only *what* tools are in the box but *when* to use each one and *how* to use it safely. This note breaks that idea into layers: tools are the raw instruments, skills are the procedures for using them well, and a capability system is the management layer that decides which ones an agent can access and under what conditions. Keeping these layers distinct makes it easier to add new tools, update skills, and control permissions without breaking the whole setup.


Tools execute a typed operation. Skills teach an agent when and how to use one or more tools, with domain procedure, examples, scripts, and safeguards. A capability system is the registry, selection policy, permissions, lifecycle, and observability that make both safe at scale.

## Separate the layers

| Layer | Responsibility | Example |
|---|---|---|
| Tool | Atomic action with schema | `search(query)` |
| Skill | Reusable procedure | Research, cite, and summarize a source |
| Registry | Discovery and metadata | Name, owner, versions, risk, inputs |
| Policy | Authority and approval | Read only; confirm before sending |
| Harness | Selection and dispatch | Load skill, validate call, record result |

Do not preload a catalog of hundreds of descriptions. It crowds out task context, makes tool selection less reliable, and creates accidental authority. Route first, retrieve only the relevant skill/tool descriptions, then expose the smallest capability set required for the task.

## Skill definition

A **skill** is an on-demand package of instructions, examples, scripts, tools, and domain knowledge—procedural memory made loadable and versionable. For the full taxonomy of memory kinds (working, conversation, semantic, episodic, procedural) and architecture choices (Mem0 vs Letta), see [[03 Context Knowledge Memory/Memory and Skills]].

## Registry implementation

A minimal registry maps each capability to metadata the harness needs for selection, safety, and lifecycle:

```yaml
# Example: registry.yaml
tools:
  - id: web_search
    name: "Web Search"
    owner: platform
    version: "2.1.0"
    risk: low          # read-only, no side effects
    latency_p50_ms: 200
    cost_per_call: "$0.003"
    input_schema:
      type: object
      properties:
        query: { type: string, description: "Search query" }
        limit: { type: integer, default: 5 }
      required: [query]
    permissions: [network_egress]
    tags: [retrieval, web]

skills:
  - id: github-pr-workflow
    name: "GitHub PR Lifecycle"
    owner: platform
    version: "1.0.0"
    risk: medium        # creates branches, opens PRs
    tools_used: [web_search, terminal, git]
    permissions: [network_egress, file_write, git_push]
    tags: [github, code-review]
```

The harness loads this at startup, indexes by `id` and `tags`, and selects the smallest relevant set per task. Route first (classify the task), then inject only the matching tool/skill descriptions.

## Tool-description optimization

Tool descriptions are the interface between your capability and the model's selection logic. Schema wording directly affects selection accuracy:

- **Be specific in descriptions.** "Search the web for current information" is better than "Search." The model uses descriptions to decide when to call a tool—vague descriptions cause both false positives (calling when unnecessary) and false negatives (not calling when needed).
- **Name parameters clearly.** `query` is better than `q`; `max_results` is better than `n`. Each parameter description should be a complete sentence.
- **State what the tool does NOT do.** "Returns text only; does not follow links or render images" prevents misuse.
- **Token budget:** each tool definition costs ~735 input tokens. At 20 tools, that's ~14,700 tokens just for tool definitions—nearly 10% of a 128K context window. Keep descriptions concise.

**Dynamic tool injection:** analyze the user message, select 5–10 most relevant tools, inject only those. Anthropic recommends this over loading all 64 tools (their hard limit). This reduces model decision complexity and saves tokens. The MCP specification defines tools as model-controlled—the LLM discovers and invokes based on contextual understanding.

## Static vs dynamic toolsets

| Approach | When to use | Trade-off |
|---|---|---|
| **Static** (all tools always loaded) | Few tools (<10), simple routing, low token budget concern | Simpler implementation; higher token cost; more selection errors with many tools |
| **Dynamic** (inject per request) | Many tools (>15), complex routing, token-budget-sensitive | Better selection accuracy; requires a routing layer; adds latency for tool-set resolution |
| **Tiered** (core + on-demand) | Core tools always loaded; specialized tools loaded on classification | Balanced: fast for common tasks, complete for rare ones |

Production agents typically use 5–20 tools. Well-designed agents rarely need more than 15. If you find yourself needing more, consider combining related tools into a single dispatcher tool with an action enum (trades schema guidance quality for lower count).

## Versioning patterns

Capabilities change over time. Version them to avoid stale instructions in agent context:

1. **Semantic versioning:** `MAJOR.MINOR.PATCH`. Breaking changes (removed parameters, changed behavior) increment MAJOR. New optional parameters increment MINOR. Documentation-only changes increment PATCH.
2. **Compatibility matrix:** registry records which agent/runtime versions are compatible with each tool/skill version. The harness refuses to load incompatible capabilities.
3. **Deprecation cycle:** mark deprecated → warn on load → remove from registry after grace period. Never leave a deprecated tool description in the active toolset—models will still try to call it.
4. **A/B during rollout:** serve two versions of a tool description, route a percentage of traffic to each, compare selection accuracy and task success. This is the same evaluation discipline as prompt A/B testing.

## Capability lifecycle

1. Define purpose, owner, inputs/outputs, permissions, dependencies, and failure modes.
2. Package instructions, examples, and scripts as a versioned skill; keep secrets outside it.
3. Register a concise discovery description and risk classification.
4. Test schema validation, authorization, successful trajectories, failure recovery, and prompt-injection resistance.
5. Publish through staged rollout; trace use, cost, errors, and approval events.
6. Deprecate or revoke capabilities without leaving stale instructions in agent context.

## Selection and safety

Tool discovery is not permission. A discovered capability still needs identity, policy checks, input validation, rate/cost limits, and approval before consequential effects. Treat tool output and skill text as data: external pages can contain instructions that try to redirect the agent.

Useful registry metadata includes required authorization, side-effect level, expected latency/cost, data classification, idempotency behavior, and compatible agent/runtime versions. A mature system can route a trivial task to deterministic code, a local model, or a frontier model—and load the matching capability only when needed.

## Sources and further reading

- MCP Tools Specification (Model Context Protocol, Jun 2025) — https://modelcontextprotocol.io/specification/2025-06-18/server/tools — tool model: name, description, inputSchema, annotations, structured output.
- "Anthropic Tool Use Limits 2026" (AI Prompts Hub, 2026) — https://aipromptshub.co/limits/anthropic-tool-use-limits — 64-tool hard limit, token cost per definition, dynamic injection recommendation.
- Anthropic, "Effective context engineering for AI agents," Sep 2025 — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — tool curation, minimal viable toolset, ~735 tokens per tool definition.

All links verified 2026-08-27.

## Related

[[03 Context Knowledge Memory/Memory and Skills]] · [[05 Protocols and Tools/MCP]] · [[02 Agents and Harnesses/Agent Harness]] · [[06 Reliability and Security/Evaluation Engineering]]

---

---

> **← [[03 Context Knowledge Memory/Memory and Skills|Memory and Skills]]** · **[[AI_Home|Home]]** · **[[03 Context Knowledge Memory/RAG|RAG]] →**
