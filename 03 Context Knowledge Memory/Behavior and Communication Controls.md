---
type: playbook
layer: context
status: evergreen
maturity: emerging
aliases: [Effort Levels, Writing Style, Response Contracts]
tags: [ai-engineering, reasoning, effort, writing-style, tone, verbosity, output-contracts]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "03 Context Knowledge Memory/Project Initialization and Instruction Files.md"
next: "04 Workflows and Orchestration/Orchestration Hub.md"
summary: "A practical control surface for choosing reasoning effort and specifying how an agent communicates: audience, tone, voice, verbosity, structure, format, and evidence."
---


# Behavior and communication controls

An agent has two different jobs: reach a good decision and communicate the result usefully. Configure them separately. More internal effort does not require a longer answer, and a polished style does not prove that the underlying work is correct.

## The control stack

Keep these layers distinct so that a style request cannot weaken a safety rule and a product requirement cannot be mistaken for a model parameter.

| Layer | Controls | Typical owner |
|---|---|---|
| Policy and authority | Allowed actions, protected data, approvals, refusal conditions | Harness and application |
| Task contract | Goal, context, constraints, acceptance criteria, verification | User, PRD, task record |
| Reasoning effort | Model tier, deliberation setting, token/time/tool budget, escalation rule | Router and harness |
| Communication style | Audience, tone, voice, formality, density, terminology, examples | Product or content owner |
| Output contract | Schema, sections, length bounds, citations, error shape, stop condition | Application and validator |
| Post-processing | Rendering, redaction, formatting, link validation, schema repair | Application |

Policy and authorization remain outside the model. Model settings can influence effort and variability but cannot grant permission.

## Effort levels

Treat effort as a budget and routing decision, not as a personality label. The names differ by provider; map them to observable limits and outcomes.

| Level | Good default for | Controls and checks |
|---|---|---|
| Low | Classification, extraction, simple transformation, known lookup, formatting | Small/fast model or deterministic code; short budget; schema validation; stop after one adequate result |
| Medium | Routine planning, synthesis across a few sources, ordinary coding changes, bounded tool use | Moderate reasoning and tool-call budget; verify key claims; allow one recovery or clarification loop |
| High | Ambiguous design, novel debugging, adversarial analysis, complex multi-step work, consequential decisions | Stronger model or larger reasoning budget; independent checks, traces, explicit uncertainty, and human review where risk requires it |

Use the lowest level that reliably meets the acceptance criteria. Escalate when the task is ambiguous, evidence conflicts, validation fails, the trajectory loops, or the consequence of an error is high. Do not escalate merely because the answer sounds less eloquent.

### Reasoning effort is not answer length

An agent may need substantial internal work to produce a two-sentence answer, while a low-effort task may produce a long report. Set separate controls for:

- internal reasoning or deliberation budget, when the model exposes one;
- model tier and routing policy;
- number and type of tool calls;
- wall-clock, token, and dollar budgets;
- user-facing answer length and section limits;
- evidence and explanation requirements.

If a model does not expose a reasoning-effort control, approximate it through routing, time/call budgets, decomposition, verification, or escalation. Never promise a specific hidden thinking process that the runtime cannot measure or configure.

## Provider effort controls

Different providers expose different knobs for controlling internal reasoning:

| Provider | Control | Values | Effect |
|---|---|---|---|
| **OpenAI** | `reasoning_effort` | `low`, `medium`, `high` | Controls how many reasoning tokens the model spends before answering. Low = fast, minimal chain-of-thought. High = extensive internal deliberation. |
| **Anthropic** | `budget_tokens` (extended thinking) | Integer token budget | Model allocates up to this many tokens for internal reasoning. Direct control over compute spend per request. |
| **Google** | `thinkingConfig` | `thinkingBudget` integer | Similar to Anthropic's budget_tokens; sets maximum thinking tokens. |
| **Deepgram** | N/A (voice pipeline) | N/A | Voice agents use latency budgets (300–800ms end-to-end) rather than reasoning budgets; VAD mode (silence vs semantic) affects responsiveness. |

These controls are the runtime implementation of the "effort level" concept. When writing agent harnesses, map your effort levels to provider-specific controls:

```python
# Example routing logic
def select_effort(task_class: str, provider: str) -> dict:
    effort_map = {
        "low":  {"openai": {"reasoning_effort": "low"},
                 "anthropic": {"budget_tokens": 1024}},
        "medium": {"openai": {"reasoning_effort": "medium"},
                   "anthropic": {"budget_tokens": 4096}},
        "high": {"openai": {"reasoning_effort": "high"},
                 "anthropic": {"budget_tokens": 16384}},
    }
    return effort_map[task_class].get(provider, effort_map["medium"])
```

## Production routing example

A practical routing layer classifies tasks and selects model + effort + budget before the agent starts:

```text
Input task
  → classify (consequence, ambiguity, novelty)
  → route:
      classification/extraction    → small model, low effort, $0.001 budget
      routine coding/synthesis     → mid model, medium effort, $0.01 budget
      ambiguous design/debugging   → strong model, high effort, $0.10 budget
      adversarial/safety-critical  → strong model, high effort, human review gate
  → apply effort control (reasoning_effort / budget_tokens)
  → apply output contract (schema, length, citations)
  → validate → escalate if threshold crossed
```

The router is deterministic code, not an LLM call. It reads task metadata (consequence score, ambiguity flag, novelty indicator) and maps to a pre-defined configuration. This keeps routing fast and auditable.

## Cost and quality trade-offs

Measured trade-off data from production deployments:

| Dimension | Low effort | Medium effort | High effort |
|---|---|---|---|
| **Token cost** | ~1× (baseline) | ~2–4× | ~5–20× |
| **Latency** | Fastest (p50 <1s) | Moderate (1–5s) | Slowest (5–30s+) |
| **Task success** | Adequate for structured extraction | Good for most coding and synthesis | Best for ambiguous, multi-step reasoning |
| **Failure recovery** | Rarely retries; accepts first answer | One recovery loop typical | Multiple self-correction passes |
| **Cost per successful task** | Lowest when task is simple | Lowest overall (best success/cost ratio) | Highest; justified only by consequence |

**Key insight:** the cheapest route that meets acceptance criteria is the right route. Escalate based on failure signals (ambiguous evidence, validation failure, trajectory loop, high consequence), not based on answer eloquence. A model that sounds confident at low effort may still be wrong—measure task success, not tone.

Anthropic's average prompt tokens grew ~4× from early 2024 (1,500) to late 2025 (6,000), reflecting growing context complexity. Prompt caching saves 40–60% on repeated-context workloads, making higher-effort configurations more affordable for stable workloads.

## Writing-style specification

Replace vague instructions such as “sound better” or “be concise” with an observable style profile.

```text
Audience: <who will read this>
Purpose: <decide, learn, execute, reassure, or compare>
Tone: <direct, warm, neutral, formal, playful, cautious>
Voice: <plainspoken, technical, conversational, authoritative but not inflated>
Formality: <casual | neutral | formal>
Density: <brief | moderate | detailed>
Perspective: <first person, second person, third person, active voice>
Terminology: <preferred terms, defined jargon, words to avoid>
Structure: <headings, bullets, tables, examples, conclusion first>
Evidence: <citations, assumptions, confidence, source links>
Formatting: <Markdown, JSON, email, memo, code, or other contract>
Length: <target range and hard maximum where appropriate>
Accessibility: <reading level, expansions for acronyms, alt text, localization>
Avoid: <hedging, hype, repetition, fake quotations, unsupported certainty>
Example: <short representative passage or output>
```

Style examples are evidence of the target, not permission to copy private content or reproduce a source too closely. Keep style separate from factual requirements and safety policy so it can change without rewriting the whole agent.

## Output contracts

For machine consumers, prefer a schema and validator. For people, define a small presentation contract. A useful contract states:

- required fields or sections;
- ordering and headings;
- allowed formats and units;
- citation or source requirements;
- maximum length or item count;
- what to do when evidence is missing;
- how uncertainty and failure are represented;
- the condition for stopping.

Example human-facing contract:

```text
Answer first in one paragraph.
Then provide up to five bullets of supporting detail.
Name assumptions and unresolved risks.
Link evidence for changing or consequential claims.
Do not invent a result, citation, tool call, or completed action.
Stop when the acceptance criteria are answered or when the missing input must come from the user.
```

Example machine-facing contract:

```json
{
  "answer": "string",
  "evidence": [{"source": "string", "claim": "string"}],
  "assumptions": ["string"],
  "confidence": "high | medium | low",
  "status": "complete | needs_input | blocked"
}
```

Validate structure in code and validate truth, style, and usefulness with evaluations. A schema can ensure that a field exists; it cannot ensure that the claim in the field is correct.

## A practical selection policy

1. Classify the task by consequence, ambiguity, novelty, and evidence requirements.
2. Choose an effort level and explicit budgets before the agent starts.
3. Apply the task and output contracts.
4. Apply the style profile only to the user-facing rendering.
5. Validate structure, then evaluate correctness, evidence, and style.
6. Escalate effort or ask for input when the failure signal crosses a defined threshold.
7. Record the model, effort setting, budgets, style profile version, and evaluation result for reproducibility.

## Testing behavior controls

Build a small matrix rather than judging one attractive example.

| Dimension | Test variation | Measure |
|---|---|---|
| Effort | Low, medium, high on the same task set | Success, cost, latency, tool use, recovery, failure rate |
| Length | Short, default, detailed | Completeness, readability, token count, omission rate |
| Style | Neutral, formal, conversational, domain-specific | Rubric adherence, clarity, audience fit, unwanted drift |
| Format | Human prose, Markdown, JSON, tabular | Validator pass rate, parse errors, rendering quality |
| Uncertainty | Complete, incomplete, conflicting evidence | Honest abstention, assumptions, citation quality |

Test style and effort against the same safety and factuality cases. A model that follows a style guide while becoming overconfident or omitting important caveats has regressed.

## Common failure modes

- **“Think harder” without a budget:** no measurable effort policy, cost ceiling, or escalation condition.
- **Conflating effort and verbosity:** asking for a deep answer accidentally produces an unnecessarily long one.
- **Persona inflation:** role-play language substitutes for audience, tone, and acceptance criteria.
- **Style as policy:** a friendly tone hides uncertainty or softens a required refusal.
- **Vague brevity:** “be concise” causes missing evidence because no minimum content contract exists.
- **Fine-tuning too early:** stable style may justify adaptation, but variable audience needs usually belong in runtime instructions and examples.
- **One example as an evaluation:** a response can imitate the example while failing on unfamiliar content.

## Sources and further reading

- Anthropic, "Effective context engineering for AI agents," Sep 2025 — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — context rot, attention budget, tool curation, prompt caching economics.
- "Deepgram vs OpenAI Realtime API" (Deepgram, Jul 2026) — https://deepgram.com/learn/deepgram-voice-agent-api-vs-openai-realtime-api — latency budgets, VAD modes, cost comparison for voice pipelines.
- Silero VAD GitHub (2024) — https://github.com/snakers4/silero-vad — <1ms per 30ms chunk, MIT licensed, zero telemetry.
- OpenAI, "A practical guide to building agents," 2025 — https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf — tooling, orchestration, guardrails, evals.

All links verified 2026-08-27.

## Related

[[01 Foundations/Context Windows and Inference]] · [[01 Foundations/What Is an LLM]] · [[03 Context Knowledge Memory/Prompting for Agents]] · [[07 Operations and Economics/Latency and Cost Engineering]] · [[01 Foundations/Fine-Tuning Decision Framework]] · [[09 Playbooks/Prompt Template]] · [[12 Templates/Template Library]]

---

---

> **← [[03 Context Knowledge Memory/Project Initialization and Instruction Files|Project Initialization and Instruction Files]]** · **[[AI_Home|Home]]** · **[[04 Workflows and Orchestration/Orchestration Hub|Orchestration Hub]] →**
