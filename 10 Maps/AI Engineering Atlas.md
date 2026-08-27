---
type: map
layer: maps
status: evergreen
maturity: established
aliases: [AI Engineering Mind Map]
tags: [ai-engineering, map, atlas]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "09 Playbooks/Learning Projects.md"
next: "11 Glossary and Sources/Sources.md"
summary: "text AI ENGINEERING ├─ Foundations: models · tokens · context · inference · structured output ├─ Agents: goals · loops · tools · state · harnesses · sandboxes ├─ Conte..."
---


# AI Engineering Atlas

```text
AI ENGINEERING
├─ Model / inference: models · tokens · context · structured output · quantization
│  └─ Hardware: VRAM · unified memory · bandwidth · GPU · CPU · NPU · local/cloud
├─ Agent: loop · harness · planning · state · skills · permissions
│  ├─ Knowledge: prompting · context · RAG · memory
│  ├─ Tools: function calling · MCP · APIs · capability discovery
│  ├─ Execution: sandbox · browser/computer use · voice · vision
│  └─ Teams: workflow · orchestration · multi-agent · debate · consensus
├─ Protocols: ACP · A2A · AG-UI · A2UI · identity boundaries
└─ Production / governance: evals · traces · AgentOps · security · oversight · cost
```

## How to read this atlas

Read top-to-bottom for the dependency chain: **Model → Agent → Protocols → Production**. Within each branch, left-to-right is increasing complexity (e.g., prompting → context → RAG → memory under Agent > Knowledge). The tree is a decision map, not a study guide — start where your current work lives and expand outward.

## Related canvases

- [Agent Stack canvas](10 Maps/Agent Stack.canvas) — detailed agent subsystem breakdown.
- [Protocols canvas](10 Maps/Protocols.canvas) — MCP, A2A, ACP relationship diagram.
- [Security Surface canvas](10 Maps/Security Surface.canvas) — threat model overlay.

For sequential study, see [[00 Start Here/Full Table of Contents|the full table of contents]]. The canvas below emphasizes semantic dependencies: hardware constrains inference, skills invoke tools, RAG supplies context, the harness enforces policies and budgets, and evaluation/observability govern production behavior.

Visual version: [full atlas](10 Maps/AI Engineering Atlas.canvas).

---

---

> **← [[09 Playbooks/Learning Projects|Learning Projects]]** · **[[AI_Home|Home]]** · **[[11 Glossary and Sources/Sources|Sources]] →**
