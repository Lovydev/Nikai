---
type: spec
title: Spec — Runtime Environment & Preflight Foundation
description: WHAT and WHY for Nikai runtime setup — local dependencies, hardware validation, preflight health checks.
tags: [sdd, spec, runtime]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 01-runtime-environment
status: planned
---

# Spec — Runtime Environment & Preflight Foundation

> Slug: `01-runtime-environment` · Status: planned · Created: 2026-10-12
> Inherits: [constitution](../constitution.md)

## Problem & why
Building a multi-agent system that orchestrates heavy local neural networks (Ollama LLMs, Kokoro ONNX voice synthesis, Fooocus SDXL, and PyTorch CUDA) without a deterministic, validated runtime environment leads to silent CUDA driver mismatches, broken audio sample rates, missing ONNX model weights, and un-debuggable runtime crashes. We need a guaranteed, single-command environment initialization that validates hardware and dependencies before any agent loop starts.

## Cost of not building it
Developers and autonomous coding agents will encounter recurring environment drift, missing native libraries (PortAudio, libsndfile), out-of-memory errors from undetected GPU limits, and invalid assumptions about local model paths, stalling agent development on basic plumbing.

## The cheapest alternative
Manually running `pip install` and checking `nvidia-smi` in chat. This fails because it provides zero automated guarantees for subsequent autonomous agent sessions and cannot verify model weight integrity on `/mnt/storage`.

## Goals
- Provide a clean Python 3.10 virtual environment foundation isolated from system packages.
- Establish centralized, type-safe environment configuration loaded via Pydantic Settings.
- Implement an automated preflight verification script (`scripts/verify_environment.py`) that performs a 100% local hardware, service, and storage health check.
- Provide a Pytest verification suite exiting with code `0`.

## Non-goals / out of scope
- Installing system-level pacman packages (handled by the host OS Arch Linux).
- Running the multi-agent state machine (handled by `02-orchestrator-core`).
- Downloading multi-gigabyte SDXL checkpoints automatically during preflight (it only checks existence and accessibility).

## Users & context
- **Human Developer & AI Agents:** Need a deterministic entrypoint to ensure the machine is healthy before issuing complex multi-agent tasks.
- **Hardware Profile:** Arch Linux, Intel i7-11700K (16T), 32 GB RAM, NVIDIA RTX 3080 (10 GB VRAM limit), secondary NVMe mounted at `/mnt/storage`.

## Behaviour
- **Main path:** User or agent runs `python scripts/verify_environment.py`. The script checks Python version, NVIDIA CUDA availability, Ollama endpoint, Kokoro ONNX weights on `/mnt/storage`, Fooocus endpoint, and storage write permissions. If all pass, it prints a clean ASCII summary and exits 0.
- **Edge cases:**
  - GPU present but PyTorch built without CUDA: script fails with an explicit guide to install CUDA-enabled wheels.
  - `/mnt/storage` unmounted or read-only: script flags permission/mount failure with exact path.
  - Ollama offline: script flags `http://127.0.0.1:11434` unreachable and lists required models.
- **Error paths:** Any check failure reports exact error, suggested remediation command, and exits non-zero (`exit 1`), blocking downstream execution.

## Acceptance criteria
- **Given** Python 3.10 and dependencies installed, **When** `pytest tests/test_environment.py` is executed, **Then** all tests pass with code 0.
- **Given** Ollama running on `127.0.0.1:11434`, **When** preflight check runs, **Then** it validates presence of required models (`llama3.1:8b`, `mistral-nemo:12b`, `qwen2.5-coder:7b`, `qwen3-vl:4b`).
- **Given** `/mnt/storage` mount point, **When** write check runs, **Then** a temporary test file is written, verified, and removed.

## Points to clarify
- **suposición tomada** — El entorno Python usará Python 3.10 gestionado por `mise` o `venv` local en `.venv/`. *Base:* Compatibilidad probada con PyTorch CUDA 12.4 y Pipecat. *Riesgo:* Ninguno; Python 3.10 es el estándar más estable para la pila actual de IA.
- **decisión diferida** — Despliegue de Ollama dentro de Docker vs nativo en systemd. De momento se valida sobre `http://127.0.0.1:11434` indistintamente de cómo esté ejecutado en el sistema host.
