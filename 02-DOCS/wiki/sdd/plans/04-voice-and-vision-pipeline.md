---
type: plan
title: Plan — Real-Time Duplex Voice & Vision Critic Pipeline
description: Implementation plan for Pipecat WebRTC pipeline, Kokoro ONNX TTS, and Qwen-VL aesthetic critic.
tags: [sdd, plan, voice, vision, pipecat]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 04-voice-and-vision-pipeline
status: draft
---

# Plan — Real-Time Duplex Voice & Vision Critic Pipeline

> Spec: [../specs/04-voice-and-vision-pipeline.md](../specs/04-voice-and-vision-pipeline.md) · Constitution: [../constitution.md](../constitution.md) · Status: draft
> Last updated: 2026-10-12

## 0. Global Constraints

- **Audio Rate:** 16,000 Hz or 24,000 Hz PCM audio streams.
- **Voice Weights:** Local ONNX file at `/mnt/storage/ai-models/kokoro/kokoro-v1.0.onnx`.
- **Vision Model:** `qwen3-vl:4b` or `qwen2.5-vl:7b` via local Ollama endpoint.
- **Latency Budget:** Speech-to-first-audio latency < 400ms.

## 1. Context & constraints

- Acceptance criteria from spec: Sub-400ms voice loop, Silero VAD interruption, Qwen-VL structured aesthetic criticism.
- Constitution rules in play: Principle 1 (Sovereign Local Execution), Principle 2 (VRAM bounds).

## 2. Architecture

```text
[ Mic Input (WebRTC / PipeWire) ]
              │
              ▼
    [ Silero VAD Filter ]
              │
              ▼
   [ Faster-Whisper STT ]
              │
              ▼
   [ LLM Conductor Stream ]
              │
              ▼
     [ Kokoro-82M ONNX TTS ]
              │
              ▼
[ Speaker / WebRTC Audio Out ]
```

**Vision Critic Subsystem:**
```text
[ Rendered Image File ] ──► [ Image Base64 Encoder ] ──► [ Qwen-VL Prompt ] ──► [ Aesthetic Critique JSON ]
```

- **`VoicePipelineManager`** (internal) — Pipecat pipeline runner orchestrating VAD, STT, and TTS.
- **`KokoroTTSClient`** (internal) — ONNX Runtime runner for Kokoro audio generation.
- **`VisualCriticService`** (internal) — Evaluates images against aesthetic rubric.

## 3. Interfaces & contracts

```text
VoicePipelineManager.start_session(transport: WebRTCTransport) -> None
  - invariant: Full-duplex with interruptibility.

VisualCriticService.critique_image(image_path: Path, concept_prompt: str) -> CritiqueReport
  - precondition: Image exists on disk.
  - returns: CritiqueReport(score: float, issues: List[str], advice: str, approved: bool)
```

## 4. Data model & flow

**CritiqueReport Model:**
- `score`: float (0.0 - 10.0)
- `composition`: str
- `lighting_and_color`: str
- `anatomical_defects`: List[str]
- `prompt_recommendations`: List[str]
- `verdict`: str (`ACCEPT` | `REVISE` | `REJECT`)

## 5. Tasks Breakdown & Done-Checks

- [ ] **Task 1: Kokoro ONNX TTS wrapper**
  - Implement `src/voice/kokoro_engine.py` loading ONNX model and `voices-v1.0.bin`.
  - *Done-check:* `python3 -c "from src.voice.kokoro_engine import synthesize; synthesize('Hola Nikai', 'test.wav')"` produces valid 24kHz audio.

- [ ] **Task 2: Pipecat WebRTC pipeline assembly**
  - Assemble Pipecat pipeline in `src/voice/pipeline.py` with Silero VAD and Whisper.
  - *Done-check:* Audio loopback benchmark reports latency < 400ms.

- [ ] **Task 3: Visual Critic service**
  - Implement `src/vision/critic.py` querying Qwen-VL with multimodal prompts.
  - *Done-check:* `pytest tests/test_visual_critic.py` parses mock and live critiques correctly.
