---
type: index
layer: templates
status: evergreen
maturity: emerging
aliases: [AI Engineering Templates, Agent Engineering Templates, Project Templates]
tags: [ai-engineering, templates, prd, agents-md, handoff, prompting, agents, workflows, evals, operations]
visibility: personal
created: 2026-08-27
updated: 2026-08-27
prev: "11 Glossary and Sources/Tool Comparison Index.md"
summary: "Copyable templates for the artifacts used throughout the AI Engineering curriculum, from project initialization and agent prompts to workflows, handoffs, evaluations, security reviews, and operations."
---


# AI Engineering template library

## Plain-English introduction

Instead of starting from a blank page every time you need to write a project plan, evaluation, or handoff document, grab one of these ready-made templates. Each template is a fill-in-the-blank structure designed to save you time and make sure you do not forget an important section. Copy it, replace the placeholders, and you have a professional document in minutes.

Use this library to turn the curriculum into working project artifacts. Copy the smallest template that fits the task, delete fields that do not apply, and replace every placeholder before treating the document as authoritative.

These templates are human-readable contracts. If software consumes an artifact, pair it with a schema, validator, version, owner, and migration path. Documentation can explain a boundary; the harness must enforce it.

## Template map

| Area | Templates |
|---|---|
| Project initialization | PRD, `AGENTS.md`, `README.md`, `docs/INDEX.md`, ADR, `PLAN.md`, `HANDOFF.md`, `STYLE.md` |
| Context and capabilities | Task brief, context packet, RAG worksheet, memory record, skill spec, tool spec |
| Agent architecture | Agent brief, harness spec, sandbox boundary, checkpoint/state record |
| Interaction | Voice/multimodal spec, browser/computer-use task |
| Workflows and teams | Workflow spec, multi-agent role/handoff contract, agent-democracy charter |
| Protocols and integrations | Protocol decision record, MCP server contract |
| Reliability and security | Evaluation plan/case, trace schema, red-team case, human oversight policy |
| Resources and operations | Hardware/model-fit worksheet, inference profile, cost/latency budget, deployment runbook |
| Adaptation and evidence | Fine-tuning decision record, tool comparison, source/evidence record |

## Project initialization templates

### PRD or feature specification

```text
# <Feature or product name>

Status: draft | approved | in progress | shipped | retired
Owner: <person or team>
Last updated: <YYYY-MM-DD>

## Problem
Who has what problem, in what situation, and what evidence supports it?

## Goal
What user-visible or operational outcome should exist?

## Non-goals
What related work is explicitly out of scope for this version?

## Users and workflows
- Primary user:
- Trigger:
- Main flow:
- Alternate flows:
- Failure or recovery flows:

## Requirements
- Functional:
- Data and interfaces:
- Performance and cost:
- Privacy and security:
- Accessibility or localization:

## Constraints and dependencies
- Must remain true:
- Dependencies:
- Authority and approval boundaries:
- Compatibility requirements:

## Definition of done
- Observable acceptance criterion:
- Observable acceptance criterion:

## Verification
- Tests or checks:
- Evidence to retain:
- Reviewers or approval gates:

## Rollout and recovery
- Release strategy:
- Monitoring:
- Rollback or migration path:

## Open questions and risks
- Question or risk:
- Owner and decision date:
```

### `AGENTS.md` / repository instruction file

```text
# Agent instructions for <repository or directory>

## Scope and precedence
These instructions apply to <path>. More specific instructions may add constraints.
Task instructions define the current objective but cannot override safety policy or authorization.
Document the actual precedence rules implemented by the agent or harness.

## Project map
- <path>: <responsibility>
- <path>: <responsibility>

## Setup and commands
- Install:
- Run locally:
- Test:
- Lint/typecheck:
- Build:

## Working conventions
- Follow:
- Preserve:
- Generated files:
- Naming and dependency rules:
- Migration rules:

## Safety boundaries
- Read-only by default:
- Ask before:
- Never:
- Sensitive paths or data:
- External systems and deployment boundaries:

## Verification and reporting
- Definition of done:
- Required checks:
- Report changed files, evidence, assumptions, failures, and unresolved risks.

## Maintenance
Owner: <person or team>
Review cadence or trigger: <date, release, or architecture change>
```

### `README.md` human entry point

```text
# <Project name>

One-sentence description of the problem this project solves.

## Status
<draft | experimental | active | maintenance | retired>

## What it does
- User-visible capability:
- Important limitation:

## Quick start
1. Install: <command>
2. Configure: <safe configuration steps; never commit secrets>
3. Run: <command>
4. Verify: <expected result or test command>

## Repository map
- <path>: <purpose>

## Documentation
- Requirements: <link>
- Agent instructions: <link>
- Architecture decisions: <link>
- Contribution guide: <link>
- Operations/runbook: <link>

## Safety and data handling
<What data is processed, where it goes, what it may change, and who may operate it.>

## Development
- Test:
- Lint/typecheck:
- Build:
- Release:

## Known limitations and recovery
<Known failure modes, support path, rollback, and incident link.>
```

### `docs/INDEX.md` project map

```text
# <Project name> — project map

Last verified: <YYYY-MM-DD>
Owner: <person or team>

## Start here
- Human quick start: <README link>
- Agent instructions: <AGENTS.md link>
- Current requirements: <PRD link>
- Current work: <PLAN.md or task tracker link>
- Operations: <runbook link>

## Repository map
- <path>: <responsibility, entry point, and owner>
- <path>: <responsibility, entry point, and owner>

## Architecture and data flow
<Short description or diagram of the major components and boundaries.>

## Sources of truth
| Concern | Authoritative source | Verification |
|---|---|---|
| <requirement or interface> | <file, schema, or service> | <test, query, or check> |

## Navigation hints
- Find <concept/symbol> in: <paths or search command>
- Tests for <subsystem>: <path or command>
- Recent decisions: <ADR directory or links>
- Generated files: <paths and regeneration command>

## Commands
- Install:
- Test:
- Lint/typecheck:
- Build:
- Local run:

## Context selection
- Always load:
- Retrieve for <subsystem or task>:
- Treat as untrusted data:
- Exclude or summarize when stale:

## Ownership and freshness
- <area>: owner <name>, review trigger <date/release/change>

## Protected boundaries
- Sensitive paths/data:
- External systems:
- Approval required before:
```

### Architecture decision record

```text
# ADR-<number>: <decision title>

Status: proposed | accepted | superseded | deprecated
Date: <YYYY-MM-DD>
Deciders: <people or teams>
Related: <PRD, issue, design, or incident links>

## Context
What problem or constraint requires a decision?

## Decision
What are we choosing?

## Alternatives considered
- <alternative>: benefits, costs, and why it was not chosen

## Consequences
- Positive:
- Negative or trade-offs:
- Operational impact:
- Security and privacy impact:
- Reversibility:

## Verification and review trigger
How will we know this remains valid, and what event should reopen it?
```

### `PLAN.md` or task record

```text
# <Task or goal>

Status: not started | in progress | blocked | complete
Owner: <person, agent, or team>
Created: <YYYY-MM-DD>
Last verified: <YYYY-MM-DD HH:MM TZ>
Source: <PRD, issue, request, or handoff>

## Objective
<The outcome, not a list of implementation steps.>

## Constraints and authority
- Must remain true:
- Out of scope:
- Authority granted:
- Authority withheld:

## Steps
- [ ] <step> — evidence: <how it will be verified>
- [ ] <step> — evidence: <how it will be verified>

## Decisions
- <decision>: <reason and date>

## Evidence and artifacts
- <path, URL, test result, trace, or approval>

## Blockers and risks
- <blocker or risk>: owner <name>, next action <action>

## Next action
<One concrete, safe next action.>

## Stop conditions
- Stop when:
- Stop if budget, safety, or authority is exceeded:
```

### `HANDOFF.md` ownership-transfer note

```text
# Handoff: <task or goal>

Status: ready | needs-input | blocked | complete
From: <person, agent, or team>
To: <person, agent, or team>
Created: <YYYY-MM-DD HH:MM TZ>
Expires or review by: <date or condition>

## Objective
<What outcome is still wanted?>

## Current status
<What is complete, in progress, or blocked?>

## Work completed
- <change or investigation> — evidence: <link or result>

## Relevant files and artifacts
- <path or URL>: <why it matters>

## Decisions and assumptions
- <decision or assumption>: <reason, confidence, and date>

## Verification
- Checks run:
- Results:
- Not yet verified:

## Open questions and blockers
- <question or blocker>: <owner or required input>

## Authority
- The recipient may:
- The recipient must ask before:
- The recipient may not:

## Risks and recovery
- <risk>: <mitigation or rollback>

## Next action
<The smallest concrete action the recipient should take first.>
```

For automated handoffs, serialize the same fields as a versioned schema such as `handoff.json`. A Markdown handoff is a human bridge; a checkpoint or structured payload is the source of truth for software.

### `STYLE.md` or behavior profile

```text
# Communication style for <product, agent, or audience>

Owner: <person or team>
Version: <semver or date>
Audience: <who will read this>
Purpose: <decide, learn, execute, reassure, compare>

## Voice and tone
- Voice:
- Tone:
- Formality: casual | neutral | formal
- Perspective: first | second | third person
- Active/passive voice:

## Density and length
- Default density: brief | moderate | detailed
- Target length:
- Hard maximum where applicable:

## Language
- Preferred terms:
- Define these terms before use:
- Avoid:
- Localization or reading-level requirements:

## Structure and formatting
- Lead with:
- Use headings, bullets, tables, or examples when:
- Output format:
- Citation and link rules:

## Accuracy and uncertainty
- State assumptions:
- Express uncertainty:
- Never claim:

## Examples
<A short representative example, without private or copyrighted content.>

## Evaluation
- Style rubric:
- Test cases:
- Review trigger:
```

## Prompt, context, and capability templates

### Agent task brief (G-C-C-D-V)

```text
Goal:
<What outcome should exist?>

Context:
- Relevant files, systems, facts, and prior decisions:
- Sources of truth:

Constraints:
- Must remain true:
- Out of scope:
- Authority granted:
- Authority withheld:

Definition of done:
- Observable acceptance criterion:
- Observable acceptance criterion:

Verification:
- Tests, evidence, review, or commands required:

Uncertainty and escalation:
- Ask before:
- Report assumptions and unresolved risks:

Stopping conditions:
- Stop when:
- Stop if budget, safety, or authority is exceeded:
```

### Context packet

```text
# Context packet for <task>

Next decision the agent must make:

Authoritative instructions:
- <source>: <scope, owner, and freshness>

Task facts:
- <fact>: <source and timestamp>

Relevant evidence:
- <file, record, or URL>: <why relevant; confidence; ACL>

Untrusted content:
- <source>: <why it is data rather than instruction>

Current state:
- Goal:
- Completed:
- Pending:
- Decisions:
- Open questions:

Available capabilities:
- <tool or skill>: <purpose, permissions, cost, and risk>

Budgets:
- Input/output tokens:
- Tool calls:
- Wall time:
- Cost:

Removal and expiry:
- Stale or duplicate material to exclude:
- Expiry or refresh trigger:

Context selection evidence:
- Why each item was included:
```

### RAG design worksheet

```text
Question and users:

Sources and authority:

Freshness, deletion, and ACL requirements:

Ingestion:
Formats, parsing, OCR, normalization, metadata, deduplication.

Chunking:
Semantic boundaries, overlap, maximum size, parent/child relationships.

Indexing:
Embedding model, keyword index, vector store, metadata filters.

Querying:
Rewrite, decomposition, hybrid retrieval, candidate count, reranking.

Context assembly:
Ordering, citations, conflict handling, token budget, untrusted-data labels.

Failure cases:
Missing, stale, contradictory, poisoned, unauthorized, or injected content.

Evaluation:
Recall/precision, groundedness, citation correctness, answer quality, latency, cost.

Operations:
Re-index schedule, deletion, access revocation, drift monitoring, rollback.
```

### Memory record

```text
Memory ID:
Type: working | conversation | semantic | episodic | procedural
Content:
Source:
Owner:
Created:
Last verified:
Confidence: high | medium | low
Visibility: private | project | team | public
Lifetime or expiry:
Why retain it:
Retrieval conditions:
Sensitive data present: yes | no
Confirmation required: yes | no
Correction and deletion path:
```

### Skill specification

```text
# Skill: <name>

Version and owner:
Purpose:
Use when:
Do not use when:

Inputs:
- Name, type, required/optional, source:

Outputs:
- Name, type, schema, evidence:

Procedure:
1. <step>
2. <step>

Tools and dependencies:
- <tool>: purpose, permission, expected cost/latency

Safety and authority:
- Read/write/execute permissions:
- Approval gates:
- Sensitive data handling:

Failure and recovery:
- Validation failure:
- Timeout or retry:
- Missing evidence:

Evaluation cases:
- Happy path:
- Edge case:
- Injection or authority-confusion case:

Deprecation and rollback:
```

### Tool specification

```text
# Tool: <name>

Purpose:
Owner and version:
Side-effect level: none | reversible | consequential
Required identity or role:

Input schema:
{
  "type": "object",
  "required": ["<field>"],
  "properties": {
    "<field>": {"type": "string", "description": "<meaning>"}
  },
  "additionalProperties": false
}

Output schema:
- Success:
- Typed errors:

Authorization:
- Allowed callers:
- Resource and data boundaries:
- Approval required when:

Operational limits:
- Timeout:
- Rate limit:
- Cost limit:
- Idempotency behavior:

Validation and observability:
- Argument checks:
- Return-value checks:
- Audit fields:
- Trace fields:

Failure examples and recovery:
```

## Agent architecture and execution templates

### Agent design brief

```text
# Agent: <name>

User and job:
Primary outcome:
Why an agent loop is preferable to deterministic code:
Non-goals:

Observe:
- <state, event, file, or tool result>

Decide:
- <bounded choice or proposal>

Act:
- <tool, API, sandbox action, or response>

Verify:
- <observation or validator that confirms the action>

Stop when:
- Goal achieved:
- Human decision required:
- Budget exceeded:
- Safe continuation impossible:

Context:
- Always available:
- Retrieved on demand:
- Untrusted data:

Capabilities:
- <tool or skill>: permissions and approval boundary

State and recovery:
- Persist:
- Checkpoint:
- Retry/idempotency:
- Handoff:

Evaluation:
- Success:
- Failure:
- Trajectory metrics:
```

### Harness specification

```text
# Harness: <name>

Control plane:
- Context manager:
- Tool registry/router:
- Planner and task state:
- Policy and approvals:
- Memory and skills:
- Retries and checkpoints:
- Tracing and evaluation:

Execution plane:
- Sandbox or worker:
- Mounted files:
- Network policy:
- Credentials:
- Resource limits:
- Artifact export rules:

Runtime loop:
user → context → model → decision → authorization → tool/sandbox → observation → verify

Authority model:
- Allowed by default:
- Requires approval:
- Forbidden:

Recovery:
- Retryable errors:
- Non-retryable errors:
- Idempotency or compensation:
- Human handoff:

Budgets:
- Tokens:
- Model calls:
- Tool calls:
- Subagents:
- Wall time:
- Cost:

Evidence:
- Trace ID:
- State version:
- Artifacts:
- Final outcome:
```

### Sandbox and execution boundary

```text
# Execution boundary: <workload>

Threat model:
- Untrusted inputs:
- Adversary or failure:
- Assets to protect:
- Required isolation strength:

Control plane owns:
- Policy and authorization:
- Credentials:
- Durable state:
- Approval:
- Telemetry:

Execution plane may access:
- Files:
- Network destinations:
- Packages:
- Environment variables:
- Time and processes:

Limits:
- CPU:
- Memory:
- Wall clock:
- Disk:
- Process count:
- Egress:

Boundary crossings:
- Inputs:
- Outputs:
- Validation and redaction:
- Human approval:

Cleanup and recovery:
- Destroy or retain:
- Exported artifacts:
- Incident response:
```

### Checkpoint and durable state

```text
State schema version:
Run or goal ID:
Objective:
Constraints:
Owner:
Status: pending | active | blocked | complete | expired

Plan:
- Step:
  Status:
  Dependencies:
  Evidence:

Completed:
Pending:
Decisions:
Approvals:
Artifacts:
Errors and retries:
Budget remaining:
Last verified external observation:
Idempotency keys:
Next action:
Expiry:
Migration or recovery handler:
```

### Voice and multimodal interaction specification

```text
# Interaction spec: <experience>

Input modalities: text | image | audio | video | screen
Output modalities:
Primary user and environment:

Capture:
- Device/source:
- Resolution or sampling:
- Consent and retention:

Interpretation:
- Transcription or vision model:
- Uncertainty representation:
- Embedded instructions treated as untrusted data:

Interaction:
- Turn detection or interruption:
- Latency target:
- Provisional versus committed output:

Action:
- Available tools:
- Confirmation threshold:
- Fallback when input is ambiguous:

Verification and accessibility:
- How the user can inspect or correct interpretation:
- Alternative modality:
- Audit evidence:
```

### Browser or computer-use task

```text
# Computer-use task: <name>

Goal and success state:
Starting page or application:
Allowed domains/windows:
Allowed actions:
Forbidden actions:

Observe:
- What screen state must be confirmed before acting?

Act:
- Click/type/scroll/navigation operations:
- Rate and time limits:

Confirm before:
- Sending, purchasing, deleting, publishing, or changing permissions:

Verification:
- What visible or API-backed observation proves success?

Recovery:
- Unexpected page:
- Login or MFA:
- Ambiguous target:
- Timeout:

Evidence and cleanup:
- Screenshot or trace:
- Sensitive data handling:
- Session teardown:
```

## Workflow, team, and protocol templates

### Workflow selection and specification

```text
# Workflow: <name>

User outcome:
Why this topology:
Alternative considered:

Topology: sequential | branching | router | parallel | map-reduce | evaluator-optimizer | agent loop | event-driven | durable

Stages:
1. <stage>: input → output; owner; timeout; retry policy
2. <stage>: input → output; owner; timeout; retry policy

Contracts:
- Input schema:
- Output schema:
- Join or aggregation rule:

State:
- Persisted fields:
- Versioning:
- Checkpoint boundaries:

Authority and safety:
- Side effects:
- Approval gates:
- Idempotency:

Budgets and stopping:
- Calls, tokens, cost, wall time:
- Stop conditions:

Evaluation:
- Stage tests:
- End-to-end acceptance:
- Recovery tests:
```

### Multi-agent role and handoff contract

```text
# Agent role: <name>

Mission:
Owns:
Does not own:
Required context:
Available tools:
Authority:

Input contract:
- Goal:
- Evidence:
- Constraints:
- Metadata:

Output contract:
- Result:
- Evidence and provenance:
- Assumptions:
- Open questions:
- Confidence:
- Status: complete | needs-input | blocked

Handoff rules:
- Transfer to <agent/person> when:
- Do not transfer when:
- Authorization check:
- Conversation/history filter:

Verification:
- Receiving party checks:
- Failure or rejection path:
```

### Agent democracy or society charter

```text
# Agent society: <name>

Purpose and scope:
Members and roles:
Human authority:

Proposal lifecycle:
1. Propose:
2. Critique:
3. Revise:
4. Vote or decide:
5. Execute:
6. Audit:

Decision rule:
- Quorum:
- Voting weight:
- Tie or dissent handling:
- Reputation and decay:
- Human veto:

Evidence requirements:
- Claims:
- Sources:
- Independent checks:

Safety:
- Forbidden actions:
- Approval gates:
- Collusion or correlated-error test:

Termination:
- Stop conditions:
- Budget:
- Deadlock recovery:
- Appeals and rollback:
```

### Protocol and integration decision record

```text
# Integration: <name>

Use case and boundary:
Participants: model | agent | client | tool | data source | UI | human
Protocol candidates: ACP | A2A | AG-UI | A2UI | MCP | HTTP/API | other

Why a protocol is needed:
Required operations and events:
Identity and authorization:
Session and state model:
Streaming, cancellation, and retries:
Schema and versioning:
Trust and data classification:
Observability:

Decision:
Chosen protocol and version:
Rejected alternatives and reasons:
Compatibility and migration plan:
```

### MCP server contract

```text
# MCP server: <name>

Purpose and owner:
Server version:
Transport:
Identity and authorization:

Tools:
- Name:
  Description:
  Input schema:
  Output schema:
  Side effects:
  Approval:

Resources:
- URI/name:
  Data owner:
  Freshness:
  ACL:
  Untrusted-content handling:

Prompts or skills:
- Name:
  Intended use:
  Parameters:

Limits and safety:
- Rate/cost/timeout:
- Secret handling:
- Logging and redaction:
- Revocation:

Evaluation:
- Schema tests:
- Authorization tests:
- Injection tests:
- Compatibility tests:
```

## Reliability, evaluation, and security templates

### Evaluation plan and case

```text
# Evaluation: <name>

Version and baseline:
Owner:
System configuration: model, prompt, skills, tools, retrieval, policy

Task case ID:
Input and authorized context:
Expected outcome:
Acceptance criteria:

Layer: unit | retrieval | tool-use | trajectory | end-to-end | human review
Assertions:
- Schema:
- Tool and arguments:
- Authorization:
- Evidence/citations:
- State transition:
- Cost/latency/budget:
- Final outcome:

Adversarial or recovery variation:
Expected safe behavior:

Actual result:
Trace ID:
Failure category:
Severity:
Root cause:
Regression test added:
```

### Trace schema

```json
{
  "trace_id": "<stable id>",
  "run_id": "<goal or session id>",
  "state_version": "<version>",
  "parent_trace_id": null,
  "task": {"goal": "<summary>", "risk": "low|medium|high"},
  "configuration": {
    "model": "<model/version>",
    "effort": "low|medium|high",
    "prompt_version": "<version>",
    "style_version": "<version>",
    "skill_versions": [],
    "tool_versions": []
  },
  "context": [{"source": "<id>", "authority": "<level>", "freshness": "<timestamp>"}],
  "events": [
    {"type": "decision|tool_call|observation|approval|handoff|error|checkpoint", "time": "<timestamp>", "data": {}}
  ],
  "budgets": {"tokens": 0, "tool_calls": 0, "wall_ms": 0, "cost": 0.0},
  "outcome": {"status": "complete|needs_input|blocked|failed", "evidence": [], "errors": []}
}
```

Redact secrets and sensitive content before storage. Retain enough context to reproduce decisions without retaining more data than the purpose requires.

### Defensive red-team case

```text
# Red-team case: <name>

Authorization: toy harness or explicitly approved target
Threat category: injection | authority confusion | poisoning | replay | exfiltration | unsafe action
Fixture and harmless capability:

Attack input:
<The controlled test fixture.>

Expected defense:
- Untrusted content is labeled:
- Policy is not replaced:
- Unauthorized action is denied:
- User is informed or asked:

Evidence to collect:
- Exact fixture:
- Context labels:
- Tool decision and arguments:
- Authorization result:
- Trace ID:
- Final response:
- Recovery and cleanup:

Pass/fail:
Severity if failed:
Remediation:
Regression test:
```

### Human oversight and approval policy

```text
# Oversight policy: <system>

Autonomy level: 0 answer | 1 recommend | 2 prepare | 3 reversible action | 4 consequential action with approval | 5 bounded autonomy

Actions by risk:
- Low/reversible:
- Medium:
- Consequential/irreversible:

Approval requirements:
- Who may approve:
- What evidence must be shown:
- Expiry of approval:
- Delegation rules:

Trust calibration:
- What the user sees:
- How uncertainty is expressed:
- How to inspect tool calls and sources:

Safety controls:
- Least privilege:
- Kill switch:
- Rate and budget limits:
- Audit retention:

Escalation and incident response:
- Ask when:
- Pause when:
- Contact:
- Rollback:
```

## Resource, operations, and adaptation templates

### Hardware and model-fit worksheet

```text
# Workload fit: <task>

Quality target:
Modalities: text | image | audio | video | code
Concurrency:
Context length:
Output length:
Tool or retrieval requirements:
Latency target: p50 / p95
Privacy or offline requirement:

Candidate model:
Parameter count and architecture:
Quantization:
Weights memory estimate:
Runtime/KV-cache/context overhead:
Total working memory estimate:

Machine:
CPU:
GPU:
VRAM:
Unified/system memory:
Memory bandwidth:
Storage and thermal limits:

Expected fit: yes | marginal | no
Measured prompt processing:
Measured generation rate:
Quality result:
Decision and fallback:
```

### Inference profile and effort policy

```text
# Inference profile: <task class>

Default effort: low | medium | high
Reasoning/deliberation control: <provider setting or approximation>
Model route:
Input token budget:
Output token budget:
Tool-call budget:
Wall-clock budget:
Dollar budget:

Use low effort when:
<classification, extraction, known lookup, or deterministic transformation conditions>

Escalate to medium when:
<ambiguity, validation failure, or one bounded recovery condition>

Escalate to high or human review when:
<novelty, conflicting evidence, adversarial risk, or consequential action>

User-facing answer contract:
- Length:
- Required evidence:
- Format:

Measure:
- Success:
- Cost:
- Latency:
- Recovery:
- Safety:
```

### Latency and cost budget

```text
# Run budget: <workflow or agent>

Quality target:
Maximum wall time:
Maximum cost:
Maximum input tokens:
Maximum output tokens:
Maximum model calls:
Maximum tool calls:
Maximum subagents:

Expected cost/latency contributors:
- Queueing:
- Prompt construction:
- Prefill:
- Decoding:
- Tools/network:
- Retries:
- Synthesis:

Controls:
- Cached prefixes:
- Retrieval limits:
- Model routing:
- Parallel work:
- Stop conditions:

Observed p50/p95:
Observed cost per successful outcome:
Tail failure:
Action if budget is exceeded:
```

### Deployment and AgentOps runbook

```text
# Release/runbook: <agent or workflow>

Versioned artifacts:
- Application:
- Model:
- Prompt and style profile:
- Skills and tools:
- Retrieval index:
- Policy:
- Evaluation set:

Pre-release gates:
- [ ] Baseline comparison complete
- [ ] Required evaluations pass
- [ ] Security review complete
- [ ] Cost and latency within budget
- [ ] Approval and kill switch tested
- [ ] Rollback artifact available

Deployment:
- Environment:
- Cohort or traffic percentage:
- Feature flag:
- Migration:
- Owner on call:

Monitor:
- Success and failure rate:
- p50/p95 latency:
- Cost:
- Tool errors and retries:
- Approval rate:
- Safety incidents:
- Drift or regression:

Incident response:
- Trigger:
- Pause/kill action:
- Evidence to capture:
- User communication:
- Rollback:
- Follow-up evaluation:

Retention and deletion:
- Traces:
- Checkpoints:
- Artifacts:
- Secrets and personal data:
```

### Fine-tuning or adaptation decision record

```text
# Adaptation decision: <behavior>

Observed gap:
Representative examples and baseline:
Is the target behavior stable? yes | no

Diagnose the missing layer:
- Current/changing information → retrieval
- Temporary or unclear instruction → prompt/context
- Missing action → tool or skill
- Stable repeated behavior gap → adaptation candidate

Alternatives tested:
- Prompt and examples:
- Schema/validator:
- RAG:
- Tool or skill:
- Routing/model change:

Adaptation option:
Dataset owner and provenance:
Positive, negative, edge, and holdout cases:
Base model and version:
Training configuration:
Safety and regression risks:
Serving cost and latency:
Rollback plan:

Promotion rule:
<What must improve without degrading factuality, safety, formatting, or unrelated tasks?>
```

### Tool or framework comparison record

```text
# Comparison: <category>

Decision context:
Required capabilities:
Constraints: privacy, budget, deployment, ecosystem, team skill

| Candidate | Best fit | Strengths | Trade-offs | Evidence date |
|---|---|---|---|---|
| <name> | <use case> | <strengths> | <risks/cost> | <date> |

Evaluation dimensions:
- Capability coverage:
- Reliability and observability:
- Security and permissions:
- Latency and cost:
- Portability and lock-in:
- Maintenance and support:

Decision:
Chosen option:
Why:
Fallback:
Review trigger:
```

### Source and evidence record

```text
# Evidence record: <claim or research question>

Claim or question:
Importance:
As-of date:

Source:
Title:
Author or owner:
URL/path:
Published or updated:
Retrieved:
Authority:

Supported claim:
Exact location:
Confidence: high | medium | low
Limitations or conflicting evidence:
Freshness/expiry:
Access and retention classification:

Used in:
- PRD, prompt, RAG answer, evaluation, decision, or runbook:
```

### Sources and further reading (concept-note block)

Copyable block that closes concept, pattern, and protocol notes. One bullet per source; state the specific claim the source supports. All URLs must be live-checked on the date shown.

```markdown
## Sources and further reading

- <Author or org>, "<Title>," <date> — <URL> — <what it grounds or one-line why it matters>.
- ... (prefer primary sources; for agent guidance prefer Anthropic/OpenAI engineering posts, papers, and practitioner archives over secondary blogs)

All links verified <YYYY-MM-DD>.
```

## Template: Incident response runbook

```text
# Incident response: <agent or workflow>

Trigger:
- What signals or thresholds fire this runbook?

Severity: P0 (15min) | P1 (1hr) | P2 (4hr) | P3 (24hr)

Immediate actions (within 1 min):
1. Kill switch: halt all agent execution.
2. Revoke API credentials / block outbound requests.
3. Page on-call: <name, channel>.

Quarantine:
- Preserve: logs, conversation history, tool call records, prompt/model version.
- Do NOT delete evidence.

Rollback:
- Revert to last known good: prompt version, model version, tool permissions, config.

Trace forensics:
1. What changed? (prompt, model, tool, retrieval, config)
2. When did failure start?
3. Single bad output or pattern?
4. Did guardrails fire?
5. Blast radius?
6. Could this happen again?

Communication:
- Internal: <channel, template>
- External / customer-facing: <template, owner>

Postmortem:
- Blameless postmortem within 48 hours.
- Add regression eval case to golden set.
- Update runbook with new failure mode.
```

## Template: Cost and TCO estimation

```text
# TCO estimate: <system>

Period: monthly | quarterly | annual

Compute:
| Component | Spec | Unit cost | Qty | Monthly |
|---|---|---|---|---|
| <GPU or instance> | <type, count> | <$/hr> | <hours> | <cost> |

Model inference:
| Model | Provider | Tokens in | Tokens out | Cost/M in | Cost/M out | Calls | Monthly |
|---|---|---|---|---|---|---|---|
| <model> | <API or self-hosted> | <count> | <count> | <price> | <price> | <n> | <cost> |

Caching savings:
- Cached prefix cost: <1.25x writes, 0.1x reads>
- Estimated monthly savings: <amount>

Storage and data:
- Vector store: <size, provider, cost>
- Object storage: <GB, cost>

Operational overhead:
- Observability platform: <cost>
- Guardrails stack: <cost>
- Engineering time: <hours/mo @ rate>

Total estimated monthly cost: <sum>
Break-even vs API: <tokens/month threshold>
Self-hosting crossover: <~3-5B tokens/month on flagship model>
```

## Template: A/B test design

```text
# A/B test: <name>

Hypothesis:
<If we change X, then Y metric improves by Z% for users in segment W.>

Variants:
| Variant | Change | Model/prompt/config |
|---|---|---|
| Control | Current production | <version> |
| Treatment | <proposed change> | <version> |

Primary metric:
- <metric>: baseline <value>, MDE <minimum detectable effect>

Secondary metrics:
- <metric 1>: guard against regression
- <metric 2>: cost, latency, or safety

Sample size and duration:
- Required N per variant: <calculate from MDE and power>
- Run duration: <days> (min 1 full business cycle)

Traffic split: <percentage per variant>

Randomization:
- Unit: user | session | request
- Stratification: <segment>

Guardrails (auto-kill if breached):
- Safety incident rate > <threshold>
- p95 latency > <threshold>
- Cost > <budget>

Analysis plan:
- Statistical test: <Welsh t, Mann-Whitney, chi-squared>
- Multiple comparison correction: <Bonferroni, Holm>
- Decision: ship treatment if primary metric significant + no guardrail breach
```

## Template: Evaluation benchmark design

```text
# Benchmark: <name>

Purpose:
<Evaluate model/prompt/system for <capability> on <dataset/segment>.>

Dataset:
| Case ID | Input | Expected behavior | Layer | Difficulty |
|---|---|---|---|---|
| <id> | <input> | <expected> | unit/trajectory/e2e | easy/medium/hard |

Size: <N> cases across <M> categories.

Metrics:
| Metric | Formula | Threshold | Source |
|---|---|---|---|
| <metric> | <formula or tool> | <pass> | eval framework |

Adversarial variations:
- Injection attempt: <fixture>
- Context overflow: <fixture>
- Tool misuse: <fixture>

Execution:
- Framework: promptfoo | DeepEval | lm-eval-harness | Braintrust
- Runner: <CLI command or pytest>
- Cadence: pre-commit | CI | weekly | pre-release
- Environment: <model, prompt version, retrieval config>

Decision rules:
- Pass: all critical metrics above threshold
- Regression: any critical metric drops > <X>%
- Ambiguous: rerun with increased sample size

Artifacts to retain:
- Trace IDs for failures
- Expected vs actual output diffs
- Model/prompt version at test time
```

> **Canonical sources:** The task brief template above is a compact version; the full canonical copy lives at [[09 Playbooks/Prompt Template]]. The RAG design worksheet template is a fill-in copy; the canonical version lives at [[09 Playbooks/RAG Design Worksheet]].

## How to use the library

1. Start with the PRD or task brief so the desired outcome is explicit.
2. Add repository instructions and a style profile only for stable, reusable guidance.
3. Choose the agent, workflow, tool, sandbox, and protocol templates that match the actual authority boundary.
4. Add a handoff or checkpoint whenever work can pause, change owners, retry, or resume later.
5. Define evaluation, trace, security, budget, and rollback artifacts before production use.
6. Before shipping, fill the incident response runbook, cost/TCO estimate, and A/B test design as needed for the deployment scope.
6. Close concept and pattern notes with a **Sources and further reading** section (block under "Source and evidence record"); log any load-bearing claim in the [[11 Glossary and Sources/Sources|source registry]].
7. Version templates and their filled instances when they change model behavior or operational decisions.

## Related curriculum notes

[[00 Start Here/Full Table of Contents]] · [[03 Context Knowledge Memory/Project Initialization and Instruction Files]] · [[03 Context Knowledge Memory/Behavior and Communication Controls]] · [[09 Playbooks/Playbooks Hub]] · [[09 Playbooks/Prompt Template]] · [[09 Playbooks/Context Checklist]] · [[09 Playbooks/RAG Design Worksheet]] · [[09 Playbooks/Evaluation and Security Review]] · [[09 Playbooks/Mode and Topology Selector]]

---

---

> **← [[11 Glossary and Sources/Tool Comparison Index|Tool Comparison Index]]** · **[[AI_Home|Home]]** · *→ end of tour*
