---
type: spec
title: Spec — Distributed Dual-Node Cluster Mesh
description: WHAT and WHY for the distributed architecture across Arch Linux (GPU compute) and Mac Mini M2 Pro (24/7 hub).
tags: [sdd, spec, cluster, distributed, macmini]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 07-distributed-cluster-mesh
status: planned
---

# Spec — Distributed Dual-Node Cluster Mesh

> Slug: `07-distributed-cluster-mesh` · Status: planned · Created: 2026-10-12
> Inherits: [constitution](../constitution.md)

## Problem & why
Heavy GPU workstations (Arch Linux PC with RTX 3080) consume significant power (~350W under load) and generate noise, making them unsuitable for running 24/7 background tasks like smart home monitoring, email triage, and scheduled vault maintenance. Conversely, a low-power Apple Silicon Mac Mini M2 Pro (16 GB Unified Memory, ~15W idle) is ideal as a permanent sentinel, but lacks the raw CUDA power to render SDXL images or run heavy LoRA fine-tuning. We need a unified, resilient dual-node cluster that distributes workloads according to hardware strengths.

## Cost of not building it
Either the main PC must remain running 24/7 (wasting power and stressing hardware), or background home automation and assistant duties cease whenever the PC is powered off.

## The cheapest alternative
Running everything on only one machine. This either sacrifices 24/7 availability or sacrifices SDXL generation and GPU fine-tuning capabilities.

## Goals
- Split duties cleanly between **Node A (Arch Linux PC)** and **Node B (Mac Mini M2 Pro)**.
- Node B (Mac Mini) serves as the 24/7 Sentinel: runs Caddy reverse proxy, AdGuard DNS, Home Assistant bridge, and Obsidian Vault synchronization.
- Node A (Arch Linux) serves as the On-Demand Heavy Compute Worker: runs Fooocus SDXL, Ollama GPU models, Pipecat WebRTC, and Unsloth.
- Implement transparent service discovery over local LAN (`*.nikai.local`).
- Implement graceful node degradation: if Node A is offline or asleep, Node B handles user queries and reports that GPU compute is offline.

## Non-goals / out of scope
- Exposing the cluster to the public internet without VPN (communication remains strictly on local LAN / WireGuard / Tailscale).
- Distributed multi-node model tensor parallelism (models run locally within their respective nodes).

## Users & context
- **User:** Interacts seamlessly whether in the studio or via local network; enjoys 24/7 domotics while heavy compute sleeps when not in use.

## Behaviour
- **Main path:**
  1. Mac Mini runs 24/7 at low power (~15W).
  2. Caddy routes internal HTTPS traffic.
  3. When user requests creative generation, Conductor routes payload over local gigabit LAN to Node A (`http://pc.nikai.local:11434` or `:7865`).
  4. Node A processes request and returns artifact.
- **Failover / Sleep path:**
  1. If Node A is unreachable via HTTP ping:
  2. LifeOps Director on Node B continues handling Home Assistant routines, alarms, and voice queries, informing user: *"El nodo de renderizado GPU está en reposo; tus sistemas de hogar y notas siguen activos"*.

## Acceptance criteria
- **Given** Node B active and Node A offline, **When** health check runs, **Then** Node B reports cluster in `DEGRADED_SENTINEL_ONLY` mode without crashing.
- **Given** both nodes online, **When** cross-node REST request is issued, **Then** latency over local LAN is < 15ms.

## Points to clarify
- **suposición tomada** — La comunicación entre nodos utiliza la red LAN Gigabit local con IPs estáticas o resolución mDNS (`.local`).
