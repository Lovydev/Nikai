# ARCHITECTURE_PRIMER.md // Nikai AI Master System Context

> **To Any AI Model or Autonomous Agent Implementing This Codebase:**  
> **READ THIS ROUTING GUIDE FIRST.** Do NOT read all files in this repository at once. Use the **Architecture Router Table** below to locate and read ONLY the specific architectural blueprint relevant to the task you are assigned.

---

## 🧭 1. System Identity, Clúster Topology & Mission

**Nikai AI** is a 100% sovereign, local autonomous multi-agent operating system distributed across a **two-node local clúster**:

```
┌─────────────────────────────────────────┐         ┌─────────────────────────────────────────┐
│         NODO 1: ARCH LINUX (PC)         │         │        NODO 2: MAC MINI M2 PRO          │
│       "Compute & Rendering Muscle"      │         │       "24/7 Sentinel & Network Hub"     │
├─────────────────────────────────────────┤         ├─────────────────────────────────────────┤
│ • CPU: Intel i7-11700K (16T), 32 GB RAM │         │ • Apple Silicon M2 Pro (16 GB Unified)  │
│ • GPU: NVIDIA RTX 3080 (10 GB VRAM)     │         │ • Bandwidth: ~200 GB/s Unified Memory   │
│ • Storage: /mnt/storage (1 TB NVMe)     │         │ • Power: ~15W (Silent 24/7 Operation)   │
│ • Core Capabilities:                    │         │ • Core Capabilities:                    │
│   - Fooocus SDXL Engine (Port 7865)     │         │   - Caddy HTTPS Reverse Proxy           │
│   - Kohya_ss LoRA Trainer               │         │   - AdGuard Home DNS Security           │
│   - Ollama Swarm & Qwen-VL (Port 11434) │         │   - Home Assistant (Domótica / IoT)     │
│   - Pipecat WebRTC Voice Conductor      │         │   - Obsidian Karpathy-Wiki Vault Host   │
│   - Unsloth Offline Fine-tuning         │         │   - Langfuse Local Telemetry (Docker)   │
└────────────────────┬────────────────────┘         └────────────────────┬────────────────────┘
                     │                                                   │
                     └──────────────── Local Gigabit LAN ────────────────┘
```

### Core Architecture Pillars:
- **Real-Time Duplex Voice (<400ms):** Pipecat AI WebRTC, Silero VAD (<50ms barge-in), and Kokoro-82M ONNX in CPU (voice `af_nicole`).
- **Multimodal Camera Vision:** "Look and Tell" real-time room perception and post-render artistic critique via Qwen-VL (`qwen3-vl:4b`).
- **High-Fidelity Diffusion:** Stable Diffusion XL generation via Fooocus with custom LoRAs trained in Kohya_ss.
- **Living Memory (Karpathy-Wiki Pattern):** Interlinked Obsidian Markdown vault on `/mnt/storage` where both Nikai and the human curate, update, and explore knowledge without black-box lock-in.
- **Three-Director Governance System (HMAS):** The system divides operational domains into 3 autonomous directors under an Executive Vocal Lead:
  1. *Creative Director (Studio Creativo)*
  2. *Personal Life & Operations Director (Vida & Domótica Home Assistant - Pluggable worker slots)*
  3. *Infrastructure, Cluster & Lab Director (Meta-Agent Hub con Agent Factory para autogenerar nuevos agentes)*.
- **Dual-Track Offline Learning:** Dynamic in-context profile updating combined with automated dataset harvesting for periodic local fine-tuning using Unsloth (LLMs) and Kohya (LoRAs) on the RTX 3080.

---

## 🗺️ 2. Architecture Router Table (Canonical SDD)

Consult this routing table to determine which living specification and technical plan to read before generating code:

| What you need to build / implement | Spec (WHAT & WHY) | Technical Plan & Tasks (HOW) | Core Focus & Responsibilities |
| :--- | :--- | :--- | :--- |
| **Initial Environment & Preflight** | [`02-DOCS/wiki/sdd/specs/01-runtime-environment.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/specs/01-runtime-environment.md) | [`plans/01-runtime-environment.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/plans/01-runtime-environment.md) | Python 3.10, `.env`, Pydantic Settings, preflight diagnostics. |
| **Orchestrator Core & State Machine** | [`02-DOCS/wiki/sdd/specs/02-orchestrator-core.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/specs/02-orchestrator-core.md) | [`plans/02-orchestrator-core.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/plans/02-orchestrator-core.md) | Hierarchical Multi-Agent System, executive dispatching, FSM loops. |
| **GPU VRAM Lease & Memory Arbiter** | [`02-DOCS/wiki/sdd/specs/03-vram-gpu-arbiter.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/specs/03-vram-gpu-arbiter.md) | [`plans/03-vram-gpu-arbiter.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/plans/03-vram-gpu-arbiter.md) | 10 GB RTX 3080 budget, `keep_alive: 0` before Fooocus SDXL render. |
| **Pipecat Voice & Vision Critic** | [`02-DOCS/wiki/sdd/specs/04-voice-and-vision-pipeline.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/specs/04-voice-and-vision-pipeline.md) | [`plans/04-voice-and-vision-pipeline.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/plans/04-voice-and-vision-pipeline.md) | WebRTC duplex streaming, Silero VAD, Kokoro ONNX, Qwen-VL critic. |
| **Creative Studio & Fooocus SDXL** | [`02-DOCS/wiki/sdd/specs/05-creative-studio-engine.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/specs/05-creative-studio-engine.md) | [`plans/05-creative-studio-engine.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/plans/05-creative-studio-engine.md) | Async Fooocus API client (port 7865), LoRA compounding, render store. |
| **Obsidian Karpathy-Wiki & Unsloth** | [`02-DOCS/wiki/sdd/specs/06-hybrid-memory-and-wiki.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/specs/06-hybrid-memory-and-wiki.md) | [`plans/06-hybrid-memory-and-wiki.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/plans/06-hybrid-memory-and-wiki.md) | Two-tier memory (<5ms RAM cache + vault on-demand), Unsloth datasets. |
| **Two-Node Distributed Clúster** | [`02-DOCS/wiki/sdd/specs/07-distributed-cluster-mesh.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/specs/07-distributed-cluster-mesh.md) | [`plans/07-distributed-cluster-mesh.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/plans/07-distributed-cluster-mesh.md) | Workload split: Arch Linux (RTX 3080 compute) + Mac Mini M2 Pro (24/7). |
| **3-Director Governance & Agent Factory**| [`02-DOCS/wiki/sdd/specs/08-three-director-hmas.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/specs/08-three-director-hmas.md) | [`plans/08-three-director-hmas.md`](file:///home/jambsik/development/agents/Nikai/02-DOCS/wiki/sdd/plans/08-three-director-hmas.md) | Creative, LifeOps (Home Assistant), Infra/Lab & Meta-Agent spawner. |

---

## 🔒 3. Inviolable Architectural Boundaries

1. **Strict 100% Sovereign Local Execution:**  
   Zero calls to OpenAI, Anthropic, or external cloud inference. All LLM and vision calls go through local Ollama (`127.0.0.1:11434`), Kokoro runs on local CPU ONNX, and Fooocus runs on local port `7865`.
2. **NVIDIA RTX 3080 Hardware Budget (10,240 MiB VRAM):**  
   Never run Fooocus SDXL rendering and heavy 8B+ LLM inference simultaneously in VRAM. The `VRAMLifecycleManager` must evict Ollama models (`keep_alive: 0`) before SDXL rendering.
3. **Storage Discipline:**  
   All heavy assets (models, LoRAs, renders, Obsidian memory vault) reside on `/mnt/storage` on the PC, or synchronized to the Mac Mini for 24/7 access.
4. **SDD Level 2 (Spec-Anchored) Discipline:**  
   Never write ad-hoc code without an approved specification in `specs/`. When you finish implementing a spec, verify it against the acceptance criteria before marking it `IMPLEMENTED`.
