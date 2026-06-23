---
type: topic
slug: rag
created: 2026-06-17
updated: 2026-06-18
source_count: 2
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

Second data point: [[../sources/2026-lee-meta-harness]] (Meta-Harness) discovered a **discovered, non-hand-engineered retrieval router** on the ArXivMath domain. The proposer converged on a 4-route BM25 setup with a LaTeX-preserving tokenizer:

- **Route 1: theorem-statement match** — index theorem statements as documents, query with the problem statement.
- **Route 2: solved-problem match** — index solved examples, query with the problem.
- **Route 3: label-primed query** — concatenate predicted answer labels with the query before retrieval (also surfaced independently in the text-classification domain).
- **Route 4: raw BM25** — plain lexical retrieval as a fallback.

The LaTeX-preserving tokenizer is non-trivial: standard BM25 tokenizers shred `\frac{a}{b}` into noise, and the proposer discovered (via filesystem-visible trace inspection) that fixing tokenization yielded outsized gains. Two takeaways:

- **Retrieval design choices can be discovered, not just hand-tuned.** The proposer in Meta-Harness re-derived a multi-route BM25 router from scratch given only execution traces. This is structurally a "RAG harness search" result.
- **Label-priming the query is the same primitive surfaced in two domains.** Both text classification and math reasoning converged on "include candidate labels / answer hints in the retrieval query." Possible candidate for a cross-domain RAG primitive.

## Key concepts

_None promoted yet. Candidates: multi-resolution / unfoldable summarization, RAG-over-team-state, verification-as-retrieval-trust, label-primed-query, latex-preserving-tokenization, discovered-retrieval-router._

## Sources

- [[../sources/2026-mao-decentralized-mas-shared-context]] — Mao & Mirhoseini (2026). _Tangential._ Uses a gist/summary/raw hierarchy with explicit unfold directives over a shared blackboard; structurally a RAG pattern applied to the agent team's own state.
- [[../sources/2026-lee-meta-harness]] — Lee et al. (2026). On the ArXivMath domain, the harness search converges to a discovered 4-route BM25 retrieval router with a LaTeX-preserving tokenizer; the "label-primed query" pattern also independently surfaces in the text-classification domain.

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
- 2026-06-18: added [[../sources/2026-lee-meta-harness]] (discovered 4-route BM25 retrieval router with LaTeX-preserving tokenizer for ArXivMath; label-primed query also independently surfaces in text classification)
