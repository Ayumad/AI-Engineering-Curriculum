---
type: concept
layer: foundations
status: evergreen
maturity: established
aliases: [Function Calling, Tool Use]
tags: [ai-engineering, structured-output, tools]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "01 Foundations/Context Windows and Inference.md"
next: "01 Foundations/Local AI Hardware and Inference.md"
summary: "Freeform text is difficult for software to validate. Structured output constrains a response to a schema, while tool calling lets a model request an operation with typ..."
---


# Structured outputs and tool calling

## Design the contract first

Treat a tool call or structured response as a contract between uncertain model behavior and deterministic software. Specify required fields, types, allowed values, bounds, defaults, and what the system should do when validation fails. The model proposes; code validates and executes only permitted operations.

Keep the schema small and separate planning from side effects. For a consequential task, ask for a typed proposal, validate it against policy and current state, present the resulting action for approval where needed, then execute with an idempotency key. Return an observation that lets the agent verify outcome rather than assume success.

## Failure handling

Malformed output should trigger bounded repair or a clear request for clarification—not unbounded retry. Tool results are untrusted input too: validate shape, redact secrets, record provenance, and never treat a tool's text as authority to bypass policy.

```json
{
  "tool": "get_weather",
  "arguments": {"city": "Seattle"},
  "reason": "The user asked for current conditions"
}
```

The harness must validate the schema, authorize the action, execute it, record the result, and return a bounded observation. A schema is not a permission: authorization belongs outside the model.

## Design rules

- Use enums and explicit required fields where possible.
- Make dangerous operations separate from read-only operations.
- Validate both arguments and returned data.
- Add idempotency keys for retried side effects.
- Return errors as typed observations the model can reason about.

## Constrained decoding

Instead of sampling freeform text and repairing it later, constrain sampling itself: mask the vocabulary so only tokens valid under a regular expression or grammar can be emitted. Outlines-style finite-state-machine-guided generation adds little to no overhead and turns malformed output from a validation problem into a design-impossible event (Willard & Louf 2023).

## Tool calling in practice

Frontier models are post-trained to emit structured tool calls—typed arguments, and with several providers, parallel calls in one response. The provider API returns machine-readable calls, but the harness remains responsible for validation, authorization, and execution (see [[02 Agents and Harnesses/Agent Harness]]). Keep schemas narrow and descriptive: if a human engineer cannot say which tool fits a situation, a model will not do better (Anthropic 2025).

## JSON-schema example

A realistic tool schema constrains the model to emit exactly the fields the harness expects:

```json
{
  "type": "function",
  "function": {
    "name": "create_issue",
    "description": "Create a tracked issue in the project board",
    "parameters": {
      "type": "object",
      "properties": {
        "title": { "type": "string", "maxLength": 200 },
        "priority": { "type": "string", "enum": ["low", "medium", "high", "urgent"] },
        "assignee_id": { "type": "string", "pattern": "^USER-[0-9]+$" },
        "labels": { "type": "array", "items": { "type": "string" }, "maxItems": 5 },
        "description": { "type": "string", "maxLength": 2000 }
      },
      "required": ["title", "priority"],
      "additionalProperties": false
    }
  }
}
```

The `enum` on priority and `required` array are the key safety rails: the model cannot invent a priority outside the four allowed values, and the harness rejects any call missing `title` or `priority`.

## Provider tool-calling differences

All major providers expose tool/function calling but differ in mechanics. OpenAI's function-calling API returns tool calls as structured JSON in a dedicated response field; the model never "writes" a function call as freeform text. Anthropic's tool_use works similarly but places tool results in `user` turns with a `tool_result` content block; Anthropic also supports computer-use tools with a distinct type. Google Gemini uses `function_declarations` with slightly different schema semantics (`anyOf` support, different required-field defaults). Local model runtimes (llama.cpp, vLLM, SGLang) expose tool calling via Jinja templates or logit-bias guidance; compatibility varies—always test with your specific model and runtime, as not all open models are equally well-tuned for function calling.

## Multi-turn repair loop

When structured output validation fails, a bounded repair loop is the standard pattern:

1. Model emits a tool call or structured response.
2. Harness validates against the schema; if invalid, constructs an error observation: *"The `priority` field must be one of low, medium, high, urgent; you sent 'critical'."*
3. The error is appended to the conversation as a tool result and the model retries.
4. Cap retries (typically 2–3) to prevent infinite loops; fall back to a human-readable error or a default safe action.

This keeps the model in control of generation while ensuring no invalid output reaches downstream systems.

## Guidance decoding as an alternative

Rather than generating freeform and repairing after the fact, guidance decoding constrains the sampler itself: a grammar (JSON, regex, or custom FSM) masks invalid tokens at each step so the model *cannot* emit them. Willard & Louf's Outlines library (2023) demonstrated this with near-zero overhead. For simpler constraints, provider-level `response_format: { type: "json_schema" }` (OpenAI) and Anthropic's tool-use enforcement achieve similar guarantees without user-side grammar files. Guidance decoding is most valuable for local or self-hosted models where provider-side schema enforcement is unavailable.

## Sources and further reading

- Willard & Louf, "Efficient Guided Generation for Large Language Models," 2023 — https://arxiv.org/abs/2307.09702 (the *Outlines* library)
- OpenAI, "A practical guide to building agents" — https://cdn.openai.com/business-guides-and-resources/a-practical-guide-to-building-agents.pdf — tool design and guardrails for production agents.
- Anthropic, "Effective context engineering for AI agents," Sep 2025 — https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents — tool-set curation guidance.
- Huyen, *AI Engineering* (O'Reilly, 2025) — https://www.oreilly.com/library/view/ai-engineering/9781098166298/

All links verified 2026-08-27.

## Related

[[02 Agents and Harnesses/What Is an Agent]] · [[05 Protocols and Tools/MCP]] · [[06 Reliability and Security/Reliability Evals and Observability]]

---

---

> **← [[01 Foundations/Context Windows and Inference|Context Windows and Inference]]** · **[[AI_Home|Home]]** · **[[01 Foundations/Local AI Hardware and Inference|Local AI Hardware and Inference]] →**
