---
title: "Building Social World Models with Large Language Models"
authors: ["Haofei Yu", "Yining Zhao", "Guanyu Lin", "Jiaxuan You"]
year: 2026
type: paper
raw: ../../raw/papers/2026-yu-social-world-models/paper.pdf
url: https://arxiv.org/abs/2606.11482
added: 2026-06-18
status: read
tags: [world-models, prediction-markets, forecasting, posterior-distillation, elbo]
topics: [social-world-models, evaluation, benchmarks]
---

# Building Social World Models with Large Language Models

> Source: [[../../raw/papers/2026-yu-social-world-models/source.md|raw entry]] · [arXiv:2606.11482](https://arxiv.org/abs/2606.11482)

## TL;DR

Yu et al. frame **social forecasting** as learning a *social world model* (SWM) — a distribution `P(s_{t+1} | s_t, e_t)` where the state `s_t` is a window of prediction-market prices on real-world questions and the event `e_t` is a news article. They build SWM-Bench (12,789 (state, event, next-state) triples from Polymarket + Kalshi, Dec 2022–Jan 2026), and train small Qwen3 models (0.6B / 4B / 8B) via **posterior-guided distillation**: a frozen ~400B Qwen3.5 LLM acts as a hindsight variational posterior `Q_φ(z | s_t, e_t, s_{t+1})` that attributes the state transition to the news event, producing pseudo-labels that supervise both a learned prior attributor `P_η(z | s_t, e_t)` and a transition model `P_θ(s_{t+1} | s_t, e_t, z)`. The 8B SWM matches or beats GPT-5.5 on directional-accuracy on both venues, and an oracle ablation (feeding the posterior `z` to the transition model) shows attribution — not transition — is the binding constraint. Light, focused paper: a new modeling primitive (latent event attribution as the bottleneck in social forecasting), a new benchmark, and one clean ablation.

## Key ideas

- **Social World Model formulation.** State `s_t` is a *windowed* vector of prediction-market prices over a panel of contracts (e.g. Polymarket / Kalshi), event `e_t` is a news article; target is `s_{t+1}`. Reframes forecasting as a one-step transition model rather than next-token prediction.
- **Latent attribution `Z_t`.** Most news doesn't move most markets. The paper introduces a latent attribution variable `Z_t` (a normalized distribution over contracts) representing which contracts the event `e_t` actually affects. The full model factors as `P_θ(s_{t+1} | s_t, e_t) = Σ_z P_η(z | s_t, e_t) P_θ(s_{t+1} | s_t, e_t, z)`.
- **Posterior-guided distillation (loose ELBO).** The frozen ~400B Qwen3.5 acts as `Q_φ(z | s_t, e_t, s_{t+1})` — a hindsight attributor that sees the realized `s_{t+1}` and decides which contracts the event "explains". This `z` becomes a pseudo-label for training (i) the prior attributor `P_η` (KL to the posterior) and (ii) the transition model `P_θ` (likelihood under posterior `z`). Variational distribution is frozen → ELBO is loose, but training is tractable on small open models.
- **Bradley-Terry/Luce-style attribution construction.** `Q_φ` is queried per-contract (a Bernoulli relevance score for each market); scores are normalized into a categorical over contracts. Empirically attribution is sparse — most causal mass lives on a single contract per event.
- **Precision-coverage tradeoff in the posterior attributor.** Bigger posterior LLMs (397B) give sharper attributions but cover fewer transitions (~11.9%); smaller LLMs (32B) cover more (~47.8%) but flatter. Authors pick 397B for the headline; the tradeoff is acknowledged.
- **SWM-Bench.** 12,789 transition triples scraped from Polymarket (10,188) + Kalshi (2,601), Dec 2022–Jan 2026. Each triple is (windowed prior prices, news article, windowed next prices). Z-score sub-sampling removes flat-day bias (transitions where nothing moves are downweighted) so the eval isn't dominated by trivial no-event days.
- **Assumptions made explicit.** (A1) first-order event approximation — only the most recent event matters; (A2) conditional exogeneity of `e_t` given `s_t` — news doesn't react to today's prices in a way that would confound attribution. Both are acknowledged as imperfect on reflexive markets.

## Results / claims

- **Headline: 8B SWM is competitive with or beats frontier closed models on directional accuracy (DA).** On Kalshi DA the 8B SWM exceeds GPT-5.5 by ~4 pp; on Polymarket the gap is smaller but the 8B model is in the same range as the frontier baselines while being orders of magnitude smaller.
- **Oracle ablation: the bottleneck is attribution, not transition.** When the trained `P_θ` is fed the *posterior* `z` from `Q_φ` (oracle attribution) instead of the *prior* `z` from `P_η`, performance jumps substantially. Reading: the small transition model is already strong; what holds it back is which contracts the prior attributor flags as event-relevant.

## Connections

- Topics: [[../topics/social-world-models]] (new stub, primary), [[../topics/evaluation]], [[../topics/benchmarks]]
- Entities: _none promoted yet_ — SWM-Bench, Polymarket, Kalshi flagged as candidates.
- Related sources: _none — first source on this topic in the wiki._

## Open questions / criticisms

- **Spurious attribution (authors' own example).** A news article about Ethereum is attributed to an unrelated Bitcoin contract because of stale residual correlation between BTC and ETH prices. The posterior LLM is being asked to do causal attribution from text + prices alone, which is brittle whenever the price panel has cross-market correlations.
- **Simulation failures on macro events (authors' own example).** A "weak yen" news article is correctly *attributed* to the relevant FX/macro contract, but the transition model under-reacts — the magnitude of the price move is wrong. So the attribution/transition split helps localize *where* the model is failing but doesn't fix transition-side failures.
- **Conditional exogeneity (A2) on reflexive markets.** On Polymarket especially, news articles are often *written about* the prediction market itself (e.g. journalism citing Polymarket odds). This violates A2 in a structured way that the model can't detect.
- **Discovery-set / eval-set overlap concerns.** SWM-Bench is scraped from two specific venues over a fixed time window; no held-out venue or held-out time-window split is reported in the body of the paper. The strong 8B-vs-GPT-5.5 numbers are on the same distribution the model was trained on.
- **Loose ELBO.** The variational `Q_φ` is frozen, so the bound is not tight; there's no joint training. Whether tightening (even partial fine-tuning of the posterior) would help is left open.
- **First-order event approximation (A1).** Real transitions often depend on the *accumulation* of recent events rather than the single most-recent one. Acknowledged but not measured.

## Log

- 2026-06-18: ingested at light depth (TL;DR + key ideas + 1 result + criticisms, no My take per user direction); created topic stub [[../topics/social-world-models]]; updated [[../topics/evaluation]] and [[../topics/benchmarks]]; deferred entity promotion for SWM-Bench, Polymarket, Kalshi.
