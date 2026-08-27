---
type: template
layer: playbooks
status: evergreen
maturity: established
aliases: [GCCDV Prompt]
tags: [ai-engineering, prompting, template]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "09 Playbooks/Mode and Topology Selector.md"
next: "09 Playbooks/Context Checklist.md"
summary: "text Goal:"
---

# Agent task template

A good prompt is like a clear brief you hand a contractor: state the goal, give the relevant background, list the rules, say how to prove the work is done, and explain when to stop. This template organizes those pieces into a fill-in structure so you don't forget anything important. Copy it, fill in the blanks for your task, and let the model do its best work with full clarity.

```text
Goal:

Context:
- Relevant files, systems, facts, prior decisions:

Constraints:
- Must remain true:
- Out of scope:
- Authority granted:
- Authority withheld:

Definition of done:
- Observable acceptance criteria:

Verification:
- Tests, evidence, review, or commands required:

Uncertainty and escalation:
- Ask before:
- Report assumptions and unresolved risks:

Stopping conditions:
- Stop when:
- Stop if budget, safety, or authority is exceeded:
```

Avoid persona inflation. Specific context, constraints, evidence, and verification usually improve outcomes more than claims that the model is an extraordinary expert.

## Worked example

```text
Goal:
Rewrite the billing error email to use plain language, reduce refund processing time,
and keep the tone calm and factual.

Context:
- Original email: "Due to an anomaly in our payment reconciliation pipeline, we have
  identified a discrepancy in your account for invoice #INV-2026-0847."
- Audience: small-business owner, non-technical, frustrated.
- Brand voice: direct, no jargon, no blame.

Constraints:
- Must remain true: refund amount ($247.50), invoice number, 5-business-day SLA.
- Out of scope: policy changes, new support channel suggestions.
- Authority granted: rewrite tone, simplify language.
- Authority withheld: cannot change refund amount or SLA.

Definition of done:
- Email uses no jargon; all sentences under 25 words.
- Subject line is action-oriented and includes the invoice number.
- Refund amount and timeline are stated in the first paragraph.

Verification:
- Read the draft aloud; flag any sentence longer than 25 words.
- Confirm the invoice number and amount match the original.

Uncertainty and escalation:
- Ask before: any mention of root cause (do we know it?).
- Report assumptions and unresolved risks: none identified.

Stopping conditions:
- Stop when: email passes the checks above.
- Stop if budget, safety, or authority is exceeded: N/A.
```

> **Canonical source:** The fill-in version of this template lives in [[12 Templates/Template Library]]. This note is the explanation and worked example. Keep both in sync to prevent drift — do not duplicate the template body here.

---

> **← [[09 Playbooks/Mode and Topology Selector|Mode and Topology Selector]]** · **[[AI_Home|Home]]** · **[[09 Playbooks/Context Checklist|Context Checklist]] →**
