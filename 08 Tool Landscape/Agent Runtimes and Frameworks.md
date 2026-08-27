---
type: index
layer: landscape
status: current-snapshot
maturity: emerging
aliases: [Agent Frameworks]
tags: [ai-engineering, frameworks, orchestration]
last_verified: 2026-08-27
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "08 Tool Landscape/Coding Agent Profiles.md"
next: "08 Tool Landscape/Infrastructure and Observability Tools.md"
summary: "These tools solve different problems. Frameworks compose model calls and tools; orchestration runtimes provide durable execution; agent platforms add userfacing contro..."
---


# Agent runtimes and frameworks

These tools solve different problems. Frameworks compose model calls and tools; orchestration runtimes provide durable execution; agent platforms add user-facing control planes.

| Tool | What to learn from it | Typical fit |
|---|---|---|
| LangGraph | Explicit graphs, state, checkpoints, human-in-loop | Stateful agent workflows |
| OpenAI Agents SDK | Harness primitives, tools, handoffs, tracing | Hosted or custom OpenAI-agent applications |
| LangChain | Model/tool abstractions and integrations | Application composition and provider breadth |
| PydanticAI | Typed Python agents and validated outputs | Type-safe application agents |
| Mastra | TypeScript agent/workflow primitives | JS/TS product teams |
| Temporal | Durable workflows, retries, timers, versioning | Long-running reliable operations |
| OpenHands SDK | Composable coding agents with execution surfaces | Developer-agent platforms |
| CrewAI | Role-based orchestration (Agents/Tasks/Crews/Flows) | Fast prototyping (30–60 min) |
| AutoGen/AG2 (MS Agent Framework) | Multi-agent conversations, merged with Semantic Kernel | Enterprise multi-language (C#, Python, Java), Azure |
| Semantic Kernel | Microsoft's agent SDK, plugin model, planners | .NET/Java shops, Azure-native |
| Haystack | Search and RAG pipelines, hybrid retrieval | Document ingestion and retrieval quality |

### Comparison dimensions

| Dimension | LangGraph | CrewAI | AutoGen/AG2 | Haystack | Direct SDK |
|---|---|---|---|---|---|
| Learning curve | Medium (graph mental model) | Low (role-based API) | Medium (conversations) | Medium (pipeline DAGs) | Lowest (just API calls) |
| Community | Large (126k+ stars, 400+ prod companies) | Growing (20k+ stars, $18M funding) | Large (Microsoft backing) | Niche (deepset, strong RAG) | Varies by provider |
| Production readiness | High (Uber, LinkedIn, Klarna) | Medium (teams hit ceiling at 6–12 months) | GA April 2026 (Agent Framework 1.0) | High for RAG pipelines | Depends on implementation |
| Best for | Complex stateful workflows with HITL | Rapid prototyping, small teams | Enterprise Azure/.NET environments | Precision retrieval, hybrid search | Single model calls, 1–2 tools |

### When raw SDK beats framework

Use the direct provider SDK when: (1) you need a single model call, (2) you have 1–2 tools, (3) the workflow is simple and linear, (4) you want minimal dependencies, (5) provider-specific features matter (extended thinking, prompt caching, vision). Use a framework when: (1) you need multi-source retrieval with reranking, (2) the workflow has multi-step tool calling with state, (3) you need long-running workflows with human approval, (4) you need full tracing with evals, (5) you're building a team-facing product, not a single pipeline.

**CrewAI caveat:** fastest to prototype but teams hit a ceiling at 6–12 months as workflow complexity grows. It was rewritten independently of LangChain. **AutoGen/Semantic Kernel note:** merged into Microsoft Agent Framework 1.0 (GA April 2026); AutoGen and Semantic Kernel are now in maintenance mode. **Haystack** is strongest for document ingestion and retrieval quality; it is not a general-purpose agent framework.

Do not choose a framework before deciding whether the problem is a workflow, an agent, or a team. A framework cannot compensate for missing authority, evaluation, or observability design.

## Sources and further reading

- LangGraph vs CrewAI vs AutoGen comparison — https://devops.gheware.com/blog/posts/langgraph-vs-crewai-vs-autogen-comparison-2026.html — side-by-side framework analysis.
- AI SDKs and Frameworks guide — https://aiunpacking.com/guides/ai-sdks-frameworks-langchain-llamaindex-autogen/ — ecosystem overview.
- LangGraph docs — https://docs.langchain.com/oss/python/langgraph/overview — graph-based workflow engine.
- OpenAI Agents SDK — https://openai.github.io/openai-agents-python/ — harness primitives and handoffs.
- PydanticAI — https://ai.pydantic.dev/ — typed Python agents.
- Mastra docs — https://mastra.ai/docs — TypeScript agent primitives.
- Temporal docs — https://docs.temporal.io/ — durable workflow execution.
- OpenHands SDK — https://docs.openhands.dev/sdk/index — composable coding agents.

All links verified 2026-08-27.

Primary references: [LangGraph](https://docs.langchain.com/oss/python/langgraph/overview), [OpenAI Agents SDK](https://openai.github.io/openai-agents-python/), [LangChain](https://docs.langchain.com/oss/python/langchain/overview), [PydanticAI](https://ai.pydantic.dev/), [Mastra](https://mastra.ai/docs), [Temporal](https://docs.temporal.io/), and [OpenHands SDK](https://docs.openhands.dev/sdk/index).

---

---

> **← [[08 Tool Landscape/Coding Agent Profiles|Coding Agent Profiles]]** · **[[AI_Home|Home]]** · **[[08 Tool Landscape/Infrastructure and Observability Tools|Infrastructure and Observability Tools]] →**
