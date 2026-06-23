---
type: topic
slug: agent-harness
created: 2026-06-17
updated: 2026-06-18
source_count: 4
---

# Agent Harnesses

The scaffolding around an LLM that turns it into an agent: control loop, tool interface, planning, error recovery, sub-agent spawning, observation formatting. Examples: AutoGPT, smolagents, OpenHands, Claude Code, Cursor agent, Devin, SWE-agent, Terminus-KIRA.

## Current understanding

Four sources now, three different framings for harness improvement, but the same convergence on what's load-bearing.

### Harness-improvement paradigms (3-way taxonomy, from Self-Harness Figure 1)

[[../sources/2026-zhang-self-harness|Self-Harness]] explicitly maps the design space into three paradigms, which clarifies framings that were implicit in earlier wiki entries:

| Paradigm | Proposer | Target | Examples |
|---|---|---|---|
| Human harness engineering | human expert | fixed agent harness | Claude Code, Codex, OpenHands, MACU, DeLM |
| Meta-improvement (external proposer) | stronger external agent | weaker fixed model's harness | [[../sources/2026-lee-meta-harness\|Meta-Harness]] (Claude Code + Opus 4.6 proposes for GPT-OSS-20B etc.) |
| Self-improvement (internal proposer) | same fixed model | its own harness | [[../sources/2026-zhang-self-harness\|Self-Harness]] (Qwen3.5/M2.5/GLM-5 propose for themselves) |

Worth tracking because before Self-Harness, the wiki implicitly conflated rows 2 and 3 under "harnesses-as-search-targets." The distinction is whether the proposer is *stronger than* the target (Meta) or *the same as* the target (Self).

### The "load-bearing knob" pattern (now 3 clean + 1 soft = 4 sources)

Across [[../sources/2026-koh-multi-agent-computer-use|MACU]], [[../sources/2026-mao-decentralized-mas-shared-context|DeLM]], [[../sources/2026-lee-meta-harness|Meta-Harness]], and (softly) [[../sources/2026-zhang-self-harness|Self-Harness]], the component most plausibly responsible for the headline result lands in the **quality / control / diagnostic dimension** — not the topology or parallelism dimension.

| Source | Topology / structure | Load-bearing component | Pole | Ablation-isolated? |
|---|---|---|---|---|
| MACU | centralized manager + mutable DAG + parallel VM-isolated subagents | **bounded replan budget B** (B=0 → 25% SR, B=10 → 58% SR on Odysseys) | control | yes |
| DeLM | decentralized blackboard + verified shared context + dependency-aware task queue | **admission-time LLM verifier** on every shared-state write (-4.9 pp when removed) | quality gate | yes |
| Meta-Harness | minimal outer loop over harness code | **raw execution traces in the proposer's filesystem** (scores-only/scores+summary → 34.6/34.9 median; full traces → 50.0) | diagnostic richness | yes |
| Self-Harness | tightly-bounded propose-evaluate-accept loop | **verifier-grounded failure-signature clustering** + **conservative non-regressive acceptance gate** (per-transition Pareto) | quality gate + diagnostic | **no** (not ablated) |

Self-Harness is a *soft* fourth data point: it matches the pattern (load-bearing parts are diagnostic + quality), but it does not run a controlled ablation to isolate them. Per current user preference, **synthesis page promotion still pending** until a 4th source with a *clean* ablation lands.

### Concrete harness design observations

### Concrete harness design observations

- **Manager / worker split is no longer obviously default.** MACU keeps a manager and makes the plan mutable; DeLM removes the manager entirely in favor of a shared verified context + task queue. Both beat their respective "centralized parallel" baselines.
- **The plan structure is a harness design choice.** MACU uses a mutable DAG with a bounded replan budget; DeLM uses a task queue with dependency tags + a shared blackboard. Trees, partial-order plans, hierarchical task networks, recursive structures (RLM-style) are all unmeasured.
- **Verification of shared state is an under-used primitive.** DeLM's biggest single ablation hit is removing the LLM verifier gate; MACU doesn't have one. Self-Harness's verifier-grounded clustering of failed traces is structurally adjacent (verifier as evidence-gate rather than admission-gate). Candidate primitive for future MAS harnesses.
- **Isolation primitives.** MACU: per-subagent VM (necessary for parallel computer-use). DeLM: per-agent context window with lock-free snapshot reads + atomic admission (sufficient for text-only). The right isolation level appears to track the domain.
- **Harness search converges to small additive patches (n=2 now).** Meta-Harness's TerminalBench-2 winner over Terminus-KIRA is a **+80-line** purely-additive env-bootstrap. Self-Harness's retained edits across all 3 models are also small additive changes to declared interfaces (instruction strings, runtime policy dict, single middleware), with the largest accepted change being a per-model tool-error-triggered artifact middleware on Qwen3.5. Two independent papers, two different harness-search regimes, both converge to small additive patches. **If this continues, discovered harnesses are deployable artifacts, not research curios.**
- **Harnesses-as-search-targets is becoming a primary framing.** Meta-Harness treats "the harness" as the optimization target and "the LLM" as fixed, inverting the usual research framing. Self-Harness does the same with a same-model proposer. Authors cite a 6× perf gap on the same benchmark depending on harness (per [Tian et al. 2026, SWE-bench Mobile]).
- **Initial-harness floor matters a lot.** Meta-Harness starts from Terminus-KIRA (a strong hand-engineered harness) and reports +10 pp on Haiku 4.5. Self-Harness starts from an *intentionally minimal* DeepAgent baseline (≈4-line system prompt, no verification, no middleware) and reports +14 to +21 pp held-out across 3 mid-tier models. The relative gain is not directly comparable across these two studies because the floor is so different. A useful future paper would run either method from *both* the minimal and the strong starting point.
- **Editable interface tightness is a design choice with a clear trade-off.** Meta-Harness lets the proposer rewrite arbitrary Python in a 100–1000-line file. Self-Harness restricts edits to declared configuration points (a handful of instruction-string builders + a runtime-policy dict + subagent/skill/middleware hooks). Tighter interface = less overfitting, more deployability, smaller search space; looser interface = more expressive, more risk of brittle if-chains and benchmark overfitting. Both regimes report wins on TerminalBench-2; the open question is which one is closer to the right ceiling.

## Key concepts

_None promoted yet. Candidates: load-bearing-diagnostic-knob pattern, admission-time verification, replan budget, filesystem-as-feedback-channel, additive-over-corrective pivot, verifier-grounded failure-signature clustering, per-transition non-regressive acceptance, human-Meta-Self harness-improvement trilemma._

## Sources

- [[../sources/2026-koh-multi-agent-computer-use]] — Koh et al. (2026). MACU: a manager + mutable-DAG + parallel-VM-subagent harness for computer use. Ablations show replan budget drives most of the gain.
- [[../sources/2026-mao-decentralized-mas-shared-context]] — Mao & Mirhoseini (2026). DeLM: a manager-less blackboard harness with verified shared context + task queue. Ablations show admission-time verification drives most of the gain. Code released.
- [[../sources/2026-lee-meta-harness]] — Lee et al. (2026). Meta-Harness: **outer-loop search over harness code** using a stronger external coding agent (Claude Code + Opus-4.6) that reads all prior candidates' source/scores/traces via filesystem. Ablations show raw trace access drives most of the gain; summaries actively hurt. First wiki source where the harness *itself* is the search target, not the contribution.
- [[../sources/2026-zhang-self-harness]] — Zhang et al. (2026). Self-Harness: same fixed model is both target and proposer; tightly-bounded propose-evaluate-accept loop with verifier-grounded failure clustering + non-regressive Pareto gate; on TerminalBench-2 (64-case subset) lifts held-out pass rate by 14–21 pp absolute across 3 mid-tier models from a minimal initial harness. **No clean ablation** to isolate which component is load-bearing — flagged as a soft 4th data point on the convergence pattern.

## Open questions

- How much does harness design matter vs. underlying model capability? Meta-Harness's TerminalBench-2 win on Haiku 4.5 (37.6%) is +10 pp over Claude Code (27.5%) on the *same* model — strong evidence harness matters even when the model is fixed. Self-Harness's 14–21 pp gains on mid-tier models from a *minimal* starting harness leave the relative role of "model capability vs harness quality" partially unresolved.
- **Does Self-Harness work on strong models with strong starting harnesses?** This is the natural follow-up the paper doesn't run. If Claude Sonnet 4.5 + Claude Code's default scaffold, run through Self-Harness on the same TerminalBench-2 subset, gains <2 pp, then "self-improvement" is mostly a weak-model story. If gains stay at ~10 pp, it's a general story. Currently unknown.
- **Self vs Meta vs Human, on the same benchmark with the same starting point.** Meta-Harness and Self-Harness both target TerminalBench-2 but with different models, different initial harnesses, and no shared comparison point. A head-to-head from a common starting harness would isolate "does using a stronger external proposer matter?"
- What harness primitives generalize across domains? Meta-Harness shows one cross-domain primitive (env bootstrap) on TerminalBench-2 and a different one (label-primed query-anchored retrieval) on text classification. The proposer doesn't transfer primitives across domains; each domain re-derives its own. Self-Harness adds model-specific differentiation *within* the same domain — different models converge to different retained edits on the same benchmark.
- Where's the line between "harness" and "agent framework"? Meta-Harness ducks this and just says "single-file Python program." Self-Harness goes the other direction and pins the harness down to a small declared interface (instruction strings + runtime policy + subagent/skill/middleware hooks). Worth tracking whether the field converges on a tighter definition.
- Does harness search converge to small additive patches, or does it sometimes find rewrites? Meta-Harness's results are 100–1000 lines per discovered harness; the TerminalBench-2 winner is at the bottom of that range and additive. Self-Harness's retained edits are uniformly small additive changes (3–4 edits per model). **n=2 for "additive wins"** on the same benchmark; if a third source bucks the trend, worth flagging.

## Related topics

- [[sandbox]]
- [[evaluation]]
- [[self-evolving-agents]] (Meta-Harness is structurally a self-evolving meta-agent over harness code)
- [[multi-agent-coordination]]

## Log

- 2026-06-17: created (stub)
- 2026-06-17: added [[../sources/2026-koh-multi-agent-computer-use]]; first non-stub "Current understanding" entry on multi-agent harness design
- 2026-06-17: added [[../sources/2026-mao-decentralized-mas-shared-context]]; expanded Current understanding to cover both MACU (centralized mutable-DAG) and DeLM (decentralized blackboard) topologies; flagged admission-time verification as a candidate primitive
- 2026-06-18: added [[../sources/2026-lee-meta-harness]]; promoted the "load-bearing knob lives in quality/diagnostic dimension" framing to the top of the page as a 3-paper signal; flagged synthesis-page promotion as pending until a 4th source corroborates
- 2026-06-18: added [[../sources/2026-zhang-self-harness]] as a **soft** 4th data point on the load-bearing-knob pattern (matches the pattern, no clean ablation isolating it); promoted the human/Meta/Self harness-improvement trilemma to the top of "Current understanding"; expanded harness-search-converges-to-additive-patches observation to n=2; synthesis page still pending until a 4th source with a *clean* isolating ablation lands
