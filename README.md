# AI Engineering Curriculum

A structured, source-checked curriculum on building with LLMs and AI agents — from transformers and inference mechanics to agent harnesses, RAG, multi-agent systems, reliability, security, and the operational economics of serving them.

64 tour notes + 4 canvas maps, organized as 12 numbered sections. Read them in order with the prev/next navigation bar at the **end** of each note.

## Start here

- **`AI_Home.md`** — entry point, guided tour, section map
- **`00 Start Here/How to Use This Vault.md`** — conventions (frontmatter schema, source blocks, nav chain)
- **`00 Start Here/Full Table of Contents.md`** — everything, one list
- **`10 Maps/AI Engineering Atlas.md`** — the whole curriculum as a map

## Structure

| Section | Covers |
|---|---|
| 01 Foundations | LLMs, context windows, local inference hardware, structured outputs, fine-tuning |
| 02 Agents and Harnesses | agent loops, harnesses, sandboxes, vision/voice/computer-use agents |
| 03 Context Knowledge Memory | context engineering, prompting, RAG, vector search & embeddings, memory, skills |
| 04 Workflows and Orchestration | workflow patterns, multi-agent systems, agent democracies |
| 05 Protocols and Tools | MCP, agent protocols (ACP/A2A) |
| 06 Reliability and Security | evals, observability, jailbreaking, human oversight, red-teaming |
| 07 Operations and Economics | latency/cost engineering, local-vs-subscription economics, deployment |
| 08 Tool Landscape | coding agents, runtimes, infra & observability tools |
| 09 Playbooks | worked exercises: RAG design, eval reviews, mode selection |
| 10 Maps | atlas + section canvases |
| 11 Glossary and Sources | glossary, pattern catalog, acronyms, verified source registry |
| 12 Templates | copyable templates for agents, workflows, evals, security reviews |

## Conventions

- Every concept/pattern/protocol note ends with `## Sources and further reading` (links live-verified, dated) followed by `## Related`.
- Frontmatter carries `type`/`status`/`layer`/`maturity`/`visibility` + `prev:`/`next:` for the guided tour.
- The nav bar is the literal last line of every note.

## Source of truth

This repo is a mirror of `AI-Engineering/` inside the main vault ([Ayumad/ayumad.vault](https://github.com/Ayumad/ayumad.vault), private). The vault is the working tree and the canonical home; this repo is the published snapshot. If they drift, the vault wins.

## Open it

Clone, then open the folder as an **Obsidian vault** (File → Open folder as vault). This curriculum is written for Obsidian — wikilinks, canvas maps, and frontmatter are core to how it works — so reading it inside Obsidian gives the best formatting and navigation. Plain Markdown/other viewers will still render the text, but expect plaintext wikilinks and no canvas support.