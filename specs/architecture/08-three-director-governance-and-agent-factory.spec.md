---
id: SPEC-ARCH-08
title: Three-Director Governance System, Home Assistant Adapter & Agent Factory
type: architecture
status: PROPOSED
created: 2026-10-11
updated: 2026-10-11
author: spec-writer
ai_mode: true
source: Multi-Domain Architecture Decision
---

# Architecture Specification: Three-Director Governance & Agent Factory

> **AI Implementation Mode**: Architectural blueprint for the three domain directors, Home Assistant domotics integration, and the autonomous Agent Factory meta-system.

---

## 1. Domain Governance Architecture

Nikai separates responsibilities across three autonomous directors, each owning its specific operational boundaries, tools, and memory partition:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        NIKAI CORE CONDUCTOR                            │
│                 (Front-facing Voice Partner & Router)                  │
├─────────────────────┬──────────────────────────┬───────────────────────┤
│  CREATIVE DIRECTOR  │    LIFE OPS DIRECTOR     │  INFRA & LAB DIRECTOR │
├─────────────────────┼──────────────────────────┼───────────────────────┤
│ • Artistic Ideation │ • Home Assistant Bridge  │ • Cluster & Node Pings│
│ • SDXL Parameters   │ • Domótica & Estados     │ • VRAM Lease Manager  │
│ • Fooocus API (7865)│ • Recordatorios & Fechas │ • Obsidian Backups    │
│ • Qwen-VL Critique  │ • Pluggable Workers Slot │ • AGENT FACTORY       │
│                     │   (Emails/Day-to-day)    │   (Meta-Agent Spawner)│
└─────────────────────┴──────────────────────────┴───────────────────────┘
```

---

## 2. Director 1: Creative Director (`CreativeDirector`)

- **Host Node:** Arch Linux (NVIDIA RTX 3080).
- **Sub-agents Governed:**
  - `Visionary` (Mistral Nemo 12B)
  - `ArtDirector` (Qwen 2.5 Coder 7B)
  - `VisualCritic` (Qwen-VL 4B)
- **Primary Tool:** `fooocus_tool_dispatcher` (Port 7865).
- **State Machine:** Discovery ➔ Divergence ➔ Convergence ➔ Render ➔ Critique.

---

## 3. Director 2: Personal Life & Operations Director (`LifeOpsDirector`)

- **Host Node:** Mac Mini M2 Pro (24/7 continuous operation).
- **Purpose:** Centralize all daily life context, home routines, physical domotics, and future personal productivity tasks without cluttering the creative studio.
- **Home Assistant Adapter Contract:**
  - Communicates via Home Assistant REST API (`/api/states`, `/api/services`) and Long-Lived Access Tokens.
  - Exposes tool methods:
    * `get_home_state(entity_id: str) -> EntityState`
    * `call_home_service(domain: str, service: str, service_data: dict) -> bool`
    * `list_active_alerts() -> List[Alert]`
- **Pluggable Day-to-Day Worker Slots:**
  - Designed as an open registration interface: worker agents (e.g. email manager, calendar manager, daily briefing) will be registered dynamically under this Director when defined by the user.

```python
from typing import Dict, Any, List
from pydantic import BaseModel

class EntityState(BaseModel):
    entity_id: str
    state: str
    attributes: Dict[str, Any]
    last_updated: str

class HomeAssistantAdapter:
    """Async bridge connecting LifeOpsDirector to local Home Assistant."""

    def __init__(self, base_url: str = "http://127.0.0.1:8123", access_token: str = ""):
        self.base_url = base_url
        self.access_token = access_token

    async def get_state(self, entity_id: str) -> EntityState:
        """Reads real-time state of lights, sensors, climate, switches."""
        ...

    async def trigger_service(self, domain: str, service: str, target: Dict[str, Any]) -> bool:
        """Executes a domotics command (e.g. turn off studio lights)."""
        ...
```

---

## 4. Director 3: Infrastructure, Cluster & Lab Director (`InfraLabDirector`)

- **Host Node:** Cross-Node Daemon (Mac Mini M2 Pro + Arch Linux).
- **Purpose:** Ensure system sovereignty, cluster health, telemetry monitoring, and the autonomous evolution of the laboratory.
- **Key Responsibilities:**
  1. **VRAM Mutual Exclusion Coordinator:** Grants GPU leases to Fooocus while evicting Ollama models.
  2. **Cluster Health & Telemetry:** Monitors disk space on `/mnt/storage`, Ollama service availability, and Langfuse trace logs.
  3. **Obsidian Vault Integrity:** Automated backup and consistency checking of Markdown notes and bidirectional links.

### The Agent Factory (Meta-Agent System)
When the user requests a completely new operational capability (e.g. *"Nikai, quiero un nuevo agente que vigile ofertas de hardware en eBay"*), the `InfraLabDirector` activates the **Agent Factory**:

1. **Spec Generation:** Emits a new **SDD Level 2** specification in `specs/features/` or `specs/components/`.
2. **Contract Registration:** Defines Pydantic input/output schemas and sandboxed tool bindings.
3. **Router Update:** Dynamically registers the new sub-agent under the appropriate Director in [`ARCHITECTURE_PRIMER.md`](file:///home/jambsik/development/agents/Nikai/ARCHITECTURE_PRIMER.md).
4. **Approval Gate:** Presents the new agent blueprint to the user before instantiation.
