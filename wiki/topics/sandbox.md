---
type: topic
slug: sandbox
created: 2026-06-17
updated: 2026-06-17
source_count: 2
---

# Sandboxes / Execution Environments

Isolated environments where agents can run code, edit files, browse, and execute commands safely. Includes container-based sandboxes (Docker, Firecracker, microVMs), browser sandboxes (Playwright, browser-use), code interpreters, and reproducible task environments for benchmarks.

## Current understanding

Early in the wiki's life — only one source so far. The first data point ([[../sources/2026-brown-skypilot-sandboxes]]) frames the design space as:

- **Hosted vendors** (Modal, E2B, etc.) — pay per sandbox-hour, code leaves your cloud, geo latency penalties outside vendor regions.
- **BYOC (bring-your-own-cloud)** — run sandboxes on your own Kubernetes; cheaper at scale, private, but you operate the infra.

Two design primitives recurring already:
- **Per-pod isolation** — container-per-sandbox as the unit of safety.
- **Warm pools** — pre-provisioned pods to hide cold-start latency.

> [!info] All claims so far come from a vendor blog post; needs corroboration from non-vendor sources.

## Key concepts

_None promoted to their own pages yet._

## Key entities

_None yet (SkyPilot, Modal mentioned in [[../sources/2026-brown-skypilot-sandboxes]] but not yet promoted to entity pages)._

## Sources

- [[../sources/2026-brown-skypilot-sandboxes]] — Brown (2026). SkyPilot's BYOC-on-K8s sandbox SDK; benchmarks vs Modal; demoed as RL reward server. _Vendor blog — treat numbers cautiously._
- [[../sources/2026-koh-multi-agent-computer-use]] — Koh et al. (2026). _Tangential:_ uses per-subagent VMs as the isolation primitive for parallel computer-use agents; ablation shows removing isolation breaks the system on tasks with state collisions. Concrete data point that VM-per-agent isolation is load-bearing for multi-agent workloads.

## Open questions

- Trade-offs between startup latency, isolation, and state persistence.
- How to make sandboxes deterministic enough for benchmarks?
- How do production deployments scale sandboxes per session?
- How do hosted vs BYOC sandbox costs really compare under realistic burst patterns (not steady-state 50k)?
- What's the right isolation level — container, gVisor, microVM (Firecracker), full VM — for different agent workloads?

## Related topics

- [[agent-harness]]
- [[benchmarks]]
- [[agentic-rl]] — sandboxes show up as reward servers

## Log

- 2026-06-17: created (stub)
- 2026-06-17: added [[../sources/2026-brown-skypilot-sandboxes]]
- 2026-06-17: added [[../sources/2026-koh-multi-agent-computer-use]] (tangential — VM-per-subagent isolation pattern)
