---
title: "Decentralized Multi-Agent Systems with Shared Context"
authors: ["Yuzhen Mao", "Azalia Mirhoseini"]
year: 2026
type: paper
raw: ../../raw/papers/2026-mao-decentralized-mas-shared-context/paper.pdf
url: https://arxiv.org/abs/2606.10662
added: 2026-06-17
status: read
tags: [multi-agent, blackboard, shared-context, verification, long-context, swe-bench, decentralized]
topics: [multi-agent-coordination, agent-harness, benchmarks, evaluation, agent-memory, rag]
---

# Decentralized Multi-Agent Systems with Shared Context

> Source: [[../../raw/papers/2026-mao-decentralized-mas-shared-context/source.md|raw entry]] · [arXiv](https://arxiv.org/abs/2606.10662) · [project + code](https://yuzhenmao.github.io/DeLM/)

## TL;DR

Centralized multi-agent systems (main-agent + sub-agents) become a coordination bottleneck as the number of subtasks grows. **DeLM (Decentralized Language Models)** replaces the manager with a **blackboard architecture**: parallel agents asynchronously claim subtasks from a **task queue**, read accumulated progress from a **shared verified context**, do local reasoning, and write back compact verified updates. Updates must pass an **admission-time LLM verifier** before becoming visible to other agents. On SWE-bench Verified (Gemini-3-Flash base), DeLM hits 65.7% Avg@1 / 77.4% Pass@4 — +9.3 pp Avg@1 over the strongest baseline at **~half the cost** ($0.12 vs $0.24+ per task). On LongBench-v2 Multi-Doc QA, it beats the best baseline by 3.6–5.7 pp across four frontier model families. Ablations show **verification is the single most important component** (-4.9 pp when removed, vs -2.4 pp for removing the hierarchy). Code released.

## Key ideas

- **State-based, not prompt-routed, coordination.** Centralized MAS pass intermediate progress through a main agent that re-encodes it into new prompts. DeLM makes progress *persistent*: agents write compact verified gists into a shared store. No re-broadcasting; later agents read directly.
- **Two global structures.** Shared context `C` (verified gists) + task queue `T` (pending subtasks). These are the entire coordination interface — no manager, no peer-to-peer messaging.
- **Compact / unfoldable hierarchy.** Each entry has three levels: `G_i` (compact gist in `C`) → `S_i` (reference-grounded bullet summary, in backing store `L`) → raw `r_i` (in `R`). Agents read gists by default; `UNFOLD` retrieves `S_i`; `DEEP_UNFOLD` retrieves the raw span. **OS analogy in the paper: gist = working set; `S_i` + raw = backing storage; unfolding = demand paging.**
- **Admission-time verification.** Before any update enters `C`, an LLM verifier checks the gist against its supporting evidence. For long source units, verification runs at both `S_i` (grounded in raw) and `G_i` (faithful to `S_i`) levels. Failed gists are rejected and regenerated up to a retry limit. This turns the shared context from a raw message buffer into curated state.
- **Asynchronous task claiming with dependency tags.** Tasks are queued with `[deps: ...]` annotations; ready-frontier tasks are claimed in parallel. When the queue empties, the most recently completed agent calls `GENERATE_MORE_SUBTASKS` with the current `C` to decide whether to continue or finalize. No central scheduler.
- **Three trace-level mechanisms** explain the SWE-bench wins (Section 4.2.1): (1) failed hypotheses become reusable constraints; (2) admitted constraints stay *binding* (centralized merging tends to soften them); (3) compact `PATCH_SUMMARY` gists let later workers reuse a discovery without re-reading the trajectory.
- **DeLM is complementary to RLM, not competing.** On OOLONG (aggregation-heavy long-context), DeLM alone underperforms RLM's code-mediated REPL; but DeLM+RLM hybrid is best. DeLM works as a *coordination layer* you can wrap around other reasoners.

## Methodology / structure

**Algorithm (Algorithm 1 in the paper).** Five stages:
1. Initialize task queue `T` from input `D` and optional source context `U`.
2. Run agents in parallel on ready subtasks (lock-free snapshot reads of `C`).
3. Compress + verify each completed result; admit only passing gists to `C`.
4. If `T` is empty, ask whether more subtasks are needed; if so, generate and re-loop.
5. Finalize the answer from accumulated verified shared state.

**Two compression paths.** For reasoning trajectories, directly compress `r_i → G_i`. For long source units, go through the hierarchical path `r_i → S_i → G_i`, because direct gist-to-raw unfolding is unreliable when the source is large.

**Concurrent admission with write-before-publish ordering.** Compression and verification run in parallel across completed results; only the final admission (atomic append of `(label, G_i)` to `C`) is synchronized. Backing content is written to `L` and `R` *before* the public entry is published, so any visible label is safely unfoldable. Slow summarization doesn't impose a global barrier.

**KV-cache reuse.** `C` is stable and always placed at the start of every model call, forming a persistent prefix. The shared-context design therefore doubles as a cache-friendly structure.

**Benchmarks.**

| Benchmark | What it stresses | Outcome |
|---|---|---|
| **SWE-bench Verified** | Test-time scaling across reasoning trajectories (sequential within a task, parallel across attempts) | DeLM best on Avg@1, Pass@2, Pass@4 with both Gemini-3-Flash and Claude Opus 4.6 |
| **LongBench-v2 Multi-Doc QA** | Within-task parallelism (concurrent inspection of evidence clusters) | DeLM best avg across GPT-5.4, Claude Sonnet 4.6, Gemini-3-Flash, DeepSeek-V4-Pro |
| **OOLONG** | Aggregation over structured timestamped/attributed data | DeLM alone < RLM; DeLM+RLM hybrid > both |

**Baselines compared.** Base (single LLM call), Claude Code (Anthropic 2026), mini-SWE-agent, AOrchestra (centralized MAS), **AOrchestra-Parallel** (the right "centralized parallel" comparison they construct themselves), ReadAgent (long-context with gists).

## Results / claims

**Headline numbers (SWE-bench Verified, Gemini-3-Flash base):**

| Method | Avg@1 | Pass@2 | Pass@4 | Cost/task |
|---|---|---|---|---|
| mini-SWE-agent | 54.7% | 65.6% | 75.1% | $0.26 |
| Claude Code | 49.3% | 57.1% | 66.3% | — (cache caveat)¹ |
| AOrchestra | 55.2% | 64.5% | 73.2% | $0.24 |
| AOrchestra-Parallel | 56.4% | 63.2% | 71.8% | $0.25 |
| **DeLM** | **65.7%** | **72.9%** | **77.4%** | **$0.12** |

¹ Claude Code's reported cost requires Anthropic API cache reuse; without it, real cost is ~$1/task with Gemini-3-Flash routing.

- **+9.3 pp Avg@1** over the best baseline, at **~50% lower cost**. Cost reduction matters because it shows the gain isn't just "spend more compute."
- **Claude Opus 4.6 base shows smaller gains** (~+1 pp Avg@1, +1 pp Pass@4). Either base model is already strong enough that coordination matters less, or the gains are partly margin-of-noise on strong bases.
- **AOrchestra-Parallel wins on Avg@1 but loses on Pass@k** vs vanilla AOrchestra — coupling parallel threads through a main agent makes attempts more consistent but reduces exploration diversity. This is exactly the bottleneck DeLM is designed around.

**LongBench-v2 Multi-Doc QA (avg across 5 domains):**

| Base Model | Best baseline | DeLM | Δ |
|---|---|---|---|
| GPT-5.4 | 54.4% (Claude Code) | **60.1%** | +5.7 |
| Claude Sonnet 4.6 | 54.5% (ReadAgent) | **59.8%** | +5.3 |
| Gemini-3-Flash | 57.1% (Base) | **61.5%** | +4.4 |
| DeepSeek-V4-Pro | 63.9% (Base) | **67.5%** | +3.6 |

Consistent across model families — suggests the win is from coordination, not from any one model.

**Ablations (LongBench-v2, GPT-5.4):**

- **No verification**: 60.1% → 55.2% **(-4.9 pp)** — the single largest drop
- **No hierarchical summary**: 60.1% → 57.7% (-2.4 pp)
- **Gist length** (50 / 100 / 150 tokens): 58.3 / 60.1 / 60.3 — threshold effect at 100, flat after
- **Summarizer model** (DeepSeek-V4-Flash / V4-Pro / GPT-5.4): 60.1 / 60.4 / 59.9 — **essentially insensitive**; a cheap summarizer is fine

**OOLONG (GPT-5):**

| Method | Acc | Cost |
|---|---|---|
| RLM | 56.0% | $0.43 |
| DeLM | 53.3% | $0.47 |
| **DeLM+RLM** | **64.0%** | **$0.40** |

DeLM alone loses to RLM on OOLONG because the benchmark wants exact aggregation (counting/filtering); natural-language shared context can't beat a REPL. But the hybrid is best on both axes — strong evidence DeLM is a coordination layer, not a complete reasoner.

## Connections

- Topics: [[../topics/multi-agent-coordination]] (primary) · [[../topics/agent-harness]] · [[../topics/benchmarks]] · [[../topics/evaluation]] · [[../topics/agent-memory]] (shared-context-as-memory + hierarchical access) · [[../topics/rag]] (the gist/summary/raw hierarchy is multi-resolution retrieval over the agent working set)
- Concepts: _none promoted yet — candidates: blackboard coordination, admission-time verification, compact-unfoldable hierarchy, write-before-publish admission_
- Entities: _none promoted yet — candidates: AOrchestra, RLM (Recursive Language Models), Claude Code, mini-SWE-agent, ReadAgent, SWE-bench Verified, LongBench-v2, OOLONG, Stanford (affiliation)_
- Related sources: [[2026-koh-multi-agent-computer-use]] — the cleanest contrast in the wiki so far. MACU = centralized mutable-DAG + VM-isolated CUA subagents (for computer use); DeLM = decentralized blackboard + verified shared context (for SWE / long-context QA). Both 2026, opposite topologies, see [[../topics/multi-agent-coordination]] for the side-by-side.

## Open questions / criticisms

- **Verification cost is glossed.** Every update gets an LLM verifier call; paper says "modest overhead" but doesn't break out the per-task cost of verification separately. If it's significant, the headline cost numbers depend on a cheap verifier choice (they use DeepSeek-V4-Flash for the gist verifier).
- **Headline number is base-model-dependent.** +9.3 pp Avg@1 on Gemini-3-Flash shrinks to +2.8 pp on Claude Opus 4.6. The strongest result is on the model where the cost baseline has the asterisked cache-reuse caveat.
- **DeLM alone loses on aggregation-heavy benchmarks** (OOLONG). They're upfront about it and propose the hybrid. But this means "DeLM is the right coordination substrate" is qualified — it's the right substrate for *natural-language* reasoning that doesn't need exact arithmetic.
- **Blackboard is not novel** — they cite Han & Zhang 2025 and Salemi et al. 2025 as prior LLM-blackboard MAS. DeLM's contribution over those is specifically (a) the **admission-time verification gate**, (b) the **`gist → S → raw` hierarchy with selective unfolding**, and (c) **dependency-aware task queueing with deadlock-avoidance via GenerateMoreSubtasks**.
- **No direct comparison to MACU-style centralized mutable-DAG.** The closest is AOrchestra-Parallel (centralized but parallel-aware). A direct head-to-head between mutable-DAG and verified-blackboard topologies is still missing.
- **Decomposition quality is acknowledged as a weakness.** "DeLM inherits the decomposition quality of agents." No adaptive split/merge/terminate yet — that's flagged as future work.
- **Prompt sensitivity across model families.** Limitations section notes prompts may need per-family tuning; they suggest GEPA-style prompt evolution as a follow-up.
- **The verification gate uses an LLM-as-judge.** Standard LLM-judge concerns apply: false rejections, false admissions, and possible bias toward the verifier's training distribution. They check it against cited evidence (good) but don't characterize the verifier's own failure modes.

## My take

> [!info] Drafted from our conversation — review and edit.

The contribution that's actually new here is **admission-time verification of shared state**. The blackboard architecture itself is a known pattern in LLM MAS (they cite the precedents honestly). The `gist → S → raw` hierarchy is RAG-adjacent and not without precedent in ReadAgent / MemGPT. What's specifically load-bearing — and what the ablation proves with the largest single delta (-4.9 pp without it) — is the **LLM verifier gate that prevents unsupported claims from entering the shared context**. That's a primitive other MAS work should adopt, including potentially [[2026-koh-multi-agent-computer-use|MACU]] which doesn't have it.

The framing as "centralized is the problem, decentralized is the answer" is partially marketing. The real story is more nuanced: DeLM still has *one* central piece (the verifier gate), and it still has *one* central planning moment per round (`GENERATE_MORE_SUBTASKS` called by the last finisher). What's actually decentralized is the *fan-out and read*; the *admission and re-planning* still require single-writer discipline. That's fine — it's the right design choice — but the paper sells it as a sharper centralized/decentralized dichotomy than it actually is.

The MACU contrast is the most interesting thing in your wiki right now. Both papers are 2026, both go after multi-agent coordination, both beat strong centralized-parallel baselines, and **they reach opposite topologies**. MACU keeps the manager and makes the plan mutable + adds VM isolation; DeLM removes the manager and adds shared verified state. These aren't contradictory — they're optimizing different parts of the problem (computer use vs text-only reasoning, partial observability vs context-window sharing) — but a synthesis paper combining the two (manager-DAG + verified shared blackboard + VM isolation) would be the obvious next move for the field.

Practically: **of the two, DeLM is the one you can actually try.** Code is released, the benchmarks are standard (SWE-bench Verified, LongBench-v2), and the architecture is straightforward enough to reproduce. MACU is the more research-paper-flavored contribution; DeLM is the more deployable one.

What I'd watch for next:
- A paper applying DeLM-style admission-time verification to MACU-style mutable-DAG computer-use systems.
- An honest cost breakdown of the verification step at scale (>10 parallel agents).
- DeLM on a benchmark that *isn't* SWE / multi-doc QA — e.g., math reasoning, scientific protocols, automated research workflows (they hint at this).
- Whether the "shared verified context = OS working set" analogy actually predicts behavior, or whether it's a clean post-hoc framing.

## Log

- 2026-06-17: ingested; PDF downloaded; raw source.md created
- 2026-06-17: read end-to-end; wiki page drafted at medium depth (no full ablation deep-dive; criticisms + comparison to MACU foregrounded); promoted [[../topics/multi-agent-coordination]] as new topic hub with side-by-side MACU vs DeLM table
