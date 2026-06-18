---
type: topic
slug: benchmarks
created: 2026-06-17
updated: 2026-06-17
source_count: 2
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

## Key concepts

_None yet._

## Key entities

_Individual benchmarks live in `wiki/entities/` with `kind: benchmark`. Candidates pending promotion: OSWorld, Online-Mind2Web, WebTailBench, Odysseys._

## Sources

- [[../sources/2026-koh-multi-agent-computer-use]] — Koh et al. (2026). Evaluated on OSWorld + Online-Mind2Web + WebTailBench + Odysseys; introduced **Odysseys** (long-horizon web).
- [[../sources/2026-mao-decentralized-mas-shared-context]] — Mao & Mirhoseini (2026). Evaluated on SWE-bench Verified + LongBench-v2 Multi-Doc QA + OOLONG; uses pass@k against single-agent baselines and reports cost-per-task alongside accuracy.

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
