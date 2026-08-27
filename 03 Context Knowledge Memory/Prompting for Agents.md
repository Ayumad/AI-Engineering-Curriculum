---
type: pattern
layer: context
status: evergreen
maturity: established
aliases: [Agent Prompting]
tags: [ai-engineering, prompting, task-specification]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "03 Context Knowledge Memory/Context Engineering.md"
next: "03 Context Knowledge Memory/Memory and Skills.md"
summary: "The most reliable agent prompts specify outcomes and invariants, not a screenplay of implementation steps."
---


# Prompting for agents

> [!summary] The gist
> The best agent prompts describe the outcome and the guardrails, not every step. A rigid script breaks the moment something unexpected happens. This note lays out a goal-constraint-definition-verification framework for writing prompts that work even when the agent takes a different path.

---

## G-C-C-D-V

```text
Goal:          what outcome should exist?
Context:       where are the relevant files, facts, and prior decisions?
Constraints:   what must not change; what authority is available?
Definition:    what observable facts mean “done”?
Verification:  which tests, evidence, or review must run before stopping?
```

Example:

```text
Goal: Add password-reset support.
Context: Auth is in src/auth; mail code is in src/email; the database is PostgreSQL.
Constraints: Preserve the session format; do not deploy or touch production.
Done: Tokens expire in 30 minutes, cannot be reused, and existing auth tests pass.
Verification: Run the auth suite, new reset tests, and linting; report failures honestly.
```

## Prompt hygiene

- State the user’s authority and the agent’s authority separately.
- Give examples only when the format or edge cases are ambiguous.
- Prefer “must remain true” over “open file X, then line Y.”
- State stopping conditions, budget limits, and when to ask.
- Tell the agent how to report uncertainty and evidence.

Use [[09 Playbooks/Prompt Template]] for a reusable blank form.

## Practice and limits

Treat the prompt as an executable task brief: goal, context, constraints, definition of done, and verification. Stable policy belongs before volatile task data; permissions, secret handling, validation, and side effects belong in the harness, not merely in prose. Test prompt changes with a fixed evaluation set because clarity can still regress cost, safety, or tool choice.

## Evidence behind the guidance

- **Chain-of-thought (Wei et al. 2022):** a few demonstrations with intermediate reasoning steps significantly improve multi-step arithmetic, symbolic, and commonsense reasoning—and the capability *emerges at sufficient model scale*. CoT is a lever for capable models, not a fix for small ones.
- **Anthropic's guidance (2024–2025):** keep instructions clear and specific, provide examples, organize prompts into sections, and start minimal—test against failures, then add detail only where needed.
- **Examples are "pictures worth a thousand words":** diverse, canonical examples portray expected behavior better than exhaustive rule lists (Anthropic 2025).
- **A prompt is not a security boundary.** Never rely on prose to enforce authority; permission, validation, and side-effect control belong in the harness (see [[06 Reliability and Security/Security and Jailbreaking]]).

## Few-shot vs zero-shot trade-offs

| Dimension | Few-shot (examples in prompt) | Zero-shot (instructions only) |
|---|---|---|
| Format consistency | Strong—model mimics example structure | Weaker—model invents its own structure |
| Token cost | Higher—each example adds 50–200+ tokens | Lower—only instructions consume tokens |
| Edge-case coverage | Better when examples are diverse and canonical | Better when the task is simple or the model is strong |
| Maintenance burden | Examples must be updated when requirements change | Instructions are easier to keep current |
| When to prefer | Ambiguous output format, complex multi-step reasoning, domain-specific conventions | Simple classification/extraction, strong instruction-following models, token-budget-constrained tasks |

Anthropic's guidance: examples are "pictures worth a thousand words"—diverse, canonical examples portray expected behavior better than exhaustive rule lists. But keep examples *diverse and canonical*, not an exhaustive edge-case laundry list.

## Provider-specific prompting features

Different providers expose different levers that affect prompt design:

| Feature | OpenAI | Anthropic | Google |
|---|---|---|---|
| System message | Supported; user/assistant/system roles | Supported; can set via `system` param | Supported via `system_instruction` |
| Reasoning effort | `reasoning_effort` param: low/medium/high | `budget_tokens` for extended thinking | N/A (Gemini uses `thinkingConfig`) |
| Tool definitions | `tools` array with `function` schema | `tools` array with `input_schema` JSON Schema | `function_declarations` |
| JSON mode | `response_format: { type: "json_object" }` | Structured output via tool use or XML | `responseMimeType: "application/json"` |
| Max tokens | `max_tokens` on response | `max_tokens` on response | `maxOutputTokens` |
| Seed/reproducibility | `seed` param with `system_fingerprint` | N/A | `seed` parameter |

These differences matter when writing portable prompts. Abstract the prompt logic from provider-specific wiring; use a thin adapter layer that maps your task contract to the target provider's API shape.

## Evaluating prompt effectiveness

A prompt change is a code change—measure before and after:

1. **Fixed evaluation set.** Assemble 20–50 representative tasks covering happy paths, edge cases, and adversarial inputs. Run the same set before and after every prompt change.
2. **Metrics that matter.** Task success rate, cost (tokens × price), latency, tool-call accuracy, hallucination rate, and safety-refusal appropriateness. Do not optimize for one at the expense of others.
3. **A/B comparison.** Run old and new prompts on the same inputs. Report deltas in a table, not anecdotes.
4. **Regression safety.** A prompt that improves style but degrades factuality or safety has regressed. Test style and effort against the same safety and factuality cases.
5. **Human evaluation for subjective tasks.** For open-ended generation, use a structured rubric (clarity, completeness, audience fit, tone adherence) with 2–3 evaluators and inter-rater reliability checks.

See [[06 Reliability and Security/Evaluation Engineering]] for the full evaluation framework.

## Sources and further reading

- Wei et al., "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models," 2022 — https://arxiv.org/abs/2201.11903
- Anthropic, "Building effective agents," Dec 19, 2024 — https://www.anthropic.com/engineering/building-effective-agents
- Anthropic, "Effective context engineering for AI agents," Sep 2025 — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
- Weng, "LLM Powered Autonomous Agents," Jun 2023 — https://lilianweng.github.io/posts/2023-06-23-agent/

All links verified 2026-08-27.

---

---

> **← [[03 Context Knowledge Memory/Context Engineering|Context Engineering]]** · **[[AI_Home|Home]]** · **[[03 Context Knowledge Memory/Memory and Skills|Memory and Skills]] →**
