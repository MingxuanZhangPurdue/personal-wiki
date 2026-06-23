---
type: entity
slug: terminalbench-2
kind: benchmark
aliases: ["Terminal-Bench-2.0", "TBench-2", "TerminalBench 2.0"]
created: 2026-06-18
updated: 2026-06-18
---

# TerminalBench-2.0

## What

A multi-turn agentic benchmark of containerized terminal/coding tasks judged by deterministic per-task verifiers. Designed to test general tool-based execution: artifact management, command use, verification behavior, and recovery from execution errors. Successor to TerminalBench (v1) and one of the central 2026 evals for long-horizon agentic coding.

Released by Merrill, Shaw, Carlini, et al. (2026), [arXiv:2601.11868](https://arxiv.org/abs/2601.11868). Uses Harbor (https://www.harborframework.com) as the execution environment.

## Affiliation / context

- Authored by a large cross-institution group; first author Mike A. Merrill.
- The benchmark sits at the agentic-coding end of the 2026 eval zoo (vs computer-use benchmarks like OSWorld / Odysseys, and vs SWE-bench Verified for repo-level patches).

## Notable contributions / properties

- **89 containerized terminal tasks** in the canonical release. Many evaluators use subsets — e.g., [[../sources/2026-zhang-self-harness]] uses a 64-case filtered subset (excluding multimodal-input tasks and tasks depending on unstable external network resources).
- **Deterministic verifiers per task.** Pass/fail is determined by inspecting the final container state with a task-specific check, not by LLM-as-judge.
- **Long-horizon agentic.** Tasks require multi-turn tool use (shell, filesystem, etc.), often including build/install steps, artifact creation, and verification.
- **Used as a primary eval in three different framings so far in this wiki:**
  - As a *comparison context* in [[../sources/2026-koh-multi-agent-computer-use|MACU]] (computer-use centralized MAS)
  - As a *harness-search target* in [[../sources/2026-lee-meta-harness|Meta-Harness]] (external strong proposer; treated as a "discovery problem" — search and eval on the same 89-task set, with regex audit for task-string leakage)
  - As a *self-improvement target* in [[../sources/2026-zhang-self-harness|Self-Harness]] (same fixed model is target and proposer; 64-case subset with held-in/held-out split)

## Reported numbers (this wiki, growing)

| Source | Model + Harness | Pass(%) | Notes |
|---|---|---|---|
| Meta-Harness (Lee 2026) | Haiku 4.5 + discovered harness | 37.6 | +10 pp over Claude Code (27.5%) on same model |
| Meta-Harness (Lee 2026) | Haiku 4.5 + Claude Code | 27.5 | Hand-engineered baseline |
| Self-Harness (Zhang 2026) | MiniMax M2.5 + initial harness | 42.2 overall, 40.5 held-out | 64-case subset, minimal initial harness |
| Self-Harness (Zhang 2026) | MiniMax M2.5 + final harness | 53.9 overall, 61.9 held-out | After 3 accepted edits |
| Self-Harness (Zhang 2026) | Qwen3.5-35B-A3B + initial | 18.0 overall, 23.8 held-out | Near-floor starting point |
| Self-Harness (Zhang 2026) | Qwen3.5-35B-A3B + final | 36.7 overall, 38.1 held-out | After 4 accepted edits |
| Self-Harness (Zhang 2026) | GLM-5 + initial | 46.1 overall, 42.9 held-out | |
| Self-Harness (Zhang 2026) | GLM-5 + final | 57.0 overall, 57.1 held-out | After 4 accepted edits |

> [!warning]
> Numbers above are **not directly comparable** across sources. Meta-Harness uses the full 89-task set; Self-Harness uses a 64-task filtered subset. Model differences and evaluation protocols also differ. Treat these as same-benchmark-family rather than head-to-head.

## Appears in

- [[../sources/2026-koh-multi-agent-computer-use]] — comparison-context benchmark
- [[../sources/2026-lee-meta-harness]] — primary harness-search target; "discovery problem" framing
- [[../sources/2026-zhang-self-harness]] — primary self-improvement target; 64-case subset with held-in/held-out split

## Related entities

- _None yet._ Candidates: Harbor (execution environment); SWE-bench Verified (sibling agentic-coding benchmark); Claude Code (hand-engineered baseline harness on this benchmark).

## Topics

- [[../topics/benchmarks]]
- [[../topics/agent-harness]] (the benchmark is increasingly used as a target *for harness search*, not just as an eval of fixed harnesses)

## External links

- Paper: https://arxiv.org/abs/2601.11868
- Harbor execution framework: https://www.harborframework.com/docs/tutorials/running-terminal-bench
- Meta-Harness's released TerminalBench-2 artifact: https://github.com/stanford-iris-lab/meta-harness-tbench2-artifact
