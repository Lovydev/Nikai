# 0003. Three-Director Hierarchical Multi-Agent System (HMAS)

- Status: accepted
- Date: 2026-10-12
- Deciders: User / Antigravity

## Context and Problem Statement
Nikai will manage visual creative production, home domotics, personal productivity, and autonomous meta-agent evolution. A flat agent pool or a single orchestrator suffers from context saturation, tool confusion, and severe latency degradation on local 8B models.

## Considered Options
- Option 1: Monolithic Agent with dozens of tool schemas.
- Option 2: Decentralized Swarm (P2P message bus between all agents).
- Option 3: Hierarchical Multi-Agent System (HMAS) with an Executive Core Conductor and Three Specialized Domain Directors (Creative, LifeOps, Infra).

## Decision Outcome
Chosen: **Option 3 (Three-Director HMAS)**, because it isolates domain contexts, provides clear boundaries of responsibility, and allows pluggable growth (e.g. LifeOps worker slots, Infra Agent Factory) without modifying core orchestration.

## Consequences
- Positive: Modular domain isolation; sub-second intent routing; pluggable agent growth.
- Negative: Requires structured handoffs and standardized `DirectorResult` contracts.
- Follow-up: Specified in `02-DOCS/wiki/sdd/specs/08-three-director-hmas.md`.
