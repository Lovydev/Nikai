---
type: plan
title: Plan — Distributed Dual-Node Cluster Mesh
description: Implementation plan for cross-node networking, Caddy reverse proxy routing, and health telemetry.
tags: [sdd, plan, cluster, distributed, macmini]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 07-distributed-cluster-mesh
status: draft
---

# Plan — Distributed Dual-Node Cluster Mesh

> Spec: [../specs/07-distributed-cluster-mesh.md](../specs/07-distributed-cluster-mesh.md) · Constitution: [../constitution.md](../constitution.md) · Status: draft
> Last updated: 2026-10-12

## 0. Global Constraints

- **Node A:** Arch Linux PC (i7-11700K, RTX 3080 10GB, `/mnt/storage`).
- **Node B:** Mac Mini M2 Pro 16GB (Caddy, AdGuard, Home Assistant, 24/7).
- **Network:** LAN Gigabit connection, local TLS via Caddy internal certificates.
- **Protocol:** HTTP/2 REST and WebSockets with shared secret token header (`X-Nikai-Cluster-Token`).

## 1. Context & constraints

- Acceptance criteria from spec: Sub-15ms LAN latency, graceful degradation when Node A sleeps.
- Constitution rules in play: Principle 1 (Sovereign Local Execution), Principle 5 (Automated Verification).

## 2. Architecture

```text
┌─────────────────────────────────────────┐         ┌─────────────────────────────────────────┐
│        NODE A: ARCH LINUX (PC)          │         │        NODE B: MAC MINI M2 PRO          │
│       "Heavy Compute & Render"          │         │       "24/7 Sentinel & Network Hub"     │
├─────────────────────────────────────────┤         ├─────────────────────────────────────────┤
│ • Ollama Swarm & Qwen-VL (:11434)       │         │ • Caddy Reverse Proxy (:443)            │
│ • Fooocus SDXL Engine (:7865)           │         │ • AdGuard Home DNS Security             │
│ • Pipecat WebRTC Voice Engine           │◄───────►│ • Home Assistant Core Bridge            │
│ • Local Worker Agent Runtime            │   LAN   │ • Obsidian Vault Primary Store          │
│ • Unsloth LoRA Trainer (Offline)        │  mDNS   │ • Infra Director Health Watchdog        │
└─────────────────────────────────────────┘         └─────────────────────────────────────────┘
```

- **`ClusterNodeMonitor`** (internal) — Periodic background heartbeat checker.
- **`CrossNodeTransport`** (internal) — Authenticated `httpx.AsyncClient` wrapper routing requests by node availability.

## 3. Interfaces & contracts

```text
ClusterNodeMonitor.probe_nodes() -> ClusterHealthReport
  - returns: ClusterHealthReport(node_a_online: bool, node_b_online: bool, latency_ms: float)

CrossNodeTransport.post(target_node: str, endpoint: str, payload: dict) -> Response
  - invariant: Automatically fails over or raises NodeOfflineError if target node is unreachable.
```

## 4. Data model & flow

**ClusterHealthReport Model:**
- `status`: str (`HEALTHY` | `DEGRADED_SENTINEL_ONLY` | `OFFLINE`)
- `node_a`: Dict[str, Any] (ip, cpu_load, vram_free_mb, online)
- `node_b`: Dict[str, Any] (ip, memory_used_mb, uptime, online)
- `timestamp`: float

## 5. Tasks Breakdown & Done-Checks

- [ ] **Task 1: Caddy reverse proxy configuration template**
  - Implement `template/caddy/Caddyfile` routing domains (`nikai.local`, `fooocus.local`, `hass.local`).
  - *Done-check:* Validate Caddyfile syntax with `caddy validate --config template/caddy/Caddyfile`.

- [ ] **Task 2: Authenticated cluster transport & health watchdog**
  - Implement `src/infrastructure/cluster.py` with async health probe pinging both nodes.
  - *Done-check:* Unit tests simulating node offline state pass and confirm clean fallback report.

- [ ] **Task 3: Integration ping script**
  - Create `scripts/check_cluster.py` for CLI cluster diagnostics.
  - *Done-check:* `python3 scripts/check_cluster.py` outputs live node matrix and exits 0.
