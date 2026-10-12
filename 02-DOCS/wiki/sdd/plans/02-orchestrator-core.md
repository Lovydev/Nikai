---
type: plan
title: Plan — Core Orchestration & Intent State Machine
description: Implementation plan for Nikai's hierarchical orchestrator, LangGraph state machine, and intent router.
tags: [sdd, plan, orchestration]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 02-orchestrator-core
status: draft
---

# Plan — Core Orchestration & Intent State Machine

> Spec: [../specs/02-orchestrator-core.md](../specs/02-orchestrator-core.md) · Constitution: [../constitution.md](../constitution.md) · Status: draft
> Last updated: 2026-10-12

## 0. Global Constraints

- **LLM Backbone:** `llama3.1:8b` via Ollama at `http://127.0.0.1:11434`.
- **Latency Budget:** Intent classification must complete in < 250ms on local GPU.
- **State Store:** SQLite file at `/mnt/storage/nikai-data/sessions/state.db`.
- **Modularity:** Directors expose async callables `execute(payload: dict) -> dict`.

## 1. Context & constraints

- Acceptance criteria from spec: High-confidence intent routing across Creative, LifeOps, Infra, and Direct Conversation.
- Constitution rules in play: Principle 1 (Sovereign Local Execution), Principle 4 (SDD Discipline).

## 2. Architecture

```text
[ User Message / Voice STT ]
             │
             ▼
[ Conductor State Node (Llama 3.1:8B) ]
             │
             ▼ Intent Router
     ┌───────┼────────────────────────┐
     ▼       ▼                        ▼
[ Creative ] [ LifeOps ]           [ Infra ]
  Director     Director              Director
     │       │                        │
     └───────┼────────────────────────┘
             ▼
[ Synthesis & Vocal Response Node (Kokoro TTS) ]
```

- **`NikaiOrchestrator`** (internal) — Master state machine manager managing the graph runtime.
- **`IntentClassifier`** (internal) — Fast structured output classifier evaluating user intent.
- **`DirectorRegistry`** (internal) — Registry where the 3 domain directors register their entrypoints.

## 3. Interfaces & contracts

```text
IntentClassifier.classify(prompt: str) -> IntentResult
  - returns: IntentResult(domain: DomainType, confidence: float, parameters: dict)

NikaiOrchestrator.step(session_id: str, message: str) -> OrchestratorResponse
  - precondition: Ollama 11434 reachable.
  - postcondition: State updated in SQLite and clean response returned.
```

## 4. Data model & flow

**Domain Enum:**
- `CREATIVE`
- `LIFEOPS`
- `INFRA`
- `CONVERSATIONAL`

**Session State Dict:**
- `session_id`: UUID str
- `messages`: List of chat messages (user/assistant)
- `active_domain`: DomainType
- `active_project`: Optional[str]
- `pending_clarification`: Optional[str]

## 5. Tasks Breakdown & Done-Checks

- [ ] **Task 1: Structured Intent Classifier**
  - Implement `src/orchestration/classifier.py` using Ollama structured outputs (JSON schema) and few-shot examples.
  - *Done-check:* `pytest tests/test_classifier.py` asserts 100% accuracy on standard test corpus.

- [ ] **Task 2: LangGraph State Machine Graph**
  - Implement `src/orchestration/graph.py` defining nodes (`intent_routing`, `domain_dispatch`, `synthesis`) and conditional edges.
  - *Done-check:* Test graph transitions with mock directors exiting cleanly with status 0.

- [ ] **Task 3: Session Persistence Engine**
  - Implement `src/orchestration/session_store.py` backed by SQLite.
  - *Done-check:* Validate save, load, and history pruning across 10 simulated turns.
