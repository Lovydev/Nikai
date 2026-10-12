# 0001. 100% Local Sovereign Stack (No Cloud Dependencies)

- Status: accepted
- Date: 2026-10-12
- Deciders: User / Antigravity

## Context and Problem Statement
Nikai is envisioned as a long-term personal creative partner, home automation controller, and evolving AI lab. Relying on commercial cloud APIs (OpenAI, Anthropic, ElevenLabs, Midjourney) creates recurring monthly subscription costs, privacy exposure of personal audio/notes, risk of vendor API deprecation, and internet dependency.

## Considered Options
- Option 1: Cloud-First API Stack (OpenAI Realtime API, ElevenLabs, Replicate)
- Option 2: Hybrid Stack (Local LLMs with Cloud Image Generation)
- Option 3: 100% Local Sovereign Stack (Ollama, Kokoro ONNX, Fooocus SDXL, local Obsidian)

## Decision Outcome
Chosen: **Option 3 (100% Local Sovereign Stack)**, because user privacy, zero subscription fees, offline reliability, and absolute control over local weights are non-negotiable core principles (Constitution Principle 1).

## Consequences
- Positive: Zero external API costs; full privacy; complete ownership of weights and datasets; resilience against internet outages.
- Negative: Compute is bounded by local hardware (10 GB VRAM on RTX 3080 and 16 GB Unified Memory on M2 Pro).
- Follow-up: Implement strict memory arbitration (ADR-0004) to manage local VRAM limits.
