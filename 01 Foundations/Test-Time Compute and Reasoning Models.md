---
type: concept
layer: foundations
status: evergreen
maturity: established
aliases: [test-time compute, inference-time compute, reasoning models, thinking tokens, o1-style models]
tags: [ai-engineering, inference, reasoning, test-time-compute]
visibility: personal
created: 2026-09-09
updated: 2026-09-09
prev: "01 Foundations/KV Cache and Long-Context Costs.md"
next: "01 Foundations/Fine-Tuning Decision Framework.md"
summary: "Reasoning models buy accuracy by spending compute per request instead of per training run; this note covers the trade, the knobs, and when to route."
---

# Test-Time Compute and Reasoning Models

> [!summary] The gist
> Training compute is not the only knob. Reasoning models spend extra tokens thinking before they answer. That buys accuracy on hard problems and adds latency to every problem. You set a budget, route by difficulty, and pay for thinking only where it helps. Like a contractor who inspects before quoting: great for a rewire, silly for a lightbulb swap.

---
## The second scaling axis

For years the recipe for a better model was fixed: more data, more parameters, more training FLOPs. Test-time compute is the other axis. The model stays frozen and you spend more compute per request. Sample more answers, search over them, verify, let the model think out loud before committing.

Snell et al. (2024) studied this directly. They asked: given a fixed, non-trivial inference compute budget, how much can a model improve on a hard prompt? Two mechanisms: searching against dense process-based verifier reward models, and adaptively updating the model's response distribution given the prompt at test time.

Their findings, in numbers:

- Effectiveness depends on prompt difficulty. Easy prompts and hopeless prompts gain little; mid-difficulty prompts gain the most.
- A "compute-optimal" strategy that allocates test-time compute adaptively per prompt improves scaling efficiency by more than 4x over a best-of-N baseline.
- In a FLOPs-matched evaluation, a smaller model given test-time compute outperformed a 14x larger model on problems where the small model already had non-trivial success rates.

That last result is the headline. On some math and reasoning tasks, spending at inference beats spending at training, per FLOP.

## What reasoning models actually do

OpenAI's o1 was the mainstream arrival of this idea. The model emits internal reasoning tokens before the visible answer. Large-scale reinforcement learning taught it to build a chain of thought, catch its own mistakes, break problems into steps, and switch approaches when one stalls. OpenAI reports o1's performance improves with more RL (train-time compute) and with more time spent thinking (test-time compute).

DeepSeek-R1 (2025) showed the same thing without human-labeled reasoning traces. Pure RL made reasoning patterns emerge: self-reflection, verification, dynamic strategy adaptation. The patterns could then be distilled into smaller models.

The o1 announcement has a clean test-time scaling ladder. On the 2024 AIME math exam:

| Setting | Score |
| --- | --- |
| GPT-4o, single sample | 12% |
| o1, single sample (pass@1) | 74% |
| o1, consensus of 64 samples | 83% |
| o1, re-ranking 1000 samples with a learned scorer | 93% |

Same model in the last three rows. Only the inference spend changed. On GPQA diamond (PhD-level science), o1 hit 77.3% pass@1 vs 50.6% for GPT-4o, above the recruited PhD experts.

One design note: OpenAI does not show o1's raw chain of thought to users, only a summary. They want the reasoning legible for monitoring but not styled for the user.

## Why this changes cost and latency

Reasoning tokens are output tokens. You pay for them and wait for them.

OpenAI's reasoning guide is explicit: reasoning models introduce reasoning tokens in addition to input and output tokens, visible in the usage object under `output_tokens_details.reasoning_tokens`. Depending on problem complexity, a model may generate anywhere from a few hundred to tens of thousands of reasoning tokens. OpenAI recommends reserving at least 25,000 tokens for reasoning plus output when starting out.

Two failure modes fall out of this:

- **The invisible bill.** If generation hits `max_output_tokens` during reasoning, you get `status: incomplete` and possibly zero visible output. You still paid for input and reasoning tokens.
- **Context pressure.** Reasoning tokens eat context window. Long agent loops with thinking enabled can exhaust the window on thinking alone. Anthropic's docs note newer Claude models keep prior turns' thinking blocks in context and bill them as input tokens.

Latency changes shape too. Time to first *visible* token gets worse because thinking happens first. OpenAI's suggested workaround: ask for a short preamble before deeper reasoning, so the user sees something while the model works.

## Budgeted thinking: the adjustable knob

Both major labs expose the dial.

**Anthropic — `budget_tokens`.** Set `thinking: {type: "enabled", budget_tokens: N}`. Minimum 1,024 tokens; must be less than `max_tokens`. It's a target, not a hard cap — Claude may stop well before exhausting it. Anthropic's tuning guidance: start near the 1,024 minimum for simple tasks, start at 16,000+ for complex ones, and expect diminishing returns plus rising latency. Above 32k thinking tokens, use batch processing; long-running requests hit system timeouts. Track real spend via `usage.output_tokens_details.thinking_tokens`. Newer models moved to `thinking: {type: "adaptive"}` plus `output_config: {effort: ...}`, where Claude decides per request whether to think at all — at low effort it can skip thinking on easy inputs entirely.

Two operational gotchas from the same docs: changing `budget_tokens` between requests invalidates prompt cache breakpoints (the budget is rendered into the prompt), so pick a budget and hold it for a cached conversation. And with a fixed budget, the model thinks on *every* request — the adaptive mode is what gives you selective skipping.

**OpenAI — `reasoning.effort`.** Values include `none`, `minimal`, `low`, `medium`, `high`, `xhigh`, `max`, model-dependent. Their effort table maps directly onto workload types:

- `none`: latency-critical, no reasoning benefit — voice, fast retrieval, classification.
- `low`: modest latency bump, tool use and planning — data analysis, drafting, support chats.
- `medium`: default for most workloads, the balanced pareto point — agentic coding, research.
- `high`: hard reasoning, complex debugging, where quality beats latency.
- `xhigh`: deep research and long agentic runs; OpenAI says use it only when evals show a clear benefit justifying the extra latency and cost.

The models also reason adaptively within a setting: fewer tokens on simpler tasks, more on complex ones.

So the same frozen model becomes a family of models across the effort axis. That reframes the buy decision.

## The trade-offs, honestly

Test-time compute is not free accuracy. The costs:

- **Cost multiplier.** Reasoning tokens bill as output tokens. A single thinking call can spend multiples of a normal completion's tokens. At scale that's the line item that surprises.
- **Latency everywhere.** Thinking delays the first visible token on *every* request, including the easy ones — unless you have adaptive thinking or route by effort.
- **Non-monotonic returns.** Snell et al. found gains depend on prompt difficulty; on very easy or very hard prompts, extra compute buys little. Anthropic's docs say the same: diminishing returns that depend on the task.
- **Overthinking.** OpenAI's own evals found o1-preview was *not* preferred over GPT-4o on some natural-language tasks. Spending reasoning on a task that doesn't need it makes things slower without making them better. Adaptive modes exist precisely to let the model skip thinking on easy inputs.
- **Cache interaction.** Changing the thinking config invalidates prompt caching. Reasoning and your cost-optimization stack can fight each other.

## Reasoning model vs bigger base model vs smarter harness

Three ways to buy quality, with different price shapes:

1. **Bigger base model.** Higher per-token price, applied to every request. Helps broadly, including easy requests. Snell et al.'s FLOPs-matched result says this is not always the best use of the money on reasoning-heavy tasks.
2. **Reasoning model at high effort.** Best for verifiable, multi-step work: math, code, agentic planning. o1's AIME/GPQA jumps show where the ceiling moved. Cost scales with the thinking budget, and you can dial it per request.
3. **Smarter harness.** Best-of-N sampling, verifier/reward-model reranking, tool use, retries. Snell et al.'s search-against-verifiers mechanism is exactly this — and it's what OpenAI used to go from 74% to 93% on AIME with the same model. The o-series IOI result is the extreme version: a learned test-time selection strategy was worth nearly 60 competition points, and with 10,000 submissions per problem the raw model crossed the gold-medal threshold without any selection at all. Brute force works when you can afford it.

Rules of thumb:

- Verifiable tasks with a clear right answer (math, tests-passing code): reasoning models and search shine because the verifier can score candidates.
- Open-ended language tasks (chat, copy, support): a good base model at low effort usually wins. o1 wasn't preferred there.
- If accuracy is stuck, ask which axis is starved before paying for the next model size. Sometimes the answer is N samples and a checker, not new weights.

## Routing: spend where it pays

The Snell et al. finding that optimal allocation varies *per prompt* is the whole argument for routing. A static "always think hard" policy wastes money on easy prompts and still underbuys hard ones.

A practical router:

- **Classify first, cheaply.** A small fast model (or rules) scores incoming prompts for difficulty. Voice, retrieval, classification go to `none`/`low` effort or a non-thinking model.
- **Tier the spend.** Easy → cheap fast model, no thinking. Medium → mid-size model, `low`/`medium` effort. Hard → reasoning model, `high`/`xhigh` effort or a large `budget_tokens`.
- **Escalate on failure.** Start low; if the answer fails validation (test suite, schema check, self-consistency across samples), retry at higher effort. Pay for depth only on the prompts that proved they need it.
- **Batch the heavy stuff.** Anything above ~32k thinking tokens belongs in batch/async pipelines, not user-facing requests.
- **Measure, don't guess.** OpenAI's guidance for `xhigh` is explicit: use it only when your evals show a benefit that justifies the latency and cost. Track accuracy-per-dollar per route, not accuracy alone.

The routing layer is where test-time compute stops being a curiosity and becomes an engineering system: one frozen model, many price/performance points, allocated per request.

## Sources and further reading

- Snell et al., 2024, "Scaling LLM Test-Time Compute Optimally can be More Effective than Scaling Model Parameters" — https://arxiv.org/abs/2408.03314 — the compute-optimal scaling result: 4x over best-of-N, 14x-larger-model comparison, difficulty-dependent gains.
- OpenAI, 2024, "Learning to Reason with LLMs" — https://openai.com/index/learning-to-reason-with-llms/ — the o1 announcement: AIME ladder (12% → 74% → 83% → 93%), GPQA above PhD experts, IOI test-time selection worth ~60 points, o1-preview not preferred on some NL tasks.
- DeepSeek-AI, 2025, "DeepSeek-R1: Incentivizing Reasoning Capability in LLMs via Reinforcement Learning" — https://arxiv.org/abs/2501.12948 — reasoning patterns emerge from pure RL, then distill into smaller models.
- OpenAI, "Reasoning models" API guide — https://developers.openai.com/api/docs/guides/reasoning — reasoning tokens, the `reasoning.effort` table, the 25k-token reservation, incomplete-response billing trap, adaptive reasoning.
- Anthropic, "Extended thinking" platform docs — https://docs.claude.com/en/docs/build-with-claude/extended-thinking — `budget_tokens` rules (1,024 min, < max_tokens), tuning guidance (16k+ for complex, batch above 32k), cache invalidation, adaptive thinking and effort.

All links verified 2026-09-09.

## Related

- [[01 Foundations/Context Windows and Inference]] — general decoding and sampling lives there; this note covers the compute dimension only.
- [[01 Foundations/Inference Engines and Serving]] — where reasoning tokens hit the serving stack.
- [[01 Foundations/KV Cache and Long-Context Costs]] — thinking tokens consume context and cache.
- [[01 Foundations/What Is an LLM]] — baseline model mechanics.
- [[07 Operations and Economics/Latency and Cost Engineering]] — TTFT, cost multipliers, and the routing economics.

---

> **← [[01 Foundations/KV Cache and Long-Context Costs|KV Cache and Long-Context Costs]]** · **[[AI_Home|Home]]** · **[[01 Foundations/Fine-Tuning Decision Framework|Fine-Tuning Decision Framework]] →**
