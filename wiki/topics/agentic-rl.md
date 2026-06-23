---
type: topic
slug: agentic-rl
created: 2026-06-17
updated: 2026-06-18
source_count: 2
---

# Agentic RL / Reinforcement Learning for Agents

Applying RL — both classical and LLM-era variants (RLHF, RLAIF, RLVR, on-policy fine-tuning of agent trajectories) — to train LLM-based agents that act in environments, use tools, and pursue multi-step goals.

## Current understanding

Two tangential data points so far. Both sit at the edge of "agentic RL" rather than inside it.

- [[../sources/2026-brown-skypilot-sandboxes]] sketches the **infrastructure side**: sandbox-cluster-as-reward-server for GRPO code-gen loops (binary exit-code reward). No learning results.
- [[../sources/2026-lee-meta-harness]] is tangential in the opposite direction: it **frames harness search as policy improvement without weight updates**. The proposer rewrites code (the "policy") based on traces (the "trajectories") and scores (the "returns"). Authors explicitly contrast this with weight-space methods: "harness-level credit assignment is more inspectable than gradient credit assignment — brittle if-chains are visible on inspection, but a brittle attention head is not." Worth tracking whether "code-space policy improvement" gets re-invented under different names.

One observation worth flagging:

- **Harness-level credit assignment vs token-level credit assignment.** Meta-Harness implicitly bets that harness-level units (functions, prompt blocks, tool wrappers) are the right granularity for credit assignment, given LLM-era capabilities. Classical RL would push this much finer (per-token, per-action). Whether the right granularity for agent learning is closer to "code edits" or "token updates" is open.

## Key concepts

_None yet. Candidates: harness-level-credit-assignment, code-space-policy-improvement, sandbox-as-reward-server._

## Key entities

_None yet._

## Sources

- [[../sources/2026-brown-skypilot-sandboxes]] — Brown (2026). _Tangential:_ sketches a GRPO code-gen RL loop where a sandbox cluster serves as the reward server (binary exit-code reward). Useful as an architectural reference, not a research result.
- [[../sources/2026-lee-meta-harness]] — Lee et al. (2026). _Tangential:_ frames harness search as policy improvement without weight updates; argues "harness-level credit assignment" is more inspectable than gradient-based credit assignment. Not RL in the classical sense (no value function, no policy gradient) but structurally analogous.

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
- 2026-06-18: added [[../sources/2026-lee-meta-harness]] (tangential — harness-level credit assignment, code-space policy improvement framing)
