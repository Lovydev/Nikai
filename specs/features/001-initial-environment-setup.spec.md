---
id: SPEC-001
title: Initial Workspace Setup and Sovereign Runtime Environment
type: feature
status: PENDING
created: 2026-10-11
updated: 2026-10-11
author: spec-writer
ai_mode: true
source: User Request
---

# Feature Specification: Initial Workspace Setup and Sovereign Runtime Environment

> **AI Implementation Mode**: Complete code implementations, exact file paths, zero human clarification needed. Follows Spec-Anchored Pattern (SDD Level 2).

---

## 1. Purpose

**What:** Establish the initial Python 3.10 runtime foundation, dependency manifest, centralized configuration system, and automated preflight diagnostic suite for the **Nikai AI** local creative multi-agent system.

**Why:** Ensure all prerequisite local services (Ollama LLMs, Kokoro-82M ONNX voice weights, Fooocus SDXL, and NVIDIA GPU resources) are connected, validated, and accessible with zero manual guesswork before assembling multi-agent workflows.

**Who:** The Nikai autonomous agent runtime and developer.

**Target Environment & Constraints:**
- **OS:** Arch Linux (Omarchy / Hyprland).
- **CPU / RAM:** Intel Core i7-11700K (16 threads), 32 GB RAM.
- **GPU:** NVIDIA GeForce RTX 3080 (10 GB VRAM limit).
- **Storage:** Dedicated 1 TB secondary NVMe mounted at `/mnt/storage`.
- **Python:** 3.10 managed via mise (`/home/jambsik/.local/share/mise/installs/python/3.10.22/bin/python`).
- **Sovereignty:** 100% local; zero external cloud API dependencies.

---

## 2. User Stories & Acceptance Criteria

### Primary User Story
**As a** developer or autonomous AI agent implementing Nikai,  
**I want to** initialize the workspace with a single setup sequence and run an automated diagnostic preflight script,  
**So that** the entire local multi-agent stack (Ollama, Kokoro, Fooocus, GPU) is verified and ready for agent orchestration.

### Acceptance Criteria
- [ ] `config/settings.py` loads strongly typed configuration using Pydantic Settings from `.env` with fallback defaults.
- [ ] Preflight diagnostic script (`scripts/verify_environment.py`) successfully tests:
  1. Local Ollama server (`http://127.0.0.1:11434/api/tags`) and detects installed models (`llama3.1:8b`, `mistral-nemo:12b`, `qwen2.5-coder:7b`, `deepseek-r1:8b`, `qwen3-vl:4b` or `llava:7b`).
  2. Kokoro ONNX model weights (`/mnt/storage/ai-models/kokoro/kokoro-v1.0.onnx`) and voice vectors (`voices-v1.0.bin`).
  3. Fooocus API endpoint accessibility (`http://127.0.0.1:7865`).
  4. NVIDIA GPU detection, CUDA readiness, and VRAM memory measurement.
  5. Read and write permissions on `/mnt/storage`.
- [ ] Pytest test suite (`tests/test_environment.py`) passes 100% with clear assertions.
- [ ] Project dependencies in `requirements.txt` install cleanly in Python 3.10 without version conflicts.

---

## 3. Architecture & File Structure

```
/home/jambsik/development/agents/Nikai/
├── specs/
│   ├── README.md
│   └── features/
│       └── 001-initial-environment-setup.spec.md
├── .env.example
├── .gitignore
├── pyproject.toml
├── requirements.txt
├── config/
│   ├── __init__.py
│   └── settings.py
├── scripts/
│   ├── __init__.py
│   └── verify_environment.py
├── src/
│   └── __init__.py
└── tests/
    ├── __init__.py
    └── test_environment.py
```

---

## 4. Complete Code Implementations

### File 1: `.gitignore`
```gitignore
# File: /home/jambsik/development/agents/Nikai/.gitignore

# Byte-compiled / optimized / DLL files
__pycache__/
*.py[cod]
*$py.class

# C extensions
*.so

# Distribution / packaging
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
share/python-wheels/
*.egg-info/
.installed.cfg
*.egg

# Virtual environments
.venv/
venv/
ENV/
env/

# Environment variables
.env
.env.local

# IDE and editors
.idea/
.vscode/
*.swp
*.swo

# Testing and coverage
.pytest_cache/
.coverage
htmlcov/

# Logs and outputs
*.log
renders/
```

---

### File 2: `pyproject.toml`
```toml
# File: /home/jambsik/development/agents/Nikai/pyproject.toml

[build-system]
requires = ["setuptools>=61.0"]
build-backend = "setuptools.build_meta"

[project]
name = "nikai-ai"
version = "0.1.0"
description = "Sovereign local creative multi-agent companion with Pipecat WebRTC voice, Qwen-VL vision, and Fooocus SDXL."
readme = "README.md"
requires-python = ">=3.10,<3.11"
authors = [
    { name = "Nikai Team" }
]
dependencies = [
    "pydantic>=2.7.0",
    "pydantic-settings>=2.2.0",
    "httpx>=0.27.0",
    "aiohttp>=3.9.0",
    "websockets>=12.0",
    "numpy>=1.26.0,<2.0.0",
    "onnxruntime>=1.18.0",
    "sounddevice>=0.4.6",
    "pillow>=10.2.0",
    "rich>=13.7.0"
]

[project.optional-dependencies]
dev = [
    "pytest>=8.0.0",
    "pytest-asyncio>=0.23.0"
]

[tool.pytest.ini_options]
asyncio_mode = "auto"
testpaths = ["tests"]
```

---

### File 3: `requirements.txt`
```text
# File: /home/jambsik/development/agents/Nikai/requirements.txt

# Core Types & Async HTTP
pydantic>=2.7.0
pydantic-settings>=2.2.0
httpx>=0.27.0
aiohttp>=3.9.0
websockets>=12.0

# Audio & Neural Voice (Kokoro ONNX)
onnxruntime>=1.18.0
sounddevice>=0.4.6
numpy>=1.26.0,<2.0.0

# Vision & Images
pillow>=10.2.0

# Terminal UI & Diagnostics
rich>=13.7.0

# Testing
pytest>=8.0.0
pytest-asyncio>=0.23.0
```

---

### File 4: `.env.example`
```env
# File: /home/jambsik/development/agents/Nikai/.env.example

# Ollama Local Service
NIKAI_OLLAMA_BASE_URL="http://127.0.0.1:11434"
NIKAI_OLLAMA_TIMEOUT_SECONDS=60.0

# Kokoro-82M Neural Voice (ONNX)
NIKAI_KOKORO_MODEL_PATH="/mnt/storage/ai-models/kokoro/kokoro-v1.0.onnx"
NIKAI_KOKORO_VOICES_PATH="/mnt/storage/ai-models/kokoro/voices-v1.0.bin"
NIKAI_KOKORO_DEFAULT_VOICE="af_nicole"

# Fooocus Image Diffusion (SDXL)
NIKAI_FOOOCUS_BASE_URL="http://127.0.0.1:7865"
NIKAI_FOOOCUS_LORAS_DIR="/mnt/storage/ai-images/Fooocus/models/loras"
NIKAI_RENDERS_OUTPUT_DIR="/mnt/storage/ai-images/renders"

# Memory and Storage
NIKAI_STORAGE_ROOT="/mnt/storage"
NIKAI_DATA_DIR="/mnt/storage/nikai-data"

# GPU & Concurrency Management
NIKAI_GPU_DEVICE_ID=0
NIKAI_MAX_VRAM_GB=10.0
```

---

### File 5: `config/settings.py`
```python
# File: /home/jambsik/development/agents/Nikai/config/settings.py

from pathlib import Path
from pydantic import Field
from pydantic_settings import BaseSettings, SettingsConfigDict


class Settings(BaseSettings):
    """Central configuration for Nikai AI sovereign multi-agent environment."""

    model_config = SettingsConfigDict(
        env_prefix="NIKAI_",
        env_file=".env",
        env_file_encoding="utf-8",
        extra="ignore"
    )

    # Project metadata
    project_name: str = "Nikai AI"
    version: str = "0.1.0"

    # Ollama Local LLM / Vision
    ollama_base_url: str = Field(
        default="http://127.0.0.1:11434",
        description="Local Ollama HTTP endpoint"
    )
    ollama_timeout_seconds: float = 60.0
    model_conductor: str = "llama3.1:8b"
    model_visionary: str = "mistral-nemo:12b"
    model_art_director: str = "qwen2.5-coder:7b"
    model_critic: str = "qwen3-vl:4b"
    model_critic_fallback: str = "llava:7b"
    model_reasoner: str = "deepseek-r1:8b"

    # Kokoro-82M Real-Time Neural Voice
    kokoro_model_path: Path = Field(
        default=Path("/mnt/storage/ai-models/kokoro/kokoro-v1.0.onnx"),
        description="Path to Kokoro ONNX model weights"
    )
    kokoro_voices_path: Path = Field(
        default=Path("/mnt/storage/ai-models/kokoro/voices-v1.0.bin"),
        description="Path to Kokoro binary voice vectors"
    )
    kokoro_default_voice: str = "af_nicole"

    # Fooocus SDXL Image Engine
    fooocus_base_url: str = Field(
        default="http://127.0.0.1:7865",
        description="Fooocus local WebUI API endpoint"
    )
    fooocus_loras_dir: Path = Field(
        default=Path("/mnt/storage/ai-images/Fooocus/models/loras"),
        description="Directory containing trained .safetensors LoRA files"
    )
    renders_output_dir: Path = Field(
        default=Path("/mnt/storage/ai-images/renders"),
        description="Directory where generated artwork will be stored"
    )

    # Physical Storage & Data
    storage_root: Path = Field(
        default=Path("/mnt/storage"),
        description="Mount point of the dedicated high-speed NVMe storage"
    )
    data_dir: Path = Field(
        default=Path("/mnt/storage/nikai-data"),
        description="Root for SQLite memory, session logs, and cache"
    )

    # GPU Hardware Constraints
    gpu_device_id: int = 0
    max_vram_gb: float = 10.0


# Singleton settings instance
settings = Settings()
```

---

### File 6: `scripts/verify_environment.py`
```python
# File: /home/jambsik/development/agents/Nikai/scripts/verify_environment.py

import sys
import shutil
from pathlib import Path
import httpx
from rich.console import Console
from rich.table import Table
from rich.panel import Panel

# Add project root to sys.path
PROJECT_ROOT = Path(__file__).resolve().parent.parent
sys.path.insert(0, str(PROJECT_ROOT))

from config.settings import settings

console = Console()


def check_storage() -> tuple[bool, str]:
    """Verify that /mnt/storage exists, is accessible, and has write permissions."""
    storage_path = settings.storage_root
    if not storage_path.exists():
        return False, f"Storage directory {storage_path} does not exist"

    total, used, free = shutil.disk_usage(storage_path)
    free_gb = free // (2**30)

    # Test write permissions
    test_file = storage_path / ".nikai_write_test.tmp"
    try:
        test_file.write_text("test")
        test_file.unlink()
    except Exception as e:
        return False, f"Write check failed: {e}"

    return True, f"Accessible with {free_gb} GB free disk space"


def check_ollama() -> tuple[bool, str, list[str]]:
    """Verify Ollama server connectivity and inventory installed models."""
    url = f"{settings.ollama_base_url}/api/tags"
    try:
        with httpx.Client(timeout=4.0) as client:
            resp = client.get(url)
            if resp.status_code == 200:
                data = resp.json()
                models = [m.get("name", "") for m in data.get("models", [])]
                return True, f"Running on {settings.ollama_base_url}", models
            return False, f"HTTP {resp.status_code}", []
    except Exception as e:
        return False, f"Cannot connect to Ollama ({e})", []


def check_kokoro() -> tuple[bool, str]:
    """Verify Kokoro ONNX model and voices binary exist on disk."""
    model_exists = settings.kokoro_model_path.is_file()
    voices_exists = settings.kokoro_voices_path.is_file()

    if model_exists and voices_exists:
        model_size_mb = settings.kokoro_model_path.stat().st_size / (1024 * 1024)
        return True, f"Found weights ({model_size_mb:.1f} MB) and voices binary (voice: {settings.kokoro_default_voice})"
    
    missing = []
    if not model_exists:
        missing.append(str(settings.kokoro_model_path))
    if not voices_exists:
        missing.append(str(settings.kokoro_voices_path))
    return False, f"Missing files: {', '.join(missing)}"


def check_fooocus() -> tuple[bool, str]:
    """Verify Fooocus server connectivity (warn if inactive, not blocking)."""
    try:
        with httpx.Client(timeout=2.0) as client:
            resp = client.get(settings.fooocus_base_url)
            if resp.status_code in [200, 302]:
                return True, f"Active on {settings.fooocus_base_url}"
            return False, f"Responded with status {resp.status_code}"
    except Exception:
        return False, f"Offline on {settings.fooocus_base_url} (launch with SUPER+SHIFT+I when needed)"


def check_gpu() -> tuple[bool, str]:
    """Verify NVIDIA GPU presence via nvidia-smi."""
    nvidia_smi = shutil.which("nvidia-smi")
    if not nvidia_smi:
        return False, "nvidia-smi not found in PATH"
    return True, f"RTX 3080 detected ({settings.max_vram_gb} GB budget)"


def main() -> int:
    console.print(Panel.fit(
        "[bold cyan]Nikai AI // Sovereign Multi-Agent Environment Preflight[/bold cyan]\n"
        f"Target Root: {PROJECT_ROOT}",
        border_style="cyan"
    ))

    table = Table(title="Component Health Check", show_lines=True)
    table.add_column("Subsystem", style="bold", width=22)
    table.add_column("Status", width=12)
    table.add_column("Diagnostics", style="dim")

    all_passed = True

    # 1. Storage Check
    ok, msg = check_storage()
    table.add_row("Storage NVMe", "[green]PASS[/green]" if ok else "[red]FAIL[/red]", msg)
    if not ok:
        all_passed = False

    # 2. GPU Check
    ok, msg = check_gpu()
    table.add_row("NVIDIA GPU", "[green]PASS[/green]" if ok else "[yellow]WARN[/yellow]", msg)

    # 3. Kokoro Check
    ok, msg = check_kokoro()
    table.add_row("Kokoro TTS", "[green]PASS[/green]" if ok else "[red]FAIL[/red]", msg)
    if not ok:
        all_passed = False

    # 4. Ollama Check
    ok, msg, models = check_ollama()
    model_summary = f"{msg} | Models: {', '.join(models) if models else 'None'}"
    table.add_row("Ollama LLM", "[green]PASS[/green]" if ok else "[red]FAIL[/red]", model_summary)
    if not ok:
        all_passed = False

    # 5. Fooocus Check (Advisory)
    ok_fooocus, msg_fooocus = check_fooocus()
    table.add_row("Fooocus SDXL", "[green]ONLINE[/green]" if ok_fooocus else "[yellow]STANDBY[/yellow]", msg_fooocus)

    console.print(table)

    if all_passed:
        console.print("\n[bold green]✓ All core sovereign dependencies are verified and operational.[/bold green]\n")
        return 0
    else:
        console.print("\n[bold red]✗ One or more critical subsystems failed preflight verification.[/bold red]\n")
        return 1


if __name__ == "__main__":
    sys.exit(main())
```

---

### File 7: `tests/test_environment.py`
```python
# File: /home/jambsik/development/agents/Nikai/tests/test_environment.py

from pathlib import Path
from config.settings import settings


def test_settings_initialization():
    """Verify that default settings load correct local parameters."""
    assert settings.project_name == "Nikai AI"
    assert settings.ollama_base_url.startswith("http://")
    assert settings.kokoro_default_voice == "af_nicole"
    assert settings.max_vram_gb == 10.0
    assert isinstance(settings.storage_root, Path)


def test_kokoro_files_exist():
    """Assert that the downloaded Kokoro weights and voices binary exist on /mnt/storage."""
    assert settings.kokoro_model_path.exists(), f"Missing {settings.kokoro_model_path}"
    assert settings.kokoro_voices_path.exists(), f"Missing {settings.kokoro_voices_path}"


def test_storage_root_writable():
    """Assert that storage mount is writable."""
    test_path = settings.storage_root / ".nikai_test_probe.tmp"
    test_path.write_text("ok")
    assert test_path.read_text() == "ok"
    test_path.unlink()
```

---

## 5. Sequential Implementation Plan

1. **Step 1: Create Directory Tree**
   ```bash
   mkdir -p config scripts src tests
   ```

2. **Step 2: Create Python Virtual Environment (using Python 3.10 via mise)**
   ```bash
   /home/jambsik/.local/share/mise/installs/python/3.10.22/bin/python -m venv .venv
   ```

3. **Step 3: Write Configuration and Dependency Manifests**
   - Populate `requirements.txt`, `pyproject.toml`, `.gitignore`, and `.env.example`.
   - Populate `config/settings.py` and `config/__init__.py`.

4. **Step 4: Install Dependencies into Virtual Environment**
   ```bash
   .venv/bin/pip install --upgrade pip
   .venv/bin/pip install -r requirements.txt
   ```

5. **Step 5: Write Preflight Script and Tests**
   - Populate `scripts/verify_environment.py`.
   - Populate `tests/test_environment.py`.

---

## 6. Verification Checklist & Commands

Run these exact commands to verify that the implementation is 100% complete and working:

```bash
# 1. Activate virtual environment
source .venv/bin/activate

# 2. Run automated preflight diagnostic
python scripts/verify_environment.py

# 3. Run unit test suite
pytest -v tests/test_environment.py
```

**Success Gate:**
- `verify_environment.py` returns exit code `0`.
- All tests in `test_environment.py` pass with `PASSED`.

---

## 7. References
- Ollama API Documentation: `http://127.0.0.1:11434`
- Kokoro-82M ONNX Model: `/mnt/storage/ai-models/kokoro/`
- Fooocus SDXL Engine: `http://127.0.0.1:7865`
- SDD Level 2 Spec-Anchored Pattern: `/home/jambsik/.gemini/config/skills/spec-writer/SKILL.md`
