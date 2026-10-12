---
id: SPEC-ARCH-03
title: Pipecat AI Duplex WebRTC Real-Time Voice Pipeline with Kokoro Nicole
type: architecture
status: PROPOSED
created: 2026-10-11
updated: 2026-10-11
author: spec-writer
ai_mode: true
source: Audio Subsystem Specification
---

# Architecture Specification: Pipecat Voice Pipeline & Kokoro af_nicole

> **AI Implementation Mode**: Architectural blueprint for sub-400ms duplex voice streaming with instant barge-in interruption.

---

## 1. Pipeline Pipeline Topology

```
[WebRTC Input] 
      │
      ▼
[Silero VAD] ──────(User speaks during bot audio)──────► [CANCEL OUTPUT STREAM]
      │                                                  (< 50ms Barge-in Cutoff)
      ▼
[Whisper STT] (Streaming token transcription in Spanish)
      │
      ▼
[Ollama LLM] (Streaming tokens from llama3.1:8b)
      │
      ▼
[Kokoro-82M TTS] (CPU ONNX Runtime, voice: af_nicole, <90ms first packet)
      │
      ▼
[WebRTC Output] (24kHz PCM Audio Stream)
```

---

## 2. Low-Latency Performance Budget

| Pipeline Stage | Implementation | Execution Hardware | Latency |
| :--- | :--- | :---: | :---: |
| **Voice Activity Detection (VAD)** | Silero VAD v5 | CPU (<1 thread) | < 5 ms |
| **Speech-to-Text (STT)** | Whisper Streaming | CPU / INT8 | ~120 ms |
| **First Token (TTFT)** | Ollama `llama3.1:8b` | GPU RTX 3080 | ~90 ms |
| **Audio Synthesis (TTS)** | Kokoro ONNX (`af_nicole`) | CPU (ONNX Runtime) | ~80 ms |
| **Transport Buffering** | WebRTC jitter buffer | Network / Localhost | ~25 ms |
| **TOTAL END-TO-END ROUNDTRIP** | — | — | **~320 ms** |

---

## 3. Kokoro Service Contract

```python
from pathlib import Path
from typing import AsyncGenerator

class KokoroTTSService:
    """CPU-bound neural voice synthesis running independently of GPU VRAM."""

    def __init__(
        self,
        model_path: Path = Path("/mnt/storage/ai-models/kokoro/kokoro-v1.0.onnx"),
        voices_path: Path = Path("/mnt/storage/ai-models/kokoro/voices-v1.0.bin"),
        voice: str = "af_nicole"
    ):
        self.model_path = model_path
        self.voices_path = voices_path
        self.voice = voice

    async def stream_audio_chunks(self, text_stream: AsyncGenerator[str, None]) -> AsyncGenerator[bytes, None]:
        """Yields 24kHz raw PCM audio chunks with sub-90ms initial packet latency."""
        ...
```
