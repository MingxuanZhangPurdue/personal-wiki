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
