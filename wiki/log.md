# Wiki Log

> Append-only chronological record of wiki activity.
> Entry prefix: `## [YYYY-MM-DD] <op> | <title>` where `<op>` is `ingest`, `query`, or `lint`.
> Greppable: `grep "^## \[" wiki/log.md | tail -10`

---

## [2026-06-17] init | Wiki scaffolded

Created repository structure per [`llm-wiki.md`](../llm-wiki.md):
- `CLAUDE.md` schema with ingest/query/lint workflows
- `raw/` (papers/, blogs/) — immutable source layer
- `wiki/` (sources/, topics/, concepts/, entities/, synthesis/, templates/) — LLM-owned layer
- `wiki/index.md` and `wiki/log.md` initialized empty
- Page templates in `wiki/templates/`

Ready to ingest first source.

## [2026-06-17] seed | topic stubs

Created empty topic hub pages for the human's current areas of interest:
- [[topics/agentic-rl]]
- [[topics/self-evolving-agents]]
- [[topics/agent-memory]]
- [[topics/rag]]
- [[topics/agent-harness]]
- [[topics/sandbox]]
- [[topics/evaluation]]
- [[topics/benchmarks]]

Updated [[index]] topic list. All stubs have 0 sources; they will fill in as ingests happen.

## [2026-06-17] ingest | SkyPilot Sandboxes: Run Agent Code on Your Own Kubernetes, at Scale

Source: Brown, L. (2026). SkyPilot Blog. https://blog.skypilot.co/sandboxes/

Compact / bookmark-style ingest at the human's request. Files touched:
- Created `raw/blogs/2026-brown-skypilot-sandboxes/clip.md` (AI-extracted via WebFetch — not a verbatim crawl)
- Created `raw/blogs/2026-brown-skypilot-sandboxes/source.md` (Wayback URL pending)
- Created [[sources/2026-brown-skypilot-sandboxes]] — compact summary with skeptical "My take" flagging vendor-blog status
- Updated [[topics/sandbox]] — added source, fleshed out "Current understanding" with the hosted-vs-BYOC framing
- Updated [[topics/agentic-rl]] — added source as tangential reference (sandbox-as-reward-server pattern)
- Updated [[index]] — sources count 0→1, sandbox+agentic-rl source counts updated

No new concept or entity pages created (per human's preference for a light-touch ingest).

Follow-ups suggested:
- Submit URL to Wayback Machine and paste archive URL into `source.md`
- Find an independent (non-vendor) benchmark/comparison of agent sandbox infrastructure to corroborate claims

## [2026-06-17] ingest | Multi-Agent Computer Use (Koh, Salakhutdinov, Fried, 2026)

Source: Koh, J. Y., Salakhutdinov, R., & Fried, D. (2026). *Multi-Agent Computer Use*. arXiv:2606.01533. https://arxiv.org/abs/2606.01533

Deep ingest at the human's request (full method/results/limitations, not bookmark-style). Files touched:

- Created `raw/papers/2026-koh-multi-agent-computer-use/paper.pdf` (downloaded from arXiv)
- Created `raw/papers/2026-koh-multi-agent-computer-use/source.md` (full metadata + BibTeX + abstract)
- Created [[sources/2026-koh-multi-agent-computer-use]] — deep summary: TL;DR, key ideas, methodology (node schema + models + benchmarks table), results with ablation breakdown, criticisms, "My take" framed against the human's question of "how is this better than prompting Claude Code"
- **Created new topic** [[topics/computer-use]] — first occupant of this hub; framed CUAs vs coding/tool-calling agents, established partial-observability + long-horizon as defining challenges
- Updated [[topics/agent-harness]] — first non-stub "Current understanding"; framed manager/worker split, plan-structure-as-design-choice, replan-budget-matters-more-than-parallelism
- Updated [[topics/benchmarks]] — added computer-use benchmarks table (OSWorld, Online-Mind2Web, WebTailBench, Odysseys); flagged "new architecture + new benchmark" pattern (MACU's biggest gains are on its own benchmark)
- Updated [[topics/evaluation]] — flagged pass@k-as-baseline and wall-clock-as-co-equal-metric patterns
- Updated [[topics/agent-memory]] — added tangential link with "plan-as-memory" framing
- Updated [[topics/sandbox]] — added tangential link for VM-per-subagent isolation pattern
- Updated [[index]] — sources 1→2, topics 8→9, source listed under Papers, source counts on touched topic pages updated

Key takeaway recorded in the source page's "My take": the contribution is the *combination* of (mutable DAG) × (bounded replan budget) × (VM-isolated subagents) × (state cloning), and the ablations show **replanning, not parallelism, drives most of the gain** (B=0 → 25% SR, B=10 → 58% SR on Odysseys). Beats pass@8 on Online-Mind2Web, which is the strongest evidence the structure matters beyond the compute budget.

No concept or entity pages promoted (candidates noted in source page for later): DAG-based decomposition, state cloning, variant attempts; OSWorld / Online-Mind2Web / WebTailBench / Odysseys benchmarks; CMU.

Follow-ups suggested:
- Find/read a direct head-to-head of MACU vs other multi-agent frameworks (AutoGen, LangGraph, Plan-and-Execute) on OSWorld / Online-Mind2Web
- Check whether the authors release code (paper as of v1 only describes the system)
- Promote OSWorld / Odysseys to entity pages once a second source references them
- Re-read after one more multi-agent CUA paper to corroborate the "replan-budget matters more than parallel-breadth" claim

## [2026-06-17] ingest | Decentralized Multi-Agent Systems with Shared Context (Mao & Mirhoseini, 2026)

Source: Mao, Y., & Mirhoseini, A. (2026). *Decentralized Multi-Agent Systems with Shared Context*. arXiv:2606.10662. https://arxiv.org/abs/2606.10662 · project page https://yuzhenmao.github.io/DeLM/

Medium-depth ingest at the human's request (full ideas + results + criticisms + My take, but not trace-level mechanics or full ablation deep dive). Files touched:

- Created `raw/papers/2026-mao-decentralized-mas-shared-context/paper.pdf` (downloaded from arXiv)
- Created `raw/papers/2026-mao-decentralized-mas-shared-context/source.md` (full metadata + BibTeX + verbatim abstract)
- Created [[sources/2026-mao-decentralized-mas-shared-context]] — medium-depth summary: TL;DR, key ideas (state-based not prompt-routed, gist/summary/raw hierarchy, admission-time verification, async claim with deps), 5-stage algorithm sketch, headline results on SWE-bench Verified / LongBench-v2 / OOLONG, ablation that isolates verification as load-bearing, criticisms, "My take" emphasizing admission-time verification as the actually-novel primitive
- **Created new topic** [[topics/multi-agent-coordination]] — sibling to agent-harness, focused specifically on coordination patterns in MAS; seeded with prominent side-by-side MACU vs DeLM comparison table (topology, plan structure, validation, isolation, code release, key ablation)
- Cross-linked DeLM ↔ MACU in both source pages (Related sources, Log entries)
- Updated [[topics/agent-harness]] — expanded Current understanding to cover both topologies; flagged admission-time verification as a candidate primitive
- Updated [[topics/benchmarks]] — added text/code benchmark table (SWE-bench Verified, LongBench-v2, OOLONG); flagged pass@k-as-baseline as converging norm
- Updated [[topics/evaluation]] — reinforced pass@k + cost-per-task patterns; promoted ablation-driven attribution and "LLM verifier as control (not just judge)" as recurring
- Updated [[topics/agent-memory]] — promoted "coordination state = team memory" pattern (DAG nodes ≈ gists) to first-class Current understanding; flagged admission-time verification as a memory-quality primitive
- Updated [[topics/rag]] — **first source on this topic**; tangential, but DeLM's gist/summary/raw hierarchy with unfold-on-demand is structurally multi-resolution retrieval over the team's own state
- Updated [[index]] — sources 2→3, topics 9→10, source counts on touched topic pages bumped, DeLM listed under Papers

Key takeaway recorded in the source page's "My take": the actually novel contribution is **admission-time verification** of shared state (the LLM verifier gate), which the ablation shows is the single biggest contributor (-4.9 pp when removed). The blackboard topology and even the multi-resolution hierarchy have precedents (Han & Zhang 2025, Salemi et al. 2025); the verifier gate doesn't. Combined with MACU's "replanning > parallelism" result, the wiki now has **two converging signals**: in 2026 MAS papers, the load-bearing component tends to be a control / quality knob (replan budget, verifier gate), not the parallel breadth itself.

The MACU vs DeLM contrast is captured in the new [[topics/multi-agent-coordination]] page as a comparison table. Per human preference, no synthesis page yet — wait for a third paper before promoting.

No concept or entity pages promoted (candidates noted: admission-time verification, compact-unfoldable hierarchy, blackboard coordination; AOrchestra, mini-SWE-agent, ReadAgent, RLM as entities once a second source references each).

Follow-ups suggested:
- Read AOrchestra (Ruan et al. 2026), cited in both MACU and DeLM as the centralized-MAS baseline
- Find/read a paper that adds DeLM-style admission-time verification to a centralized topology (or vice versa — hybrid)
- Check whether decentralized blackboard coordination can scale to computer-use / partial-observability domains
- Promote SWE-bench Verified to an entity page once a second source uses it

## [2026-06-17] ingest | ML Job Interviews: The Ultimate Guide (Sapora, 2026)

Source: Sapora, S. (2026, June). *ML Job Interviews: The Ultimate Guide*. silviasapora.github.io. https://silviasapora.github.io/blog/ml-interviews.html

Compact / bookmark-style ingest at the human's request, with My take framed for **meta** intent (studying how senior MLEs / new-grad PhDs talk about hiring), not personal interview prep. Files touched:

- Created `raw/blogs/2026-sapora-ml-interviews/clip.md` (AI-extracted via WebFetch — not a verbatim crawl; section paraphrases + verbatim quotes + verbatim technical-topics list)
- Created `raw/blogs/2026-sapora-ml-interviews/source.md` (Wayback URL pending)
- Created [[sources/2026-sapora-ml-interviews]] — compact summary with TL;DR, key load-bearing claims, notable quotes, technical-topics list pointer, and a meta-flavored "My take" focused on what the post is evidence of rather than what it advises
- **Created new topic** [[topics/careers]] — first non-research, career-mechanics hub; framed for ML hiring funnels, prep canons, compensation, negotiation; seeded with five "implicit structural claims" observations from Sapora's post
- Updated [[index]] — sources 3→4, topics 10→11, source listed under Blogs/webpages

Key takeaways recorded in the source page's "My take":
- The "top labs" callback bar (3+ first-author top-venue papers + ≥1 internship) is publicly legible enough that a hire writes it down without hedging — useful calibration point.
- The technical-topics list is a **2026 snapshot of the canon** and should be re-read against future posts to track drift.
- LLM-mock-interviews are reported as substantively effective; raises an open question about whether interviewers will start asking things LLMs are bad at producing (signal inflation forcing canon shift).
- RSU vs. stock-options paragraph is the most underrated section — reference-worthy for "should I take the startup offer?" questions.
- Skepticism: n=1 from a strong-credential candidate at a moment of high demand; generalizations about negotiation leverage and offer movement probably don't transfer to less leverage-rich situations.

No concept or entity pages promoted (candidates noted in topic page for later: peer-offer leverage, "top labs" callback bar, the RSU/options discount, LLM-mock-interview-as-prep; DeepMind / Anthropic / OpenAI / Cohere / Isomorphic Labs / Meta / Mistral as entities once a second source references each).

Follow-ups suggested:
- Submit URL to Wayback Machine and paste archive URL into `source.md`
- Find/read the equivalent guide from the MLE / RE side (system-design-heavy interviews) to triangulate vs. the RS-specific framing here
- Find/read a hiring-manager-side post (recruiter incentives, calibration practices, how offer numbers get set) for the other side of the table
- Re-read the technical-topics list in 12+ months to see what drops off and what's added

## [2026-06-18] ingest | Meta-Harness: End-to-End Optimization of Model Harnesses (Lee et al., 2026)

Source: Lee, Y., Nair, R., Zhang, Q., Lee, K., Khattab, O., & Finn, C. (2026). *Meta-Harness: End-to-End Optimization of Model Harnesses*. arXiv:2603.28052. https://arxiv.org/abs/2603.28052 · project page https://yoonholee.com/meta-harness/

Resumed an interrupted ingest (only `paper.pdf` existed on disk). Medium-depth per human request (TL;DR + key ideas + results + criticisms + My take, like DeLM). No entities promoted, no synthesis page (waiting for a fourth source before promoting the "load-bearing diagnostic knob" synthesis). Files touched:

- Created `raw/papers/2026-lee-meta-harness/source.md` (full metadata + BibTeX + verbatim abstract + project resources)
- Created [[sources/2026-lee-meta-harness]] — medium-depth summary: TL;DR, key ideas (filesystem-as-feedback-channel, Pareto reporting over accuracy × context, additive-over-corrective pivot, harness-search-as-policy-improvement), 4 notable quotes, three-domain methodology, ablation table (raw traces 50.0 vs scores-only 34.6 median), text-classification vs ACE comparison, math-reasoning OOD across 5 base models, TerminalBench-2 winner (+80-line additive env-bootstrap), connections (cross-linked to MACU and DeLM with "third converging signal" framing), open questions / criticisms (TerminalBench-2 search=eval; proposer=Claude Code is uncontrolled), My take
- Updated [[topics/agent-harness]] — promoted the "load-bearing knob lives in quality/control/diagnostic dimension" framing to the top of the page as a 3-paper signal (MACU replan budget / DeLM admission verifier / Meta-Harness raw traces all land in this dimension, not topology); added comparison table; flagged synthesis-page promotion as pending until a 4th source corroborates
- Updated [[topics/self-evolving-agents]] — **first source on this topic**; framed Current understanding around code-space vs weight-space self-improvement, filesystem-as-experience-store, additive-over-corrective pivot, non-Markovian iteration across runs
- Updated [[topics/evaluation]] — added Pareto-frontier reporting (Meta-Harness Figure 3, 8 non-dominated harness variants), search-set vs test-set discipline (2/3 domains clean, TerminalBench-2 acknowledged compromise), cross-model OOD eval as stronger generalization claim
- Updated [[topics/benchmarks]] — added third benchmark cluster (USPTO-50k + Symptom2Disease + LawBench for online text classification; IMO-AnswerBench + IMO-ProofBench + ArXivMath for math; TerminalBench-2 for agentic coding); flagged "discovery problem" framing as a known compromise; added TerminalBench-2 and IMO-AnswerBench as entity-promotion candidates
- Updated [[topics/rag]] — added second source (discovered 4-route BM25 retrieval router with LaTeX-preserving tokenizer for ArXivMath; label-primed query also surfaces independently in text classification); flagged label-primed-query as a cross-domain RAG primitive candidate
- Updated [[topics/agentic-rl]] — added tangential link with "harness-level credit assignment" / "code-space policy improvement" framing; contrasted against gradient-based credit assignment
- Updated [[index]] — sources 4→5, source counts on touched topics bumped (agent-harness 2→3, self-evolving-agents 0→1, evaluation 2→3, benchmarks 2→3, rag 1→2, agentic-rl 1→2), Meta-Harness listed under Papers, last-touched date updated

Key takeaway recorded in the source page's "My take": the most important contribution isn't the search algorithm (which is minimal) but the **filesystem-as-feedback-channel** primitive — letting a coding-agent proposer freely browse all prior candidates' source/scores/traces is what unlocks the search. The ablation is unambiguous: scores-only / scores+summary medians cluster at ~34.6–34.9 vs full-trace median 50.0. This is the third paper in the wiki where the load-bearing component lives in the quality/control/diagnostic dimension rather than the topology/parallelism dimension; the synthesis page is now warranted on a 4th corroboration.

No concept or entity pages promoted (candidates noted: filesystem-as-feedback-channel, additive-over-corrective pivot, label-primed-query, latex-preserving-tokenization, discovered-retrieval-router, code-space-self-improvement, non-Markovian-iteration; TerminalBench-2, IMO-AnswerBench, Claude Code, ACE as entity candidates once a second source references each).

Follow-ups suggested:
- Read ACE (the SOTA context-management baseline Meta-Harness beats by 7.7 pp at 4× fewer context tokens) — important to understand what's being beat
- Read Tian et al. 2026 (SWE-bench Mobile) which the paper cites for the "6× perf gap on the same benchmark depending on harness" claim
- Watch for a fourth source corroborating the "load-bearing knob lives in quality/control/diagnostic dimension" pattern — promote to a synthesis page when it lands
- Promote TerminalBench-2 to an entity page once a third source references it (already in MACU as comparison context, now primary in Meta-Harness)
- Check whether the discovered TerminalBench-2 harness artifact (released on GitHub) reproduces the reported gains in independent hands

## [2026-06-18] ingest | Self-Harness: Harnesses That Improve Themselves

Ingested Zhang et al. (2026), arXiv:2606.09498v1. Sibling paper to Meta-Harness — same problem (automating harness improvement), opposite half of the human/Meta/Self trilemma the Self-Harness paper itself names: same fixed model is both *target* and *proposer*, instead of an external stronger proposer. On a 64-case filtered subset of TerminalBench-2, the method lifts held-out pass rate by 14–21 pp absolute across three mid-tier models (MiniMax M2.5, Qwen3.5-35B-A3B, GLM-5) from a minimal initial harness. Medium depth per user request. User's initial reading notes flagged two real confounds (non-SOTA models; minimal initial harness amplifying gains) — preserved as a labeled subsection of "My take" with Claude's independent assessment alongside, per user direction. Files touched:

- Created `raw/papers/2026-zhang-self-harness/source.md` (full metadata + BibTeX + verbatim abstract; PDF was already downloaded into the folder)
- Created [[sources/2026-zhang-self-harness]] — medium-depth summary: TL;DR framed against Meta-Harness contrast, 8 key ideas (trilemma taxonomy, verifier-grounded failure clustering, diverse-yet-minimal proposal, conservative non-regressive Pareto acceptance gate Δ_in≥0 ∧ Δ_ho≥0 ∧ max>0, tight editable interface, merge-multiple-accepted, model-specific scaffold adaptation, self-loop without stronger external proposer), 4 notable quotes, methodology section with Algorithm 1 description + main results table + per-model retained edits table, 7 criticisms (non-SOTA models, minimal initial harness, no head-to-head, tight interface, merge conflicts, 64-case subset, no isolating ablation), "My take" split into three labeled subsections: Human's initial reading notes (preserved verbatim concerns, flagged for revisit), Closer reading (Claude's assessment alongside), What's worth taking forward
- Created [[entities/terminalbench-2]] — **first entity page in the wiki**. Promoted from candidate after 3 sources reference it. Frontmatter `kind: benchmark`, aliases for variant spellings. Cross-source results table with explicit warning that numbers are not directly comparable (Meta-Harness uses full 89, Self-Harness uses 64-case subset; different models/protocols)
- Updated [[topics/agent-harness]] — source_count 3→4; added "Harness-improvement paradigms (3-way taxonomy from Self-Harness Figure 1)" section with human/Meta/Self table at top of Current understanding; updated "load-bearing knob" pattern section to "3 clean + 1 soft = 4 sources" with new "Ablation-isolated?" column flagging Self-Harness as soft (no clean ablation); updated harness-search-converges-to-additive-patches observation from n=1 to n=2; added "Initial-harness floor matters a lot" and "Editable interface tightness" observations; expanded Open questions with "Does Self-Harness work on strong models with strong starting harnesses?" follow-up. **Synthesis page promotion still pending** per user direction — Self-Harness matches the pattern but lacks the clean isolating ablation
- Updated [[topics/self-evolving-agents]] — source_count 1→2; added proposer-vs-target axis table at top of Current understanding mapping onto the human/Meta/Self trilemma; framed Self-Harness as the cleaner self-evolution datapoint (same model is proposer + target); added model-specific-convergence and acceptance-gate-as-regression-defense observations
- Updated [[topics/evaluation]] — source_count 3→4; added non-regressive Pareto acceptance gate as a new **in-loop** evaluation primitive distinct from post-hoc final-system comparisons; added per-iteration held-in/held-out split as the in-loop analog of search-set/test-set discipline
- Updated [[topics/benchmarks]] — source_count 3→4; promoted TerminalBench-2 to its own entity page and added a "Key entities" link to it; reframed "discovery problem" vs "in-loop held-in/held-out split" as the design axis to watch for harness-search papers
- Updated [[sources/2026-lee-meta-harness]] (cross-link) — added Self-Harness to Related sources with explicit framing of the two as opposite halves of the trilemma both targeting TerminalBench-2; noted no head-to-head exists and what it would isolate
- Updated [[index]] — sources 5→6, entities 0→1, last-touched note bumped; source counts on touched topics updated (agent-harness 3→4, self-evolving-agents 1→2, evaluation 3→4, benchmarks 3→4); first Entities listing populated; Self-Harness added under Papers

Key takeaways:
- **Pattern reinforcement (soft):** Self-Harness's most plausibly load-bearing components — verifier-grounded failure clustering (diagnostic richness) and the non-regressive acceptance gate (quality control) — sit in the same dimension as MACU's replan budget, DeLM's admission-time verifier, and Meta-Harness's raw trace access. n=3 clean + 1 soft = 4. **Synthesis page still pending** until a 4th *cleanly ablated* source lands.
- **The human/Meta/Self trilemma framing (Self-Harness Figure 1)** is a useful taxonomy the wiki now uses across topics/agent-harness and topics/self-evolving-agents.
- **Human reviewer's concerns are real:** Self-Harness's choice of non-SOTA models and a deliberately minimal initial harness genuinely confounds the headline numbers. The natural follow-up — same protocol on Claude Sonnet 4.5 + Claude Code's default scaffold — is not run.
- **Convergent observation:** harness-search converges to small additive edits in both Meta-Harness and Self-Harness. n=2 on the same benchmark family.

Follow-ups suggested:
- Run Self-Harness's protocol with Claude Sonnet 4.5 starting from Claude Code's default scaffold on the same TerminalBench-2 subset — directly tests the user's primary concern
- Run a head-to-head between Self-Harness and Meta-Harness from a common starting harness on TerminalBench-2 — isolates "does a stronger external proposer matter?"
- Watch for a 4th source with a clean isolating ablation to land the load-bearing-quality-knob synthesis page
- Read ACE (still pending from Meta-Harness ingest) — referenced in both papers' baselines
- Track DeepAgent SDK (Self-Harness's underlying framework) as a potential entity candidate alongside Claude Code
