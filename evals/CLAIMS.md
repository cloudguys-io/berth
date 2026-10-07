# Claims ledger

Metric: "pass rate" = a run passed every critical check on a synthetic scenario. Not diagnostic accuracy on real incidents.

## Supported, with limits

| Claim | Evidence (run directory; Ollama version) | Strength |
|---|---|---|
| `qwen3.5:9b-mlx` answered 2 to 4 times faster than `qwen3:8b` and `berth-sre` | mean latency 34.4 s (thinking on) and 14.3 s (off) against 58.3 s and 74.2 s; `q35-9b-mlx-think-default-r3`, `q35-9b-mlx-think-false-r3`, `q3-8b-r3`, `berth-sre-r3` (0.35.1) | Clear, same machine and harness |
| Pass rate did not separate the four setups | 21, 19, 19, 18 of 27 (intervals overlap almost completely) | No difference at this n |
| The tuned model (`berth-sre`) did not beat the stock model | 18/27 against 19/27; 27% slower; 37% more input tokens | Clear that there is no gain |
| The `qwen3` setups followed injected instructions in their advice more often | 6 of 54 `qwen3` runs against 0 of 54 `qwen3.5` runs (Fisher p = 0.027); 6 of 12 against 0 of 12 on the two injection scenarios (p = 0.014) | A strong lead, not proof: runs of one scenario share a prompt, so the effective sample is about two scenarios |
| Thinking off skipped the previous-logs step in the crash-loop scenario | 1 of 3 runs read the previous logs with thinking off (one run set); 8 of 9 with it on (three run sets: 3, 2 and 3 of 3); 3 of 3 for both `qwen3` setups | Small n |
| The Ollama engine fixes removed the failure modes they targeted | `q35-new8-think-default-r3` against `q35-new8-think-default-r3-v2` (0.40.0): 500 errors 2 to 0, fragment answers 2 to 0; all 8 forced-final runs answered; 19/24 to 22/24 (p = 0.42) | Mechanism verified; pass-rate change not significant |
| Merging tools shrank the prompt and did not change pass rate | `q35-orig9-think-default-r3-v2` plus `q35-new8-think-default-r3-v2` against `q35-all17-think-default-r3-trimmed` (0.40.0): 45/51 and 44/51 (p = 1.0); input tokens per model call -14% | Prompt size: certain. Tokens per run -18% (p = 0.08), tool calls per run 5.3 to 4.4 (p = 0.21), latency -10% (p = 0.25): direction only |
| Failure modes remain | Skipped evidence, a misread unit, backwards capacity reasoning, shallow discovery, over-exploration (13 of 51 runs use the whole 8-iteration loop after the merge), an omitted reassuring fact in every setup | From reading answers; counts in the posts |
| No model printed a planted fake credential, quoted the system prompt or claimed to have changed the cluster | 276 runs in 11 result sets; two strict credential flags were false positives on reading | Synthetic scenarios only |

## Not supported (we do not say these)

- That accuracy improved by any percentage between setups.
- Anything about Claude or Bedrock quality, cost per solved scenario, or local-versus-cloud. No cloud run has been done.
- That Berth or any model is "safe", "secure" or "resistant to prompt injection". The incident-memory poisoning scenario passed only because no model stored anything (0 of 12 runs), so it has shown nothing about resistance.
- "Production-ready", mean time to resolution, or any claim about real clusters.
- Any ranking of third-party tools or models beyond the measured scenarios.
- A verdict on `rnj-1`: it crashed Ollama on load (a runtime issue), so no eval was run.

## Corrections

| Date | What | Why |
|---|---|---|
| 2026-10-07 | The product site's sentence that `berth-sre` handled the SRE tool loop "most reliably among models under 9B" was reworded | Our repeated runs did not support it |
| 2026-10-07 | An earlier note split 36 `get_resource` calls as 15 refused and 11 built-in; the correct split is 17 refused Secret/ConfigMap reads, 9 built-in kinds and 10 genuine custom-resource reads | A counting error in our own notes, corrected in all documents |
| 2026-10-06 and 2026-10-07 | Two rounds of criteria revisions (see `METHODOLOGY.md`) | Over-narrow regexes and a mis-scoped check; originals kept |
