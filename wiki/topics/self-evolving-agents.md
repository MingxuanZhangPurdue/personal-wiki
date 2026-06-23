---
type: topic
slug: self-evolving-agents
created: 2026-06-17
updated: 2026-06-18
source_count: 2
---

# Self-Evolving Agents

Agents that improve themselves over time — through self-generated data, self-reflection, prompt/tool/skill updates, curriculum self-play, or online weight updates. Includes systems like Voyager, ADAS, self-improving coding agents, and continuous-learning loops.

## Current understanding

Two sources now, sitting at adjacent points on a "who is the proposer?" axis that maps onto the [[agent-harness#harness-improvement-paradigms-3-way-taxonomy-from-self-harness-figure-1|human / Meta / Self trilemma]] from Self-Harness's Figure 1:

| Source | Proposer | Target | Loop closure |
|---|---|---|---|
| [[../sources/2026-lee-meta-harness\|Meta-Harness]] | **Stronger external** coding agent (Claude Code + Opus-4.6) | Weaker fixed model's harness (Haiku 4.5 / GPT-OSS-20B etc.) | Open at the top: requires a strictly stronger proposer |
| [[../sources/2026-zhang-self-harness\|Self-Harness]] | **Same fixed model** as the target | Its own harness | Closed: model evaluates, proposes, accepts edits to its own scaffold |

Self-Harness is the cleaner "self-evolving" datapoint of the two: in Meta-Harness the *target* is fixed but the *proposer* is a different, stronger system, which makes the loop more like distillation-of-engineering-effort than true self-evolution. Self-Harness collapses proposer and target into one model, so any improvement is genuinely something the agent did to itself.

A few framings worth tracking:

- **"Code-space self-improvement" vs "weight-space self-improvement."** Meta-Harness operates entirely in code: the proposer writes new harness code, runs it, and the new code becomes part of the experience pool for the next round. No weights are updated. This sidesteps a lot of the typical self-evolving-agent failure modes (reward hacking, mode collapse from RL noise, catastrophic forgetting). Authors flag this in the discussion: "overfitting in code space is more inspectable: brittle if-chains or hard-coded class mappings are visible on inspection in a way that weight-space overfitting is not."
- **Self-evolution = search + memory + experience replay over an immutable history.** Meta-Harness's filesystem is, in effect, an append-only experience store that's both the input to the next iteration and the long-term memory of the system. The proposer chooses what to retrieve from this store — i.e., the agent does its own experience-replay scheduling. This is closer to "external memory + adaptive access" than to classical self-play.
- **The skill / scaffold matters more than the iteration budget.** Authors' Appendix D: "iterating on the skill text had a larger effect on search quality than changing iteration count or population size." This is a useful caveat for anyone trying to build a self-evolving agent: the *steering* (how the agent decides what to look at and what to try) is probably more important than the raw number of rounds.
- **Non-Markovian self-improvement.** Per Meta-Harness Appendix A.2 iter 10: the proposer references results from a separate earlier run ("The evolution history showed 'don't cleanup service artifacts' was worth +18pp"). The system can carry lessons across distinct search runs because the filesystem is persistent. Worth tracking whether this becomes a standard pattern.
- **Model-specific self-evolution.** Self-Harness's qualitative analysis shows the three target models (MiniMax M2.5, Qwen3.5-35B-A3B, GLM-5) converge to *different* retained edits even from the same initial harness on the same benchmark — e.g., Qwen3.5 ends up with a tool-error-triggered artifact middleware that the other two never accept. This is evidence that self-evolution is genuinely producing model-specific scaffolding rather than just finding generic "good harness" prompts. Meta-Harness can't observe this directly because its single proposer optimizes for one target at a time.
- **Acceptance gate as the regression-defense primitive.** Self-Harness uses a strict non-regressive Pareto rule: an edit is accepted only if it does not decrease in-distribution pass rate **and** does not decrease held-out pass rate, with at least one strictly increasing. Meta-Harness uses Pareto-frontier maintenance plus search-set/test-set discipline. Both are *conservative*; both seem to be load-bearing for avoiding degeneration. Candidate primitive: any self-evolving-agent design needs an explicit non-regressive acceptance gate, not just a "score went up" check.

## Key concepts

_None promoted yet. Candidates: code-space self-improvement, filesystem-as-experience-store, additive-over-corrective pivot, non-Markovian iteration, non-regressive acceptance gate, model-specific scaffold convergence._

## Sources

- [[../sources/2026-lee-meta-harness]] — Lee et al. (2026). Outer-loop search over harness code where the proposer (Claude Code + Opus-4.6) reads all prior candidates' source/scores/traces via filesystem. Structurally a self-evolving meta-agent operating in code space rather than weight space — but the proposer is a *different, stronger* model than the target, so the loop is not strictly self-closing.
- [[../sources/2026-zhang-self-harness]] — Zhang et al. (2026). Tightly-bounded propose-evaluate-accept loop where the *same fixed model* is both target and proposer. Three stages: Weakness Mining (verifier-grounded failure clustering on traces), Harness Proposal (diverse-yet-minimal edits to a declared interface of instruction strings + runtime policy + subagent/skill/middleware hooks), Proposal Validation (non-regressive Pareto acceptance gate on in-distribution + held-out subsets). The cleaner self-evolution datapoint of the two; demonstrates model-specific scaffold convergence across three mid-tier models from the same starting harness.

## Open questions

- What's the distinction between "self-improvement" via context updates vs weight updates? Meta-Harness and Self-Harness both operationalize the former cleanly; comparisons against weight-space methods (RL fine-tuning) on the same tasks would be informative.
- How is forgetting / regression handled? Meta-Harness uses Pareto-frontier maintenance; Self-Harness uses an explicit per-transition non-regressive Pareto acceptance rule (`Δ_in ≥ 0 AND Δ_ho ≥ 0 AND max > 0`). Both are conservative, neither is empirically compared. Worth seeing which (or what combination) becomes standard.
- What stops self-evolving agents from drifting into reward-hacked or degenerate behavior? Meta-Harness's defense is "search-set vs test-set discipline" in 2/3 domains plus regex leakage audits; Self-Harness's defense is the non-regressive acceptance gate + held-out evaluation per round. Neither tests against an adversarial held-out set.
- Does self-evolution converge or oscillate? Meta-Harness reports flat or improving curves across 20–40 iterations; Self-Harness retains only 3–4 edits across 10 rounds (mostly rejected), suggesting steeper diminishing returns under the tighter acceptance gate. Whether this is a property of the gate or of the smaller search space is open.
- **Does same-model self-evolution scale to strong models?** Self-Harness intentionally uses mid-tier models on a minimal initial harness, so the gains may be amplified by a low starting floor. Whether a frontier-tier model (e.g., Claude Sonnet 4.5) starting from a strong hand-engineered harness (e.g., Claude Code) can self-evolve a non-trivial improvement is the natural follow-up neither paper runs.

## Related topics

- [[agentic-rl]] (the harness-level credit-assignment framing in Meta-Harness §2 maps loosely onto non-weight policy improvement)
- [[agent-memory]] (the filesystem-as-experience-store is structurally agent memory at the meta-level)
- [[agent-harness]] (Meta-Harness uses self-evolution to *discover* harnesses)

## Log

- 2026-06-17: created (stub)
- 2026-06-18: added [[../sources/2026-lee-meta-harness]] as first source; first non-stub "Current understanding" framing around code-space (vs weight-space) self-improvement and filesystem-as-experience-store
- 2026-06-18: added [[../sources/2026-zhang-self-harness]] as the cleaner self-evolution datapoint (same model as proposer + target); added "proposer-target" axis at top of Current understanding mapping onto the human/Meta/Self trilemma; added model-specific-convergence and acceptance-gate-as-regression-defense observations; expanded Open questions to include the "does it scale to strong models" follow-up
