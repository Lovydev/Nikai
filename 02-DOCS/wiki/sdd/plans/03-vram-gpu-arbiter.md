---
type: plan
title: Plan — Strict 10 GB VRAM Lifecycle & GPU Resource Arbiter
description: Implementation plan for the async VRAM Arbiter, NVML monitoring, and mutual exclusion lock.
tags: [sdd, plan, vram, gpu]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 03-vram-gpu-arbiter
status: draft
---

# Plan — Strict 10 GB VRAM Lifecycle & GPU Resource Arbiter

> Spec: [../specs/03-vram-gpu-arbiter.md](../specs/03-vram-gpu-arbiter.md) · Constitution: [../constitution.md](../constitution.md) · Status: draft
> Last updated: 2026-10-12

## 0. Global Constraints

- **Hardware Hard Ceiling:** 10,240 MiB VRAM (RTX 3080).
- **Minimum Safety Headroom:** 900 MiB free VRAM buffer.
- **Eviction Protocol:** HTTP call to Ollama endpoint with `{"keep_alive": 0}`.
- **Concurrency:** Mutual exclusion lock (`asyncio.Lock`) protecting GPU execution.

## 1. Context & constraints

- Acceptance criteria from spec: Safe Ollama unload, NVML memory verification, zero concurrent OOM crashes.
- Constitution rules in play: Principle 2 (Hardware Budget & Memory Safety).

## 2. Architecture

```text
[ Creative Director Request ]
             │
             ▼
[ VRAMArbiter.acquire_lease() ]
             │
             ├──► 1. Acquire asyncio.Lock()
             ├──► 2. POST /api/generate {"keep_alive": 0} (Ollama Unload)
             ├──► 3. NVML Probe (Assert Free VRAM >= 8,000 MiB)
             │
             ▼
[ Fooocus SDXL Render Execution ]
             │
             ▼
[ VRAMArbiter.release_lease() ]
             │
             ├──► 1. POST /api/chat {"model": next_model, "keep_alive": "5m"} (Warmup)
             └──► 2. Release asyncio.Lock()
```

- **`VRAMArbiter`** (internal) — Manages leases and locks.
- **`GPUInspector`** (internal) — Queries NVIDIA NVML / `pynvml` for exact millisecond VRAM measurements.

## 3. Interfaces & contracts

```text
VRAMArbiter.acquire_render_lease(timeout_sec: float = 10.0) -> LeaseToken
  - precondition: NVML initialized.
  - postcondition: Ollama evicted, Free VRAM >= 8,000 MiB. Raises VRAMTimeoutError on failure.

VRAMArbiter.release_render_lease(token: LeaseToken, warmup_model: str = "qwen3-vl:4b") -> None
  - precondition: Active lease token held.
  - postcondition: Fooocus released, warmup triggered, lock freed.
```

## 4. Data model & flow

**VRAMSnapshot Model:**
- `total_mb`: int
- `used_mb`: int
- `free_mb`: int
- `timestamp`: float

**LeaseToken Model:**
- `lease_id`: UUID str
- `acquired_at`: float
- `holder`: str

## 5. Tasks Breakdown & Done-Checks

- [ ] **Task 1: NVML GPU telemetry module**
  - Implement `src/infrastructure/gpu.py` using `pynvml` or `torch.cuda.mem_get_info()`.
  - *Done-check:* `python3 -c "from src.infrastructure.gpu import get_vram_snapshot; print(get_vram_snapshot())"` outputs valid memory readings.

- [ ] **Task 2: VRAM Arbiter core & Ollama eviction**
  - Implement `src/infrastructure/vram_arbiter.py` with async context manager `async with arbiter.render_lease():`.
  - *Done-check:* Unit test simulating eviction and warm-up cycles exits 0.

- [ ] **Task 3: Integration test with live Ollama**
  - Test real Ollama model unloading and assert memory reclaimed in under 3 seconds.
  - *Done-check:* `pytest tests/test_vram_arbiter.py` passes 100%.
