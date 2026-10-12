---
id: SPEC-ARCH-01
title: Hierarchical Multi-Agent System (HMAS) Orchestration & State Machine
type: architecture
status: PROPOSED
created: 2026-10-11
updated: 2026-10-11
author: spec-writer
ai_mode: true
source: System Design
---

# Architecture Specification: HMAS Orchestration & State Machine

> **AI Implementation Mode**: Architectural blueprint for Nikai's 3-Director hierarchical multi-agent state machine and cross-node coordination.

---

## 1. System Topology & Hierarchical Governance (HMAS)

To prevent context collapse and allow infinite modular growth across domains, Nikai adopts a **Hierarchical Multi-Agent System (HMAS)** with an Executive Vocal Conductor and 3 Domain Directors:

```
                          ┌───────────────────────────┐
                          │   Nikai Core Conductor    │
                          │   (Executive Voice Lead)  │
                          │        llama3.1:8b        │
                          └─────────────┬─────────────┘
                                        │
        ┌───────────────────────────────┼───────────────────────────────┐
        ▼                               ▼                               ▼
┌───────────────────────┐   ┌───────────────────────┐   ┌───────────────────────┐
│   Creative Director   │   │   Life Ops Director   │   │  Infra & Lab Director │
│  (Studio Creativo)    │   │  (Vida & Domótica)    │   │   (Meta-Agent Hub)    │
│  [Node: Arch / GPU]   │   │  [Node: Mac Mini 24/7]│   │  [Node: Mac Mini / PC]│
├───────────────────────┤   ├───────────────────────┤   ├───────────────────────┤
│ • El Visionario (12B) │   │ • Home Assistant      │   │ • VRAM Lease Manager  │
│ • Art Director (7B)   │   │   Integration Bridge  │   │ • Node Health & Telemetry│
│ • Qwen-VL Critic (4B) │   │ • Day-to-Day Workers  │   │ • Agent Factory       │
│ • Fooocus SDXL Engine │   │   (Pluggable slots)   │   │   (Creates new agents)│
└───────────────────────┘   └───────────────────────┘   └───────────────────────┘
```

---

## 2. High-Level Executive State Machine (FSM)

```
[VOICE_INPUT_RECEIVED]
          │
          ▼
[INTENT_DISPATCH] ───► High-level classification by Core Conductor (Llama 3.1)
          │
          ├─────────────────────────┬─────────────────────────┐
          ▼                         ▼                         ▼
   [CREATIVE_DOMAIN]        [LIFE_OPS_DOMAIN]          [INFRA_DOMAIN]
   (Dispatched to            (Dispatched to             (Dispatched to
    Creative Director)        Life Ops Director)         Infra & Lab Director)
          │                         │                         │
          ▼                         ▼                         ▼
  [Studio FSM:              [Home Assistant Bridge:    [Cluster Health /
   Ideate -> Prompt ->       Query States -> Actuate    VRAM Mutex /
   Render -> Critic]         -> Log to Obsidian Vault]  Agent Factory]
          │                         │                         │
          └─────────────────────────┼─────────────────────────┘
                                    │
                                    ▼
                         [SYNTHESIS_AND_PRESENTATION]
                         (Core Conductor replies via Kokoro af_nicole)
```

---

## 3. Creative Studio Sub-FSM (Arch Linux / RTX 3080)

When the Creative Director is engaged:

```
[CREATIVE_DISCOVERY] ──► [DIVERGENT_IDEATION] (Visionary generates 3 avenues)
                                │
                                ▼
[CONVERGENT_SELECTION] ◄── [USER_ALIGNMENT]
         │
(Art Director compiles SDXL parameters & LoRAs)
         │
         ▼
[IMAGE_GENERATION] ──► [CRITIC_EVALUATION] (Qwen-VL likeness & anatomy)
                                │
         ┌──────────────────────┴──────────────────────┐
         ▼ (Approved)                                  ▼ (Issues detected)
  [STUDIO_SUCCESS]                              [IMAGE_GENERATION] (Max 2 retries)
```

---

## 4. Director Roles & Responsibilities

| Director Entity | Host Node | Model / Runtime | Primary Scope |
| :--- | :--- | :--- | :--- |
| **Nikai Core Conductor** | Arch Linux / PC | `llama3.1:8b` (Pipecat) | Conversational voice partner, empathetic active listening, top-level intent routing. |
| **Creative Director** | Arch Linux (RTX 3080) | Ollama Multi-Agent Swarm | Governs the artistic studio: ideation, photographic syntax, Fooocus diffusion, visual critique. |
| **Life Ops Director** | Mac Mini M2 Pro (24/7) | Python Async / Home Assistant | Governs home routines, Home Assistant entity states, personal reminders, daily life context. |
| **Infra & Lab Director** | Mac Mini M2 Pro / Arch | Python Daemon / Langfuse | Governs cluster health, GPU VRAM leases, Obsidian Vault backups, and spawns new sub-agents via the Agent Factory. |
