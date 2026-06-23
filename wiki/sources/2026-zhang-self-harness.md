---
title: "Self-Harness: Harnesses That Improve Themselves"
authors: ["Hangfan Zhang", "Shao Zhang", "Kangcong Li", "Chen Zhang", "Yang Chen", "Yiqun Zhang", "Lei Bai", "Shuyue Hu"]
year: 2026
type: paper
raw: ../../raw/papers/2026-zhang-self-harness/paper.pdf
url: https://arxiv.org/abs/2606.09498v1
added: 2026-06-18
status: read
tags: [harness-search, self-improvement, terminalbench, model-specific-adaptation, regression-gate]
topics: [agent-harness, self-evolving-agents, evaluation, benchmarks]
---

# Self-Harness: Harnesses That Improve Themselves

> Source: [[../../raw/papers/2026-zhang-self-harness/source.md|raw entry]] · [arXiv:2606.09498v1](https://arxiv.org/abs/2606.09498v1)

## TL;DR

A propose–evaluate–accept loop in which **the same fixed model is both the target agent and the proposer of edits to its own harness**, with no stronger external proposer. Three stages: cluster failed traces by verifier-grounded signature (Weakness Mining) → generate K parallel diverse-but-minimal candidate edits (Harness Proposal) → conservative non-regressive promotion gate over held-in and held-out splits (Proposal Validation). Instantiated on a 64-task subset of TerminalBench-2.0 with three mid-tier base models (MiniMax M2.5, Qwen3.5-35B-A3B, GLM-5) starting from an intentionally minimal DeepAgent-based harness. Held-out pass-rate gains of 21.4 / 14.3 / 14.2 percentage points (relative: 53% / 60% / 33%). Frames itself explicitly as a *third* paradigm sitting between human harness engineering and [[2026-lee-meta-harness|Meta-Harness]] (which uses a stronger external agent).

## Key ideas

- **Three paradigms of harness improvement, made explicit (Figure 1).** Human engineering → Meta-Harness (stronger external proposer) → Self-Harness (same fixed model is both target and proposer). The paper's framing depends on this taxonomy, so it lives or dies on whether "self" really matters relative to "external strong proposer."
- **Verifier-grounded failure clustering (not semantic clustering).** Failures are grouped by exact agreement on a 3-tuple `ϕ(r) = (cause, causal-status, mechanism)` — the terminal verifier reason, the agent-side behavior that caused it, and the abstract reusable mechanism. Two timeouts may not cluster together if they reflect different mechanisms; two superficially different failures may cluster if the mechanism matches. This is deliberately not learned similarity.
- **Diverse-yet-minimal proposal constraint.** K parallel proposals required to be *materially distinct* across branches (different surface, mechanism, or hypothesis) but *minimal* within each branch (touch only the surface needed). Explicitly forbids broad rewrites of the control architecture. This is structurally more conservative than Meta-Harness's "rewrite the whole single-file Python harness."
- **Conservative non-regressive acceptance rule.** A candidate is accepted iff $\Delta^{(j)}_\text{in} \geq 0$ AND $\Delta^{(j)}_\text{ho} \geq 0$ AND $\max(\Delta^{(j)}_\text{in}, \Delta^{(j)}_\text{ho}) > 0$. Trades-off across splits are rejected even if total pass count rises. Stronger non-regression guarantee than Meta-Harness's Pareto-frontier-of-end-states framing — but only inside a single benchmark, so it doesn't catch cross-benchmark overfitting.
- **Tight editable interface.** The proposer can only modify declared configuration points: `build_system_prompt`, `build_bootstrap_instruction`, `build_execution_instruction`, `build_verification_instruction`, `build_failure_recovery_instruction`, `build_runtime_control_policy`, `build_subagents`, `build_skills`, plus a middleware hook. This is much narrower than Meta-Harness's "any Python file 100–1000 lines."
- **Merge-multiple-accepted-edits per round.** If several candidates pass the gate in the same round, all their edits are merged into the next harness. Paper does not discuss merge conflicts or non-additivity.
- **Model-specific adaptation as the qualitative claim.** Per-model retained edits differ: M2.5 → "create output early" + content-tag formatting + bounded tool-call budget; Qwen3.5-35B-A3B → dependency precheck + exact-retry mitigation + tool-error-triggered middleware; GLM-5 → persistent PATH/env across shell sessions + exploration→implementation transition. The same initial harness exposes different pathologies in different models — used as evidence that the loop *adapts* rather than *adds boilerplate.*
- **Self-loop without external strong proposer.** The crucial difference from Meta-Harness: the proposer model is the *same model* that just failed, under the *same harness* that just failed. No Claude Code + Opus 4.6 sitting outside. This is either a meaningful capability claim (weak models can self-improve their own scaffold) or a confound (initial harness is so minimal that almost any edit helps).

## Notable quotes

> [!quote] p.4
> "Self-Harness operates over a lineage of harnesses h₀, h₁, …, where each transition corresponds to a bounded edit to the execution protocol rather than an update to the model weights."

> [!quote] p.6
> "The clustering is deterministic and evaluator-grounded: two failed cases are grouped together only when they agree on what the verifier ultimately rejected, how the agent behavior contributed to that rejection, and which reusable behavioral mechanism was involved. The goal is not to discover latent semantic similarity among traces, but to aggregate failures that plausibly admit the same harness-level intervention."

> [!quote] p.7
> "A candidate is accepted only if it improves at least one split without degrading the other ... Proposals that only trade off one split against the other are rejected, even if their total pass count increases."

> [!quote] p.14 (Conclusion, limitations)
> "Self-Harness also has important limits. It studies bounded harness edits under fixed benchmarks, not open-ended self-improvement. Accepted edits may still reflect benchmark-specific failure patterns, and the protocol depends on the quality of verifier outcomes and trace records. Higher-stakes harness changes would require stronger acceptance gates than pass-rate non-regression alone."

## Methodology / structure

**Algorithm 1 (page 4):** For T rounds: (i) evaluate current harness `h_t` on held-in `D_in` and held-out `D_ho`; (ii) build evidence bundle `B_t` from clustered failed traces on held-in; (iii) generate K parallel proposals `P_t = {(Δ_j, a_j)}` where `a_j` is an audit record (target pattern, edited surface, expected effect, regression risks); (iv) for each proposal, instantiate `h_t^(j) = Δ_j(h_t)`, re-evaluate on both splits, apply the conservative acceptance rule; (v) if any accepted, merge into `h_{t+1}`, else `h_{t+1} = h_t`.

**Setup.**
- *Benchmark:* TerminalBench-2.0 (Merrill et al. 2026), 89 containerized terminal tasks, 64-case subset (multimodal and unstable-external-resource tasks excluded). Held-in/held-out split fixed across all harness variants.
- *Models:* MiniMax M2.5 (hosted API), GLM-5 (OpenRouter), Qwen3.5-35B-A3B (locally deployed on 4× H200 via SGLang).
- *Initial harness (Figure 3):* `BASELINE_SYSTEM_PROMPT` is a 4-line "you are in a Terminal Bench 2 task; use filesystem and shell tools; verify outcomes" prompt. `build_bootstrap_instruction` = "Start by inspecting the workspace…". `build_runtime_control_policy = {"enabled": False, ...}`. `build_subagents` and `build_skills` both empty. No verification beyond "verify with the most targeted command."
- *Metric:* Pass(%), averaged over 2 repeated attempts per harness candidate.
- *Acceptance rule repeats:* When evaluation is stochastic, repeat candidate eval and apply the rule on aggregate pass counts.

**Main results (Figure 4, page 9).**

| Model | Initial held-in | Final held-in | Initial held-out | Final held-out | Held-out Δ (pp) | Held-out relative |
|---|---|---|---|---|---|---|
| MiniMax M2.5 | 43.0 | 50.0 | 40.5 | 61.9 | +21.4 | +53% |
| Qwen3.5-35B-A3B | 15.1 | 36.0 | 23.8 | 38.1 | +14.3 | +60% |
| GLM-5 | 47.7 | 57.0 | 42.9 | 57.1 | +14.2 | +33% |

The Qwen3.5 held-in relative (+138%) is what the paper headlines, but starts from 15.1% — i.e. near-floor.

**Per-model evolution trajectories (Figures 5–6, 10).** All three runs reach the final harness in 5–18 iterations through 3–4 accepted edits. Many proposed candidates are rejected. Notably, the Qwen3.5 run has **abandoned branches** (subagent and skill branches were tried, did not improve further, were dropped) — useful negative evidence that bigger structural changes do not automatically help.

**Per-model retained edits, summarized (Figures 5b, 6b, 10b).**

| Model | Retained edits |
|---|---|
| MiniMax M2.5 | "create output early" bootstrap; correct content-tag formatting (e.g. `image_url` not `image`); runtime policy enabled with `max_total_tool_messages = 50` and instruction to redirect after stalled loops |
| Qwen3.5-35B-A3B | Dependency precheck bootstrap; exploration/missing-artifact loop breaker; "do not retry exact same command with exact same parameters"; tool-error-triggered artifact-creation middleware |
| GLM-5 | Persistent PATH export across shell sessions (write to `/etc/profile.d` or `~/.bashrc`); "transition from exploration to implementing and testing" verification nudge |

**Trace-level case studies (Figures 7–9).** The paper shows specific before/after traces for `count-dataset-tokens` (M2.5: stops dataset exploration earlier, writes answer file, reads it back), `extract-elf` (Qwen3.5: middleware re-creates deleted artifact before stopping), and `build-pov-ray` (GLM-5: bounded staged operations, validates archive evidence early, repairs failed sanity check before finalizing). These are useful as existence proofs that the discovered edits actually change agent behavior in the diagnosed direction, not just shift aggregate numbers.

## Results / claims

- **Headline claim:** Self-Harness improves Pass(%) across all 3 model backends without degrading either split, using only harness-level edits. Held-out gains 14–21 pp absolute, 33–60% relative.
- **Mechanism claim:** Qualitative analyses show retained edits are *model-specific* (not a generic prompt) and are *targeted* at the failure mechanisms surfaced in the evidence bundle. Backed by retained-edits tables and trace-level case studies but not by a controlled ablation.
- **What is not claimed (correctly):** Open-ended self-improvement; cross-benchmark generalization; capability beyond the 3 mid-tier models tested; competitiveness with externally-optimized harnesses on the same benchmark.
- **What is implicitly claimed but not isolated:** That the *self* part (same model as both target and proposer) is important. Without an ablation that swaps in a stronger external proposer, this is a framing claim, not a measured one.

## Connections

- Topics: [[../topics/agent-harness]] · [[../topics/self-evolving-agents]] · [[../topics/evaluation]] · [[../topics/benchmarks]]
- Entities: [[../entities/terminalbench-2]]
- Related sources:
  - [[2026-lee-meta-harness]] — **direct sibling**. Both target TerminalBench-2.0 with harness search. Self-Harness explicitly contrasts itself with Meta-Harness as "same model is proposer" vs "stronger external proposer." No head-to-head experiment despite the comparable setup.
  - [[2026-koh-multi-agent-computer-use]] — uses TerminalBench-2.0 as a comparison-context benchmark for centralized MAS topology.
  - [[2026-mao-decentralized-mas-shared-context]] — different topology direction (decentralized MAS) but shares the "load-bearing knob is a quality gate" pattern (admission-time verifier ↔ non-regressive acceptance gate).

## Open questions / criticisms

- **Non-SOTA model selection.** All three test models are mid-tier 2026 Chinese-lab models (MiniMax M2.5, Qwen3.5-35B-A3B, GLM-5). No Claude / GPT / Gemini comparison, and notably no Claude Haiku 4.5 (which Meta-Harness *does* hold out on the same benchmark). It's plausible that stronger models — which already have many of the "create artifact early," "don't retry same command" behaviors internalized — would gain much less from Self-Harness, or none at all.
- **Intentionally minimal initial harness.** Figure 3 is essentially a no-op harness: 4-line system prompt, default DeepAgent tools, no verification scaffolding beyond "verify with the most targeted command." Starting from this floor inflates relative gains (138% on Qwen3.5 held-in is from 15.1% → 36.0%). The relevant comparison would be Self-Harness applied to *Terminus-KIRA* (the harness Meta-Harness improves on) — but the paper doesn't run that.
- **No head-to-head with Meta-Harness on TerminalBench-2.0.** Both papers use the same benchmark and report final TerminalBench-2 numbers. They cite each other (Meta-Harness predates Self-Harness by ~3 months). The absence of a direct comparison is conspicuous.
- **Editable interface is tightly bounded.** The proposer can only modify declared configuration points (instruction strings, runtime policy dict, subagents list, skills list, middleware hook). It cannot rewrite the control loop, change the tool set beyond what subagents/skills/middleware allow, or change the trace format. This *constrains overfitting* but also limits what kinds of harness improvement the protocol can ever discover. It is unclear how much of the "Self-Harness works" claim is "self-improvement works" vs "even tightly-constrained edits to a minimal harness help."
- **Merge of multiple accepted candidates in one round** is not analyzed for conflicts. Two compatible-when-evaluated-separately edits could be incompatible-when-merged. No ablation.
- **64-case subset of an 89-task benchmark.** Multimodal and unstable-external-resource tasks excluded "to reduce evaluation noise from factors outside the harness." Defensible per task, but introduces selection on the eval set that interacts with which failure mechanisms the proposer ever sees.
- **Verifier dependence is acknowledged but not stress-tested.** Authors flag in conclusion: "the protocol depends on the quality of verifier outcomes and trace records."
- **No ablation isolating the load-bearing component.** Unlike MACU (replan budget), DeLM (verifier gate), and Meta-Harness (raw traces in proposer filesystem), Self-Harness does not run a clean ablation that says "this one design choice drives most of the gain." Candidates: the verifier-grounded clustering, the non-regressive Pareto gate, the diverse-yet-minimal proposal constraint. None isolated.

## My take

> [!info] Drafted from human's first-pass notes + Claude's closer reading — review and edit.

### Human's initial reading notes (first-pass, flagged for revisit)

Per human's first read, two main concerns + one structural question:

1. **Non-SOTA models inflate the apparent gain.** "The models are bad, hence the harness here is very helpful — if we do Claude or OpenAI models, what would happen?" This is exactly right and a Meta-Harness-style held-out check on Claude Haiku 4.5 / Sonnet would be the obvious correction.
2. **Intentionally weak initial harness is a confound.** "Like training neural nets, this is like starting with init model, of course optimization works. What if you do it from a trained model?" Also exactly right — the relevant comparison is Self-Harness on Terminus-KIRA (or even Claude Code's default scaffold), not Self-Harness on a 4-line system prompt.
3. **Methodological flexibility is unclear.** "Seems like not high" — the editable interface is indeed tightly bounded to declared configuration points + subagents/skills/middleware. The protocol cannot, e.g., change the trace format, rewrite the control loop, or add new tool primitives outside what middleware can express. This is by design (it limits overfitting and keeps the system tractable) but it also caps what kinds of improvement can ever be discovered.
4. **Acknowledged caveat:** "Automation is helpful, very helpful, but still flag." Agreed — the paper's contribution to the *paradigm-space mapping* (third option in the human–Meta–self trilemma) is real even if the empirical case is overstated.

### Closer reading (Claude's assessment alongside)

The human's three criticisms hold up under closer reading. I'd add the following:

- **The structural framing is the strongest contribution, not the empirics.** Figure 1's trilemma (human / Meta / Self) is a genuinely useful piece of taxonomy. Before this paper, the wiki implicitly conflated Meta-Harness and "self-evolving harness" — they are not the same thing because Meta-Harness uses a *stronger external proposer*. The Self-Harness framing forces the distinction. This is worth tracking even if the empirics don't fully support the dichotomy yet.
- **The conservative non-regressive acceptance rule is methodologically nice and worth borrowing.** It is strictly stronger than Meta-Harness's "produce a Pareto frontier and pick the best end-state." Pareto-front-of-end-states does not guarantee that any individual transition in the lineage was non-regressive — Self-Harness's rule does, at the per-transition level. This matters more for *deployable* harness search than for *paper-headline* harness search.
- **Verifier-grounded failure clustering is structurally adjacent to DeLM's admission-time verification.** Both use an LLM (or in Self-Harness's case, deterministic signature matching) to gate what counts as actionable evidence. Pattern continues from the [[../topics/agent-harness]] page: load-bearing components live in the quality/diagnostic dimension.
- **The "same model is proposer" claim is the unfalsified one.** The paper does not run "swap in a stronger external proposer with everything else fixed" — which is the obvious ablation to isolate whether *self* matters or whether just *any* propose–evaluate–accept loop with this evidence bundle would work. Without that, the Self-Harness vs Meta-Harness comparison is confounded by ~5 design choices simultaneously.
- **My single biggest follow-up:** Run the experiment the paper should have run — Claude Code's default scaffold as the initial harness, Claude Sonnet 4.5 as the fixed model, Self-Harness loop on the same TerminalBench-2 subset. If gains drop to <2 pp, the paper's headline is "self-improvement works on weak models with weak initial harnesses." If gains stay at ~10 pp, the paper's headline is "self-improvement works in general." Currently we don't know which.

### What's worth taking forward

- The *paradigm trilemma* (human / Meta / Self) is genuinely clarifying and should propagate into the [[../topics/self-evolving-agents]] page.
- The *per-transition non-regressive acceptance rule* is a methodological primitive worth promoting into [[../topics/evaluation]] alongside Meta-Harness's Pareto-frontier reporting.
- The *verifier-grounded failure-signature clustering* (`ϕ = (cause, causal-status, mechanism)`) is a primitive worth tracking — possibly a sharper version of DeLM's verifier-gated admission.
- Treat the empirical claim ("Self-Harness improves all 3 models by 14–21 pp held-out") as soft until the natural ablations (stronger proposer, stronger initial harness, stronger base model) are run.

## Log

- 2026-06-18: ingested at medium depth; medium discussion with human in chat; per human's instruction, initial reading notes preserved as a labeled subsection of My take with Claude's closer reading alongside; cross-linked to [[2026-lee-meta-harness]] as direct sibling; promoted [[../entities/terminalbench-2]] as first entity page
