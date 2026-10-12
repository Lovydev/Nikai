---
id: SPEC-ARCH-06
title: Karpathy-Wiki Memory Engine, Obsidian Vault & Offline Unsloth/Kohya Training Pipeline
type: architecture
status: PROPOSED
created: 2026-10-11
updated: 2026-10-11
author: spec-writer
ai_mode: true
source: User Architecture Decision
---

# Architecture Specification: Karpathy-Wiki Memory Engine & Training Pipeline

> **AI Implementation Mode**: Architectural blueprint and contracts for Nikai's personal memory vault, real-time voice retrieval, and offline training dataset generation.

---

## 1. Context & Architecture Decision

### Problem Statement
How should Nikai store long-term memories, aesthetic preferences, personal user context, and artistic knowledge without falling into:
1. Black-box vector databases where memories are opaque and un-editable by the human.
2. High-latency RAG searches that break Pipecat's sub-400ms real-time duplex voice loop.
3. Static conversational agents that never genuinely learn or evolve past their initial prompt.

### Architectural Decision: The Karpathy-Wiki + Two-Tier Memory + Offline Training Pattern
Instead of naive chunked vector storage, Nikai adopts a **three-pillar architecture**:

1. **Pillar 1: The Karpathy-Wiki Pattern in an Obsidian Vault (`/mnt/storage/nikai-data/vault/`)**
   - Memories, entities, projects, and knowledge are compiled into human-readable Markdown files interconnected via bidirectional `[[wikilinks]]`.
   - The user can open Obsidian directly on Arch Linux and inspect, curate, or edit Nikai's memories at any time.
   - Background compilation extracts concepts, tags (`#preference`, `#critique`, `#character`), and resolves contradictions using local Ollama models.

2. **Pillar 2: Two-Tier Memory Hierarchy for Real-Time Duplex Voice (<400ms)**
   - **Tier 1 (Core In-Memory Cache, <5ms):** Active identity prompt, active project metadata, and primary user facts reside permanently pinned in RAM.
   - **Tier 2 (Deep Vault Retrieval, On-Demand):** Pipecat's orchestrator exposes an asynchronous tool `recall_vault_knowledge(topic: str)` called only when an in-depth past memory or specific reference is needed.

3. **Pillar 3: Dual-Track Learning & Offline Fine-Tuning Pipeline**
   - **In-Context Evolution:** Nikai updates user profiles and project wikis dynamically at the end of each creative session.
   - **Dataset Harvester (Unsloth & Kohya):** High-value interactions (successful prompts, approved critiques, visual feedback) are automatically exported into JSONL formats (`alpaca` / `sharegpt`) ready for periodic offline fine-tuning with **Unsloth** (LLM conversational tone) and **Kohya_ss** (SDXL LoRA image weights) during GPU idle hours.

---

## 2. Vault Structure (`/mnt/storage/nikai-data/vault/`)

```
/mnt/storage/nikai-data/vault/
├── .obsidian/                       # Obsidian settings and community plugins
├── 00-Meta/
│   ├── UserProfile.md              # Living document of user preferences, tastes, boundaries
│   └── SystemPrompt-Nikai.md       # Synchronized core persona definition
├── 01-Projects/
│   ├── Project-Amiga-Beach.md      # Active projects with links to LoRAs, concepts, and seeds
│   └── Project-Retro-Futurism.md
├── 02-Characters/
│   ├── Character-Nicole.md         # Trigger words, personality traits, LoRA weights (0.85)
│   └── Character-Laura.md
├── 03-Aesthetic-Knowledge/
│   ├── Cinematography-Lenses.md    # Curated knowledge: 35mm anamorphic, lighting, film stocks
│   └── Lighting-Chiaroscuro.md
├── 04-Episodic-Sessions/
│   └── 2026-10-11-Session-01.md    # Session logs, decisions, rejected ideas, approved artworks
└── 05-Training-Datasets/
    ├── llm_unsloth_conversations.jsonl  # Accumulated DPO/SFT training pairs
    └── lora_image_captions.txt          # Kohya training captions
```

---

## 3. Data Flow & Two-Tier Latency Budget

```
[Usuario Habla por Voz]
          │
          ▼
   [Pipecat Pipeline]
          │
    (Latencia < 5ms)
          ├─────────────────────────────┐
          ▼                             ▼
[Tier 1: Core RAM Context]     [Ollama Llama 3.1 Streaming]
• Perfil del Usuario                    │
• Proyecto Activo                       │ (Responde en <300ms)
• Reglas Estéticas Fijas                ▼
                                  [Kokoro TTS af_nicole]
                                        │
                                  (¿Requiere memoria profunda?)
                                        │
                                        ▼ Sí (Tool Call Asíncrono)
                               [Tier 2: Vault Search]
                               • Búsqueda en Vault Markdown
                               • Inyección para el siguiente turno
```

---

## 4. Component Interfaces & Contracts

### Interface: `VaultManager`
```python
from pathlib import Path
from typing import List, Dict, Optional
from pydantic import BaseModel

class MemoryNote(BaseModel):
    path: Path
    title: str
    content: str
    tags: List[str]
    links: List[str]
    last_modified: float

class VaultManager:
    def __init__(self, vault_path: Path = Path("/mnt/storage/nikai-data/vault")):
        self.vault_path = vault_path

    async def get_core_profile(self) -> str:
        """Returns Tier-1 in-memory summary for fast injection (<5ms)."""
        ...

    async def query_vault(self, query: str, limit: int = 3) -> List[MemoryNote]:
        """Tier-2 deep search across markdown notes and backlinks."""
        ...

    async def append_episodic_turn(self, session_id: str, turn_summary: Dict) -> None:
        """Appends session insights into 04-Episodic-Sessions/."""
        ...

    async def export_training_sample(self, prompt: str, response: str, feedback_score: float) -> None:
        """Appends high-quality samples to 05-Training-Datasets/ for Unsloth."""
        ...
```

---

## 5. Offline Training Strategy (Unsloth & Kohya)

1. **Unsloth (LLM Local Fine-Tuning):**
   - Tool: Unsloth running in a separate conda/venv on RTX 3080.
   - Dataset: `05-Training-Datasets/llm_unsloth_conversations.jsonl` filtered by high satisfaction ratings.
   - Goal: Teach `llama3.1:8b` or `mistral-nemo:12b` Nikai's custom poetic cadence and artistic vocabulary without cloud fine-tuning.
2. **Kohya_ss (SDXL LoRA Training):**
   - Tool: Kohya WebUI (`SUPER + SHIFT + K`).
   - Output: `/mnt/storage/ai-images/lora-workspace/output/`.
   - Goal: Continuous refinement of character and style LoRAs with auto-generated synthetic tags from Qwen-VL.
