---
type: checklist
layer: playbooks
status: evergreen
maturity: established
aliases: [Context Engineering Checklist]
tags: [ai-engineering, context-engineering, checklist]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "09 Playbooks/Prompt Template.md"
next: "09 Playbooks/RAG Design Worksheet.md"
summary: "[ ] What is the next decision the model must make? [ ] What is the minimum evidence needed for that decision? [ ] Which instructions are authoritative, and which conte..."
---


# Context checklist

## Usage guidance

Use this checklist at three scales:

- **Per-turn:** Before sending a single message, verify the model has the minimum context for the next decision. Skip if the turn is trivial.
- **Per-task:** Before starting a multi-step task, confirm files, tools, and constraints are scoped to the task. Re-check if the task changes scope.
- **Per-session:** When opening a new session or resuming after a long gap, verify project rules, permissions, and state are still current.

## Checklist

- [ ] What is the next decision the model must make?
- [ ] What is the minimum evidence needed for that decision?
- [ ] Which instructions are authoritative, and which content is untrusted data?
- [ ] Are project rules separated from task-specific details?
- [ ] Are files, logs, and retrieved chunks scoped to the task and ACL?
- [ ] Does every retrieved item retain source, timestamp, and confidence?
- [ ] Is there enough room for tool output, errors, and recovery?
- [ ] Can stale transcript be summarized without losing decisions or risks?
- [ ] Are skills loaded on demand rather than dumped into every prompt?
- [ ] Is sensitive data redacted or excluded?
- [ ] Does the context contain success criteria and stopping conditions?
- [ ] Can a reviewer reconstruct why this context was selected?

## Good context vs bad context

**Bad context:** "Fix the bug." — no files, no error message, no reproduction steps, no authority scope. The model must guess or ask, wasting a turn.

**Good context:** "The `process_invoice()` function in `src/billing.py` raises `ValueError` on line 142 when `invoice.total` is `None`. Reproduction: run `pytest tests/test_billing.py::test_none_total`. You may change parsing logic but not the database schema. Stop when the test passes."

The difference: the good version names the decision (fix the bug), provides minimum evidence (file, line, error, repro), scopes authority (parsing only), and states a stopping condition (test passes).

For deeper context-engineering theory, see [[03 Context Knowledge Memory/Context Engineering]].

---

---

> **← [[09 Playbooks/Prompt Template|Prompt Template]]** · **[[AI_Home|Home]]** · **[[09 Playbooks/RAG Design Worksheet|RAG Design Worksheet]] →**
