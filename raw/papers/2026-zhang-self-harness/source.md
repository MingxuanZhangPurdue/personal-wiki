# Self-Harness: Harnesses That Improve Themselves

<!--
Place this file at: raw/papers/<slug>/source.md
This is the IMMUTABLE metadata file for a raw source. Edit on creation; don't change after.
The corresponding wiki page lives at: wiki/sources/<slug>.md
-->

- **Type:** paper
- **Title:** Self-Harness: Harnesses That Improve Themselves
- **Authors:** Hangfan Zhang, Shao Zhang, Kangcong Li, Chen Zhang, Yang Chen, Yiqun Zhang, Lei Bai, Shuyue Hu
- **Year:** 2026
- **Venue / Publisher:** arXiv preprint (cs.CL), 8 June 2026
- **URL:** https://arxiv.org/abs/2606.09498v1
- **Archive URL:** _(not applicable; arXiv is canonical archive)_
- **DOI / arXiv:** arXiv:2606.09498v1
- **Local file:** paper.pdf
- **Date added:** 2026-06-18
- **Added by:** Ming (human curator)

## Affiliations

- Shanghai Artificial Intelligence Laboratory (all authors)
- Corresponding authors: Lei Bai, Shuyue Hu

## Abstract (verbatim)

> The performance of LLM-based agents is jointly shaped by their base models and the harnesses that mediate their interaction with the environment. Because different models exhibit distinct behaviors, effective harness design is inherently model-specific. Yet agent harnesses are still largely engineered by human experts, a paradigm that scales poorly as modern LLMs become increasingly diverse and rapidly evolving. In this paper, we introduce **Self-Harness**, a new paradigm in which an LLM-based agent improves its own operating harness, without relying on human engineers or stronger external agents. We operationalize Self-Harness as an iterative loop with three stages: *Weakness Mining*, which identifies model-specific failure patterns from execution traces; *Harness Proposal*, which generates diverse yet minimal harness modifications tied to these failures; and *Proposal Validation*, which accepts candidate edits only after regression testing. We instantiate Self-Harness on Terminal-Bench-2.0 using a minimal initial harness and three base models from diverse families: MiniMax M2.5, Qwen3.5-35B-A3B, and GLM-5. Across all three models, Self-Harness consistently improves performance, with held-out pass rates increasing from 40.5% to 61.9%, 23.8% to 38.1%, and 42.9% to 57.1%, respectively. Qualitative analyses further show that Self-Harness does not simply add generic instructions, but effectively turns model-specific weaknesses into concrete, executable harness changes. These results suggest a path toward LLM-based agents that are not merely shaped by their harnesses, but can also participate in reshaping them.

## Citation

```bibtex
@article{zhang2026selfharness,
  title  = {Self-Harness: Harnesses That Improve Themselves},
  author = {Zhang, Hangfan and Zhang, Shao and Li, Kangcong and Zhang, Chen and Chen, Yang and Zhang, Yiqun and Bai, Lei and Hu, Shuyue},
  journal= {arXiv preprint arXiv:2606.09498},
  year   = {2026},
  url    = {https://arxiv.org/abs/2606.09498}
}
```

## Why I'm reading this

Sibling paper to Meta-Harness [[wiki/sources/2026-lee-meta-harness]] — same problem (automating harness improvement), different framing (same fixed model as both target and proposer, instead of a stronger external proposer). Direct hit on [[wiki/topics/agent-harness]] and [[wiki/topics/self-evolving-agents]]. Same benchmark (TerminalBench-2.0) as Meta-Harness, so a natural comparison point even though the paper does not run head-to-head. Also flagged on first read for non-SOTA models + intentionally weak initial harness — worth checking how much of the gain is "any optimization off a near-zero baseline."
