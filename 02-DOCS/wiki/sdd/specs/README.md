# Nikai AI: Canonical SDD Specifications Index

This directory hosts the canonical **Spec-Driven Development (SDD)** specifications for **Nikai AI**, adhering strictly to the `sovereign-harness` SDD methodology.

Every feature or architectural component is divided into:
1. **`specs/<slug>.md`** — The **WHAT** and **WHY** contract (Problem, Goals, Non-goals, Acceptance Criteria Given/When/Then).
2. **`plans/<slug>.md`** — The **HOW** architecture (Interfaces, Data Flows, and Ordered Tasks with executable Done-Checks).

---

## 📋 Canonical SDD Registry

| Slug | Domain | Spec (WHAT & WHY) | Plan & Tasks (HOW & EXECUTION) | Status |
|:---|:---|:---|:---|:---:|
| `01-runtime-environment` | Runtime | [[specs/01-runtime-environment\|01-runtime-environment.md]] | [[plans/01-runtime-environment\|01-runtime-environment.md]] | `planned` |
| `02-orchestrator-core` | Orchestration | [[specs/02-orchestrator-core\|02-orchestrator-core.md]] | [[plans/02-orchestrator-core\|02-orchestrator-core.md]] | `planned` |
| `03-vram-gpu-arbiter` | Hardware / GPU | [[specs/03-vram-gpu-arbiter\|03-vram-gpu-arbiter.md]] | [[plans/03-vram-gpu-arbiter\|03-vram-gpu-arbiter.md]] | `planned` |
| `04-voice-and-vision-pipeline` | Audio / Vision | [[specs/04-voice-and-vision-pipeline\|04-voice-and-vision-pipeline.md]] | [[plans/04-voice-and-vision-pipeline\|04-voice-and-vision-pipeline.md]] | `planned` |
| `05-creative-studio-engine` | Studio / SDXL | [[specs/05-creative-studio-engine\|05-creative-studio-engine.md]] | [[plans/05-creative-studio-engine\|05-creative-studio-engine.md]] | `planned` |
| `06-hybrid-memory-and-wiki` | Memory / Vault | [[specs/06-hybrid-memory-and-wiki\|06-hybrid-memory-and-wiki.md]] | [[plans/06-hybrid-memory-and-wiki\|06-hybrid-memory-and-wiki.md]] | `planned` |
| `07-distributed-cluster-mesh` | Infrastructure | [[specs/07-distributed-cluster-mesh\|07-distributed-cluster-mesh.md]] | [[plans/07-distributed-cluster-mesh\|07-distributed-cluster-mesh.md]] | `planned` |
| `08-three-director-hmas` | Governance | [[specs/08-three-director-hmas\|08-three-director-hmas.md]] | [[plans/08-three-director-hmas\|08-three-director-hmas.md]] | `planned` |

---

## 🏛️ Governance Documents

- [[constitution|Project Constitution (Non-Negotiable Principles)]]
- [[config|SDD Runtime Configuration]]
- [[decisions|Architecture Decision Records (MADR 4.0.0 Log)]]

---

## 🛠️ Canonical SDD Execution Flow

1. **Verify Constitution:** Ensure the task does not violate any numbered principle in `constitution.md`.
2. **Review Spec & Plan:** Confirm acceptance criteria and task breakdown with the human.
3. **Implement Task by Task:** Execute in discrete TDD steps, running the specified done-check command after each task.
4. **Automated Verification:** All test suites and tool connectors must exit with code `0`.
