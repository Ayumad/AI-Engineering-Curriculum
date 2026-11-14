---
type: system
status: active
visibility: personal
created: 2026-11-14
updated: 2026-11-14
summary: "Schema and maintenance contract for the AI-Engineering wiki: layering, generated files, verification stamping, lint duties, and navigation chain rules."
aliases: []
tags: [system, agents, ai-engineering, governance]
canonical_id: "system:agents-ai-engineering"
rag_scope: exclude
system_type: domain-instructions
---

# AI-Engineering Agent Instructions

Schema layer for the sub-vault. Router: [[AGENTS|Root Agent Governance]].

## Layers

- **Sources** — `11 Glossary and Sources/Sources.md` (verified link registry) plus the
  `## Sources and further reading` section inside each concept note. Never rewrite a source
  to match a note; fix the note or mark the source superseded with a date.
- **Wiki** — everything else under `AI-Engineering/`. Agent-owned, human-readable.
- **Schema** — this file. Rules only. Never encode a current fact here (model names, prices,
  versions, counts) — those belong in the note that owns them.

## Generated files — do not hand-edit

| File | Owner | Regenerate with |
|---|---|---|
| `index.md` | `99 System/Tools/ai_wiki.py` | `python3 "99 System/Tools/ai_wiki.py" build` |
| `log.md` | `99 System/Tools/ai_wiki.py` | same script, `lint` appends a health entry |

`index.md` is the routing surface: read it before walking the tree, and treat it as a cache —
if it disagrees with a note, the note wins and the index gets rebuilt.

## Verification contract

- `last_verified` is a claim that the content was checked against a real source on that date.
- Bump it only after reading the note and confirming the claim. Never bump on a formatting pass.
- Unverified notes (`current-snapshot` with no `last_verified`) are the default work queue.
- `evergreen` notes are principles and do not decay; they do not need a stamp to stay valid.

## Lint duties

`ai_wiki.py lint` covers what a script can see: staleness, unverified notes, orphans, broken
outward links, and the prev/next chain. It cannot see contradictions between sibling notes or
claims a newer source has superseded. That review is the agent's job:

- Compare a stale note against the note it disagrees with, and against its own sources.
- Correct the older claim in place, note the supersession in the note, append the reasoning to `log.md`.
- Never delete a claim to resolve a contradiction — mark it superseded with a date.

## Navigation chain

- Every curriculum note carries `prev` / `next` frontmatter and ends with the
  `**← Previous · Home · Next →**` bar, per [[AI-Engineering/00 Start Here/How to Use This Vault|How to Use This Vault]].
- Links are reciprocal: if A's `next` is B, B's `prev` is A. `ai_wiki.py lint` fails closed on breaks.
- Entry point stays [[AI-Engineering/AI_Home|AI-Engineering Home]]; the catalog is a map, not a replacement tour.

## Change Log

- 2026-11-14 — Created with the level-3 wiki layer (index, log, lint script).
