# Local LLM Optimization Notes

These are practical lessons from optimizing local `llama.cpp` models through a benchmark harness. They should generalize to most small or mid-sized local instruction models.

## Benchmark First

Do not tune from vibes. Run the model through a repeatable benchmark that covers the behaviors you care about: instruction following, JSON/data extraction, tool calls, reasoning/math, coding, agent tasks, and speed. Track quality, overall score, reliability, speed, runtime, tokens/sec, retries, and per-category scores. The best policy is often not the one with the highest single category score.

Keep raw outputs. Averages tell you where to look; task-level diffs tell you what to fix.

## Tune the Harness Before the Prompt

If the benchmark has a fixed system prompt or tool format, avoid changing it. A thin proxy/harness can often recover a lot of score while preserving the benchmark contract:

- Normalize malformed JSON, escaped JSON, fenced JSON, and batched tool calls.
- Coerce obvious scalar mismatches in extraction tasks, such as `"true"` to `true`, `"false"` to `false`, and null-like strings to `null`.
- Strip hidden-thought or scratchpad blocks before scoring.
- Canonicalize final answer lines for tasks that require exact output shape.
- Validate generated code with real parsers before accepting it.
- Retry only targeted failure modes: empty output, malformed JSON, malformed code, or missing tool calls.

For code validation, use AST parsers rather than regex-only checks. In Node.js, Babel can validate JavaScript/TypeScript, and Lezer or another grammar parser can validate Python without shelling out to Python.

## Keep Repairs Policy-Gated

Every repair should be switchable by policy/config. A repair that saves one model or category can regress another. Make it easy to run:

1. Baseline benchmark.
2. Analyze task failures.
3. Enable one small group of harness fixes.
4. Re-benchmark.
5. Promote only if shared metrics improve.

This is especially important for small models, where formatting repairs can help tool/JSON tasks but longer generation caps can hurt speed or make reasoning drift.

## Token Caps Matter

Use task-aware generation caps. A single large `max_tokens` value usually wastes time and can reduce quality by encouraging rambling.

Good starting pattern:

- Low cap for simple/default answers.
- Medium cap for JSON and tool calls.
- Higher cap for reasoning/math and instruction following.
- Very conservative cap for coding if you have a validated fallback or parser.

Longer caps can improve reasoning, but they can also reduce value and reliability. Measure the tradeoff instead of assuming more tokens is better.

## Sampler Settings

For instruction-tuned local models, a useful broad starting point is:

```text
temperature=1.00
top_p=0.95
top_k=64
```

If outputs are too loose, try stepping down gradually:

```text
temperature=0.95 top_p=0.93 top_k=58
temperature=0.90 top_p=0.91 top_k=52
temperature=0.85 top_p=0.90 top_k=47
temperature=0.80 top_p=0.90 top_k=43
temperature=0.75 top_p=0.90 top_k=40
```

Do not assume lower temperature is always better. Some models lose reasoning or tool-call recovery when sampling gets too constrained.

## Read Failures Line by Line

The highest-return optimization step is often a forensic pass over failures:

- Compare the same task across good and bad runs.
- Look for exact-format misses rather than wrong reasoning.
- Separate semantic failures from parser/serialization failures.
- Identify high-variance tasks where the model sometimes succeeds.
- Turn repeated failure patterns into narrow post-processing rules.

Examples of fixable patterns include missing final answer labels, booleans returned as strings, tool calls written as prose, JSON wrapped in Markdown fences, and code surrounded by explanations.

## Promotion Criteria

Promote a policy only after a full benchmark pass. Prefer a policy that improves the shared aggregate and does not crater any important category. Keep the previous best policy around; benchmark noise is real, and model-specific policies may beat one shared policy if you serve multiple models.

The practical loop is simple:

```text
run benchmark -> inspect failures -> add narrow harness fix -> re-run benchmark -> compare -> promote or discard
```

Small, measured changes beat clever broad rules.

## Copy-Paste Harness Optimization Prompt

Use this when asking an agent to build and benchmark a local-model harness. Replace the model names, sampler settings, runtime, and iteration count as needed.

```text
You are an expert agent harness optimization engineer.

Create a lightweight, model-specific BenchLoop harness optimized for:

* Qwopus3.6-27B-v2-MTP-Q4_K_M.gguf, temp=0.85
* Qwopus3.6-35B-A3B-v1-Q5_K_M.gguf, temp=0.85
* Qwopus3.6-27B-v2-MTP-IQ4_XS.gguf, temp=0.80

Use the local inference runtime already available in this workspace, preferably llama.cpp / llama-server if present.

Do NOT modify the original system prompt, task prompts, or tool-calling formats.

Build harness-level improvements only, tuned for the quirks:

* Robust response parser: escaping, malformed JSON, partial outputs, batch-call recovery, normalization, date/value cleanup.
* Main agent/runtime loop: post-processing, retry logic, error recovery, timeout handling, trace logging.
* Model-specific scaffolding: lightweight L2 preamble, "write code" framing for reasoning-heavy tasks, isolated corrector pass for malformed or inconsistent answers.
* BenchLoop integration: runnable benchmark script, config, result logging, failure summaries, and leaderboard-compatible outputs.
* Iterative optimizer: run BenchLoop, analyze failures by category, apply harness-only fixes, re-run.
* Full forensic per-task audit: exhaustively inspect every task output from every iteration line-by-line before choosing or promoting any harness change.

Run at least 15 optimization iterations with BenchLoop.

Selection rules:

* Do not fake benchmark scores.
* Keep every run's raw outputs, run JSON, logs, summaries, and policy/config snapshots.
* Promote a harness policy only after comparing full benchmark results, per-category scores, reliability, speed, and per-task failures.
* If one shared policy is worse than model-specific policies, report that clearly and keep model-specific configs.
* Keep the implementation clean, concise, and well-commented.
```
