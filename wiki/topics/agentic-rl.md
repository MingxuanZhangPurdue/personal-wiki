---
type: topic
slug: agentic-rl
created: 2026-06-17
updated: 2026-06-17
source_count: 1
---

# Agentic RL / Reinforcement Learning for Agents

Applying RL — both classical and LLM-era variants (RLHF, RLAIF, RLVR, on-policy fine-tuning of agent trajectories) — to train LLM-based agents that act in environments, use tools, and pursue multi-step goals.

## Current understanding

_Empty. Will populate as sources are ingested._

## Key concepts

_None yet._

## Key entities

_None yet._

## Sources

- [[../sources/2026-brown-skypilot-sandboxes]] — Brown (2026). _Tangential:_ sketches a GRPO code-gen RL loop where a sandbox cluster serves as the reward server (binary exit-code reward). Useful as an architectural reference, not a research result.

## Open questions

- How do agentic RL methods compare to pure imitation / SFT on long-horizon tasks?
- What reward signals work for open-ended agent behavior?
- How to handle credit assignment across long tool-use trajectories?

## Related topics

- [[self-evolving-agents]]
- [[evaluation]]
- [[benchmarks]]

## Log

- 2026-06-17: created (stub)
- 2026-06-17: added [[../sources/2026-brown-skypilot-sandboxes]] (tangential — sandbox-as-reward-server pattern)
