---
type: concept
layer: agents
status: evergreen
maturity: established
aliases: [Long-Horizon Agents, Long-Horizon Tasks]
tags: [ai-engineering, agents, long-horizon, failure-modes]
visibility: personal
created: 2026-09-09
updated: 2026-09-09
prev: "02 Agents and Harnesses/Computer-Use and Browser Agents.md"
next: "03 Context Knowledge Memory/Context Engineering.md"
summary: "Agents fail on long tasks because small per-step errors compound, and the failure mix shifts toward planning and memory breakdowns as the horizon grows."
---

# Long-Horizon Tasks and Failure Modes

> [!summary] The gist
> Agents break when tasks stretch past a few linked steps. Errors compound: a small per-step failure rate multiplies across dependent steps. HORIZON shows the failure mix itself shifts as horizons grow. Planning and memory break first. LongCLI-Bench is brutal: per-step scores above 98%, end-to-end pass rates of 70-88%. The fix is engineering, not bigger models. Durable state, checkpoints, summarization, verification gates.

---

## Why errors compound

Failure on long tasks is multiplicative, not additive. If each step carries even a small chance of going wrong, and later steps depend on earlier ones, the odds of a clean run fall fast. Twenty dependent steps at 95% per-step reliability gives about 36% end-to-end success. Fifty steps gives about 8%.

The HORIZON paper puts it plainly: "even a small per-step error rate compounds across dependent steps, driving agents from reliable short-task performance to near-systematic failure at longer horizons." Their example: an embodied agent handles single-step manipulation reliably, then fails completely once the task needs three sequential steps. Agents that look solid on atomic tasks collapse at 3+ chained steps.

This is why "the model is good at X" rarely survives contact with "the model must do X, then Y, then Z, where Z depends on both."

## HORIZON: failure changes shape, not just rate

[The Long-Horizon Task Mirage? Diagnosing Where and Why Agentic Systems Break](https://arxiv.org/html/2604.11978v1) (HORIZON, 2026) is a cross-domain diagnostic benchmark. The authors ran GPT-5 variants and Claude-4 models over 3100+ trajectories in four domains: Web, OS, Embodied, and Database. They built task families with systematically increasing step counts, then attributed failures with a trajectory-grounded LLM-as-a-Judge pipeline validated against human annotation.

Three findings matter here.

**Failure composition shifts structurally.** Long-horizon failure is "not merely a drop in success rate, but a structural shift in failure composition." As the horizon grows, planning-related failures (especially subplanning errors) and memory-related failures (catastrophic forgetting of goals and constraints) become dominant. Short-horizon failures look different: local execution slips that the agent often recovers from. Early subplanning errors are path-dependent and expensive to roll back, so they convert recoverable mistakes into irreversible trajectory-level derailment.

**Forgetting happens with the constraint still in context.** HORIZON's most disturbing case: an email agent told at session start to never respond to external domains. After hundreds of routine turns, it answered an external request without hesitation. The instruction was still in the context window. It just wasn't attended to. Catastrophic forgetting here is inattention to early instructions buried deep in a long trajectory, not literal memory loss.

**Breaking points are transition regions, and they're domain-dependent.** There's no universal step count where agents die. Embodied tasks degrade steeply with minimal extension. Web collapses at very small extension levels in their construction. OS and Database hold up longer. Within each domain, collapse is abrupt but spans a narrow range of extension levels rather than a single threshold. Also notable: once agents enter the breaking region, performance gaps between frontier models narrow. Scaling the base model buys less than the leaderboard suggests.

Their conclusion: different failure types demand different interventions. Forgetting needs constraint tracking. Planning errors need sub-goal decomposition. Environment disturbances need monitoring and recovery. One-size-fits-all scaling won't fix it.

## LongCLI-Bench: perfect steps, broken systems

[LongCLI-Bench](https://arxiv.org/html/2602.14337v1) (2026) benchmarks long-horizon agentic programming in the CLI: 20 curated tasks across from-scratch builds, feature additions, bug fixes, and refactors, drawn from ~1,000 CS assignments and real workflows to avoid GitHub data contamination.

It uses a dual-set protocol. FailPass (F2P) tests check whether new requirements got implemented. PassPass (P2P) tests check whether existing functionality still works, which is regression detection. Both get step-level scores for partial credit.

The P2P numbers are the interesting part. Average P2P step scores run above 98% across models, but P2P pass rates only reach 70.0-88.3% (a pass requires a 100% step score). In plain terms: agents introduced broken modifications in roughly 12-30% of the tasks where they attempted complex edits, while looking near-perfect step by step. The authors' diagnosis: agents "often lose sight of the broader context when focusing on new features," or drift from instructions as task difficulty rises. They also observed context drift in long runs, such as forgetting earlier constraints.

Two more results worth noting:

- End-to-end pass rates (both F2P and P2P) sit below 20% for every agent tested, including Claude Code with Opus and Codex with GPT-5.x-Codex. Most tasks stall below 30% completion; failures concentrate early.
- Self-correction rounds gave marginal gains and sometimes widened change scope, adding new regression risk. Human plan injection and interactive guidance helped far more.

One ironic data point: OpenHands with DeepSeek-V3.1 had the highest P2P pass rate (88.3%) mainly because it was weaker at implementing features, so it broke less. Doing less is a regression strategy, not a solution.

## Long-Horizon-Terminal-Bench: dense grading, brutal scores

[Long-Horizon-Terminal-Bench](https://arxiv.org/html/2607.08964v1) (Tencent HY et al., 2026) pushes terminal tasks to real long-horizon scale: 46 tasks across nine categories (experiment reproduction, software engineering, multimodal analysis, interactive games, scientific computing). Each task is decomposed into graded subtasks, so the grader gives dense partial credit instead of binary pass/fail. That separates "got 90% of the way there" from "failed at step one," which outcome-only grading can't see.

The scale: rollouts average about 231 episodes, 9.9M tokens, and 85.3 minutes per task under a 90-minute timeout. Across 15 frontier models, the strongest (GPT-5.5) reached only 15.2% success at a 0.95 partial-reward threshold; the mean pass rate was 4.3%. Their failure analysis: agents fail "not because every local step is wrong, but because they cannot reliably sustain progress, verify completion, and finish long-horizon tasks within budget." Timeout-driven incomplete progress, premature stopping, and weak self-verification are distinct failure signatures that dense rewards expose.

For reference on what "long-horizon" means in production training: [Surge AI's long-horizon agent work](https://surgehq.ai/blog/cross-benchmark-generalization-for-long-horizon-agentic-tasks) defines its Toolathlon tasks as needing 25-40+ tool-calling turns and 80K-100K tokens. That's a normal work task, and it's already past where naive context handling breaks. They also found dense per-criterion rewards mattered for training: sparse binary rewards gave usable signal on only 16.8% of tasks; dense rewards lifted that to 82.7%.

## The mitigation layer

None of this is fixed by waiting for a better base model. HORIZON's authors say scaling alone is unlikely to solve it. What works is architectural: make state durable, verify as you go, and keep the context window honest.

### State as an explicit durable object

Keep a written task state outside the model's head: goal, constraints, decisions made, open questions, verified evidence, next steps. This directly counters catastrophic forgetting by inattention. A constraint buried 200 turns deep gets ignored; a constraint re-surfaced at the top of the current context doesn't. HORIZON recommends exactly this class of fix: memory mechanisms that "preserve and re-surface long-range constraints."

Anthropic's [structured note-taking](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) is the same idea: the agent writes notes to files outside the context window and reads them back after resets. Their agents continue multi-hour tasks across context resets this way.

### Checkpoint, replay, compensate

Long tasks need resume points. Checkpoint state after verified progress so a derailment costs steps, not the whole run. When a step can't be undone, prefer compensating actions (explicit rollback moves) over hoping the next edit papers over it. LongCLI-Bench's regression results are an argument for this: agents broke working systems in 12-30% of complex edits, and extra self-correction rounds sometimes widened the damage.

### Summarize instead of replaying the transcript

Anthropic's compaction pattern: when context nears the limit, summarize and reinitialize with the summary, keeping architectural decisions, unresolved bugs, and implementation details while discarding redundant tool outputs. In Claude Code the agent continues with the compressed context plus the five most recently accessed files. The safest light-touch version is tool-result clearing: old raw tool outputs rarely deserve their tokens. Tune compaction prompts for recall first, then precision; over-aggressive compaction loses details whose importance shows up later.

### Manage altitude, primacy, and recency

Anthropic frames prompts at the "right altitude": specific enough to guide behavior, flexible enough to leave the model heuristics. Both extremes fail over long runs. Hardcoded brittle logic breaks on unexpected states; vague guidance gives no signal to hold onto across hundreds of turns.

Position matters too. Models attend unevenly across long contexts (the "lost in the middle" effect HORIZON cites). Early instructions fade under recency pressure. Put durable goals and constraints where attention actually lands, and re-state them periodically rather than trusting a single early mention.

### Verification gates

Check before building on a result. A gate after each dependent step catches the small per-step error before it compounds through the chain. Long-Horizon-Terminal-Bench found weak self-verification is a distinct failure mode: agents stopped early or declared done without evidence. Gates should demand evidence (test output, file contents, observed behavior), not the agent's say-so. HORIZON's recommendation of "execution-time plan verification and repair" is the same move at the plan level.

### Evaluate trajectories, not just final answers

Outcome-only grading hides where things break. Step-level scores (LongCLI-Bench), dense subtask rewards (Long-Horizon-Terminal-Bench), and failure attribution over full trajectories (HORIZON's judge pipeline) all reveal more than a pass rate. Two agents at 40% success can fail for completely different reasons, and the fixes differ. If you run agents on long tasks, log the trajectory and grade the process, or you're debugging blind.

## Summary of the numbers

| Source | Scale | Headline result |
| --- | --- | --- |
| HORIZON | 3100+ trajectories, 4 domains, GPT-5 + Claude-4 | Failure mix shifts to planning + memory as horizon grows; model gaps narrow past breaking region |
| LongCLI-Bench | 20 CLI tasks, dual F2P/P2P protocol | P2P step scores >98% but pass rates 70-88%; overall pass <20%; regressions in 12-30% of complex edits |
| Long-Horizon-Terminal-Bench | 46 tasks, ~231 episodes, 9.9M tokens, ~85 min/task | Best model 15.2% at 0.95-reward threshold; mean 4.3% |
| Surge AI (Toolathlon) | production RL training | Long-horizon tasks = 25-40+ tool turns, 80-100K tokens; dense reward lifted usable signal 16.8% -> 82.7% |

## Sources and further reading

- HORIZON: The Long-Horizon Task Mirage? Diagnosing Where and Why Agentic Systems Break - https://arxiv.org/html/2604.11978v1
- LongCLI-Bench: A Preliminary Benchmark and Study for Long-horizon Agentic Programming in Command-Line Interfaces - https://arxiv.org/html/2602.14337v1
- Long-Horizon-Terminal-Bench: Testing the Limits of Agents on Long-Horizon Terminal Tasks with Dense Reward-Based Grading - https://arxiv.org/html/2607.08964v1
- Surge AI: Training on Long-Horizon Agent Tasks - https://surgehq.ai/blog/cross-benchmark-generalization-for-long-horizon-agentic-tasks
- Anthropic: Effective context engineering for AI agents - https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

All links verified 2026-09-09.

## Related

- [[02 Agents and Harnesses/What Is an Agent]]
- [[02 Agents and Harnesses/Agent Harness]]
- [[02 Agents and Harnesses/Planning State and Persistence]]
- [[03 Context Knowledge Memory/Large Project Navigation and Context Scaling]]
- [[04 Workflows and Orchestration/Orchestration Hub]]
- [[09 Playbooks/Long-Horizon Task Review]]

---

> **← [[02 Agents and Harnesses/Computer-Use and Browser Agents|Computer-Use and Browser Agents]]** · **[[AI_Home|Home]]** · **[[03 Context Knowledge Memory/Context Engineering|Context Engineering]] →**
