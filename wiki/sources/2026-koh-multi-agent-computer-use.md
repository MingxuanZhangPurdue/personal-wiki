---
title: "Multi-Agent Computer Use"
authors: ["Jing Yu Koh", "Ruslan Salakhutdinov", "Daniel Fried"]
year: 2026
type: paper
raw: ../../raw/papers/2026-koh-multi-agent-computer-use/paper.pdf
url: https://arxiv.org/abs/2606.01533
added: 2026-06-17
status: read
tags: [computer-use, multi-agent, planning, dag, partial-observability, long-horizon]
topics: [computer-use, agent-harness, benchmarks, evaluation]
---

# Multi-Agent Computer Use

> Source: [[../../raw/papers/2026-koh-multi-agent-computer-use/source.md|raw entry]] · [arXiv](https://arxiv.org/abs/2606.01533) · [project page](https://jykoh.com/multi-agent-computer-use)

## TL;DR

Computer use agents (CUAs) today are single, serial ReAct loops on one desktop/browser — slow and brittle on long-horizon tasks. **MACU (Multi-Agent Computer Use)** replaces that with a *manager model* that maintains a **mutable DAG of subtasks** and dispatches **parallel CUA subagents** in **isolated VMs**, each carrying out a node on the DAG's ready frontier. As subagents return, the manager **re-plans** — adding, canceling, or rewriting nodes — within a fixed replan budget `B`. Across four benchmarks (OSWorld, Online-Mind2Web, WebTailBench, Odysseys) MACU improves success rate by **+3.4–25.5 percentage points** over strong single-agent CUA baselines while **also being ~1.47× faster** on the long-horizon Odysseys benchmark. Notably it **beats pass@8** of the same base CUA, evidence that the structure (decomposition + replanning) is doing work beyond just spending more compute.

## Key ideas

- **Plan as a mutable DAG, not a static checklist.** Each node has `goal`, `dependencies`, `expected_output`, and optional `init_from` (clone another node's VM state) and `variant_of` (parallel attempts of the same goal with different methods). The DAG is rewritten on every subagent return.
- **Manager / subagent split with different strengths.** Manager = a strong reasoning model (Claude Opus 4.6 in the main results); subagents = a cheaper but capable CUA (Qwen3.6-27B-Computer). Manager never touches the screen; subagents never see the global plan.
- **Per-subagent VM isolation.** Each subagent runs in its own desktop/browser VM. Two subagents can log into the same site, run conflicting shell commands, etc., without colliding. This is what makes parallelism actually safe for computer use.
- **State cloning (`init_from`).** A subagent halfway through a checkout/setup flow can be cloned so two children continue from the exact UI state, diverging into different branches. This is a capability you cannot get by "just prompting harder."
- **Variant attempts (`variant_of`).** Run multiple methods for the same goal in parallel; the manager selects the winner. Pass@k-style robustness without re-doing setup work.
- **Bounded replan budget `B`.** Replanning is the loop, but it's capped (default `B=10` on Odysseys). The ablation shows `B=0` (decompose once, never revise) gets ~25% SR on Odysseys; `B=10` gets 58.3% SR. Replanning, not parallelism, is where most of the gain comes from.
- **Information retention via the DAG.** Findings observed by a subagent that downstream agents physically cannot re-observe (modals, dynamic prices, one-time codes) get folded back into the DAG as node metadata, so dependents start with that context.

## Notable quotes

> [!quote] Abstract
> "Computer use agents (CUAs) today are primarily deployed as single serial agents. This setup is suboptimal for complex long-horizon tasks that benefit from task decomposition, parallel execution, and consistent re-planning based on new information."

> [!quote] Framing of partial observability
> "This design treats the partially observable environment of computer use as a first class challenge: information that downstream agents may not be able to re-observe are retained and passed forward through the manager and DAG structure."

> [!quote] Headline empirical claim
> "MACU consistently improves over strong single-agent baselines by 3.4–25.5% on desktop (OSWorld) and web navigation (Online-Mind2Web, WebTailBench, Odysseys) benchmarks, exhibits more favorable test-time scaling, and solves complex long-horizon tasks where single-agent CUAs get stuck."

## Methodology / structure

**System.** A loop with three roles:

1. **Manager (LLM).** Owns the DAG. On each tick, picks all nodes whose dependencies are satisfied (the "ready frontier"), dispatches them as subagent calls in parallel, waits for at least one to return, ingests the return, and *optionally re-plans* (add, cancel, rewrite nodes) up to `B` times total.
2. **CUA subagents (VLM + action policy).** Each runs in its own VM/browser. Receives a goal + relevant context distilled from the DAG, returns a structured result (success flag, evidence/observations, artifacts).
3. **Environment (VMs).** Per-subagent container or VM with a desktop or browser. Supports state snapshot/restore for `init_from`.

**Node schema.** `(id, goal, dependencies, expected_output, init_from?, variant_of?, status, observations)`.

**Models used.** Manager: Claude Opus 4.6. Subagents: Qwen3.6-27B-Computer (an open-weights CUA). Default `N=4` parallel subagents, `B=10` replan budget. Ablations vary both.

**Benchmarks evaluated.**

| Benchmark         | Domain               | Style                       | Used for       |
| ----------------- | -------------------- | --------------------------- | -------------- |
| OSWorld           | Linux desktop        | Mixed apps, mostly mid-horizon | Established headline |
| Online-Mind2Web   | Live web             | Real websites, single-goal  | Web navigation |
| WebTailBench      | Live web (long-tail) | Less common sites/flows     | Generalization |
| **Odysseys**      | Live web             | Long-horizon multi-step (their contribution) | Stress test |

**Baselines.** Single-agent versions of the same CUA (Qwen3.6-27B-Computer), plus pass@k variants (independent serial runs, take best) to control for "MACU just uses more compute."

## Results / claims

**Headline success-rate deltas over single-agent baseline:**

- OSWorld: **+3.4–6.1 pp** (smaller — these tasks are mid-horizon, less room for decomposition to pay off)
- Online-Mind2Web: **+6.8 pp** (62.1% vs ~55%)
- WebTailBench: **+~9 pp**
- Odysseys: **+25.5 pp** (58.3% vs 32.8%) — biggest gain, as expected for long-horizon

**Wall-clock speedup.** On Odysseys, MACU is **~1.47× faster** per successful task than the single-agent baseline, *despite* higher success rate. Parallel branches actually translate to wall-clock wins.

**Beats pass@8.** On Online-Mind2Web, MACU (N=4, B=10) > pass@8 of single-agent at equal or lower aggregate cost. This is the cleanest evidence that the structure matters — N=4 parallel agents *with replanning* > 8 independent serial agents.

**Ablations (the most interesting part):**

- **Replan budget `B`:** Odysseys SR climbs roughly monotonically — `B=0` ≈ 25%, `B=2` ≈ ~40%, `B=10` = 58.3%. Most of MACU's gain is here, not in parallelism.
- **Parallel breadth `N`:** Going `N=1 → 4` helps modestly; `N=4 → 8` shows diminishing returns. Suggests the right framing is "manager + replanner" first, "parallel" second.
- **Manager strength:** Weaker manager (e.g., subagent-class model as manager) cuts gains substantially. The structure isn't a substitute for a good planner.
- **VM isolation:** Removing isolation (subagents share a single VM) breaks the system on tasks with state collisions — confirms isolation is load-bearing, not cosmetic.

**Qualitative wins.** Anecdotes / case studies of MACU completing long-horizon flows where single-agent gets stuck in loops — typically because the manager catches a failed subagent and rewrites the plan rather than letting the subagent keep clicking.

## Connections

- Topics: [[../topics/computer-use]] · [[../topics/agent-harness]] · [[../topics/benchmarks]] · [[../topics/evaluation]] · [[../topics/agent-memory]] (DAG-as-state-carrier framing) · [[../topics/sandbox]] (per-subagent VMs)
- Concepts: _none promoted yet — candidates: DAG-based decomposition, mutable plan with replan budget, state cloning, variant attempts_
- Entities: _none promoted yet — candidates: OSWorld, Online-Mind2Web, WebTailBench, Odysseys, CMU (affiliation)_
- Related sources: [[2026-mao-decentralized-mas-shared-context]] — the clean contrast. DeLM (Mao & Mirhoseini, 2026) removes the manager entirely in favor of a decentralized blackboard with verified shared state; targets SWE-bench + long-context QA rather than computer use. Both papers beat their respective "centralized parallel" baselines; opposite topologies. See [[../topics/multi-agent-coordination]] for the side-by-side comparison table.

## Open questions / criticisms

- **No comparison vs other multi-agent frameworks.** Baseline is single-agent + pass@k. We don't know MACU > AutoGen / LangGraph / CrewAI / Plan-and-Execute on the same benchmarks. The right competitor for "DAG + manager" is "other plan structures," not "the same agent run alone."
- **Odysseys is their own benchmark.** The 25.5 pp headline number lives on a benchmark introduced in the same paper. Independent re-runs needed. (To their credit, gains on established OSWorld are also positive, just smaller.)
- **Cost analysis is thin.** Per-task token cost of MACU vs single-agent vs pass@k is mentioned but not deeply broken down. Manager + N subagents + VM-hours add up.
- **No public code release mentioned.** Reproducing requires rebuilding the harness — paper is the design spec, not a deployable system.
- **DAG is the assumed plan structure.** No comparison to trees, partial-order plans, hierarchical task networks, or "just a re-orderable list." DAG might be overkill or underkill — we don't know.
- **Model choice is opinionated.** Opus-class manager + Qwen-27B subagents is a specific configuration; weaker/stronger combinations might tell a different story (the ablation hints at this for manager strength but doesn't fully map it out).
- **No human-in-the-loop variant.** All-autonomous; the manager's replan decisions are not gated. For real deployments (booking, purchases, irreversible actions), there's no obvious place to insert a confirmation step in this framework.

## My take

> [!info] Drafted from our conversation — review and edit.

The headline contribution isn't "use multiple agents" — that's table stakes in 2026. It's the **specific combo of (mutable DAG) × (bounded replan budget) × (VM-isolated subagents) × (state cloning)**, plus the ablations showing the **replan loop is where the win actually comes from**. That's a non-obvious result. My prior would have been that parallelism does the heavy lifting; the paper shows it's replanning.

What this means practically: if I wanted to approximate MACU by prompting Claude Code, I could probably get 60–70% of the way there with a careful "decompose into a DAG, spawn parallel Task subagents, replan after each returns" prompt. What I'd be missing — and what the paper shows actually matters — is **per-subagent VM isolation** (so parallel actions don't collide) and **state cloning** (so a child can pick up exactly where a parent left off in a complex UI flow). Those are infrastructure features, not prompting tricks.

What I'd flag as research-paper-flavored over deployment-ready:

- The biggest gains live on the authors' own long-horizon benchmark.
- Comparison is to single-agent + pass@k, not to other multi-agent frameworks.
- No code release mentioned, so the contribution is the design + empirical study, not a tool I can pick up.
- Cost numbers exist but aren't the focus; in practice $/task and wall-clock vs success-rate tradeoffs would dominate adoption.

What I'd want to read next to triangulate:

- An independent or industry deployment of a similar architecture (manager + DAG + VM-isolated subagents) — does it hold up outside the paper?
- A direct head-to-head MACU vs LangGraph / AutoGen / Plan-and-Execute on OSWorld + Online-Mind2Web.
- Anything on cost-aware scheduling for the manager (right now `N` and `B` are hyperparameters, but in practice they should adapt to budget).

Net: the paper is a real contribution to the **computer-use sub-field** specifically (not multi-agent systems in general). It's the first clean published instance I've seen of "manager-DAG-subagents + VM isolation + replan-loop" applied to GUI/web automation with proper ablations. The replan-budget ablation alone is worth the read.

## Log

- 2026-06-17: ingested; PDF downloaded; raw source.md created
- 2026-06-17: read end-to-end; wiki page drafted with deep methodology + ablation breakdown; promoted [[../topics/computer-use]] as new topic hub
- 2026-06-17: cross-linked [[2026-mao-decentralized-mas-shared-context]] as the contrasting topology (decentralized blackboard vs MACU's centralized mutable-DAG)
