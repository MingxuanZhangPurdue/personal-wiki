---
type: topic
slug: social-world-models
created: 2026-06-18
updated: 2026-06-18
source_count: 1
---

# Social World Models

Models of the form `P(s_{t+1} | s_t, e_t)` over **social** state: prices on prediction markets, opinion-poll vectors, public-sentiment indices, or similar aggregates of human belief/action. Distinct from physics or video world models in that (i) the state is itself produced by other reasoning agents and (ii) the "event" driving transitions is typically natural-language news or discourse rather than a control action. Adjacent to forecasting and prediction-market literature but framed as a *transition model* rather than a calibrated single-question predictor.

## Current understanding

One source ([[../sources/2026-yu-social-world-models]]):

- **The unit is a transition, not a question.** Standard prediction-market work treats each contract as an independent calibrated forecast. SWM-style work treats the price *panel* as a state and a news article as the event that moves the panel. This is closer in spirit to world-model formulations in RL/video than to classical forecasting.
- **Attribution is a latent variable, not a model output.** The Yu et al. formulation introduces a latent `Z_t` (a normalized distribution over contracts) representing which markets a given event actually affects. Empirically this is sparse — most events move ~one contract.
- **Posterior-guided distillation is the training recipe.** A frozen large LLM provides hindsight pseudo-labels for `Z_t`; a smaller learned attributor + learned transition model are distilled against those pseudo-labels (loose ELBO with frozen variational). Open whether tightening the bound helps.
- **The binding constraint observed so far is attribution, not transition.** Oracle ablation in Yu et al. shows the small transition model is already strong; the bottleneck is the prior attributor's accuracy. Worth tracking whether this generalizes beyond their setup.
- **Reflexivity is the main domain-specific hazard.** On prediction markets, news is often *about* the market itself, breaking conditional exogeneity of events. Any social-world-model setup needs an explicit story for this.

## Key concepts

_None yet. Candidates: latent-event-attribution, posterior-guided-distillation, conditional-exogeneity (for social-state transitions), first-order-event approximation._

## Key entities

_None promoted yet. Candidates: SWM-Bench, Polymarket, Kalshi._

## Sources

### Foundational

- [[../sources/2026-yu-social-world-models]] — Yu, Zhao, Lin, You (2026). Introduces the SWM formulation, SWM-Bench, and posterior-guided distillation; shows 8B distilled SWM matches frontier closed models on directional accuracy and that attribution (not transition) is the binding constraint.

## Open questions

- Does the "attribution is the bottleneck" finding hold outside Polymarket/Kalshi (e.g. on polls, on opinion indices, on multi-event windows)?
- Can posterior-guided distillation be tightened — partial fine-tuning of the variational posterior, or iterative refinement — without losing tractability?
- How should reflexive social state (where events comment on the state itself) be handled? Is conditional exogeneity recoverable with explicit modeling, or does it require a different formulation?
- How do SWMs relate to standard prediction-market calibration / arbitrage literature? Is there a head-to-head against classical calibrated forecasters?
- What does a held-out *venue* or held-out *time-window* eval look like (not just held-out triples from the same distribution)?

## Related topics

- [[evaluation]] — SWM-Bench introduces a new (state, event, next-state) eval format and an oracle-attribution ablation that isolates attribution from transition.
- [[benchmarks]] — SWM-Bench is a new benchmark cluster (prediction-market-derived transitions) distinct from the agent-task benchmarks already tracked here.

## Log

- 2026-06-18: created from [[../sources/2026-yu-social-world-models]] ingest.
