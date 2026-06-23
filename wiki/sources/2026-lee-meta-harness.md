---
title: "Meta-Harness: End-to-End Optimization of Model Harnesses"
authors: ["Yoonho Lee", "Roshen Nair", "Qizheng Zhang", "Kangwook Lee", "Omar Khattab", "Chelsea Finn"]
year: 2026
type: paper
raw: ../../raw/papers/2026-lee-meta-harness/paper.pdf
url: https://arxiv.org/abs/2603.28052
added: 2026-06-18
status: read
tags: [harness-engineering, outer-loop-search, coding-agent, filesystem-as-context, ablation, terminal-bench, swe-style-coding, retrieval, claude-code]
topics: [agent-harness, self-evolving-agents, evaluation, benchmarks, rag, agentic-rl]
---

# Meta-Harness: End-to-End Optimization of Model Harnesses

> Source: [[../../raw/papers/2026-lee-meta-harness/source.md|raw entry]] · [arXiv](https://arxiv.org/abs/2603.28052) · [project page](https://yoonholee.com/meta-harness/) · [TerminalBench-2 artifact](https://github.com/stanford-iris-lab/meta-harness-tbench2-artifact)

## TL;DR

Harnesses — the code that decides what an LLM stores, retrieves, and sees at each step — matter as much as model weights (6× perf gap on the same benchmark depending on harness, per [47]) but are still designed by hand. **Meta-Harness** automates harness search by running an outer loop where a coding agent (Claude Code + Opus-4.6) proposes new harnesses by **selectively reading a filesystem containing every prior candidate's source code, scores, and execution traces** — roughly 10 MTok of available context per iteration, three orders of magnitude beyond prior text optimizers (OPRO, TextGrad, GEPA, AlphaEvolve, TTT-Discover, Feedback Descent). Wins across three diverse domains: text classification (+7.7 pp over ACE, 4× fewer tokens), retrieval-augmented IMO-level math (+4.7 pp avg across 5 held-out models), TerminalBench-2 (**#1 among Haiku 4.5 agents**, #2 among Opus 4.6 agents). **Key ablation:** with scores-only or scores+summary the median search-set accuracy is 34.6 / 34.9; with full traces, 50.0. Raw execution traces are the load-bearing ingredient — summaries actively destroy signal.

## Key ideas

- **Harness engineering as a search problem, not a craft.** Prior framing: practitioners inspect failures, adjust heuristics, iterate. This paper: write an outer loop and let a coding agent do the iteration.
- **Coding-agent proposer, not raw LLM.** The accumulated history quickly exceeds any context window, so the proposer must *decide what to inspect* and validate edits via direct code interaction. Raw next-token models with fixed prompts can't do this. In practice, the proposer reads a **median of 82 files per iteration** (Appendix A), split ~41% source / 40% traces / 6% scores / 13% other.
- **Filesystem-as-feedback-channel.** Every evaluated harness writes a directory of `{source code, scores, execution traces}`. The proposer queries it with `grep`, `cat`, etc. — no monolithic prompt packing. The store is the entire interface; no per-candidate summarization layer in between.
- **Minimal outer loop, no parent-selection rule.** Maintains a population and Pareto frontier, but imposes no tournament or evolutionary parent selection. The agent can inspect any prior harness. This is deliberate: "leaving diagnosis and edit decisions to the proposer rather than hard-coding search heuristics, Meta-Harness can improve automatically as coding agents become more capable" (§3).
- **Per-domain skill is the only steering.** The proposer is guided by a single domain-specific natural-language skill that tells it where to write new harnesses, how to inspect prior ones, and what files it can/can't modify. Authors' Appendix D says: "iterating on the skill text had a larger effect on search quality than changing iteration count or population size."
- **Pareto over (accuracy, context cost).** Multi-objective is native: the agent is given the trade-off in the task spec and produces a smooth Pareto frontier (Figure 3), not a single point.
- **Search-set vs test-set discipline** in two of three settings (text classification, math). TerminalBench-2 is treated as a *discovery problem* — search and final eval on the same 89 tasks — defended on grounds that (a) standard practice in TerminalBench writeups, (b) the benchmark is too small/expensive to split, and (c) they regex-audit for task-string leakage. This is the weakest empirical setup of the three.

## Notable quotes

> [!quote] p.2 (on the framing gap)
> "Existing text optimizers are poorly matched to this setting because they compress feedback too aggressively: they are memoryless, condition only on scalar scores, or restrict feedback to short templates or summaries."

> [!quote] p.5 (on why minimality is deliberate)
> "This simplicity is deliberate: by leaving diagnosis and edit decisions to the proposer rather than hard-coding search heuristics, Meta-Harness can improve automatically as coding agents become more capable."

> [!quote] p.6 (the headline ablation)
> "Access to raw execution traces is the key ingredient for enabling harness search."

> [!quote] p.16 (the Iteration-7 pivot, verbatim from proposer)
> "All 6 prior iterations regressed from the 64.4% baseline because they modified the completion flow, prompt template, or observation processing. evo_env_bootstrap takes a different approach — **purely additive**. It gathers an environment snapshot via a single shell command before the first LLM call and appends it to the initial prompt."

## Methodology / structure

**Objective.** Find $H^* = \arg\max_H \mathbb{E}_{x \sim \mathcal{X}, \tau \sim p_M(H,x)} r(\tau, x)$ — the harness that maximizes expected reward under a fixed base model $M$ on task distribution $\mathcal{X}$.

**Algorithm 1 (compact):**
1. Seed population $\mathcal{H}$ with baseline harnesses; evaluate each; write all logs to filesystem $\mathcal{D}$.
2. For $N$ iterations:
   - Proposer agent queries $\mathcal{D}$ via terminal tools (grep / cat / etc.)
   - Proposes $k$ new harnesses
   - Each candidate that passes a lightweight interface-validation test is evaluated; logs appended to $\mathcal{D}$
3. Return Pareto frontier from $\mathcal{D}$ (final test-set eval applied only here).

**Three experimental domains:**

| Domain | Base model | Search budget | Result |
|---|---|---|---|
| Online text classification (USPTO / S2D / LawBench, online setup from Zhang et al.'s ACE) | GPT-OSS-120B | 20 iters × 2 candidates = 40 evals | +7.7 pp over ACE at **4× fewer tokens** |
| RAG for olympiad math (corpus: 500K+ solved problems; eval: 200 IMO-level held-out from IMO-AnswerBench / IMO-ProofBench / ArXivMath) | GPT-OSS-20B (search), then 5 held-out models | 40 iters → 109 candidates | +4.7 pp avg vs no-retriever across 5 models |
| TerminalBench-2 agentic coding (89 tasks) | Claude Opus 4.6 and Claude Haiku 4.5 | 10 iters | 76.4% (#2 Opus 4.6) / 37.6% (**#1 Haiku 4.5**) |

**Proposer interface ablation (Table 3, text classification):**

| Method | Scores | Code | Summary | Traces | Median | Best Acc | runs > zero-shot |
|---|:-:|:-:|:-:|:-:|---:|---:|---:|
| Scores Only | ✓ | ✓ | ✗ | ✗ | 34.6 | 41.3 | 26 |
| Scores + Summary | ✓ | ✓ | ✓ | ✗ | 34.9 | 38.7 | 23 |
| **Meta-Harness (full)** | ✓ | ✓ | — | ✓ | **50.0** | **56.7** | **39** |

The **median full-Meta-Harness candidate beats the best scores-only/scores+summary candidate.** Authors interpret this as evidence that summaries actively *destroy* useful signal (best-of-N is worse with summaries than without).

**Text optimizer comparison (Table 4, same search budget):**

| Method | Median | Best |
|---|---:|---:|
| GEPA | 32.6 | 40.2 |
| Best-of-N | 34.0 | 44.2 |
| OpenEvolve | 39.1 | 43.3 |
| TTT-Discover | 34.1 | 45.6 |
| **Meta-Harness** | **50.0** | **56.7** |

Meta-Harness matches OpenEvolve/TTT-Discover's final accuracy within the first **4 evaluations** (Figure 1, Figure 4) and ends 10+ points above them.

**Math results (Table 6, 200 IMO-level problems, pass@1 avg of 3 samples):**

| Method | GPT-5.4n | GPT-5.4m | Gem-3.1FL | Gem-3F | GPT-20B | Avg |
|---|---:|---:|---:|---:|---:|---:|
| No Retriever | 23.0 | 28.8 | 28.6 | 42.6 | 47.6 | 34.1 |
| Dense Retrieval k=5 | 31.1 | 28.3 | 37.1 | 47.2 | 46.7 | 38.1 |
| BM25 | 30.2 | 29.2 | 32.8 | 46.6 | 48.9 | 37.5 |
| **Meta-Harness** | **31.7** | **30.4** | 34.9 | 46.3 | **50.6** | **38.8** |

Wins on 5/5 models vs no-retriever; matches/beats BM25 on average **without introducing a dense encoder**. The discovered harness is a 4-route lexical router (combinatorics / geometry / number theory / default) over BM25 with route-specific reranking — a structure that *emerged* rather than being specified.

**TerminalBench-2 (89 tasks):**

| Model | Hand-engineered best | Meta-Harness | Δ |
|---|---|---|---|
| Claude Opus 4.6 | ForgeCode 81.8% (irreproducible from public code), Capy 75.3%, Terminus-KIRA 74.7% | **76.4%** | beats KIRA by +1.7 pp; #2 overall |
| Claude Haiku 4.5 | Goose 35.5%, Terminus-KIRA 33.7%, Mini-SWE-Agent 29.8% | **37.6%** | beats Goose by +2.1 pp; **#1 overall** |

The TerminalBench-2 win is a single **+80-line additive change** to Terminus-KIRA: a pre-loop `gather_env_snapshot()` shell command that reports working dir, /app listing, available languages (python/gcc/node/java/rustc/go), pkg managers, memory. Eliminates 2–4 wasted "discover the environment" turns. Found via a 7-iteration causal-reasoning arc described verbatim in Appendix A.2 (see "Qualitative behavior" below).

## Results / claims

**Headline claims:**
1. **Filesystem access to full prior history beats compressed-feedback text optimizers** by orders of magnitude in efficiency (matches OpenEvolve/TTT-Discover after 4 evals) and ~10 pp in final accuracy (Figure 1, Table 4).
2. **Discovered harnesses generalize OOD.** Text-classification harness selected on USPTO/S2D/LawBench wins on 6/9 unseen datasets (avg 73.1% vs ACE 70.2%, Table 5). Notably, naively adding more few-shot examples beyond 32 hurts on 7/9 — Meta-Harness doesn't degrade this way.
3. **Discovered math-retrieval harness transfers across base models.** A harness discovered using GPT-OSS-20B as the base model wins on **all 5** held-out models (Table 6), including GPT-5.4-nano/mini and Gemini-3-Flash variants the search never saw.
4. **The proposer does causal reasoning, not random mutation.** Appendix A.2 documents an iteration-by-iteration arc where the proposer identifies a confound between two simultaneous changes, isolates it, observes that fixing the diagnosed bug still regresses, and then pivots to a "purely additive" intervention (env bootstrap) that becomes the winning candidate. This is presented as qualitative evidence that filesystem access enables hypothesis-formation, not just pattern-matching.

**Qualitative behavior (Appendix A.2 narrative arc):**

| Iter | Action | Result | Insight |
|---|---|---|---|
| 1–2 | Two different structural fixes, both bundled with a cleanup-prompt rewrite | -5.6 pp / -6.7 pp | Confounded |
| 3 | Strip prompt change; test only the structural fixes | -1.1 pp | Diagnosed: prompt edit was the harmful piece |
| 4–6 | Refine completion-flow / prompt / smart-waiting | All regress | Lesson: prompt/completion edits are *high risk* |
| **7** | **Drop control-loop edits entirely; add env snapshot before first LLM call (purely additive)** | **Best so far** | "Additive over corrective" pivot |
| 8 | Compose env-bootstrap + marker-strip + loop-breaker | Further gain | Compositional reasoning |
| 10 | References results from a prior separate run | Cross-run transfer | Search is non-Markovian across runs |

## Connections

- Topics:
  - [[../topics/agent-harness]] **(primary)** — this paper is the wiki's first source on *meta*-optimization of harnesses. Third converging signal that the load-bearing component of a 2026 agent system is in a quality/diagnostic knob (here: raw traces), not architectural breadth.
  - [[../topics/self-evolving-agents]] — **first source on this topic**. Meta-Harness is search-over-code with experience replay; structurally a self-improving meta-agent.
  - [[../topics/evaluation]] — formalizes Pareto-frontier reporting over (accuracy, context cost); cleanly separates search set / test set in 2/3 domains; uses TerminalBench-2 as a discovery problem (acknowledged trade-off).
  - [[../topics/benchmarks]] — TerminalBench-2 (now referenced by two sources via comparison context), IMO-AnswerBench / IMO-ProofBench / ArXivMath cluster, LawBench / USPTO-50k / Symptom2Disease (online-classification cluster).
  - [[../topics/rag]] — the discovered math harness is a 4-route lexical router over BM25 with route-specific reranking and a math-aware tokenizer that preserves LaTeX tokens. A non-trivial RAG architecture that *emerged* from search, not from manual design.
  - [[../topics/agentic-rl]] — tangential. Reframes harness optimization as *harness-level credit assignment* on past rollouts, a non-weight analog of policy improvement.
- Concepts: _none promoted yet. Candidates: harness engineering, filesystem-as-feedback-channel, additive-over-corrective pivot, search-set-vs-test-set discipline, Pareto-over-accuracy-and-cost._
- Entities: _none promoted yet. Candidates strongly suggested by a second wiki reference now: Claude Code (used as proposer here; also in [[topics/agent-harness]] discussion + Sapora's LLM-mock-interview note), TerminalBench-2 (this paper makes it a primary eval), ACE / Agentic Context Engineering (baseline here, also referenced in DeLM's setup), Terminus-KIRA (the harness Meta-Harness beats — KRAFTON product), Stanford IRIS Lab._
- Related sources:
  - [[2026-koh-multi-agent-computer-use]] — MACU. Both 2026, both go after the "harness is the contribution" framing. MACU proposes one harness (mutable-DAG manager/worker); Meta-Harness proposes a *procedure that discovers harnesses*. Meta-Harness's TerminalBench-2 winner is a +80-line additive patch on Terminus-KIRA, a kind of structurally minimal contribution that would be very hard to find by hand.
  - [[2026-mao-decentralized-mas-shared-context]] — DeLM. The convergence is striking: MACU's load-bearing knob is the replan budget; DeLM's is admission-time verification; Meta-Harness's is raw trace access. Three different topologies, three different "load-bearing knobs" — but all of them sit in the diagnostic/quality dimension, not the parallelism/topology dimension. (Not promoting to a synthesis page yet; waiting for a 4th source per user preference.)
  - [[2026-zhang-self-harness]] — **Sibling paper, same problem, opposite half of the [[../topics/agent-harness#harness-improvement-paradigms-3-way-taxonomy-from-self-harness-figure-1|human/Meta/Self trilemma]]**. Self-Harness's Figure 1 explicitly maps Meta-Harness into the "external stronger proposer" cell and itself into the "same fixed model is proposer + target" cell. Both target [[../entities/terminalbench-2|TerminalBench-2]] but with no head-to-head comparison: Meta-Harness uses full 89-task set + Claude Code/Opus-4.6 as proposer + Terminus-KIRA as starting harness; Self-Harness uses 64-task filtered subset + the target model as its own proposer + a minimal initial harness. The two designs answer different questions ("can a stronger external agent discover a better harness?" vs "can a model improve its own scaffold?") and the natural follow-up neither paper runs is a head-to-head from a common starting point. Self-Harness is a **soft 4th data point** on the load-bearing-knob pattern (verifier-grounded clustering + non-regressive acceptance gate sit in the quality/diagnostic dimension) but lacks an isolating ablation, so synthesis-page promotion is still pending.
  - [[2026-brown-skypilot-sandboxes]] — distant: the kind of infra that makes the Meta-Harness outer loop tractable in practice. Each harness evaluation needs to run hundreds of times in isolated sandboxes.

## Open questions / criticisms

- **TerminalBench-2 is treated as a discovery problem** — search and eval on the same 89 tasks. The defenses (standard practice, eval cost, regex audits for task-string leakage) are reasonable but the regex audit is a weak check against semantic overfitting. A real test would be: take the discovered harness, hold out *future* tasks added to TerminalBench-3 or a different long-horizon benchmark. The 37.6% Haiku-4.5 win in particular is at a margin (37.6 vs 35.5) where overfitting could plausibly account for the difference.
- **Single proposer agent.** All experiments use Claude Code + Opus-4.6. Authors flag this explicitly as future work, but it leaves open: how much of the gain is from Meta-Harness's design vs from Claude Code being the strongest available coding agent? A scores-only ablation with a much weaker proposer would tell you whether the filesystem-access design is doing the work, or whether a sufficiently strong agent makes any feedback channel work.
- **10 MTok/iter is enormous.** Table 1 makes this look like a feature (3 orders of magnitude more diagnostic context), but it also makes Meta-Harness's per-iteration cost roughly 3 orders of magnitude higher than OPRO/GEPA. The headline "10× faster convergence vs OpenEvolve" comparison holds only if you're measuring evaluations, not tokens consumed. For practitioners, the cost framing matters.
- **The "summaries actively hurt" finding is striking** but might be domain-specific. Their summary condition has both raw scores and raw code already; the summary is *additional* info. It's possible the summaries are bad (LLM-generated, not curated) rather than that summaries-in-general are bad. The framing "summaries can't recover the missing signal" is strong; the framing "**our** summary policy can't recover" is more defensible.
- **The math-retrieval harness uses BM25 + lexical tokenization preserving LaTeX.** This is a clever discovery but it's also a known trick — math IR has long preferred lexical methods for symbol matching. The headline "+4.7 pp without a dense encoder" is real but the implicit framing that *search discovered an insight humans missed* is debatable.
- **Ablation-via-iteration-7 is a great story but also a single anecdote.** The narrative arc in Appendix A.2 is compelling, but: how reproducible is it across random seeds? Different proposer model versions? They report one search trajectory in detail and present it as illustrative. A more rigorous claim would aggregate causal-reasoning indicators across many runs.
- **OOD generalization claim has a 7/9 caveat that cuts both ways.** "Naively adding more few-shot examples beyond 32 hurts on 7/9 tasks" is presented as evidence that Meta-Harness's strategies are smarter than few-shot. But the discovered harness *is itself* a memory-of-examples system; the difference is the access pattern. The cleaner comparison would be against a *strong* memory-based baseline tuned per-task, not against naive scaling.
- **Comparison set excludes test-time training methods other than TTT-Discover.** No comparison against e.g. SEAL, Voyager-style skill libraries, ADAS, or AFlow [58] except in related work prose. The "we beat all text optimizers" claim is qualified by which optimizers were in the comparison.
- **No claim about discovered harnesses being interpretable, but the paper hints at it** (Discussion: "brittle if-chains or hard-coded class mappings are visible on inspection"). This is an asserted advantage of code-space over weight-space search but isn't quantified anywhere.

## My take

> [!info] Drafted from our conversation — review and edit.

The headline contribution is **not** "search over harness code." AlphaEvolve and OpenEvolve already do that. The actual contribution is **"give the proposer raw, selective access to every prior candidate's full state via a filesystem"** — i.e., refuse to design a compression layer at all and instead let the agent decide what to look at. Table 3 is the ablation that proves this is load-bearing. Scores-alone is 34.6 median; scores+summary is 34.9; full trace access is 50.0. The median full-Meta-Harness candidate beats the *best* of either ablation. That's a real result.

Pattern reinforcement with the prior two ingests: **in 2026 LLM-agent papers, the load-bearing component sits in the diagnostic/quality/control dimension**, not in the topology / parallelism / breadth dimension.
- [[2026-koh-multi-agent-computer-use|MACU]]: replan budget (a control knob) drives most of the gain; parallelism per se doesn't.
- [[2026-mao-decentralized-mas-shared-context|DeLM]]: admission-time verification (a quality gate on state) is the single largest contributor; the blackboard architecture itself isn't.
- **Meta-Harness**: raw execution-trace access (a diagnostic input richness knob) is the single largest contributor; the outer-loop search structure is intentionally minimal.

Three different topologies, three different "load-bearing knobs," but all of them in the same dimension. If a fourth source lands with this pattern, this becomes a synthesis page.

What's *honest and useful* about this paper:
- The discipline of search-set vs test-set in two of three domains is correct, and they're upfront about the TerminalBench-2 exception.
- The Iteration-7 "additive over corrective" qualitative narrative is the kind of trace you wish more agentic-systems papers showed. Whether the *quantitative* claim "the proposer does causal reasoning" is robust is another question, but the anecdote is at least specific and falsifiable.
- The cost framing (4× fewer tokens for +7.7 pp on text classification) takes the dimension seriously rather than burying it.

What's *less* honest or convenient:
- TerminalBench-2 as discovery problem. The defense is reasonable but the result is also the most marketable one ("#1 among Haiku-4.5 agents") and the closest to overfitting.
- "Match OpenEvolve in 4 evaluations" is wall-clock evaluations, not tokens. Per-iteration tokens are ~500× higher (Table 1). The right framing is "Meta-Harness is sample-efficient on harness evaluations, which are the expensive primitive in this regime." It's true but more nuanced than the headline.
- The 6/9 OOD generalization win is presented as transfer; the 6/9 also reflects per-task variance and the headline averages mask big swings (-18 pp on USPTO few-shot variants, +13 pp on SciC).

**Most underrated finding:** the TerminalBench-2 winner is a +80-line additive patch on Terminus-KIRA. It's *trivially small*. That's important: if harness search converges to small, additive, interpretable patches, then "discovered harnesses" become a deployable artifact, not a research curio. The MACU paper, by contrast, produces a complex bespoke system that you wouldn't just drop into your stack. Meta-Harness's TerminalBench-2 result is the *most practically transferable* result in the wiki right now.

**Most overrated finding:** the "summaries hurt" claim. The scores+summary condition reaches *lower* best-of-N than scores-only (38.7 vs 41.3). This is striking but might be an artifact of their specific summarization policy. I'd want to see this replicated with a curated-summary condition (e.g., DeLM-style admission-time verification of summary quality) before accepting "summaries are categorically bad."

**Follow-up I'd want:**
- Run Meta-Harness with the DeLM proposer (or a weaker coding agent) to disentangle "filesystem access matters" from "Claude Code is strong."
- Apply Meta-Harness to MACU's Odysseys benchmark. Does the discovered harness look like MACU's mutable-DAG, or does it converge to something different? Either answer is informative.
- A *combined* MACU+DeLM+Meta-Harness paper: discover the harness, where the search space includes admission-time verification, replan budgets, and blackboard coordination as primitives. Would the discovered harness use all three?
- Track whether harness-search literature converges on small additive patches or on full bespoke rewrites. The early evidence (TerminalBench-2 winner = +80 lines) is suggestive but n=1.

## Log

- 2026-06-18: ingestion resumed after interruption; PDF was already in raw (downloaded earlier); read end-to-end; raw `source.md` created
- 2026-06-18: wiki page drafted at medium depth (TL;DR + key ideas + results + ablations + criticisms + My take, without trace-level reproduction of Appendix B harness internals); cross-linked to MACU and DeLM with the "third converging signal" framing; promoted [[../topics/self-evolving-agents]] as first non-stub source on that topic
- 2026-06-18: cross-linked to sibling paper [[2026-zhang-self-harness]] under Related sources; added the human/Meta/Self trilemma framing (Self-Harness Figure 1) explicitly placing this paper in the "external stronger proposer" cell
