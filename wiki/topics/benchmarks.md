---
type: topic
slug: benchmarks
created: 2026-06-17
updated: 2026-06-18
source_count: 5
---

# Benchmarks

Specific datasets and task suites used to measure agent and LLM performance: SWE-bench, GAIA, WebArena, OSWorld, AgentBench, MLE-bench, τ-bench, etc. Includes design choices, contamination concerns, and what each benchmark actually measures vs. claims to measure.

## Current understanding

Computer-use benchmarks now have a small zoo (per [[../sources/2026-koh-multi-agent-computer-use]]):

| Benchmark         | Domain               | Horizon        | Notes |
| ----------------- | -------------------- | -------------- | ----- |
| OSWorld           | Linux desktop        | Mid            | Established; mixed apps |
| Online-Mind2Web   | Live web             | Single-goal    | Real sites |
| WebTailBench      | Live web (long-tail) | Single-goal    | Less common sites/flows; tests generalization |
| Odysseys          | Live web             | **Long**       | Introduced in MACU paper; stress test for long-horizon |

Pattern to watch: papers proposing multi-agent / planning architectures tend to *also* introduce a new long-horizon benchmark on which the gains are largest. Need to track when an architecture's headline number is on its own benchmark vs. an established one (in MACU: +25.5 pp on Odysseys (theirs) vs +3.4–6 pp on OSWorld (established)).

On the **text / code** side, [[../sources/2026-mao-decentralized-mas-shared-context]] reports on a different cluster of benchmarks:

| Benchmark              | Domain                         | Horizon            | Notes |
| ---------------------- | ------------------------------ | ------------------ | ----- |
| SWE-bench Verified     | Real GitHub issues             | Mid (multi-file)   | De facto standard for SWE agents; DeLM reports Avg.@1 / Pass@2 / Pass@4 |
| LongBench-v2 Multi-Doc QA | Long-context multi-document QA | Long input        | DeLM's headline long-context eval |
| OOLONG                 | Long-context reasoning         | Long input         | Used as a secondary long-context check in DeLM |

Pattern reinforcement: DeLM, like MACU, leans heavily on **pass@k as a baseline** (not just pass@1) — both authors treat pass@k of single-agent as the "spend the same compute differently" comparison, and report wall-clock / cost-per-task alongside accuracy. This is converging into a community norm worth tracking.

Counter-pattern: DeLM's main results live on **established** benchmarks (SWE-bench Verified, LongBench-v2). The closest thing to "the authors introduce their own benchmark" is the OOLONG check, which is a smaller secondary eval. This is a cleaner empirical setup than MACU's Odysseys-centered headline.

[[../sources/2026-lee-meta-harness]] adds a third cluster spanning three very different domains:

| Benchmark              | Domain                            | Horizon            | Notes |
| ---------------------- | --------------------------------- | ------------------ | ----- |
| USPTO-50k              | Online patent classification      | Single-shot (streamed) | One of 3 datasets in the text-classification track; tests context management under streaming labels |
| Symptom2Disease        | Symptom → disease classification  | Single-shot (streamed) | Held-out test split used for OOD-from-search |
| LawBench (classification subset) | Legal-text classification | Single-shot (streamed) | Round-robin with USPTO-50k + Symptom2Disease for search-set construction |
| IMO-AnswerBench        | IMO-style math QA (numeric answer)| Single-shot        | Math-reasoning headline benchmark; 200 problems held out for final eval |
| IMO-ProofBench         | IMO-style math QA (proof)         | Single-shot        | Same family, harder eval mode (proof grading) |
| ArXivMath              | Retrieved-context math reasoning  | Single-shot        | Corpus is arXiv math papers; tests retrieval-augmented reasoning |
| TerminalBench-2        | Long-horizon agentic coding       | **Long**           | 89 terminal/coding tasks; used as both search set and final eval (acknowledged compromise) |

Patterns reinforced and extended:

- **TerminalBench-2 is now a recurring eval target — promoted to its own entity page.** First seen in MACU as a baseline-comparison context; now appears as the primary eval target in *two* successive papers ([[../sources/2026-lee-meta-harness|Meta-Harness]] reporting Haiku 4.5 37.6% vs Claude Code 27.5%, +10 pp; [[../sources/2026-zhang-self-harness|Self-Harness]] reporting 14–21 pp held-out gains across three mid-tier models from a minimal initial harness). Promoted to [[../entities/terminalbench-2]] for cross-source comparison. **Cross-source comparability warning** lives on the entity page: Meta-Harness uses the full 89-task set, Self-Harness uses a 64-task filtered subset.
- **"Discovery problem" framing as a recurring compromise.** Meta-Harness treats TerminalBench-2 as both search set and final eval and defends it on cost grounds + regex audit of task-string leakage. Self-Harness instead splits the 64-case subset 50/50 into held-in and held-out and evaluates both every round — a stricter setup for the same benchmark. Worth tagging as the design axis to watch for harness-search papers: same-set discovery vs in-loop held-out split.
- **Cross-model OOD eval as a stronger generalization claim.** Meta-Harness's math-reasoning results hold out **5 base models** (GPT-5.4-nano/mini, Gemini-3.1-Flash-Lite, Gemini-3-Flash, plus the search-time GPT-OSS-20B) and **200 problems**. This is a cleaner test of "the discovered structure is model-agnostic" than the more common "fix the base model, hold out the test split." Self-Harness deliberately *doesn't* test cross-model transfer because its claim is the opposite — that discovered harnesses are model-specific.
- **Online text classification as a benchmark family.** USPTO-50k + Symptom2Disease + LawBench is a new (to this wiki) cluster — streamed-input classification where context-management strategy matters more than raw reasoning. Worth tracking whether other papers re-use this triple.

[[../sources/2026-yu-social-world-models]] adds a benchmark cluster that is **off the agent-task axis entirely** — prediction-market transitions:

| Benchmark              | Domain                                  | Horizon            | Notes |
| ---------------------- | --------------------------------------- | ------------------ | ----- |
| SWM-Bench (Polymarket) | Prediction-market state transitions     | Single-step        | 10,188 (state, news, next-state) triples; Dec 2022–Jan 2026 |
| SWM-Bench (Kalshi)     | Prediction-market state transitions     | Single-step        | 2,601 triples; same window; macro/policy-heavy contracts |

Patterns to note for this off-axis cluster:

- **First benchmark in the wiki where the eval unit is a (state, event, next-state) transition triple rather than a (prompt, gold) pair or an agentic task rollout.** Closer in spirit to world-model evals in RL/video than to agent benchmarks.
- **Z-score sub-sampling to remove flat-day bias** — transitions where nothing moves are downweighted so the eval isn't dominated by trivial no-event days. Worth tracking as a standard hygiene step for any "events drive state changes" benchmark.
- **No held-out venue or held-out time-window split reported.** Headline numbers (8B SWM vs GPT-5.5) are on the same distribution the model was trained on. Distribution-shift evaluation is a clean follow-up gap.
- **SWM-Bench, Polymarket, Kalshi all candidates for entity promotion** but deferred per user direction for this ingest.

## Key concepts

_None yet._

## Key entities

- [[../entities/terminalbench-2]] — TerminalBench-2.0 (first entity-page promotion; appears across 3 sources).

_Other candidates pending promotion: OSWorld, Online-Mind2Web, WebTailBench, Odysseys, IMO-AnswerBench, SWE-bench Verified, SWM-Bench, Polymarket, Kalshi._

## Sources

- [[../sources/2026-koh-multi-agent-computer-use]] — Koh et al. (2026). Evaluated on OSWorld + Online-Mind2Web + WebTailBench + Odysseys; introduced **Odysseys** (long-horizon web).
- [[../sources/2026-mao-decentralized-mas-shared-context]] — Mao & Mirhoseini (2026). Evaluated on SWE-bench Verified + LongBench-v2 Multi-Doc QA + OOLONG; uses pass@k against single-agent baselines and reports cost-per-task alongside accuracy.
- [[../sources/2026-lee-meta-harness]] — Lee et al. (2026). Evaluated on USPTO-50k + Symptom2Disease + LawBench (online text classification), IMO-AnswerBench + IMO-ProofBench + ArXivMath (math reasoning with cross-model OOD), and [[../entities/terminalbench-2|TerminalBench-2]] (agentic coding, treated as a "discovery problem" with both search and eval on the same 89-task set).
- [[../sources/2026-zhang-self-harness]] — Zhang et al. (2026). Evaluated solely on [[../entities/terminalbench-2|TerminalBench-2]] (64-case filtered subset, excluding multimodal-input and unstable-network tasks); 50/50 held-in/held-out split inside the optimization loop. Reports held-out pass rates as the headline metric.
- [[../sources/2026-yu-social-world-models]] — Yu et al. (2026). Off-axis: introduces SWM-Bench, a (state, event, next-state) transition benchmark over Polymarket (10,188 triples) + Kalshi (2,601 triples), Dec 2022–Jan 2026. First non-agent-task benchmark in this wiki. Uses Z-score sub-sampling to remove flat-day bias. No held-out venue or time-window split reported.

## Open questions

- Which benchmarks have aged well, which haven't?
- How fast are benchmarks saturating?
- Where are the gaps — what's not being measured?

## Related topics

- [[evaluation]]
- [[agent-harness]]

## Log

- 2026-06-17: created (stub)
- 2026-06-17: added [[../sources/2026-koh-multi-agent-computer-use]]; added computer-use benchmark zoo table; noted "new architecture + new benchmark" pattern
- 2026-06-17: added [[../sources/2026-mao-decentralized-mas-shared-context]]; added text/code benchmark table (SWE-bench Verified, LongBench-v2, OOLONG); flagged pass@k-as-baseline as converging community norm
- 2026-06-18: added [[../sources/2026-lee-meta-harness]]; added text-classification + math-reasoning + agentic-coding benchmark cluster (TerminalBench-2, IMO-AnswerBench family, USPTO-50k / Symptom2Disease / LawBench); flagged "discovery problem" framing as a known compromise for harness-search papers; added TerminalBench-2 and IMO-AnswerBench as entity-promotion candidates
- 2026-06-18: added [[../sources/2026-zhang-self-harness]] as 3rd TerminalBench-2 source; promoted TerminalBench-2 to first entity page [[../entities/terminalbench-2]]; added Self-Harness's 50/50 held-in/held-out split as the stricter counterpoint to Meta-Harness's same-set "discovery problem" framing
- 2026-06-18: added [[../sources/2026-yu-social-world-models]] as 5th source (off-axis); added SWM-Bench (Polymarket + Kalshi prediction-market transitions) as first non-agent-task benchmark cluster; added SWM-Bench / Polymarket / Kalshi to entity-promotion candidates (deferred)
