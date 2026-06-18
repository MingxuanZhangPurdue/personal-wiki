# SkyPilot Sandboxes: Run Agent Code on Your Own Kubernetes, at Scale

> **Note on this clip:** This is an AI-extracted structured capture of the article via WebFetch, not a verbatim HTML→markdown clip. Replace with an Obsidian Web Clipper version if a verbatim copy is needed. Source URL and Wayback archive are in `source.md`.

**Author:** Lloyd Brown
**Date:** June 8, 2026
**URL:** https://blog.skypilot.co/sandboxes/

---

## Introduction

The article addresses a critical challenge faced by teams building AI agents and coding assistants: executing untrusted, model-generated code safely at scale. Rather than relying on expensive hosted sandbox vendors, SkyPilot Sandboxes enables code execution on existing Kubernetes infrastructure.

**Key Promise:** "A single cluster sustains 50,000+ sandboxes, with multi-cluster support to go further. Individual launches in less than a second."

## What is a Sandbox, and Why Do You Need One?

LLMs generate code that requires isolation before execution. Untrusted code can loop infinitely, exhaust memory, write files, or spawn malicious processes. Teams currently use hosted vendors, but this approach carries three major tradeoffs:

- **Cost:** Paying vendor margins on top of owned compute resources
- **Privacy:** Code and data leave the organization to third parties
- **Latency:** Non-US users face geographic distance penalties

## SkyPilot Sandboxes Run on Your Own Infrastructure

Five core features:

1. **Per-pod isolation:** Each sandbox runs in its own container with dedicated resources; misbehaving code is contained
2. **Massive parallelism:** Launch multiple sandboxes in single calls with concurrent command distribution
3. **Sub-second launches:** Warm pools maintain pre-provisioned pods, reducing launch time by 50%+
4. **Data privacy:** "Code and data never leave your cloud"
5. **Familiar API:** `create()`, `exec()`, `terminate()` methods with async variants for fan-out

## Code Examples

**Basic usage:**
```python
import sky.sandbox

sb = sky.sandbox.create(image="python:3.12", cpus=1, memory_gb=2)
result = sb.exec("python", "-c", "print(2 + 2)")
print(result["stdout"])  # "4"
sb.terminate()
```

**Batch creation:**
```python
sandboxes = sky.sandbox.create(image="python:3.12", num_sandboxes=100)
for sb in sandboxes:
    sb.exec("pytest", "-q", "tests/")
```

**Async fan-out:**
```python
sandboxes = await sky.sandbox.create.aio(image="python:3.12", num_sandboxes=64)
results = await asyncio.gather(
    *(sb.exec.aio("python", "-c", code) for sb in sandboxes))
```

## Real-World Application: RL Training Example

The article demonstrates SkyPilot Sandboxes in a reinforcement learning pipeline that post-trains code-generation models. The system includes five distributed services:

1. **Data Server:** Serves programming problems with hidden test cases
2. **Rollout Server (SGLang):** Generates candidate solutions from the policy model
3. **Sandbox Reward Server:** Executes generated code in isolated sandboxes and scores results
4. **Replay Buffer:** Stores scored training examples
5. **PPO Trainer (GRPO):** Updates model weights based on rewards

The reward signal is binary: exit code 0 (all tests pass) = reward 1.0; any failure = reward 0.0. This design prevents bad rollouts from crashing the training loop.

**Reward server implementation:**
```python
async def score_batch(items):
    sandboxes = await sky.sandbox.create.aio(
        name="reward", num_sandboxes=len(items), pool=POOL_NAME)
    try:
        rewards = await asyncio.gather(
            *(score_one(sb, item) for sb, item in zip(sandboxes, items)))
    finally:
        await asyncio.gather(*(sb.terminate.aio() for sb in sandboxes),
                             return_exceptions=True)
    return list(rewards)
```

## Performance Benchmarks vs. Modal

**Scale:** A single cluster sustained ~50,000 healthy sandboxes across 220 nodes.

**Launch speed (create + first exec):**
- SkyPilot: p50 ~1.0s, p99 ~1.5s
- Modal: p50 ~1.2s, p99 ~2.0s

SkyPilot achieves ~20% faster first command execution with tighter tail latency.

**Geographic latency:** Modal incurs ~3.9x latency increase for APAC clients (~0.096s US vs ~0.37s APAC). SkyPilot avoids this penalty by running locally.

## Cost Analysis

**General-purpose nodes (2 vCPU, 4GB per sandbox):**
- Hosted: $19,030/hour for 50,000 sandboxes
- SkyPilot BYOC: $4,650/hour (~4x cheaper)

**Burstable nodes (2 vCPU, 2GB per sandbox):**
- Hosted: $16,610/hour for 50,000 sandboxes
- SkyPilot BYOC: $1,680/hour (~9.9x cheaper)

## Key Comparison Table

| Metric | SkyPilot BYOC | Hosted Service |
|--------|---------------|----------------|
| Infrastructure | Your K8s cluster | Vendor regions |
| Create + first exec (p50) | ~1.0s | ~1.2s |
| Cost/hr (50k sandboxes) | $1,680–$4,650 | $16,610–$19,030 |
| Exec latency | Local | In-region low; 3.9x cross-region |
| Code & data | Never leave cloud | Sent to third party |
| Secrets management | Injected at runtime | Configured in vendor dashboard |

## Takeaways

SkyPilot Sandboxes provides isolated, concurrent, fast code execution on owned infrastructure. The async SDK enables simple fan-out patterns for RL scoring, parallel evaluation, and agent workflows — all while maintaining data privacy and reducing costs significantly.

## Next Steps

- **Get access:** Limited early access via signup form
- **Run the example:** Full five-service RL pipeline in the SkyPilot GitHub repository
- **Read docs:** SDK, warm pools, and secrets injection guidance available

---

## References & Links

- GitHub: https://github.com/skypilot-org/skypilot/
- Docs: https://docs.skypilot.co/en/latest/sandboxes.html
- Community: https://slack.skypilot.co/
- Twitter: https://twitter.com/skypilot_org
- Benchmark script: https://gist.githubusercontent.com/lloyd-brown/58bdefdea5ff15f1563efa81fbed272a/raw/benchmark.py

## Images (not downloaded)

- Banner: https://blog.skypilot.co/sandboxes/assets/banner.png
- Cost comparison chart
- RL architecture diagram (five-service pipeline)
