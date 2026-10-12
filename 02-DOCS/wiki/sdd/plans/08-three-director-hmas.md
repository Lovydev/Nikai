---
type: plan
title: Plan — Three-Director Governance System & Agent Factory
description: Implementation plan for the Director abstract base class, Home Assistant adapter, and the autonomous Agent Factory.
tags: [sdd, plan, governance, directors, factory]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 08-three-director-hmas
status: draft
---

# Plan — Three-Director Governance System & Agent Factory

> Spec: [../specs/08-three-director-hmas.md](../specs/08-three-director-hmas.md) · Constitution: [../constitution.md](../constitution.md) · Status: draft
> Last updated: 2026-10-12

## 0. Global Constraints

- **Architecture:** Hierarchical Multi-Agent System (HMAS) with 3 independent domain directors.
- **Domotics Standard:** Home Assistant REST API (`/api/states`, `/api/services`) with Bearer token authentication.
- **Factory Standard:** Any newly spawned agent must generate a `01-TOOLS/<tool>/test_connection.py` exiting with code 0.
- **Safety:** Agent Factory cannot modify production settings without explicit approval.

## 1. Context & constraints

- Acceptance criteria from spec: Unified Director contract, Home Assistant adapter, automated subagent scaffolding.
- Constitution rules in play: Principle 4 (SDD Discipline), Principle 5 (Automated Verification Floor).

## 2. Architecture

```text
                        [ Nikai Core Conductor ]
                                   │
         ┌─────────────────────────┼─────────────────────────┐
         ▼                         ▼                         ▼
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Creative        │       │ LifeOps         │       │ Infra & Lab     │
│ Director        │       │ Director        │       │ Director        │
├─────────────────┤       ├─────────────────┤       ├─────────────────┤
│ • Studio FSM    │       │ • Home Assistant│       │ • Cluster Health│
│ • Art Director  │       │   API Adapter   │       │ • VRAM Leases   │
│ • Fooocus SDXL  │       │ • Open Worker   │       │ • AGENT FACTORY │
│ • Qwen-VL Critic│       │   Slots         │       │   (Meta-Spawner)│
└─────────────────┘       └─────────────────┘       └─────────────────┘
```

- **`BaseDirector`** (internal) — Abstract class defining `handle_intent()`, `list_tools()`, and `health_check()`.
- **`HomeAssistantAdapter`** (internal) — Async client for querying entities and executing home automation.
- **`AgentFactory`** (internal) — Scaffolding engine generating SDD artifacts, directories, and verification scripts.

## 3. Interfaces & contracts

```text
BaseDirector.handle_intent(intent: str, parameters: dict) -> DirectorResult
  - returns: DirectorResult(success: bool, response_text: str, artifacts: List[str])

HomeAssistantAdapter.get_state(entity_id: str) -> EntityState
  - returns: EntityState(entity_id: str, state: str, attributes: dict)

AgentFactory.create_agent(name: str, domain: str, purpose: str) -> AgentScaffoldResult
  - postcondition: Generates spec, plan, 01-TOOLS directory, and test_connection.py.
```

## 4. Data model & flow

**EntityState Model:**
- `entity_id`: str
- `state`: str
- `attributes`: Dict[str, Any]
- `last_updated`: str

**DirectorResult Model:**
- `success`: bool
- `message`: str
- `data`: Dict[str, Any]
- `suggested_action`: Optional[str]

## 5. Tasks Breakdown & Done-Checks

- [ ] **Task 1: BaseDirector interface & registry**
  - Implement `src/governance/base_director.py` and `src/governance/registry.py`.
  - *Done-check:* Unit tests asserting polymorphic director registration and execution pass.

- [ ] **Task 2: Home Assistant async adapter**
  - Implement `src/lifeops/homeassistant.py` with mock and live REST capabilities.
  - *Done-check:* `pytest tests/test_homeassistant.py` verifies state parsing and command execution with exit 0.

- [ ] **Task 3: Meta Agent Factory**
  - Implement `src/infra/agent_factory.py` capable of creating new tools under `01-TOOLS/` and registering them with `test_connection.py`.
  - *Done-check:* Run test agent creation; verify generated `test_connection.py` exits 0.
