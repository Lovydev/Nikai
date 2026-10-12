---
id: SPEC-ARCH-04
title: Qwen-VL Multimodal Vision Engine: Camera Observer & Visual Critic
type: architecture
status: PROPOSED
created: 2026-10-11
updated: 2026-10-11
author: spec-writer
ai_mode: true
source: Vision Subsystem Specification
---

# Architecture Specification: Qwen-VL Camera Observer & Critic

> **AI Implementation Mode**: Architectural blueprint for real-time camera ingestion and post-render anatomical/likeness quality inspection.

---

## 1. Dual Operational Modes

1. **Mode A: Live Camera Observer ("Look and Tell")**
   - Ingests WebRTC camera frames from device/webcam.
   - Downsamples to 720p with JPEG compression (<15ms).
   - Rate-limited to 1 evaluation per second to preserve GPU bandwidth.
   - Qwen-VL returns natural Spanish descriptions streamed directly to Kokoro TTS.

2. **Mode B: Post-Render Visual Critic**
   - Automatically triggered when Fooocus saves an SDXL render to `/mnt/storage/ai-images/renders/`.
   - Compares face geometry with character LoRA expectations and art direction rules.
   - Outputs strongly typed JSON rating:
     `{"approved": bool, "likeness_score": float, "anatomy_issues": List[str], "lighting_feedback": str, "suggested_seed_adjustment": str}`.

---

## 2. Service Contract

```python
from typing import Dict, Any, List
from pydantic import BaseModel

class CritiqueReport(BaseModel):
    approved: bool
    likeness_score: float
    anatomy_issues: List[str]
    lighting_feedback: str
    suggested_seed_adjustment: str

class QwenVisionService:
    def __init__(self, ollama_url: str = "http://127.0.0.1:11434", model: str = "qwen3-vl:4b"):
        self.ollama_url = ollama_url
        self.model = model

    async def inspect_live_frame(self, frame_bytes: bytes, user_query: str) -> str:
        """Processes real-time camera frame and returns voice-ready explanation."""
        ...

    async def evaluate_render(self, image_path: str, lora_name: str, art_direction: str) -> CritiqueReport:
        """Inspects generated SDXL artwork and returns structured critique."""
        ...
```
