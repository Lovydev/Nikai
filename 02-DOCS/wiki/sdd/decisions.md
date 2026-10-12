# Architecture Decision Log (ADRs)

This document tracks all architectural, infrastructural, and protocol decisions for **Nikai AI** following the **MADR 4.0.0** convention. Every significant architectural choice is documented with context, options, trade-offs, and consequences.

## Decision Index

| ID | Title | Status | Date | Decision Driver |
|:---|:------|:-------|:-----|:----------------|
| [[decisions/0001-sovereign-local-stack\|0001]] | 100% Local Sovereign Stack (No Cloud Dependencies) | Accepted | 2026-10-12 | Privacy, zero subscription, offline resilience |
| [[decisions/0002-dual-node-topology\|0002]] | Dual-Node Distributed Architecture (Arch Linux + Mac Mini) | Accepted | 2026-10-12 | RTX 3080 compute + 24/7 low-power M2 Pro node |
| [[decisions/0003-hierarchical-directors\|0003]] | Three-Director Hierarchical Multi-Agent System (HMAS) | Accepted | 2026-10-12 | Modularity, context isolation, pluggable growth |
| [[decisions/0004-vram-arbiter-lease\|0004]] | Strict Mutual Exclusion VRAM Arbiter for 10 GB Budget | Accepted | 2026-10-12 | 10 GB VRAM limit, zero CUDA OOM crashes |
| [[decisions/0005-karpathy-wiki-memory\|0005]] | Karpathy-Wiki Memory Pattern in Obsidian with Two-Tier RAM Cache | Accepted | 2026-10-12 | Human-auditable memory, sub-400ms voice loop |
| [[decisions/0006-realtime-voice-pipecat\|0006]] | Full-Duplex Real-Time Voice via Pipecat WebRTC + Kokoro ONNX | Accepted | 2026-10-12 | Natural conversational interruptions, sub-400ms latency |

---

## Decision Record Format (MADR 4.0.0)

When recording a new decision, create a new document in `02-DOCS/wiki/sdd/decisions/NNNN-<slug>.md` using the standard MADR 4.0.0 template and register it in the table above.
