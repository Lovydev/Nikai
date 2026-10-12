---
type: spec
title: Spec — Real-Time Duplex Voice & Vision Critic Pipeline
description: WHAT and WHY for Pipecat WebRTC voice loop with Kokoro-82M ONNX and Qwen-VL visual critique.
tags: [sdd, spec, voice, vision, pipecat]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 04-voice-and-vision-pipeline
status: planned
---

# Spec — Real-Time Duplex Voice & Vision Critic Pipeline

> Slug: `04-voice-and-vision-pipeline` · Status: planned · Created: 2026-10-12
> Inherits: [constitution](../constitution.md)

## Problem & why
Voice interaction that relies on turn-taking walkie-talkie audio or cloud TTS APIs introduces jarring latency (>1.5s), breaks conversational immersion, and violates sovereignty by shipping raw microphone streams to third parties. Additionally, creative copiloting requires "eyes": the agent must visually inspect generated artwork, cameras, or screen viewports using a vision language model (Qwen2.5-VL) to give aesthetic critiques and detect anatomical errors before the human spots them.

## Cost of not building it
Nikai would be reduced to a text chat interface or high-latency clunky voice app unable to interrupt or be interrupted naturally, and unable to evaluate artwork autonomously.

## The cheapest alternative
Using cloud APIs (OpenAI Realtime API or ElevenLabs). This violates our non-negotiable Principle 1 (Sovereign Local Execution) and incurs continuous pay-per-minute subscription fees.

## Goals
- Full-duplex real-time voice streaming with sub-400ms end-to-end latency using **Pipecat AI** over WebRTC.
- Speech-to-Text via local Whisper (ONNX / Faster-Whisper) with Silero VAD for natural interruption.
- Text-to-Speech via local **Kokoro-82M ONNX** using the `af_nicole` voice profile running entirely on CPU/GPU.
- Visual inspection engine leveraging **Qwen2.5-VL** (via Ollama or vLLM) capable of analyzing composition, lighting, style consistency, and anatomy on rendered images.

## Non-goals / out of scope
- Training a custom voice model from scratch (we use Kokoro v1.0 ONNX).
- Real-time 60fps video frame-by-frame analysis (we sample discrete snapshot frames).

## Users & context
- **Human Creator:** Converses hands-free with Nikai while sketching or working in Omarchy/Hyprland.
- **Audio Stack:** PipeWire on Arch Linux, 16kHz audio input/output, WebRTC transport.

## Behaviour
- **Main path:**
  1. User speaks. Silero VAD detects speech onset.
  2. Faster-Whisper streams transcription tokens to Conductor.
  3. Llama 3.1 streams reply tokens directly to Kokoro-82M TTS.
  4. Audio packets stream back to user's headphones in < 350ms.
  5. If user speaks mid-sentence, Silero VAD triggers barge-in / cancellation of current TTS playback.
- **Vision critic path:**
  1. Image is rendered to disk.
  2. Vision engine feeds image + critic prompt to Qwen-VL.
  3. Critic returns structured JSON evaluation: score (1-10), composition assessment, anatomy issues, suggested prompt tweaks.

## Acceptance criteria
- **Given** voice audio stream, **When** user pauses speaking, **Then** Kokoro TTS playback begins in < 400ms.
- **Given** active assistant TTS playback, **When** user interrupts, **Then** playback ceases in < 150ms.
- **Given** a rendered PNG file, **When** `evaluate_artwork(image_path)` runs, **Then** it returns structured aesthetic critique JSON within 4 seconds.

## Points to clarify
- **suposición tomada** — El perfil de voz `af_nicole` proporciona el timbre cálido e íntimo deseado para Nikai. *Base:* Pruebas preliminares de calidad fonética con Kokoro ONNX.
- **suposición tomada** — Pipecat WebRTC se ejecuta sobre un servidor local `localhost:8765` para conectar con el frontend visual.
