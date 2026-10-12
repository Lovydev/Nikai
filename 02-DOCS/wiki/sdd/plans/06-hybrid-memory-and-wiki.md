---
type: plan
title: Plan — Hybrid Memory Engine & Obsidian Karpathy-Wiki
description: Implementation plan for Nikai's vault storage manager, two-tier cache, and offline dataset exporter.
tags: [sdd, plan, memory, obsidian, unsloth]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 06-hybrid-memory-and-wiki
status: draft
---

# Plan — Hybrid Memory Engine & Obsidian Karpathy-Wiki

> Spec: [../specs/06-hybrid-memory-and-wiki.md](../specs/06-hybrid-memory-and-wiki.md) · Constitution: [../constitution.md](../constitution.md) · Status: draft
> Last updated: 2026-10-12

## 0. Global Constraints

- **Vault Root:** `/mnt/storage/nikai-data/vault/`.
- **Latency Floors:** Tier 1 RAM lookup < 5ms; Tier 2 Vault scan < 100ms.
- **Dataset Target:** JSONL formatted in `alpaca` and `sharegpt` standards for Unsloth.
- **Format:** Pure Markdown files with YAML frontmatter and bidirectional `[[wikilinks]]`.

## 1. Context & constraints

- Acceptance criteria from spec: Instant Tier 1 memory injection, fast Markdown search, automated post-session summarization.
- Constitution rules in play: Principle 3 (Storage Hierarchy Discipline).

## 2. Architecture

```text
[ Obsidian Vault (/mnt/storage/nikai-data/vault/) ]
            │
            ├──► 00-Meta/ (UserProfile.md, SystemPrompt.md)
            ├──► 01-Projects/ (Active creative projects)
            ├──► 02-Characters/ (Character cards & LoRA weights)
            ├──► 03-Aesthetic-Knowledge/ (Cinematography, lighting)
            ├──► 04-Episodic-Sessions/ (Past conversation summaries)
            └──► 05-Training-Datasets/ (Unsloth JSONL pairs)
                    │
            ┌───────┴────────────────────────┐
            ▼                                ▼
[ Tier 1 In-Memory Cache ]        [ Tier 2 Vault Retriver ]
(Loaded at startup into RAM)      (Asynchronous tool on demand)
            │                                │
            └───────────────┬────────────────┘
                            ▼
                  [ Conductor Context ]
                            │
                            ▼ (Session End)
               [ Vault Gardener & Dataset Exporter ]
```

- **`VaultManager`** (internal) — File I/O manager reading and writing Obsidian Markdown notes.
- **`MemoryTierController`** (internal) — Coordinates RAM caching and semantic vault searching.
- **`DatasetHarvester`** (internal) — Formats training samples into JSONL files for Unsloth.

## 3. Interfaces & contracts

```text
MemoryTierController.get_active_context() -> CoreContext
  - returns: CoreContext(user_profile: dict, active_project: dict, persona: str)
  - latency: < 5ms

VaultManager.search(query: str, top_k: int = 3) -> List[VaultSnippet]
  - returns: List of relevant markdown snippets with filename and score.

DatasetHarvester.record_interaction(user_input: str, assistant_response: str, critique_feedback: Optional[str]) -> None
  - postcondition: Appends structured JSON line to 05-Training-Datasets/conversations.jsonl.
```

## 4. Data model & flow

**VaultSnippet Model:**
- `file_path`: Path
- `title`: str
- `section`: str
- `content`: str
- `tags`: List[str]

## 5. Tasks Breakdown & Done-Checks

- [ ] **Task 1: Obsidian Vault directory scaffolding & default cards**
  - Implement `src/memory/vault.py` initializing directories and seeding `UserProfile.md`.
  - *Done-check:* Inspect initialized vault on disk; assert valid YAML frontmatter.

- [ ] **Task 2: Fast Markdown search & link resolver**
  - Implement `src/memory/search.py` performing fast regex/tag scans across vault files.
  - *Done-check:* Querying `#preference` returns matching sections in < 50ms.

- [ ] **Task 3: Post-session summarizer & dataset harvester**
  - Implement `src/memory/harvester.py` writing episodic notes and Unsloth JSONL records.
  - *Done-check:* Run test session consolidation; verify JSONL line parses with `json.loads`.
