---
type: spec
title: Spec — Strict 10 GB VRAM Lifecycle & GPU Resource Arbiter
description: WHAT and WHY for GPU memory arbitration between Ollama LLMs and Fooocus SDXL on the RTX 3080.
tags: [sdd, spec, vram, gpu]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 03-vram-gpu-arbiter
status: planned
---

# Spec — Strict 10 GB VRAM Lifecycle & GPU Resource Arbiter

> Slug: `03-vram-gpu-arbiter` · Status: planned · Created: 2026-10-12
> Inherits: [constitution](../constitution.md)

## Problem & why
The NVIDIA RTX 3080 has a hard physical ceiling of 10,240 MiB VRAM. Running an active Ollama 8B LLM context (~5,200 MiB) concurrently with a Fooocus SDXL image rendering pass (~8,500 MiB) plus desktop Hyprland compositor overhead (~600 MiB) exceeds 14,000 MiB, immediately triggering CUDA Out-Of-Memory (OOM) crashes and killing the python processes. We must guarantee deterministic mutual exclusion between conversational LLMs and image generation.

## Cost of not building it
Frequent runtime crashes mid-generation, corrupted GPU contexts, and frozen desktop sessions that require manual process kills.

## The cheapest alternative
Running Fooocus on CPU or using low-vram switches that degrade SDXL generation speed to 5+ minutes per image. This is unacceptable for an interactive creative copilot.

## Goals
- Guarantee that Fooocus SDXL and Ollama LLM never occupy VRAM simultaneously during image generation.
- Implement an automated `VRAMArbiter` lease manager with async locks.
- Automatically evict Ollama models to system RAM via `POST /api/generate` with `{"keep_alive": 0}` before triggering Fooocus renders.
- Warm up the subsequent model (e.g. `qwen3-vl:4b` or `llama3.1:8b`) after render completion.
- Maintain at least 900 MiB of safety headroom at all times.

## Non-goals / out of scope
- Dynamic multi-GPU clustering (Nikai currently runs on a single RTX 3080 on Node A).
- Modifying Fooocus internal PyTorch code (we interface via its local REST/WebSocket API).

## Users & context
- **Creative Director & Vision Agents:** Need fast image synthesis without crashing the conversational voice pipeline.

## Behaviour
- **Main path:**
  1. Creative Director decides to render an image.
  2. Acquires lease from `VRAMArbiter`.
  3. Arbiter sends `keep_alive: 0` to Ollama.
  4. Arbiter queries `nvidia-smi` / PyTorch NVML to assert free VRAM >= 8,000 MiB.
  5. Creative Director executes Fooocus SDXL render via API.
  6. Render completes and image is saved.
  7. Creative Director releases lease.
  8. Arbiter warms up the vision critic or conversational model.
- **Error paths:**
  - If free VRAM does not reach threshold within 5 seconds, arbiter retries eviction or aborts render gracefully before PyTorch OOMs.

## Acceptance criteria
- **Given** Ollama holding 5GB in VRAM, **When** `acquire_render_lease()` is called, **Then** Ollama VRAM drops to 0 MiB within 3 seconds.
- **Given** an active render lease, **When** a concurrent task requests an LLM generation, **Then** it is queued asynchronously until the render finishes.
- **Given** render completion, **When** `release_render_lease()` executes, **Then** the designated model is warmed back into VRAM.

## Points to clarify
- **suposición tomada** — La API de Ollama responde de manera determinista al parámetro `keep_alive: 0` descargando los pesos a la RAM del sistema. *Base:* Comportamiento nativo documentado del servidor Ollama.
