# Berth AI SRE evaluation data

This directory publishes how we test the AI SRE in [Berth](https://berth.cloudguys.io), a self-hosted Kubernetes dashboard with a read-only assistant (restricted beta). It is data and method, not a leaderboard.

## What is here

| Path | Contents |
|---|---|
| `scenarios/` | 17 synthetic scenarios as JSON: a fixture cluster, a user question (or a short conversation) and explicit pass criteria |
| `results/<run>/` | One directory per run set: `results.jsonl` (one record per run: the tool calls with arguments and results, token counts, latency, the answer, every check), `summary.md`, and where criteria were revised, `rescored.jsonl` and `rescored.md` beside the **unmodified originals** |
| `results/adjudication.md` | For each quoted run: the strict score, a person's reading, and why they differ |
| `METHODOLOGY.md` | How the harness works, what is real and what is faked, scoring, repeats and intervals, the rule for changing criteria, environment pinning |
| `CLAIMS.md` | Every public number mapped to a run directory, plus the claims we do not make |
| `LICENSE` | CC BY 4.0 for the data |

## What is not here

**The harness that runs the scenarios.** It imports internal packages of the Berth application, whose source is not public, so you can read and audit every scenario, criterion and run, but you cannot re-run the evaluation yourself yet. We would rather say so than imply a reproducibility we do not have. We also keep a **held-out set** of scenarios private, to notice when public scores drift because we tuned against the public ones.

## How to read it

- **"Pass rate"** means a run passed every critical check on a synthetic scenario. It is not diagnostic accuracy on real incidents.
- Every figure has a run count and a 95% Wilson interval. With three runs per scenario the intervals are wide; most differences between setups sit inside them.
- Scoring is deterministic (tool-trace checks and regular expressions over the answer), with no model judge. It has produced false failures; see the adjudication log and the criteria-revision disclosure in `METHODOLOGY.md`.
- Results from before and after an engine or tool change are comparable only when the same criteria and Ollama version were used; `CLAIMS.md` says which.

## Headline results (original 9 scenarios, local models, Apple M4 Pro, Ollama 0.35.1)

| Setup | Passed (95% interval) | Mean / p95 latency |
|---|---|---|
| `qwen3:8b` | 19/27 (52 to 84%) | 58 s / 101 s |
| `sabbir/berth-sre` (tuned `qwen3:8b`) | 18/27 (48 to 81%) | 74 s / 108 s |
| `qwen3.5:9b-mlx`, thinking on | 21/27 (59 to 89%) | 34 s / 49 s |
| `qwen3.5:9b-mlx`, thinking off | 19/27 (52 to 84%) | 14 s / 30 s |

These do not separate the setups on pass rate. They do show `qwen3.5` as clearly faster on this machine and our tuned model as no better than the one it is built on. Everything else, with caveats, is in `CLAIMS.md`.

## Not measured

Cloud models (no Claude or Bedrock run yet), real clusters, mean time to resolution, general resistance to prompt injection, anything about third-party tools.

## Cite and correct

Cite as "Berth AI SRE evaluation data, round eval-2026-10". If a scenario is wrong or a criterion is unfair, open an issue: we will publish the change and the reason, and keep the original.
