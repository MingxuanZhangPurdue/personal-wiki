# Decentralized Multi-Agent Systems with Shared Context

- **Type:** paper
- **Title:** Decentralized Multi-Agent Systems with Shared Context
- **Authors:** Yuzhen Mao, Azalia Mirhoseini
- **Year:** 2026
- **Venue / Publisher:** arXiv preprint (cs.MA primary; cs.AI)
- **URL:** https://arxiv.org/abs/2606.10662
- **PDF URL:** https://arxiv.org/pdf/2606.10662
- **Project page:** https://yuzhenmao.github.io/DeLM/
- **Archive URL:** _(not applicable for arXiv — PDF is mirrored)_
- **DOI / arXiv:** arXiv:2606.10662
- **Local file:** paper.pdf
- **Date added:** 2026-06-17
- **Date submitted:** 2026-06-09
- **Added by:** Mingxuan

## Citation

> Mao, Y., & Mirhoseini, A. (2026). *Decentralized Multi-Agent Systems with Shared Context* (arXiv:2606.10662) [Preprint]. arXiv. https://arxiv.org/abs/2606.10662

```bibtex
@article{mao2026delm,
  title   = {Decentralized Multi-Agent Systems with Shared Context},
  author  = {Mao, Yuzhen and Mirhoseini, Azalia},
  journal = {arXiv preprint arXiv:2606.10662},
  year    = {2026}
}
```

## Abstract

Multi-agent systems (MAS) can scale large language model reasoning at test time by decomposing complex problems into parallel subtasks. However, most existing MAS rely on centralized orchestration, where a main agent assigns work, collects outputs, and merges results. As the number of subtasks grows, this controller becomes a communication and integration bottleneck. We propose Decentralized Language Models (DeLM), a MAS framework that decentralizes coordination through parallel agents, a shared verified context, and a task queue. Agents asynchronously claim subtasks, read accumulated progress, perform local reasoning, and write back compact verified updates. The shared context acts as a common communication substrate, enabling agents to build on one another's verified progress without routing every update through a central controller. Empirically, DeLM improves both software-engineering test-time scaling and long-context reasoning. On SWE-bench Verified, DeLM achieves the best performance across Avg.@1, Pass@2, and Pass@4, with gains of up to 10.5 percentage points over the strongest baseline, while reducing cost per task by roughly 50%. On LongBench-v2 Multi-Doc QA, DeLM achieves the highest average accuracy across four frontier model families, improving over the strongest baseline by up to 5.7 percentage points. The code is available on our project website at https://yuzhenmao.github.io/DeLM/.

## Why I'm reading this

_(Mingxuan to fill in.)_
