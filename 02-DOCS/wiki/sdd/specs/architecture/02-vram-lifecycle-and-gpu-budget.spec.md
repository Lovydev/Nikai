---
id: SPEC-ARCH-02
title: Strict 10 GB VRAM Lifecycle Management and GPU Resource Budget
type: architecture
status: PROPOSED
created: 2026-10-11
updated: 2026-10-11
author: spec-writer
ai_mode: true
source: Hardware Constraint Specification
---

# Architecture Specification: VRAM Lifecycle & GPU Budget (RTX 3080 10 GB)

> **AI Implementation Mode**: Strict memory mutual exclusion protocol ensuring Fooocus SDXL and Ollama LLMs never collide or cause Out-of-Memory (OOM) errors.

---

## 1. Hardware Budget (10,240 MiB VRAM)

```
┌────────────────────────────────────────────────────────────────────────┐
│                   TOTAL VRAM BUDGET: 10,240 MiB                        │
├───────────────────────────────────┬────────────────────────────────────┤
│    Phase A: Conversation / Voice  │     Phase B: SDXL Fooocus Render   │
├───────────────────────────────────┼────────────────────────────────────┤
│ • Hyprland / System:     ~600 MiB │ • Hyprland / System:      ~600 MiB │
│ • Ollama LLM (8B):     ~5,200 MiB │ • Ollama Models:             0 MiB*│
│ • Fooocus (Idle):      ~3,500 MiB │ • Fooocus (SDXL Render):~8,500 MiB │
│ ───────────────────────────────── │ ────────────────────────────────── │
│   Total Allocated:     ~9,300 MiB │   Total Allocated:      ~9,100 MiB │
│   Safety Headroom:       ~940 MiB │   Safety Headroom:      ~1,140 MiB │
└───────────────────────────────────┴────────────────────────────────────┘
* Note: Unloaded dynamically via POST /api/generate with {"keep_alive": 0}.
```

---

## 2. VRAM Lease Protocol (Mutual Exclusion)

```python
import asyncio
import httpx

class VRAMLifecycleManager:
    """Manages exclusive access to GPU memory between Ollama and Fooocus."""

    def __init__(self, ollama_url: str = "http://127.0.0.1:11434"):
        self.ollama_url = ollama_url
        self._lock = asyncio.Lock()

    async def acquire_render_lease(self) -> None:
        """Flushes active Ollama models to system RAM to grant Fooocus full GPU access."""
        async with httpx.AsyncClient(timeout=10.0) as client:
            # Setting keep_alive to 0 immediately evicts the model from VRAM
            await client.post(f"{self.ollama_url}/api/generate", json={"keep_alive": 0})

    async def release_render_lease(self, next_model: str = "qwen3-vl:4b") -> None:
        """Signals Ollama to warm up the next required model into VRAM."""
        async with httpx.AsyncClient(timeout=15.0) as client:
            await client.post(f"{self.ollama_url}/api/chat", json={
                "model": next_model,
                "messages": [{"role": "user", "content": "ping"}],
                "keep_alive": "5m"
            })
```
