---
type: spec
title: Spec — Core Orchestration & Intent State Machine
description: WHAT and WHY for Nikai's master multi-agent orchestration loop and intent routing.
tags: [sdd, spec, orchestration, hmas]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 02-orchestrator-core
status: planned
---

# Spec — Core Orchestration & Intent State Machine

> Slug: `02-orchestrator-core` · Status: planned · Created: 2026-10-12
> Inherits: [constitution](../constitution.md)

## Problem & why
A single monolithic AI agent prompt collapses when handling simultaneous tasks across creative art generation, home domotics, and infrastructure management. Context limits get saturated, tool descriptions hallucinate, and latency spikes past real-time tolerances. Nikai requires an Executive Orchestration State Machine (Core Conductor) powered by local Llama 3.1 8B that rapidly triages user intent and delegates execution to specialized domain directors.

## Cost of not building it
Without hierarchical orchestration, adding future agents (emails, finance, calendar, sub-models) will pollute the primary conversational prompt, degrading voice latency (>1.5s) and causing catastrophic tool selection errors.

## The cheapest alternative
A single big system prompt with all tool definitions. This fails on local 8B/12B models due to attention dilution and context length VRAM exhaustion.

## Goals
- Provide an Executive Finite State Machine (FSM) driven by LangGraph or lightweight asynchronous state loops.
- Route user intents across 3 primary domains: `CREATIVE_DOMAIN`, `LIFE_OPS_DOMAIN`, `INFRA_DOMAIN`.
- Maintain strict session context and state transitions with sub-second turnaround.
- Support seamless synthesis and presentation back to the user via Kokoro voice output.

## Non-goals / out of scope
- Direct execution of SDXL image rendering (delegated to Creative Director).
- Direct actuation of Zigbee/Home Assistant devices (delegated to LifeOps Director).
- Audio streaming packet management (handled by `04-voice-and-vision-pipeline`).

## Users & context
- **User:** Speaks or sends messages naturally; expects immediate conversational feedback without technical jargon.
- **Conductor Model:** `llama3.1:8b` running locally on Ollama (Port 11434).

## Behaviour
- **Main path:**
  1. Conductor receives text/audio prompt.
  2. Conductor classifies intent into `creative`, `life_ops`, `infra`, or `conversational`.
  3. If domain intent matches, dispatches task payload to the corresponding Director.
  4. Collects asynchronous execution result and summarizes it in a concise, warm persona response.
- **Edge cases:**
  - Ambiguous intent: Conductor asks one clarifying question in-character.
  - Multi-intent prompt (e.g., "Dim the lights and draw a futuristic city"): Conductor executes LifeOps actuation first, then hands off to Creative Studio.
  - Director timeout/failure: Conductor catches error gracefully and informs the user without exposing raw stack traces.
- **Error paths:**
  - Offline director: Reports graceful fallback (e.g. "El estudio GPU está en reposo").

## Acceptance criteria
- **Given** user input "Dibuja un atardecer cyberpunk", **When** intent router processes the prompt, **Then** it classifies domain as `CREATIVE_DOMAIN` with confidence > 0.85.
- **Given** user input "Apaga la luz del estudio", **When** intent router processes the prompt, **Then** it classifies domain as `LIFE_OPS_DOMAIN`.
- **Given** user input "Hola, ¿cómo estás?", **When** intent router processes the prompt, **Then** it responds conversationally without dispatching external tools.

## Points to clarify
- **suposición tomada** — LangGraph se usa como motor de grafo de estados por su compatibilidad con agentes asíncronos y memoria persistente. *Base:* Estándar actual de la industria para agentes dirigidos por grafos.
- **decisión diferida** — Mecanismo de persistencia de sesiones (SQLite local vs Postgres). Iniciaremos con SQLite local en `/mnt/storage/nikai-data/state.db`.
