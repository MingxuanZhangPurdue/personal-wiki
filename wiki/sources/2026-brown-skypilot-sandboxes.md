---
title: "SkyPilot Sandboxes: Run Agent Code on Your Own Kubernetes, at Scale"
authors: ["Lloyd Brown"]
year: 2026
type: blog
raw: ../../raw/blogs/2026-brown-skypilot-sandboxes/clip.md
url: https://blog.skypilot.co/sandboxes/
added: 2026-06-17
status: read
tags: [sandbox, kubernetes, byoc, rl, code-execution, vendor-blog]
topics: [sandbox, agentic-rl, agent-harness]
---

# SkyPilot Sandboxes: Run Agent Code on Your Own Kubernetes, at Scale

> Source: [[../../raw/blogs/2026-brown-skypilot-sandboxes/clip.md|raw clip]] · [original](https://blog.skypilot.co/sandboxes/)
> Kept primarily as a **bookmark/reminder**: this is the route for running sandboxes on your own K8s cluster via SkyPilot.

## TL;DR

SkyPilot announces an SDK (`sky.sandbox.create/exec/terminate`, sync + async) for running per-pod-isolated sandboxes on your own Kubernetes cluster (BYOC). Pitched against hosted vendors (Modal, etc.) on cost (~4–10× cheaper), latency (sub-second launches via warm pools), and privacy (code stays in your cloud). Demonstrated at ~50k concurrent sandboxes on 220 nodes, and used as the reward server in a GRPO code-gen RL pipeline.

## Key claims

- **Scale:** single cluster sustained ~50k healthy sandboxes / 220 nodes.
- **Launch (create + first exec):** p50 ~1.0s / p99 ~1.5s (SkyPilot) vs p50 ~1.2s / p99 ~2.0s (Modal).
- **Cost (50k sandboxes/hr):** $1,680–$4,650 BYOC vs $16,610–$19,030 hosted.
- **Geo:** APAC latency to US-hosted vendors ~3.9× worse; BYOC sidesteps it.
- **API:** `create`, `exec`, `terminate`, plus `.aio` async variants and `num_sandboxes=N` batch creation.
- **Warm pools:** pre-provisioned pods cut launch time ~50%.
- **RL example:** five-service GRPO pipeline (data server → SGLang rollout → sandbox reward server → replay buffer → PPO trainer); binary reward = exit-code 0.

## Useful pointers

- Docs: https://docs.skypilot.co/en/latest/sandboxes.html
- GitHub: https://github.com/skypilot-org/skypilot
- Benchmark script: https://gist.githubusercontent.com/lloyd-brown/58bdefdea5ff15f1563efa81fbed272a/raw/benchmark.py

## Connections

- Topics: [[../topics/sandbox]], [[../topics/agentic-rl]], [[../topics/agent-harness]]

## My take

> [!warning] Vendor blog — read with skepticism.
> - All benchmarks are **self-reported by SkyPilot** in a post promoting their own product; the head-to-head against Modal is structured to favor SkyPilot.
> - "~20% faster" and "4–10× cheaper" claims need **independent reproduction** before being cited as facts. The cost comparison conflates "your owned K8s capacity at marginal cost" with "vendor list price including margin + SLA + on-demand burst" — apples-to-oranges in important ways.
> - The RL pipeline is a **demo**, not a published result; treat as architectural illustration, not evidence of training efficacy.
> - What's genuinely useful here is the **architectural pattern**: BYOC sandboxes + warm pools + simple async SDK as the substrate for RL reward servers and agent harnesses. The numbers are less interesting than the design.

## Log

- 2026-06-17: ingested (compact / bookmark-style)
