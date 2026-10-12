---
type: spec
title: Spec — Hybrid Memory Engine & Obsidian Karpathy-Wiki
description: WHAT and WHY for Nikai's long-term memory, Obsidian Karpathy-Wiki vault, and offline Unsloth dataset generator.
tags: [sdd, spec, memory, obsidian, unsloth]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 06-hybrid-memory-and-wiki
status: planned
---

# Spec — Hybrid Memory Engine & Obsidian Karpathy-Wiki

> Slug: `06-hybrid-memory-and-wiki` · Status: planned · Created: 2026-10-12
> Inherits: [constitution](../constitution.md)

## Problem & why
Traditional AI agent memory relies either on opaque vector databases (where memories cannot be audited, curated, or manually corrected by the user) or naively stuffs entire chat histories into prompts (exhausting context windows and spiking latency). Furthermore, naive RAG searches take 800ms+, destroying Pipecat's real-time voice latency budget (<400ms). Nikai requires a hybrid two-tier memory architecture grounded in an open, human-readable Obsidian Vault (the Karpathy-Wiki pattern).

## Cost of not building it
Nikai will suffer from conversational amnesia across sessions, cannot accumulate consistent artistic knowledge, and provides zero visibility to the human into what facts the AI has stored about them.

## The cheapest alternative
Storing raw JSON chat dumps in a database. This cannot be edited in Obsidian, creates zero conceptual links between projects and characters, and cannot be harvested for model fine-tuning.

## Goals
- Maintain an open **Obsidian Knowledge Vault** at `/mnt/storage/nikai-data/vault/` with bidirectional `[[wikilinks]]`.
- Support **Two-Tier Memory Access**:
  - *Tier 1 (Core RAM, <5ms):* Pinned user profile, active project card, fixed persona rules.
  - *Tier 2 (Deep Vault Retrieval, Asynchronous):* On-demand tool call `recall_vault_knowledge(topic)`.
- Implement **Session Summarizer**: Automatically distill creative sessions into episodic log entries and update project wikis at session end.
- Implement **Dataset Harvester**: Accumulate high-quality conversation pairs and critique feedback into JSONL datasets (`alpaca` / `sharegpt`) ready for offline fine-tuning with **Unsloth**.

## Non-goals / out of scope
- Running the heavy GPU Unsloth training loop in real time (training occurs offline when GPU is idle).
- Replacing the human's personal Obsidian notes (Nikai's memory vault is partitioned under its own dedicated root).

## Users & context
- **Human Creator:** Opens Obsidian on Arch Linux / Mac to inspect, prune, or enrich Nikai's memories at any time.

## Behaviour
- **Main path:**
  1. Conductor boots: loads Tier 1 profile into memory.
  2. During voice chat, if user references past context ("¿Recuerdas cómo iluminamos la escena de Nicole ayer?"), agent triggers Tier 2 search against vault markdown files.
  3. Session completes: background task parses dialogue, extracts key decisions, writes `04-Episodic-Sessions/<date>.md`, updates project card, and appends valid training pairs to `05-Training-Datasets/conversations.jsonl`.

## Acceptance criteria
- **Given** user facts in `UserProfile.md`, **When** session starts, **Then** Tier 1 context loads into memory in < 5ms.
- **Given** search query, **When** `search_vault(query)` executes, **Then** it parses markdown headers and links returning concise context in < 80ms.
- **Given** session end, **When** `session_gardener.distill()` runs, **Then** an episodic markdown note is committed to the vault.

## Points to clarify
- **suposición tomada** — El vault utiliza la sintaxis estándar de Markdown con frontmatter YAML compatible con Obsidian y wikilinks estándar `[[nota]]`.
