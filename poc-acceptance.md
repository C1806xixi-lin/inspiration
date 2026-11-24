# PoC acceptance plan

This is a proposed test plan for the [synthetic hospital operations case](discovery.md). Every result is **pending**. Agree on the thresholds with the application, ML, and security owners before running the test.

## Run the comparison

1. Start with the [H200 SXM TP2 BF16 candidate](gpu-decision.md). Record the checkpoint, tokenizer, serving runtime, weight/KV formats, driver, GPU and interconnect, batching, and sequence cap. Keep the workload, tokenizer, quality rubric and lifecycle boundaries consistent. GPU and TP width change together in this comparison, so report the result for the complete configuration. To isolate a bottleneck, change one setting in a separate follow-up run.
2. Generate synthetic prompts to match the short/long token mix. For the capacity run, control output lengths and check the resulting token counts; retries must not conceal failures. Warm up once, then send open-loop traffic at 2 requests/s for 15 minutes. Add three one-minute bursts at 4 requests/s, with at least two minutes at 2 requests/s between bursts. Do not slow the sender to keep pace with the server; count overload, timeouts, and rejections.
3. Use a separate, fixed quality set with natural stopping behavior. Compare each configuration with the BF16 baseline and an expected key-fact rubric. Ask reviewers to score without seeing which configuration produced each output. Run the same quality set on any quantized candidate.
4. Save the harness settings, request counts, and anonymized per-request timings. Use synthetic data only; do not include prompts or responses in logs. Report steady and pooled burst results separately, plus the worst individual burst and short/long latency breakdown. If a candidate fails, keep the failed run and change one thing before retesting.

## Proposed acceptance gates

| Gate | Pass criteria for this example | Evidence |
|---|---|---|
| Memory and stability | All candidates that proceed must load the model and run at the eight-sequence cap with the 8,192-token context limit in a dedicated stress run. Zero out-of-memory crashes or restarts in the stress and load runs. | Peak device memory, runtime allocation and cache metrics, restart count. |
| Responsiveness | For completed requests, p95 TTFT ≤2 s and p95 TPOT ≤100 ms in both steady and pooled burst windows, over all eligible requests in each window. TTFT runs from client send to first generated content; TPOT uses first-to-last elapsed time divided by output tokens minus one. Control and report the number of tokens actually emitted; exclude one-token responses only from TPOT, and report exclusions. | Client trace, sample count, and short/long latency breakdown; report server queue, prefill and decode timing separately where available. |
| Reliability | ≥99% of attempted requests complete successfully within a 75-second per-request deadline in steady and pooled burst windows. Include client timeouts, overload rejections and server failures in the denominator. Do not count retries as a rescue for the original attempt. | Attempts, completions, error classes, and backlogs. |
| Summary quality | On 40 synthetic fixtures, at least 38 summaries capture every required fact. Zero invented critical operational details, such as dates, locations, or policy instructions. Any quantized candidate must pass the same gate and score no more than two fixtures below the BF16 baseline. | Blind rubric, expected facts, checklist scores and critical-error review. |
| Data boundary | Synthetic inputs only. No prompt or completion content in logs or observability exports; no unauthorized outbound traffic from the test environment. | Approved fixtures, logging configuration, sample log review and network egress check. |

See the [LLM latency troubleshooting lab](https://github.com/C1806xixi-lin/llm-latency-troubleshooting) for an offline example of separating queueing, prefill, and decode signatures. It is not evidence for any PoC outcome here.

## Make the decision

Proceed only if a candidate passes every gate. For survivors, compare confirmed supply, cost and operating requirements using the **measured SLA-qualified requests per second per replica**. Validate real replica scaling and reserve margin before recommending a fleet size. If no candidate passes, describe the first failing gate, the smallest change worth testing, and which requirement the customer would have to relax. Do not convert the memory estimate into an order quantity.
