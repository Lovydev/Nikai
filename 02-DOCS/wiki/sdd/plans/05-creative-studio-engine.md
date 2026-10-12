---
type: plan
title: Plan — Creative Studio Engine & Fooocus SDXL Dispatcher
description: Implementation plan for the Creative Studio FSM, Fooocus REST API adapter, and render output manager.
tags: [sdd, plan, creative, sdxl, fooocus]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 05-creative-studio-engine
status: draft
---

# Plan — Creative Studio Engine & Fooocus SDXL Dispatcher

> Spec: [../specs/05-creative-studio-engine.md](../specs/05-creative-studio-engine.md) · Constitution: [../constitution.md](../constitution.md) · Status: draft
> Last updated: 2026-10-12

## 0. Global Constraints

- **Renderer:** Local Fooocus API at `http://127.0.0.1:7865`.
- **Render Output Directory:** `/mnt/storage/nikai-data/renders/`.
- **VRAM Protocol:** Must wrap Fooocus API calls inside `VRAMArbiter.acquire_render_lease()`.
- **Format:** High-resolution PNG with embedded EXIF or JSON sidecar.

## 1. Context & constraints

- Acceptance criteria from spec: Multi-stage creative FSM, character LoRA injection, automated image saving.
- Constitution rules in play: Principle 2 (Hardware Budget & VRAM bounds), Principle 3 (Storage Hierarchy).

## 2. Architecture

```text
[ User Creative Request ]
           │
           ▼
[ Creative Director State Node ]
           │
     ┌─────┴─────────────────────────────────────┐
     ▼                                           ▼
[ Visionary Agent (12B) ]             [ Art Director Agent (7B) ]
(Ideation & Artistic Angles)          (Prompt & LoRA Calibration)
                                                 │
                                                 ▼
                                     [ VRAM Lease Acquisition ]
                                                 │
                                                 ▼
                                     [ Fooocus API Dispatcher (7865) ]
                                                 │
                                                 ▼
                                     [ Render Artifact Saved (/mnt/storage) ]
                                                 │
                                                 ▼
                                     [ Visual Critic Evaluation (Qwen-VL) ]
```

- **`CreativeDirector`** (internal) — Coordinates ideation, compilation, and critique.
- **`FooocusClient`** (internal) — Async client for Fooocus REST endpoints.
- **`RenderStore`** (internal) — Manages storage paths, filenames, and sidecar metadata.

## 3. Interfaces & contracts

```text
FooocusClient.generate(params: SDXLParams) -> RenderResult
  - precondition: VRAM lease acquired, Fooocus 7865 healthy.
  - returns: RenderResult(image_path: Path, seed: int, metadata: dict)
  - postcondition: Image file written to disk.
```

## 4. Data model & flow

**SDXLParams Model:**
- `prompt`: str
- `negative_prompt`: str
- `aspect_ratio`: str (e.g. `1152*896` / `896*1152`)
- `image_number`: int (default: 1)
- `style_selections`: List[str]
- `loras`: List[Dict[str, Any]] (model_name, weight)
- `performance_selection`: str (`Speed` | `Quality` | `Extreme Speed`)

## 5. Tasks Breakdown & Done-Checks

- [ ] **Task 1: Fooocus API client & payload builder**
  - Implement `src/creative/fooocus_client.py` handling async image generation, progress polling, and timeout management.
  - *Done-check:* Unit tests with mock Fooocus server pass with exit 0.

- [ ] **Task 2: Art Director prompt & LoRA compiler**
  - Implement `src/creative/art_director.py` parsing project/character cards and generating optimal SDXL parameters.
  - *Done-check:* Assert correct trigger words injected into prompt.

- [ ] **Task 3: End-to-end Studio FSM test**
  - Wire ideate ➔ render ➔ critique flow in `src/creative/studio.py`.
  - *Done-check:* `pytest tests/test_creative_studio.py` passes 100%.
