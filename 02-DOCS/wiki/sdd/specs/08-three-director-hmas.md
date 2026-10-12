---
type: spec
title: Spec — Three-Director Governance System & Agent Factory
description: WHAT and WHY for domain governance across Creative, LifeOps, and Infra Directors, plus the autonomous Agent Factory.
tags: [sdd, spec, governance, directors, factory]
timestamp: 2026-10-12T11:45:00Z
topic: sdd
slug: 08-three-director-hmas
status: planned
---

# Spec — Three-Director Governance System & Agent Factory

> Slug: `08-three-director-hmas` · Status: planned · Created: 2026-10-12
> Inherits: [constitution](../constitution.md)

## Problem & why
As a local AI lab grows, an assistant cannot remain a hardcoded set of scripts. In the future, the user will want agents that manage email, handle personal finances, track health, or even specialize in coding. If these roles are ad-hoc, they will collide in state and tools. Nikai requires an architectural governance model based on **Three Domain Directors**:
1. **Creative Director:** Owns artistic creation, prompts, Fooocus, vision critiques.
2. **LifeOps Director:** Owns Home Assistant, physical domotics, calendar, personal routines, and hosts open slots for future daily-life workers.
3. **Infra & Lab Director:** Owns cluster health, VRAM leases, storage backups, and runs the **Agent Factory** (the meta-agent that generates, tests, and equips new subagents safely).

## Cost of not building it
The codebase would turn into a tangled monolith, where adding a new domotics device could break image rendering, and creating a new agent requires manual boilerplate code each time.

## The cheapest alternative
Adding flat functions inside a single python file. This makes autonomous subagent expansion impossible and causes catastrophic prompt bloat.

## Goals
- Formalize the **Director Interface** (`BaseDirector`) that every domain manager must implement.
- Implement the **Home Assistant Adapter** within the LifeOps Director to query states and actuate devices.
- Define pluggable worker slots in LifeOps Director so future personal agents (email, agenda) register without altering core code.
- Implement the **Agent Factory** in Infra Director: a meta-tool that generates new agent specifications, plans, and code following `sovereign-harness` standards.

## Non-goals / out of scope
- Pre-implementing daily life workers right now (email/calendar workers are deferred until explicitly defined by the user).
- Giving the Agent Factory permission to deploy code to production without human confirmation.

## Users & context
- **User & Autonomous Lab:** Directs high-level life and creative goals; Nikai creates and tunes workers to fulfill them.

## Behaviour
- **Main path:**
  1. Conductor delegates intent to corresponding Director.
  2. Director coordinates its internal sub-agents or tool adapters.
  3. Returns uniform `DirectorResult` back to Conductor.
- **Agent Factory path:**
  1. User asks Nikai to create a new agent (e.g. "Crea un agente que lea los logs del router").
  2. Infra Director's Agent Factory generates SDD spec and plan under `02-DOCS/wiki/sdd/`, generates conector in `01-TOOLS/`, verifies with `test_connection.py`, and presents for user approval.

## Acceptance criteria
- **Given** Home Assistant token and endpoint, **When** LifeOps Director calls `get_home_state("light.studio")`, **Then** it returns typed `EntityState` model.
- **Given** new subagent requirement, **When** Agent Factory runs `scaffold_subagent(name, role)`, **Then** it initializes directory structure with valid test file.

## Points to clarify
- **suposición tomada** — Los agentes de vida diaria específicos (emails, finanzas) no se definen ahora; se dejan los slots de registro abiertos en el LifeOps Director.
