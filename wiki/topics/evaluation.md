---
type: topic
slug: evaluation
created: 2026-06-17
updated: 2026-06-18
source_count: 5
---

# Evaluation

How to measure agent and LLM quality: task-level success, process metrics (trajectory quality, cost, latency), LLM-as-judge, pairwise comparison, calibration, contamination, statistical rigor. Separate from but adjacent to [[benchmarks]] (which are specific datasets/tasks).

## Current understanding

Five data points now. The four agent-systems papers (MACU, DeLM, Meta-Harness, Self-Harness) cluster on similar evaluation primitives (pass@k baselines, wall-clock-as-metric, Pareto reporting, ablation-as-contribution, in-loop held-in/held-out). A fifth source ([[../sources/2026-yu-social-world-models]]) sits **off-axis** — it's a social-forecasting / world-model paper, not an agent paper — but contributes one transferable evaluation idea: **oracle-substitution ablation as attribution diagnostic.** Yu et al. swap the learned prior attributor for the frozen posterior attributor at eval time; the resulting gap localizes the binding constraint to attribution (not transition). Same shape as "ablation isolates one module" in MACU/DeLM/Meta-Harness, but the substitution is done with a *trusted oracle* rather than removal, which gives an upper-bound diagnostic rather than a lower-bound one. Worth tracking as a complementary ablation primitive.

- **Pass@k as a baseline matters.** MACU compares against pass@k of its single-agent CUA; DeLM compares against Avg.@1 / Pass@2 / Pass@4 of `mini-SWE-agent` and other baselines on SWE-bench Verified. Both treat "spend the same compute as N independent serial attempts" as the right ceiling to beat. Beating pass@k is evidence the *structure* helps, not just the *budget* — papers without this comparison should be viewed with suspicion.
- **Wall-clock / $-per-task as a co-equal metric to accuracy.** MACU: ~1.47× faster on Odysseys *with* higher SR. DeLM: ~50% reduction in cost-per-task on SWE-bench *with* higher pass@k. Meta-Harness: +7.7 pp over ACE at **4× fewer context tokens** on text classification, and produces a Pareto frontier over (accuracy, context cost) rather than a single point. For long-horizon agentic tasks, $/task and tokens/task are first-class metrics, not afterthoughts.
- **Pareto-frontier reporting over multi-objective trade-offs.** Meta-Harness (Figure 3, Table 9) gives the proposer the trade-off explicitly and produces a smooth Pareto curve across 8 non-dominated harness variants, ranging from a 5.4K-context "Draft Verification" variant (40.1 avg acc) to a 45.5K-context "Label-Primed Query" variant (48.6 avg acc). This is a stronger evaluation discipline than reporting a single hand-picked operating point.
- **Ablation as the actual contribution.** All three papers' most useful section is the ablation table, not the headline number. MACU: replan budget drives most of the gain (not parallelism). DeLM: admission-time verification drives most of the gain (not hierarchy or queue). Meta-Harness: raw execution traces drive most of the gain (not scores or summaries). Ablation-driven attribution is now the implicit bar for serious new agent-systems papers.
- **Verification / LLM-as-judge inside the harness.** DeLM uses an LLM verifier as an admission gate on every shared-state write; ablation says removing it is the single biggest hit. This is distinct from LLM-as-judge for *evaluation* (post-hoc scoring) — it's LLM-as-judge for *control* (live filtering of state). Worth tracking as a separate evaluation-adjacent primitive.
- **Search-set vs test-set discipline is becoming standard, but not universal.** Meta-Harness applies this discipline cleanly in text classification (held-out test set) and math reasoning (5 held-out base models *and* 200 problems from held-out benchmarks). On TerminalBench-2 they treat the same 89 tasks as both search and final eval ("discovery problem" framing) and defend it on cost grounds + regex audit of task-string leakage. This is the weakest of their three empirical setups, and worth flagging as the kind of compromise to watch for in future harness-search papers.
- **OOD generalization across base models is now a tested claim.** Meta-Harness searches with one base model (GPT-OSS-20B) and evaluates the discovered harness on four held-out models (GPT-5.4-nano/mini, Gemini-3.1-Flash-Lite, Gemini-3-Flash). The harness wins on all four. This is a cleaner test than the more common "fix the base model, hold out the test split" — it tests whether the discovered structure is model-agnostic.
- **Non-regressive Pareto acceptance as an in-loop evaluation primitive.** Self-Harness's acceptance gate is `Δ_in ≥ 0 AND Δ_ho ≥ 0 AND max(Δ_in, Δ_ho) > 0`: a candidate edit is accepted only if it does not regress on either an in-distribution set *or* a held-out set, with at least one strictly increasing. This is a stricter rule than "scalar score went up" or even "score went up on aggregate" — it forces every edit to be Pareto-non-dominated across two evaluation sets. Distinct from MACU/DeLM/Meta-Harness, which compare *final* systems, this is evaluation *inside* the optimization loop as a regression-defense primitive. Candidate primitive to track for any iterative agent-improvement system.
- **Held-in vs held-out split inside the optimization loop, not just at the end.** Self-Harness splits TerminalBench-2's 64 cases 50/50 into held-in (for trace mining and signal) and held-out (acceptance and final reporting), and evaluates *both* every round. This is the in-loop analog of Meta-Harness's search-set/test-set discipline: both are defending against overfitting the search trajectory, but Self-Harness's version operates per-iteration rather than only at the end.

## Key concepts

_None yet. Candidates: pass@k-as-baseline, wall-clock-as-metric, Pareto-frontier-reporting, ablation-driven-attribution, "new architecture on new benchmark" pattern, search-set-vs-test-set discipline, cross-model OOD transfer, non-regressive Pareto acceptance gate, in-loop held-in/held-out split._

## Sources

- [[../sources/2026-koh-multi-agent-computer-use]] — Koh et al. (2026). Headline gains include a wall-clock speedup, not just SR; baseline includes pass@k of the same CUA.
- [[../sources/2026-mao-decentralized-mas-shared-context]] — Mao & Mirhoseini (2026). Reports ~50% cost reduction alongside higher pass@k on SWE-bench Verified; ablation isolates admission-time verification as the largest single contributor.
- [[../sources/2026-lee-meta-harness]] — Lee et al. (2026). Strongest example so far of multi-objective Pareto reporting (accuracy × context cost); cross-model OOD eval on 5 held-out base models for math reasoning; cleanly separates search and test sets in 2/3 domains. TerminalBench-2 is the exception (discovery-problem framing), which is acknowledged.
- [[../sources/2026-zhang-self-harness]] — Zhang et al. (2026). Introduces a strict per-iteration non-regressive Pareto acceptance gate over (held-in, held-out) pass-rate deltas as the primary defense against search-trajectory overfitting. Splits the 64-case TerminalBench-2 subset 50/50 into held-in and held-out, evaluates both every round. Reports both held-in and held-out final numbers — the held-out gain (40.5→61.9, 23.8→38.1, 42.9→57.1 across three models) is the headline rather than the in-distribution one.
- [[../sources/2026-yu-social-world-models]] — Yu et al. (2026). Off-axis source (social forecasting, not agents). Contributes **oracle-substitution ablation as upper-bound attribution diagnostic**: swapping the learned prior attributor for the frozen posterior at eval time localizes the binding constraint to attribution rather than transition. Same shape as MACU/DeLM/Meta-Harness ablations but via oracle substitution rather than module removal.

## Open questions

- LLM-as-judge: when is it reliable, when does it fail?
- How to evaluate open-ended / long-horizon agent behavior?
- How to detect benchmark contamination and dataset leakage?

## Related topics

- [[benchmarks]]
- [[agent-harness]]

## Log

- 2026-06-17: created (stub)
- 2026-06-17: added [[../sources/2026-koh-multi-agent-computer-use]]; noted pass@k-as-baseline and wall-clock-as-metric patterns
- 2026-06-17: added [[../sources/2026-mao-decentralized-mas-shared-context]]; reinforced pass@k + cost-per-task patterns; flagged ablation-driven attribution and "LLM verifier as control (not just judge)" as recurring
- 2026-06-18: added [[../sources/2026-lee-meta-harness]]; added Pareto-frontier reporting and cross-model OOD generalization as patterns; flagged TerminalBench-2 search-set/test-set compromise
- 2026-06-18: added [[../sources/2026-zhang-self-harness]]; added non-regressive Pareto acceptance gate and per-iteration held-in/held-out split as a new in-loop evaluation primitive
- 2026-06-18: added [[../sources/2026-yu-social-world-models]] as 5th source (off-axis, social forecasting); added oracle-substitution ablation as upper-bound attribution diagnostic primitive
