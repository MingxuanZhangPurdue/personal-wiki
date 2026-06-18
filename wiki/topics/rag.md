---
type: topic
slug: rag
created: 2026-06-17
updated: 2026-06-17
source_count: 1
---

# Retrieval-Augmented Generation (RAG)

Augmenting LLM generation with retrieved external context. Spans naive vector RAG, hybrid (BM25 + dense), re-ranking, graph RAG, agentic RAG (where the agent decides what/when to retrieve), long-context vs RAG trade-offs, and evaluation methodology (RAGAS, faithfulness, answer relevance).

## Current understanding

First (tangential) data point: [[../sources/2026-mao-decentralized-mas-shared-context]] (DeLM) doesn't propose a RAG system per se, but its **gist `G_i` → reference-grounded summary `S_i` → raw evidence `r_i`** hierarchy is structurally RAG-adjacent. Agents read the gist layer by default and explicitly *unfold* to a fuller view (`UNFOLD` for the summary, `DEEP_UNFOLD` for the raw evidence) only when needed. This is essentially **multi-resolution retrieval over the agent team's own working set**:

- The "index" is the blackboard (the team's accumulated verified gists).
- "Retrieval" is the unfold directive issued by an agent that needs more detail.
- "Chunking" is the hierarchy itself — three levels of resolution per unit of progress, pre-computed at admission time.
- The retrieval target is **the team's prior work**, not an external corpus.

Two observations worth tracking as more sources land:

- **RAG-over-team-state** is a different shape from RAG-over-external-corpus. The freshness, authorship, and verification story is completely different — DeLM's verifier gate is the analog of "trust the document."
- **The OS-paging analogy applies cleanly.** Gist = working set in context; summary + raw = backing store on the blackboard; unfold = demand paging. Worth seeing whether other 2026 papers reach for the same analogy independently.

## Key concepts

_None promoted yet. Candidates: multi-resolution / unfoldable summarization, RAG-over-team-state, verification-as-retrieval-trust._

## Sources

- [[../sources/2026-mao-decentralized-mas-shared-context]] — Mao & Mirhoseini (2026). _Tangential._ Uses a gist/summary/raw hierarchy with explicit unfold directives over a shared blackboard; structurally a RAG pattern applied to the agent team's own state.

## Open questions

- When does long-context obviate RAG? When does it not?
- How does agentic RAG compare to single-shot retrieval?
- What's the right way to evaluate retrieval vs. generation quality independently?

## Related topics

- [[agent-memory]]
- [[evaluation]]

## Log

- 2026-06-17: created (stub)
- 2026-06-17: added [[../sources/2026-mao-decentralized-mas-shared-context]] (tangential — multi-resolution unfold-on-demand over the team blackboard); first source on this topic
