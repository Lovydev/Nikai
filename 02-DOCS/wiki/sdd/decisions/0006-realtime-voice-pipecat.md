# 0006. Full-Duplex Real-Time Voice via Pipecat WebRTC + Kokoro ONNX

- Status: accepted
- Date: 2026-10-12
- Deciders: User / Antigravity

## Context and Problem Statement
Traditional conversational bots rely on turn-taking walkie-talkie HTTP requests (press to record, wait 2 seconds, download mp3, play). This breaks conversational flow, makes natural interruptions impossible, and feels robotic.

## Considered Options
- Option 1: Sequential HTTP Turn-Taking (STT ➔ LLM ➔ TTS as discrete blocking steps).
- Option 2: Commercial WebRTC Cloud (OpenAI Realtime API / ElevenLabs Conversational AI).
- Option 3: Local Full-Duplex WebRTC pipeline with Pipecat AI, Faster-Whisper, Silero VAD, and Kokoro-82M ONNX TTS.

## Decision Outcome
Chosen: **Option 3 (Pipecat AI WebRTC + Local Kokoro ONNX)**, because it achieves natural full-duplex conversational audio with barge-in interruption support, sub-400ms end-to-end latency, and 100% local sovereignty.

## Consequences
- Positive: Natural, fluid conversational copilot; instant interruption handling; high-quality acoustic tone (`af_nicole`); zero cloud API costs.
- Negative: Requires audio pipeline management (sample rates, VAD thresholds, WebRTC transports).
- Follow-up: Specified in `02-DOCS/wiki/sdd/specs/04-voice-and-vision-pipeline.md`.
