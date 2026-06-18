---
type: topic
slug: computer-use
created: 2026-06-17
updated: 2026-06-17
source_count: 1
---

# Computer Use Agents (CUAs)

Agents that operate a real desktop or browser via screenshots + mouse/keyboard primitives, instead of code APIs. Distinct from coding agents (which edit text/files in a repo) and tool-calling agents (which invoke functions): a CUA's interface is the screen. Includes desktop CUAs (OSWorld-style: file managers, office apps, terminals) and web CUAs (browse, fill forms, navigate flows).

Key challenges that distinguish this from other agent settings:

- **Partial observability** — the screen at time `t` may not be re-derivable at `t+1` (modals close, prices change, OTPs expire).
- **High step counts** — even simple tasks take dozens of clicks/scrolls; long-horizon tasks (booking, research) can take 100+ steps.
- **Wall-clock cost** — each step requires a screenshot + VLM call + action; serial CUAs are slow.
- **Non-recoverable side effects** — "click submit" can charge a card; rollback is harder than in a sandboxed coding env.

## Current understanding

Only one source so far ([[../sources/2026-koh-multi-agent-computer-use]]). The early framing the wiki is adopting:

- **Single-agent CUAs are the default** today (Claude Computer Use, OpenAI CUA, Operator) — one serial ReAct loop, one screen, one action stream.
- **The first natural scaling axis is parallel decomposition** — manager + N subagents, each in its own VM, coordinated by a DAG or similar plan structure. MACU is the cleanest published instance of this so far.
- **Replanning is more important than parallelism alone** — MACU's ablation shows the replan budget drives most of the gain (25% → 58% SR on Odysseys when `B` goes 0 → 10), while parallel breadth `N` matters less.
- **Partial observability is a first-class design constraint** — once you split work across subagents in separate VMs, downstream agents can't re-observe what upstream agents saw, so the manager + plan structure must carry forward state.

## Key concepts

_None promoted to their own pages yet. Candidates from MACU: DAG-based task decomposition, mutable plan with replan budget, state cloning between subagents (`init_from`), variant attempts (`variant_of`)._

## Key entities

_None yet. Benchmarks mentioned but not yet promoted: OSWorld, Online-Mind2Web, WebTailBench, Odysseys._

## Sources

### Foundational

- [[../sources/2026-koh-multi-agent-computer-use]] — Koh, Salakhutdinov, Fried (2026). Proposes multi-agent CUAs with a mutable DAG + parallel subagents; +3.4–25.5% SR over single-agent CUAs across 4 benchmarks; introduces the Odysseys long-horizon benchmark.

## Open questions

- How does this compare to general multi-agent frameworks (AutoGen, LangGraph, CrewAI) on the same benchmarks?
- Is a DAG the right plan structure, or would a tree / partial-order plan / re-orderable list work as well?
- What's the right manager model — does it need to be the strongest available, or is a smaller specialized planner sufficient?
- How does this hold up on tasks with strong sequential dependencies (where parallelism can't help)?
- Cost: parallel subagents = N× token + VM cost; what's the right benchmark for $/task vs success rate?

## Related topics

- [[agent-harness]] — MACU is a harness design; the manager+DAG+subagent loop is the harness
- [[benchmarks]] — OSWorld, Online-Mind2Web, WebTailBench, Odysseys
- [[evaluation]] — Odysseys is itself an evaluation contribution
- [[agent-memory]] — the DAG-as-carrier-of-partial-observable-state idea overlaps with explicit memory
- [[sandbox]] — per-subagent VMs are sandboxes; same isolation primitive

## Log

- 2026-06-17: created on ingest of [[../sources/2026-koh-multi-agent-computer-use]]
