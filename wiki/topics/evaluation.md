---
type: topic
slug: evaluation
created: 2026-06-17
updated: 2026-06-17
source_count: 2
---

# Evaluation

How to measure agent and LLM quality: task-level success, process metrics (trajectory quality, cost, latency), LLM-as-judge, pairwise comparison, calibration, contamination, statistical rigor. Separate from but adjacent to [[benchmarks]] (which are specific datasets/tasks).

## Current understanding

Two data points now ([[../sources/2026-koh-multi-agent-computer-use]] + [[../sources/2026-mao-decentralized-mas-shared-context]]) and the same evaluation-design patterns recur:

- **Pass@k as a baseline matters.** MACU compares against pass@k of its single-agent CUA; DeLM compares against Avg.@1 / Pass@2 / Pass@4 of `mini-SWE-agent` and other baselines on SWE-bench Verified. Both treat "spend the same compute as N independent serial attempts" as the right ceiling to beat. Beating pass@k is evidence the *structure* helps, not just the *budget* — papers without this comparison should be viewed with suspicion.
- **Wall-clock / $-per-task as a co-equal metric to accuracy.** MACU: ~1.47× faster on Odysseys *with* higher SR. DeLM: ~50% reduction in cost-per-task on SWE-bench *with* higher pass@k. For long-horizon agentic tasks, $/task and seconds/task are first-class metrics, not afterthoughts.
- **Ablation as the actual contribution.** Both papers' most useful section is the ablation table, not the headline number. MACU's ablation pinpoints replan budget (not parallelism) as the load-bearing knob; DeLM's ablation pinpoints admission-time verification (not the hierarchy or the queue) as the load-bearing component. Ablation-driven attribution is becoming the norm and should be the bar for new MAS papers.
- **Verification / LLM-as-judge inside the harness.** DeLM uses an LLM verifier as an admission gate on every shared-state write; ablation says removing it is the single biggest hit. This is distinct from LLM-as-judge for *evaluation* (post-hoc scoring) — it's LLM-as-judge for *control* (live filtering of state). Worth tracking as a separate evaluation-adjacent primitive.

## Key concepts

_None yet. Candidates: pass@k-as-baseline, wall-clock-as-metric, "new architecture on new benchmark" pattern._

## Sources

- [[../sources/2026-koh-multi-agent-computer-use]] — Koh et al. (2026). Headline gains include a wall-clock speedup, not just SR; baseline includes pass@k of the same CUA.
- [[../sources/2026-mao-decentralized-mas-shared-context]] — Mao & Mirhoseini (2026). Reports ~50% cost reduction alongside higher pass@k on SWE-bench Verified; ablation isolates admission-time verification as the largest single contributor.

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
