---
type: topic
slug: multi-agent-coordination
created: 2026-06-17
updated: 2026-06-17
source_count: 2
---

# Multi-Agent Coordination

How multi-agent LLM systems coordinate: what's the topology (centralized manager vs. decentralized peers vs. blackboard), what's the communication substrate (prompts vs. shared state vs. messages), how is intermediate progress shared, and how is it validated. Distinct from [[agent-harness]] (which is the broader scaffolding category) by focusing specifically on the *coordination* problem in MAS — what happens when N > 1 agents need to work together.

## Current understanding

Two data points so far, and they form a striking dialectic — two different answers to "how should multi-agent LLM systems coordinate":

| | [[../sources/2026-koh-multi-agent-computer-use]] (MACU) | [[../sources/2026-mao-decentralized-mas-shared-context]] (DeLM) |
|---|---|---|
| **Topology** | Centralized: manager + parallel CUA subagents | Decentralized blackboard: parallel agents + shared context + task queue |
| **Coordination** | Manager mutates a DAG, dispatches subagents, replans on each return | No manager; agents asynchronously claim tasks, read shared state, write verified updates back |
| **Plan structure** | Mutable DAG with `init_from` / `variant_of` nodes | Shared verified context (gists) + task queue with dependency tags |
| **State carrier** | DAG node metadata | Compact verified gists (`G_i`), with backing `S_i` summaries and raw evidence |
| **Validation** | None mentioned | LLM admission-time verifier (rejects/regenerates failed gists) |
| **Isolation primitive** | Per-subagent VM | Per-agent context window |
| **Domain demonstrated** | Computer use (OSWorld, web) | SWE-bench Verified + LongBench-v2 long-context QA |
| **Key ablation finding** | Replan budget matters more than parallel breadth | Verification matters more than the unfolding hierarchy |
| **Code release** | Not mentioned | Yes — `https://yuzhenmao.github.io/DeLM/` |

Patterns the wiki is starting to notice:

- **Centralized vs. decentralized is the active design axis** in 2026 multi-agent papers. MACU keeps a manager but makes the plan mutable; DeLM removes the manager entirely and routes coordination through state. Both demonstrate >5 pp gains over their respective "centralized parallel" baselines.
- **"Compact summaries of progress, shared across agents" is converging as a primitive.** MACU's DAG node metadata and DeLM's gists are doing similar work: distilling what one agent learned into something the next agent can use without reading the full trace.
- **The validation gap.** DeLM explicitly flags that unverified shared state propagates errors (ablation: removing verification is the biggest single hit). MACU does not have an admission-time verifier. This is a candidate primitive other MAS papers should adopt.
- **Domain shapes topology.** Computer use is partial-observability-heavy and needs VM isolation; SWE / long-context QA is text-only and can use a context-window blackboard. Whether either topology generalizes across both domains is open.

## Key concepts

_None promoted to their own pages yet. Candidates:_
- DAG-based decomposition (MACU)
- Mutable plan with replan budget (MACU)
- State cloning (`init_from`) and variant attempts (`variant_of`) (MACU)
- Blackboard / shared-context coordination (DeLM, with cited precedents Han & Zhang 2025, Salemi et al. 2025)
- Admission-time verification (DeLM)
- Compact-unfoldable hierarchy (`gist → summary → raw`) (DeLM)
- Pass@k-as-baseline for MAS evaluation (both)

## Key entities

_None yet. Both MACU and DeLM cite a recurring set of comparison systems worth tracking as entities once a second source references each:_
- AOrchestra (Ruan et al. 2026) — centralized MAS baseline in both papers
- Claude Code / Claude Code Subagents (Anthropic 2026) — baseline in both papers
- ReadAgent (Lee et al. 2024) — long-context baseline in DeLM
- RLM / Recursive Language Models (Zhang et al. 2025) — DeLM combines with this; complementary, not competing
- mini-SWE-agent (Yang et al. 2024) — SWE-bench baseline in DeLM

## Sources

### Foundational

- [[../sources/2026-koh-multi-agent-computer-use]] — Koh, Salakhutdinov, Fried (2026). Centralized mutable-DAG topology with parallel VM-isolated CUA subagents; ablations show replanning > parallel breadth.
- [[../sources/2026-mao-decentralized-mas-shared-context]] — Mao, Mirhoseini (2026). Decentralized blackboard topology with verified shared context; ablations show admission-time verification is the load-bearing component.

## Open questions

- **Is "validated shared state" a primitive that should be added to centralized MAS too?** DeLM shows it's the biggest contributor; MACU doesn't have it. What would MACU + admission-time verification look like?
- **Does decentralized coordination work for computer use?** DeLM is evaluated on text-only tasks (SWE, QA); MACU's VM-isolated subagents handle partial observability through DAG metadata. Would DeLM-style coordination scale to GUI agents?
- **Manager strength vs. no-manager.** MACU's ablation shows weakening the manager hurts a lot. DeLM has no manager. Are these papers really comparable, or are they optimizing different parts of the design space?
- **Cost of verification.** DeLM admits an LLM call per update; paper says "modest" but cost breakdown is thin. At what subagent count / update rate does verification become the bottleneck?
- **Decomposition quality.** Both papers admit they inherit the initial decomposition's quality. Adaptive decomposition (decide when to split/merge/terminate subtasks based on observed state) is open.
- **Beyond two topologies:** what about hybrid (a weak manager + a shared blackboard)? Or hierarchical blackboards?
- **Independent re-runs.** Both papers compare against centralized-parallel baselines they specify themselves. Cross-paper / independent re-runs on the same benchmarks would help triangulate.

## Related topics

- [[agent-harness]] — multi-agent coordination is a sub-category of harness design; both topics will cross-link as more sources land
- [[agent-memory]] — DeLM's shared context = "plan-as-memory" pattern; MACU's DAG node metadata is similar
- [[rag]] — DeLM's `gist → summary → raw` unfolding is RAG-adjacent (multi-resolution retrieval over the agent's working set)
- [[benchmarks]] — SWE-bench Verified, OSWorld, LongBench-v2, Odysseys, OOLONG all show up here
- [[evaluation]] — both papers raise pass@k-as-baseline and cost-per-task as first-class metrics
- [[computer-use]] — MACU's home topic
- [[sandbox]] — MACU's VM-per-subagent and DeLM's per-agent context windows are both "isolation primitives" for parallel agents

## Log

- 2026-06-17: created on ingest of [[../sources/2026-mao-decentralized-mas-shared-context]]; seeded comparison table with [[../sources/2026-koh-multi-agent-computer-use]]
