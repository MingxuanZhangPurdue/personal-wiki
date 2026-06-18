---
type: topic
slug: agent-harness
created: 2026-06-17
updated: 2026-06-17
source_count: 2
---

# Agent Harnesses

The scaffolding around an LLM that turns it into an agent: control loop, tool interface, planning, error recovery, sub-agent spawning, observation formatting. Examples: AutoGPT, smolagents, OpenHands, Claude Code, Cursor agent, Devin, SWE-agent.

## Current understanding

Both data points so far are *multi-agent* harnesses. The deeper analysis lives in [[multi-agent-coordination]], but the harness-side observations are:

- **Manager / worker split is no longer obviously default.** [[../sources/2026-koh-multi-agent-computer-use|MACU]] keeps a manager and makes the plan mutable; [[../sources/2026-mao-decentralized-mas-shared-context|DeLM]] removes the manager entirely in favor of a shared verified context + task queue. Both beat their respective "centralized parallel" baselines.
- **The plan structure is a harness design choice.** MACU uses a mutable DAG with a bounded replan budget; DeLM uses a task queue with dependency tags + a shared blackboard. Trees, partial-order plans, hierarchical task networks, recursive structures (RLM-style) are all unmeasured.
- **Different ablation conclusions, both load-bearing.** MACU: replanning matters more than parallel breadth. DeLM: admission-time verification of shared state matters more than the unfolding hierarchy. Two different "what's actually doing the work" answers from two different topologies.
- **Isolation primitives.** MACU: per-subagent VM (necessary for parallel computer-use). DeLM: per-agent context window with lock-free snapshot reads + atomic admission (sufficient for text-only). The right isolation level appears to track the domain.
- **Verification of shared state is an under-used primitive.** DeLM's biggest single ablation hit is removing the LLM verifier gate; MACU doesn't have one. Candidate primitive for future MAS harnesses.

## Key concepts

_None yet._

## Sources

- [[../sources/2026-koh-multi-agent-computer-use]] — Koh et al. (2026). MACU: a manager + mutable-DAG + parallel-VM-subagent harness for computer use. Ablations show replan budget drives most of the gain.
- [[../sources/2026-mao-decentralized-mas-shared-context]] — Mao & Mirhoseini (2026). DeLM: a manager-less blackboard harness with verified shared context + task queue. Ablations show admission-time verification drives most of the gain. Code released.

## Open questions

- How much does harness design matter vs. underlying model capability?
- What harness primitives generalize across domains?
- Where's the line between "harness" and "agent framework"?

## Related topics

- [[sandbox]]
- [[evaluation]]

## Log

- 2026-06-17: created (stub)
- 2026-06-17: added [[../sources/2026-koh-multi-agent-computer-use]]; first non-stub "Current understanding" entry on multi-agent harness design
- 2026-06-17: added [[../sources/2026-mao-decentralized-mas-shared-context]]; expanded Current understanding to cover both MACU (centralized mutable-DAG) and DeLM (decentralized blackboard) topologies; flagged admission-time verification as a candidate primitive
