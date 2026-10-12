# Nikai AI: Specifications Index (SDD Level 2)

This directory hosts the living technical specifications for the **Nikai AI** project, strictly adhering to the **Spec-Anchored (SDD Level 2)** pattern.

Specifications are written first to establish a complete, unambiguous technical contract before writing code, and they remain permanently as living architectural documentation.

---

## 📁 Specifications Structure

```
specs/
├── README.md                  # This index
├── architecture/              # High-level architecture decisions and system state machines
├── features/                  # End-to-end feature blueprints with full implementations
├── components/                # Discrete reusable service components, connectors, and tools
└── fixes/                     # Bug fixes and optimization specifications
```

---

## 📋 Living Specs Registry

| ID | Title | Type | Status | File Path |
| :--- | :--- | :---: | :---: | :--- |
| **SPEC-001** | Initial Workspace Setup & Sovereign Runtime | `feature` | `PENDING` | [`specs/features/001-initial-environment-setup.spec.md`](file:///home/jambsik/development/agents/Nikai/specs/features/001-initial-environment-setup.spec.md) |
| **SPEC-ARCH-01** | MAS Supervisor-Worker State Machine | `architecture` | `PROPOSED` | [`specs/architecture/01-orchestration-and-state-machine.spec.md`](file:///home/jambsik/development/agents/Nikai/specs/architecture/01-orchestration-and-state-machine.spec.md) |
| **SPEC-ARCH-02** | 10 GB VRAM Lifecycle & GPU Budget | `architecture` | `PROPOSED` | [`specs/architecture/02-vram-lifecycle-and-gpu-budget.spec.md`](file:///home/jambsik/development/agents/Nikai/specs/architecture/02-vram-lifecycle-and-gpu-budget.spec.md) |
| **SPEC-ARCH-03** | Pipecat Voice Pipeline & Kokoro af_nicole | `architecture` | `PROPOSED` | [`specs/architecture/03-pipecat-realtime-voice-kokoro.spec.md`](file:///home/jambsik/development/agents/Nikai/specs/architecture/03-pipecat-realtime-voice-kokoro.spec.md) |
| **SPEC-ARCH-04** | Qwen-VL Live Camera Observer & Critic | `architecture` | `PROPOSED` | [`specs/architecture/04-qwen-vl-camera-and-critic.spec.md`](file:///home/jambsik/development/agents/Nikai/specs/architecture/04-qwen-vl-camera-and-critic.spec.md) |
| **SPEC-ARCH-05** | Fooocus SDXL & Kohya LoRA Tooling | `architecture` | `PROPOSED` | [`specs/architecture/05-fooocus-and-kohya-sdxl-dispatcher.spec.md`](file:///home/jambsik/development/agents/Nikai/specs/architecture/05-fooocus-and-kohya-sdxl-dispatcher.spec.md) |
| **SPEC-ARCH-06** | Karpathy-Wiki Vault & Unsloth Pipeline | `architecture` | `PROPOSED` | [`specs/architecture/06-karpathy-wiki-memory-and-unsloth-training.spec.md`](file:///home/jambsik/development/agents/Nikai/specs/architecture/06-karpathy-wiki-memory-and-unsloth-training.spec.md) |
| **SPEC-ARCH-07** | Distributed Clúster (PC RTX 3080 + Mac Mini M2 Pro) | `architecture` | `PROPOSED` | [`specs/architecture/07-distributed-cluster-pc-macmini.spec.md`](file:///home/jambsik/development/agents/Nikai/specs/architecture/07-distributed-cluster-pc-macmini.spec.md) |
| **SPEC-ARCH-08** | 3-Director Governance & Agent Factory | `architecture` | `PROPOSED` | [`specs/architecture/08-three-director-governance-and-agent-factory.spec.md`](file:///home/jambsik/development/agents/Nikai/specs/architecture/08-three-director-governance-and-agent-factory.spec.md) |

---

## 🛠️ SDD Level 2 Workflow

1. **Review Specification:** Review the spec requirements, file paths, and code implementations.
2. **Execute Implementation:** Prompt an autonomous coding agent (or Claude/Gemini) to execute:
   ```bash
   # "Implement specs/features/001-initial-environment-setup.spec.md"
   ```
3. **Verify:** Run automated verification commands defined in the spec.
4. **Update Status:** Change status from `PENDING` to `IMPLEMENTED` once all tests pass.
