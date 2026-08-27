---
type: checklist
layer: playbooks
status: evergreen
maturity: established
aliases: [Agent Review Checklist]
tags: [ai-engineering, evals, security, release]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "09 Playbooks/RAG Design Worksheet.md"
next: "09 Playbooks/Learning Projects.md"
summary: "[ ] Define userlevel success and unacceptable failure. [ ] Create representative happypath, edge, adversarial, and recovery cases. [ ] Test tool schemas, authorization..."
---


# Evaluation and security review

> [!summary] The gist
> Before an AI system ships, it has to be checked for quality and for harm. This is that checklist: define success and failure, run test scenarios from easy to adversarial, verify tools and data access are locked down, and have an incident plan ready. The tool table says what you can automate, and the severity ratings say how fast to respond.

---

## Before release

- [ ] Define user-level success and unacceptable failure.
- [ ] Create representative happy-path, edge, adversarial, and recovery cases.
- [ ] Test tool schemas, authorization, timeouts, retries, and idempotency.
- [ ] Test retrieval relevance, provenance, ACL enforcement, and stale data.
- [ ] Run harmless indirect-injection, tool-output, memory-poisoning, and cross-agent tests from [[06 Reliability and Security/Defensive Red-Team Labs]].
- [ ] Confirm secrets are brokered, redacted, rotated, and absent from fixtures.
- [ ] Verify sandbox filesystem, network, package, and credential boundaries.
- [ ] Inspect traces, cost, latency, approval rate, and failure ownership.
- [ ] Establish a kill switch, escalation path, retention policy, and incident runbook.
- [ ] Obtain human sign-off for consequential side effects.

## Test-tooling recommendations

| Tool | Best for | Notes |
|---|---|---|
| promptfoo | Zero-budget CI/CD red-teaming | Open-source, YAML test cases, live reloads, all providers. Acquired by OpenAI March 2026. MIT. |
| DeepEval | pytest-native metric suite | 50+ metrics (hallucination, faithfulness, toxicity), agent trace capture, Apache 2.0. |
| Braintrust | Hosted eval-as-product-craft | Active observability, SOC 2 Type II, quality gates. |
| lm-eval-harness | Model comparison benchmarks | 60+ benchmarks, CLI, EleutherAI. |
| Garak | Breadth-first probe scanning | 37+ probe categories, NVIDIA, CI/CD integration. Apache 2.0. |
| PyRIT | Multi-turn red-teaming | Microsoft, text/image/audio/video. |

## Finding severity classification

| Severity | Description | Response time |
|---|---|---|
| P0 Critical | Active data breach, financial loss, safety risk | 15 minutes |
| P1 High | Wrong outputs at scale, authority bypass | 1 hour |
| P2 Medium | Degraded performance, partial failure | 4 hours |
| P3 Low | Minor quality issues, cosmetic errors | 24 hours |

Promote only when the required tests pass, residual risks are documented, and rollback is proven. A high average score does not excuse a single catastrophic authority failure.

## Related

See [[06 Reliability and Security/Evaluation Engineering]] for the eval framework deep-dive and [[06 Reliability and Security/Defensive Red-Team Labs]] for the red-teaming methodology and lab setup.

---

---

> **← [[09 Playbooks/RAG Design Worksheet|RAG Design Worksheet]]** · **[[AI_Home|Home]]** · **[[09 Playbooks/Learning Projects|Learning Projects]] →**
