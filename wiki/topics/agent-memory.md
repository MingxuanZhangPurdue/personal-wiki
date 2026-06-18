---
type: topic
slug: agent-memory
created: 2026-06-17
updated: 2026-06-17
source_count: 2
---

# Agent Memory

How agents store, retrieve, and update knowledge across turns, sessions, and tasks. Includes short-term context management, episodic memory, semantic/working memory, scratchpads, memory-augmented architectures (MemGPT, A-MEM, Generative Agents), and forgetting strategies.

## Current understanding

Both data points so far frame "memory" not as a separate store but as a structured artifact the harness itself maintains:

- [[../sources/2026-koh-multi-agent-computer-use]] (MACU): **plan-as-memory.** In computer-use, observations that downstream agents physically cannot re-observe (closed modals, dynamic prices, one-time codes) are folded back into the DAG as node metadata. There's no separate memory store — the in-progress plan *is* the working memory of the team.
- [[../sources/2026-mao-decentralized-mas-shared-context]] (DeLM): **shared-context-as-memory, with a multi-resolution access pattern.** A central blackboard holds verified gists `G_i` for each completed subtask; each gist has a backing reference-grounded summary `S_i` and raw evidence `r_i`. Agents read at the gist level by default, then "unfold" to `S_i` or `r_i` via explicit directives only when more detail is needed. This is essentially a working-set + backing-store + demand-paging memory model lifted into the prompt layer.

Pattern starting to form: **2026 multi-agent harnesses are treating coordination state and team memory as the same artifact.** MACU's DAG nodes and DeLM's gists both serve double duty — they're "the plan / coordination substrate" *and* "what one agent remembered for the next." There's no separate retrieval system; memory is whatever the harness writes down to make coordination work.

DeLM also explicitly adds **verification** as a memory-write primitive: every gist passes through an LLM verifier before it's admitted to shared state, and the ablation says removing this gate causes the biggest single drop in performance. This is a memory-quality primitive that's separable from retrieval and worth tracking.

Open question for this topic: how does plan-/context-as-memory compare to RAG-as-memory and to explicit memory primitives (MemGPT, A-MEM) for partially observable or long-horizon environments? And does the "admission-time verification" idea transfer to those frameworks?

## Key concepts

_None yet._

## Sources

- [[../sources/2026-koh-multi-agent-computer-use]] — Koh et al. (2026). _Tangential:_ frames the manager's DAG as a carrier of partial-observability state across subagents — "plan-as-memory" rather than explicit memory primitives.
- [[../sources/2026-mao-decentralized-mas-shared-context]] — Mao & Mirhoseini (2026). Shared-context blackboard with gist/summary/raw hierarchy and unfold-on-demand access; verified at admission time. Treats coordination state and team memory as the same artifact.

## Open questions

- When is RAG sufficient vs. when do you need explicit memory primitives?
- How is memory consolidated, compressed, and pruned?
- How do agents decide what's worth remembering?

## Related topics

- [[rag]]
- [[self-evolving-agents]]

## Log

- 2026-06-17: created (stub)
- 2026-06-17: added [[../sources/2026-koh-multi-agent-computer-use]] (tangential — plan-as-memory framing)
- 2026-06-17: added [[../sources/2026-mao-decentralized-mas-shared-context]]; promoted the "coordination state = team memory" pattern to first-class Current understanding; flagged admission-time verification as a memory-quality primitive
