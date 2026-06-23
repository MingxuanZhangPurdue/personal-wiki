# Meta-Harness: End-to-End Optimization of Model Harnesses

<!--
Place this file at: raw/papers/<slug>/source.md
This is the IMMUTABLE metadata file for a raw source. Edit on creation; don't change after.
The corresponding wiki page lives at: wiki/sources/<slug>.md
-->

- **Type:** paper
- **Title:** Meta-Harness: End-to-End Optimization of Model Harnesses
- **Authors:** Yoonho Lee, Roshen Nair, Qizheng Zhang, Kangwook Lee, Omar Khattab, Chelsea Finn
- **Year:** 2026
- **Venue / Publisher:** arXiv preprint (cs.AI), 30 March 2026
- **URL:** https://arxiv.org/abs/2603.28052
- **Archive URL:** _(not applicable; arXiv is canonical archive)_
- **DOI / arXiv:** arXiv:2603.28052v1
- **Local file:** paper.pdf
- **Date added:** 2026-06-18
- **Added by:** Ming (human curator)

## Affiliations

- Stanford University (Yoonho Lee, Roshen Nair, Qizheng Zhang, Chelsea Finn)
- KRAFTON (Kangwook Lee)
- MIT (Omar Khattab)

## Project resources

- Project page (with interactive demo): https://yoonholee.com/meta-harness/
- Optimized TerminalBench-2 harness: https://github.com/stanford-iris-lab/meta-harness-tbench2-artifact

## Abstract (verbatim)

> The performance of large language model (LLM) systems depends not only on model weights, but also on their *harness*: the code that determines what information to store, retrieve, and present to the model. Yet harnesses are still designed largely by hand, and existing text optimizers are poorly matched to this setting because they compress feedback too aggressively: they are memoryless, condition only on scalar scores, or restrict feedback to short templates or summaries. We introduce **Meta-Harness**, an outer-loop system that searches over harness code for LLM applications. It uses an agentic proposer that accesses the source code, scores, and execution traces of all prior candidates through a filesystem. On online text classification, Meta-Harness improves over a state-of-the-art context management system by 7.7 points while using 4× fewer context tokens. On retrieval-augmented math reasoning, a single discovered harness improves accuracy on 200 IMO-level problems by 4.7 points on average across five held-out models. On agentic coding, discovered harnesses surpass the best hand-engineered baselines on TerminalBench-2. Together, these results show that richer access to prior experience can enable automated harness engineering.

## Citation

```bibtex
@article{lee2026metaharness,
  title  = {Meta-Harness: End-to-End Optimization of Model Harnesses},
  author = {Lee, Yoonho and Nair, Roshen and Zhang, Qizheng and Lee, Kangwook and Khattab, Omar and Finn, Chelsea},
  journal= {arXiv preprint arXiv:2603.28052},
  year   = {2026},
  url    = {https://arxiv.org/abs/2603.28052}
}
```

## Why I'm reading this

Direct hit on [[topics/agent-harness]]: this paper takes "harness engineering" as a first-class research target (rather than something practitioners do by hand) and operationalizes it as outer-loop search using a coding agent as the proposer. Adjacent to the MACU / DeLM cluster already in the wiki — but instead of proposing a *new* harness, it proposes a *meta-procedure for discovering harnesses*. Also notable: the proposer is Claude Code with Opus-4.6, which is interesting from a "what is Claude Code being used for in research?" angle.
