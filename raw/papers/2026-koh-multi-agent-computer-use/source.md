# Multi-Agent Computer Use

- **Type:** paper
- **Title:** Multi-Agent Computer Use
- **Authors:** Jing Yu Koh, Ruslan Salakhutdinov, Daniel Fried
- **Year:** 2026
- **Venue / Publisher:** arXiv preprint (cs.MA primary; cs.CL, cs.LG)
- **URL:** https://arxiv.org/abs/2606.01533
- **PDF URL:** https://arxiv.org/pdf/2606.01533v1
- **Project page:** https://jykoh.com/multi-agent-computer-use
- **Archive URL:** _(not applicable for arXiv — PDF is mirrored)_
- **DOI / arXiv:** arXiv:2606.01533v1
- **Local file:** paper.pdf
- **Date added:** 2026-06-17
- **Date submitted:** 2026-06-01
- **Added by:** Mingxuan

## Citation

> Koh, J. Y., Salakhutdinov, R., & Fried, D. (2026). *Multi-Agent Computer Use* (arXiv:2606.01533) [Preprint]. arXiv. https://arxiv.org/abs/2606.01533

```bibtex
@article{koh2026multiagentcomputeruse,
  title   = {Multi-Agent Computer Use},
  author  = {Koh, Jing Yu and Salakhutdinov, Ruslan and Fried, Daniel},
  journal = {arXiv preprint arXiv:2606.01533},
  year    = {2026}
}
```

## Abstract

Computer use agents (CUAs) today are primarily deployed as single serial agents. This setup is suboptimal for complex long-horizon tasks that benefit from task decomposition, parallel execution, and consistent re-planning based on new information. In this paper, we argue that we should instead move towards evaluating and building multi-agent computer use (MACU) systems. These systems, which emphasize planning and parallel execution, alleviate many of the shortcomings of single-agent CUAs. We propose a general multi-agent setup in which a manager model decomposes computer use tasks as a directed acyclic graph (DAG), encoding relevant dependencies and goals for subagents. At each iteration, the manager dispatches parallel CUA subagents to carry out nodes on the ready frontier of the DAG, and continuously revises the DAG (adding, canceling, or rewriting nodes) as new findings arrive from subagents. This design treats the partially observable environment of computer use as a first class challenge: information that downstream agents may not be able to re-observe are retained and passed forward through the manager and DAG structure. We demonstrate that MACU consistently improves over strong single-agent baselines by 3.4-25.5% on desktop (OSWorld) and web navigation (Online-Mind2Web, WebTailBench, Odysseys) benchmarks, exhibits more favorable test-time scaling, and solves complex long-horizon tasks where single-agent CUAs get stuck. On Odysseys, a long-horizon web navigation benchmark, MACU improves average task completion wall-clock time by ~1.5×, demonstrating its efficacy in speeding up traditionally slow CUA pipelines.

## Why I'm reading this

_(Mingxuan to fill in.)_
