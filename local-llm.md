# Local LLM Optimization Notes

These are practical lessons from optimizing local `llama.cpp` models with benchmark harnesses. Benchmarks are useful instrumentation, not the product. A good local-model harness should improve normal chat, extraction, tool use, coding, and agent workflows without becoming shaped around one benchmark's exact tasks.

## Benchmark First

Do not tune from vibes. Run the model through a repeatable benchmark that covers the behaviors you care about: instruction following, JSON/data extraction, tool calls, reasoning/math, coding, agent tasks, and speed. Track quality, overall score, reliability, speed, runtime, tokens/sec, retries, and per-category scores. The best policy is often not the one with the highest single category score.

Keep a small real-use eval set beside the benchmark: your actual tool schemas, code prompts, extraction documents, long-context tasks, and everyday chat/instruction prompts. BenchLoop or any other benchmark can be one evaluation input, but it should not be the only promotion gate if the goal is production usefulness.

Keep raw outputs. Averages tell you where to look; task-level diffs tell you what to fix.

## Tune the Harness Before the Prompt

If the client, benchmark, or app has a fixed system prompt or tool format, avoid changing it. A thin proxy/harness can often recover quality while preserving the original contract:

- Normalize malformed JSON, escaped JSON, fenced JSON, and batched tool calls.
- Coerce obvious scalar mismatches only when the target schema makes the type unambiguous, such as `"true"` to `true`, `"false"` to `false`, and null-like strings to `null`.
- Strip hidden-thought or scratchpad blocks before scoring.
- Normalize wrappers, fences, and final-answer markers, but do not inject exact benchmark answers or task-specific constants.
- Validate generated code with real parsers before accepting it.
- Retry only targeted, general failure modes: empty output, malformed JSON, malformed code, missing required tool calls, or truncated structured output.

For local testing and production, a lightweight Fastify Node.js proxy is a good default harness shape. Put it between callers such as BenchLoop or your app and `llama-server`, keep the OpenAI-compatible API surface, and pass through the original messages/tool schemas unchanged. This gives you one clean place for sampler enforcement, policy-gated repairs, targeted retries, response normalization, trace logging, and per-request diagnostics without touching prompts. It also keeps the optimization portable: the same policy can be tested by a benchmark and then used by real clients.

For code validation, use AST parsers rather than regex-only checks. In Node.js, Babel can validate JavaScript/TypeScript, and Lezer or another grammar parser can validate Python without shelling out to Python.

## Keep Repairs Policy-Gated

Every repair should be switchable by policy/config. A repair that saves one model or category can regress another. Make it easy to run:

1. Baseline benchmark.
2. Analyze task failures.
3. Enable one small group of harness fixes.
4. Re-benchmark.
5. Validate on real-use prompts.
6. Promote only if benchmark metrics and real-use behavior improve.

This is especially important for small models, where formatting repairs can help tool/JSON tasks but longer generation caps can hurt speed or make reasoning drift.

## Token Caps Matter

Use broad generation guardrails, not a benchmark-shaped matrix of tiny per-category caps. A single huge `max_tokens` value can waste time and encourage rambling, but too many hand-tuned caps can quietly overfit to one benchmark's categories.

Good starting pattern:

- Max out usable context for the model when the runtime supports it, then rely on the caller's prompt and `max_tokens` budget when provided.
- Use only a few broad caps, for example shorter caps for structured/tool responses and larger caps for general or long-form work.
- Prefer truncation detection and targeted continuation over guessing that one fixed cap is correct for every task.
- Avoid hardcoded caps whose only evidence is a single benchmark category win.

Longer caps can improve reasoning, but they can also reduce value, speed, and reliability. Measure the tradeoff against both benchmark tasks and normal workloads.

## Sampler Settings

Reference:
- https://x.com/KyleHessling1/status/2062744362656899310

Do not assume lower temperature is always better. Some models lose reasoning or tool-call recovery when sampling gets too constrained.

For CoT- or reasoning-tuned models, especially Qwopus-style finetunes, keep temperature high enough to let the fine-tune express itself. In practice, `temperature=0.85` to `1.00` is often a better search range than very low temperatures. Low temperature can make the base model's safest/simple completion style dominate, which may flatten the reasoning behavior the fine-tune was meant to encourage.

## Read Failures Line by Line

The highest-return optimization step is often a forensic pass over failures:

- Compare the same task across good and bad runs.
- Look for exact-format misses rather than wrong reasoning.
- Separate semantic failures from parser/serialization failures.
- Identify high-variance tasks where the model sometimes succeeds.
- Turn repeated, generalizable failure patterns into narrow post-processing rules.
- Reject fixes that depend on exact task IDs, exact benchmark answers, hidden leaderboard knowledge, or constants that would not make sense in real usage.

Examples of fixable patterns include missing final answer labels, booleans returned as strings, tool calls written as prose, JSON wrapped in Markdown fences, and code surrounded by explanations.

## Promotion Criteria

Promote a policy only after a full benchmark pass and a representative real-use check. Prefer a policy that improves the shared aggregate, does not crater any important category, and still behaves well on normal prompts. Keep the previous best policy around; benchmark noise is real, and model-specific policies may beat one shared policy if you serve multiple models.

The practical loop is simple:

```text
run benchmark -> inspect failures -> add general harness fix -> re-run benchmark -> test real prompts -> promote or discard
```

Small, measured changes beat clever broad rules. Benchmark-only wins that make the harness less honest, less portable, or worse for real users should be discarded.

## Copy-Paste Harness Optimization Prompt

Use this when asking an agent to build and benchmark a local-model harness. Replace the model names, sampler settings, runtime, representative workload, and iteration count as needed.

```text
You are an expert agent harness optimization engineer.

Create a lightweight, model-specific local LLM harness/proxy optimized for real usage and evaluated with BenchLoop or an equivalent benchmark suite:

* Qwopus3.6-27B-v2-MTP-Q4_K_M.gguf, temp=0.85
* Qwopus3.6-35B-A3B-v1-Q5_K_M.gguf, temp=0.85
* Qwopus3.6-27B-v2-MTP-IQ4_XS.gguf, temp=0.80

Use the local inference runtime already available in this workspace, preferably llama.cpp / llama-server if present.

Do NOT modify the original system prompt, task prompts, user prompts, or tool-calling formats.

Do NOT overfit to BenchLoop or any single benchmark. Do not hardcode task IDs, exact benchmark answers, hidden constants, leaderboard-specific behavior, or category-specific token caps whose only evidence is one benchmark score. Benchmarks are for discovering failure patterns; promote only fixes that are general enough to help real clients.

Build harness-level improvements only, tuned for the quirks:

* Robust response parser: escaping, malformed JSON, partial outputs, batch-call recovery, normalization, date/value cleanup.
* Main agent/runtime loop: post-processing, retry logic, error recovery, timeout handling, trace logging.
* Model-specific runtime policy: sampler defaults, stop sequences, parser expectations, broad token guardrails, and isolated corrector pass for malformed or inconsistent structured outputs.
* Prompt-change isolation: if any preamble, wrapper, or "write code" framing is tested, report it separately and do not use it when the benchmark or app requires fixed prompts.
* Benchmark integration: runnable benchmark script, config, result logging, failure summaries, and leaderboard-compatible outputs when applicable.
* Real-use validation: representative prompts for normal chat, extraction, tool use, coding, long-context work, and agent workflows.
* Iterative optimizer: run the benchmark, analyze failures by category, apply harness-only fixes that generalize, re-run, then validate on real-use prompts.
* Full forensic audit: inspect every failed or changed task output line-by-line before choosing or promoting any harness change. Promote patterns, not memorized answers.

Run at least 15 optimization iterations with BenchLoop or the configured benchmark suite, then validate the best candidates on representative real prompts.

Selection rules:

* Do not fake benchmark scores.
* Keep every run's raw outputs, run JSON, logs, summaries, and policy/config snapshots.
* Promote a harness policy only after comparing full benchmark results, per-category scores, reliability, speed, per-task failures, and real-use validation.
* Prefer simple production-safe policies: broad token guardrails, schema-aware normalization, parser-backed code checks, targeted retries, and traceable repairs.
* If one shared policy is worse than model-specific policies, report that clearly and keep model-specific configs.
* Keep the implementation clean, concise, and well-commented.
```
