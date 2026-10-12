# 0002. Dual-Node Distributed Architecture (Arch Linux + Mac Mini M2 Pro)

- Status: accepted
- Date: 2026-10-12
- Deciders: User / Antigravity

## Context and Problem Statement
Running 24/7 background tasks (Home Assistant, network monitoring, memory backups) on a heavy GPU gaming rig (Arch Linux PC, RTX 3080) consumes excessive power (~350W under load) and creates unnecessary wear. Running image generation and LoRA training on an Apple Silicon Mac Mini lacks CUDA acceleration.

## Considered Options
- Option 1: Run everything exclusively on the Arch Linux PC.
- Option 2: Run everything exclusively on the Mac Mini M2 Pro.
- Option 3: Dual-Node Topology: Mac Mini as 24/7 Sentinel Node and PC as On-Demand Compute Node.

## Decision Outcome
Chosen: **Option 3 (Dual-Node Topology)**, because it leverages the ultra-low-power (~15W) 24/7 silent profile of the M2 Pro for sentinel duties while delegating raw CUDA tensor workloads to the RTX 3080 only when needed.

## Consequences
- Positive: Energy efficient; 24/7 home automation and notes stay online even when the PC sleeps; full CUDA power available on demand.
- Negative: Requires local networking orchestration (Caddy reverse proxy, mDNS/LAN discovery, health telemetry).
- Follow-up: Specified in `02-DOCS/wiki/sdd/specs/07-distributed-cluster-mesh.md`.
