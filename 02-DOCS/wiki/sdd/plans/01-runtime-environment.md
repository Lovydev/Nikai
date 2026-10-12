---
type: plan
title: Plan — Runtime Environment & Preflight Foundation
description: Technical architecture, configuration schema, and preflight test plan for Nikai.
tags: [sdd, plan, runtime]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 01-runtime-environment
status: draft
---

# Plan — Runtime Environment & Preflight Foundation

> Spec: [../specs/01-runtime-environment.md](../specs/01-runtime-environment.md) · Constitution: [../constitution.md](../constitution.md) · Status: draft
> Last updated: 2026-10-12

## 0. Global Constraints

- **Python Runtime:** Python 3.10 strictly (managed via `mise` or virtual environment at `.venv/`).
- **GPU Resource:** NVIDIA RTX 3080 with 10,240 MiB VRAM budget limit.
- **Local Sovereignty:** 0 external cloud inference APIs allowed in runtime code.
- **Storage Target:** Large model weights and data reside on `/mnt/storage/ai-models/` and `/mnt/storage/nikai-data/`.
- **Verification Floor:** Every script and test must pass deterministically with exit code `0`.

## 1. Context & constraints

- Acceptance criteria from spec: Pydantic Settings configuration, automated preflight check, Pytest suite passing 100%.
- Constitution rules in play: Principle 1 (Sovereign Local Execution), Principle 3 (Storage Hierarchy), Principle 5 (Automated Verification Floor).
- Out of scope: Running the speech loop or invoking image generation passes.

## 2. Architecture

```text
[ .env / Environment Variables ]
              │
              ▼
[ config/settings.py (Pydantic Settings) ]
              │
      ┌───────┴────────────────────────┐
      ▼                                ▼
[ scripts/verify_environment.py ]  [ tests/test_environment.py ]
      │                                │
      ├──► Probe Ollama (11434)        └──► Pytest Assertions (Exit 0)
      ├──► Probe Kokoro ONNX Weights
      ├──► Probe Fooocus API (7865)
      ├──► Probe PyTorch CUDA & VRAM
      └──► Probe /mnt/storage write
```

- **`config.settings.Settings`** (internal) — Strongly typed configuration schema with defaults and validation.
- **`scripts.verify_environment`** (internal) — Standalone diagnostic CLI script for preflight health inspection.
- **`tests.test_environment`** (internal) — Pytest suite asserting environment integrity.

## 3. Interfaces & contracts

```text
Settings() -> SettingsInstance
  - Loads from .env, validates types, provides paths to /mnt/storage.

verify_environment.run_all_checks() -> bool
  - precondition: Network loopback (127.0.0.1) available.
  - returns: True if all checks pass, False otherwise.
  - postcondition: Exits with sys.exit(0) on success, sys.exit(1) on failure.
```

## 4. Data model & flow

**Settings Model Schema:**
- `app_env`: str (`development` | `production`)
- `ollama_base_url`: HttpUrl (default: `http://127.0.0.1:11434`)
- `fooocus_api_url`: HttpUrl (default: `http://127.0.0.1:7865`)
- `storage_root`: Path (default: `/mnt/storage`)
- `kokoro_model_path`: Path (default: `/mnt/storage/ai-models/kokoro/kokoro-v1.0.onnx`)
- `kokoro_voices_path`: Path (default: `/mnt/storage/ai-models/kokoro/voices-v1.0.bin`)
- `vram_budget_mb`: int (default: `10240`)

## 5. Tasks Breakdown & Done-Checks

- [ ] **Task 1: Dependency manifest & project packaging**
  - Create `pyproject.toml` and `requirements.txt` with locked versions (`pydantic-settings`, `httpx`, `torch`, `pytest`, `rich`).
  - *Done-check:* `python3 -m pip install -r requirements.txt --dry-run` or virtualenv verification exits 0.

- [ ] **Task 2: Pydantic Settings module**
  - Implement `config/settings.py` and `.env.example`.
  - *Done-check:* `python3 -c "from config.settings import settings; print(settings.app_env)"` outputs `development` and exits 0.

- [ ] **Task 3: Preflight verification suite**
  - Implement `scripts/verify_environment.py` with probes for Ollama, CUDA, Kokoro weights, Fooocus, and `/mnt/storage`.
  - *Done-check:* `python3 scripts/verify_environment.py` runs all checks and outputs status table.

- [ ] **Task 4: Pytest unit test harness**
  - Implement `tests/test_environment.py` asserting configuration loading and fallback safety.
  - *Done-check:* `pytest tests/test_environment.py` passes with 100% success (exit 0).
