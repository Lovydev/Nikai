---
id: SPEC-ARCH-05
title: Fooocus SDXL Engine & Kohya LoRA Async Tool Dispatcher
type: architecture
status: PROPOSED
created: 2026-10-11
updated: 2026-10-11
author: spec-writer
ai_mode: true
source: Diffusion Subsystem Specification
---

# Architecture Specification: Fooocus SDXL & Kohya LoRA Dispatcher

> **AI Implementation Mode**: Architectural blueprint for Fooocus local API integration, dynamic LoRA matching, and render artifact handling.

---

## 1. Engine Boundaries & Storage

- **Base Checkpoint:** `juggernautXL_v8Rundiffusion.safetensors` in `/mnt/storage/ai-images/Fooocus/models/checkpoints/`.
- **LoRA Directory:** `/mnt/storage/ai-images/Fooocus/models/loras/`.
- **Kohya Training Output:** `/mnt/storage/ai-images/lora-workspace/output/`.
- **Render Output:** `/mnt/storage/ai-images/renders/{session_id}/`.
- **Local API:** `http://127.0.0.1:7865`.

---

## 2. Dispatcher Contract & Tool Schema

```python
from pathlib import Path
from typing import Dict, Any, Optional
from pydantic import BaseModel

class ImageGenerationRequest(BaseModel):
    prompt: str
    negative_prompt: str = "(worst quality, low quality:1.4), deformed, bad anatomy"
    lora_name: Optional[str] = None
    lora_weight: float = 0.85
    aspect_ratio: str = "896*1152"  # 3:4 portrait
    style_preset: str = "Fooocus V2"
    seed: Optional[int] = None

class ImageGenerationResult(BaseModel):
    file_path: Path
    generation_time_seconds: float
    seed_used: int
    lora_applied: Optional[str]

class FooocusToolDispatcher:
    def __init__(self, base_url: str = "http://127.0.0.1:7865", loras_dir: Path = Path("/mnt/storage/ai-images/Fooocus/models/loras")):
        self.base_url = base_url
        self.loras_dir = loras_dir

    def scan_available_loras(self) -> Dict[str, Path]:
        """Scans filesystem for .safetensors LoRA files."""
        ...

    async def execute_generation(self, request: ImageGenerationRequest) -> ImageGenerationResult:
        """Dispatches job to Fooocus API, awaits completion, and returns output path."""
        ...
```
