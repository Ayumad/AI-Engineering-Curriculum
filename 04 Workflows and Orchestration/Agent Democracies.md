---
type: concept
layer: orchestration
status: evergreen
maturity: experimental
aliases: [Agent Society, Agent Governance]
tags: [ai-engineering, multi-agent, governance, consensus]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "04 Workflows and Orchestration/Multi-Agent Systems.md"
next: "05 Protocols and Tools/Protocols Hub.md"
summary: "An agent democracy is a multiagent governance pattern in which agents propose, critique, deliberate, vote, delegate, or form a quorum before a decision. It is a design..."
---


# Agent democracies and societies

> [!summary] The gist
> Agent democracies let multiple AI agents independently propose, critique, and vote on a decision before acting — borrowing the structure of human committees and juries. Powerful for high-stakes choices where a single agent's answer is too risky. This note covers the governance mechanics and when the overhead pays off.

---

## Lifecycle

```text
proposal → evidence packet → debate → critique → vote/quorum → execute → audit → reputation update
```

Useful roles include proposer, specialist, critic, fact checker, moderator, voter, executor, and auditor. Keep proposal text, evidence, votes, dissent, and final authority separate.

## Design questions

- Is the decision decomposable and independently reviewable?
- How are voters selected and weighted?
- What prevents collusion, sybil agents, majority hallucination, or stale evidence?
- Can a human veto or inspect the evidence?
- Which decisions are advisory versus authorized to act?
- How does reputation decay after errors, and who can correct it?

Consensus can improve coverage, but it can also amplify a shared wrong assumption. Diversity of models, prompts, data sources, and failure modes matters more than simply increasing agent count.

## Implementation mechanics

### Proposer/critic rounds

1. **Propose**: A proposer agent generates a candidate answer or plan, including evidence and confidence.
2. **Critique**: One or more independent critic agents review the proposal against a rubric (factual accuracy, logical consistency, completeness, risk). Each produces a structured critique with specific objections.
3. **Revise**: The proposer revises based on critiques. This can run for 1–3 rounds — diminishing returns set in quickly (Song et al. 2024).
4. **Vote**: N voter agents independently score or rank the final proposal. Use structured output (JSON schema) to force consistent vote format.

### Quorum decisions

- **Simple majority**: Default for low-stakes decisions. Fast but vulnerable to systematic bias if voters share a model.
- **Supermajority** (2/3 or 3/5): Use for higher-stakes decisions. Adds robustness against narrow majorities.
- **Unanimity**: Only for critical decisions requiring absolute consensus. High cost, high risk of deadlock.
- **Weighted voting**: Weight by reputation score, domain expertise, or historical accuracy on similar tasks.

### Reputation tracking

After execution, evaluate outcomes against ground truth or human review. Update voter/proposer reputation scores. Agents with consistently poor accuracy get downweighted. Reputation decays over time — a score from 6 months ago should count less than last week's.

## Cost analysis of consensus

Consensus multiplies cost along two dimensions:

- **Voter cost**: N voters × tokens per vote. For 5 voters each generating 1k-token evaluations = 5k tokens just for voting.
- **Round cost**: If the proposer/critic loop runs R rounds, total cost = R × (proposer_tokens + N × critic_tokens).
- **Example**: A 3-round debate with 1 proposer (3k tokens/round) and 3 critics (1k tokens/round each): 3 × (3k + 3 × 1k) = 18k tokens. Compare to a single agent producing the same answer in 5k tokens — 3.6× cost overhead.
- **When it's worth it**: High-stakes decisions where the cost of a wrong answer exceeds the consensus overhead. Medical triage, financial decisions, safety-critical actions. Not worth it for routine queries.
- **Cost control**: Cap rounds at 2–3. Use cheaper models for voters/critics than for the proposer. Early termination when confidence exceeds threshold.

## Safe use

Use debate or voting for bounded exploration and adjudication, never as a substitute for authorization. Preserve proposals, evidence, dissent, vote rules, and final authority; evaluate correctness, diversity, convergence, and the ability to escalate weak evidence to a human.

## Evidence

- **LLM debate works — up to a point (Song et al. 2024):** multiple agents debating or sampling-and-voting improves reasoning accuracy, but gains are asymptotic; more agents yields diminishing returns and "false consensus" is a documented failure mode. Effectiveness tracks task difficulty.
- **Diversity beats count:** consensus amplifies a shared wrong assumption when agents share a model, prompt, or data source. Independent models, prompts, and evidence bases are the defense (Song et al. 2024; Weng 2023 on multi-agent and reflection patterns).
- Treat democratic patterns as **adjudication machinery**, not authorization. A vote result is output data for the harness to weigh; the human retains final authority on consequential actions (matches this note's design questions).

## Sources and further reading

- Song et al., "More Agents Is All You Need," 2024 — https://arxiv.org/abs/2402.05120
- Weng, "LLM Powered Autonomous Agents," Jun 2023 — https://lilianweng.github.io/posts/2023-06-23-agent/ — planning, reflection, and multi-agent patterns.

All links verified 2026-08-27.

---

---

> **← [[04 Workflows and Orchestration/Multi-Agent Systems|Multi-Agent Systems]]** · **[[AI_Home|Home]]** · **[[05 Protocols and Tools/Protocols Hub|Protocols Hub]] →**
