---
type: spec
title: Spec — Creative Studio Engine & Fooocus SDXL Dispatcher
description: WHAT and WHY for Nikai's artistic ideation, LoRA compounding, and Fooocus SDXL render loop.
tags: [sdd, spec, creative, sdxl, fooocus]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 05-creative-studio-engine
status: planned
---

# Spec — Creative Studio Engine & Fooocus SDXL Dispatcher

> Slug: `05-creative-studio-engine` · Status: planned · Created: 2026-10-12
> Inherits: [constitution](../constitution.md)

## Problem & why
Image generation prompts are difficult for humans to fine-tune manually, often requiring complex negative prompts, CFG tuning, aspect ratio selection, and LoRA weight calibration. Furthermore, generating images blindly without a collaborative creative dialogue produces repetitive or generic art. Nikai requires an autonomous Creative Studio Engine governed by the Creative Director that moves through structured ideation (Visionary), technical prompt formulation (Art Director), and automated execution (Fooocus API).

## Cost of not building it
The user is forced to copy-paste prompts into web UIs manually, breaking the voice flow and losing the collaborative pair-creation experience.

## The cheapest alternative
A basic one-shot text-to-image API call. This fails to support iterative refinement, character LoRA consistency, or artistic critique loops.

## Goals
- Support the Creative Studio FSM: `Discovery` ➔ `Divergent Ideation` ➔ `Convergent Selection` ➔ `Render Dispatch` ➔ `Critique`.
- Dispatch renders directly to local **Fooocus SDXL** (`http://127.0.0.1:7865/v1/generation/text-to-image`).
- Support compounding multiple LoRAs (e.g. character LoRA at 0.85 + film stock LoRA at 0.4).
- Save generated artworks and full metadata (prompt, seed, steps, sampler, LoRAs) directly into `/mnt/storage/nikai-data/renders/`.

## Non-goals / out of scope
- Inpainting or video generation in Phase 1 (reserved for later specs).
- Cloud image generators (Midjourney, DALL-E) — 100% local SDXL only.

## Users & context
- **User & Nikai:** Collaborate on visual art series, character designs, or retro-futuristic illustrations.

## Behaviour
- **Main path:**
  1. Creative Director engages on user topic (e.g., "Retrato cinematográfico de Nicole en la playa").
  2. Visionary generates 3 distinct artistic avenues (lighting, mood, lens).
  3. User picks or refines an avenue.
  4. Art Director compiles Fooocus parameters: prompt, negative prompt, aspect ratio, steps (30), sampler (dpmpp_2m_sde_gpu), LoRAs.
  5. VRAM lease acquired, Fooocus renders image.
  6. Image saved with JSON sidecar and presented to user.
  7. Visual Critic runs critique and suggests refinements.

## Acceptance criteria
- **Given** creative prompt and character name, **When** Art Director compiles parameters, **Then** correct trigger words and LoRA weights are included from character vault.
- **Given** Fooocus running at port 7865, **When** render request is dispatched, **Then** PNG image and JSON metadata are saved to `/mnt/storage/nikai-data/renders/`.

## Points to clarify
- **suposición tomada** — Fooocus API se expone vía `fooocus-api` o el servidor headless integrado en puerto 7865.
