---
id: SPEC-ARCH-07
title: Distributed Two-Node Local Sovereign Cluster: Arch Linux & Mac Mini M2 Pro
type: architecture
status: PROPOSED
created: 2026-10-11
updated: 2026-10-11
author: spec-writer
ai_mode: true
source: Infrastructure Architecture Decision
---

# Architecture Specification: Distributed Cluster (PC RTX 3080 + Mac Mini M2 Pro)

> **AI Implementation Mode**: Architectural blueprint for distributing services across Arch Linux (heavy compute) and the Mac Mini M2 Pro (24/7 sovereign hub).

---

## 1. Node Topology & Workload Allocation

```
┌─────────────────────────────────────────┐         ┌─────────────────────────────────────────┐
│         NODO 1: ARCH LINUX (PC)         │         │        NODO 2: MAC MINI M2 PRO          │
│       "Compute & Rendering Muscle"      │         │       "24/7 Sentinel & Network Hub"     │
├─────────────────────────────────────────┤         ├─────────────────────────────────────────┤
│ • CPU: Intel i7-11700K (16T), 32 GB RAM │         │ • Apple Silicon M2 Pro (16 GB Unified)  │
│ • GPU: NVIDIA RTX 3080 (10 GB VRAM)     │         │ • Bandwidth: ~200 GB/s Unified Memory   │
│ • Storage: /mnt/storage (1 TB NVMe)     │         │ • Power: ~15W (Silent 24/7 Operation)   │
│ • Services:                             │         │ • Services:                             │
│   - Fooocus SDXL Engine (Port 7865)     │         │   - Caddy HTTPS Reverse Proxy           │
│   - Kohya_ss LoRA Trainer               │         │   - AdGuard Home DNS Security           │
│   - Ollama Swarm & Qwen-VL (Port 11434) │         │   - Home Assistant (Domótica / IoT)     │
│   - Pipecat WebRTC Voice Conductor      │         │   - Obsidian Karpathy-Wiki Vault Host   │
│   - Unsloth Offline Fine-tuning         │         │   - Langfuse Local Telemetry (Docker)   │
└────────────────────┬────────────────────┘         └────────────────────┬────────────────────┘
                     │                                                   │
                     └──────────────── Local Gigabit LAN ────────────────┘
```

---

## 2. Cross-Node Service Communication

1. **Gateway & TLS Termination (Caddy on Mac Mini):**
   - Caddy handles clean local domain resolution (e.g. `https://nikai.local`, `https://fooocus.local`, `https://hass.local`) using local SSL certificates.
   - AdGuard Home provides local DNS rewrites pointing `*.nikai.local` to the appropriate node.
2. **REST & WebSocket Cross-Calls:**
   - The Life Ops Director running 24/7 on the Mac Mini reaches the PC via local IP (`http://pc.nikai.local:11434`) when GPU compute is online.
   - When the PC is in low-power or offline mode, the Mac Mini continues running Home Assistant routines, background email summaries, and maintains the Obsidian Vault without interruption.
3. **Storage & Vault Synchronization:**
   - The master Obsidian Vault is hosted on the Mac Mini or synchronized via Syncthing/rsync to `/mnt/storage/nikai-data/vault/` on the PC.
   - The PC's secondary NVMe (`/mnt/storage`) serves as the cold backup target for periodic clúster snapshots.

---

## 3. Node Health & Failover Strategy

- If the PC with the RTX 3080 is sleeping or busy with offline LoRA training:
  - Nikai gracefully notifies the user via audio/chat: *"El estudio de renderizado GPU está en reposo o entrenando; las funciones de vida y domótica permanecen 100% operativas en el Mac Mini"*.
- The Infra Director continuously monitors node availability via lightweight HTTP health probes.
