# 0004. Strict Mutual Exclusion VRAM Arbiter for 10 GB Budget

- Status: accepted
- Date: 2026-10-12
- Deciders: User / Antigravity

## Context and Problem Statement
The NVIDIA RTX 3080 has a hard ceiling of 10,240 MiB VRAM. Simultaneously loading an active Ollama LLM context (~5,200 MiB) and running a Fooocus SDXL render (~8,500 MiB) exceeds available memory and causes CUDA Out-Of-Memory (OOM) fatal crashes.

## Considered Options
- Option 1: Run models in lowvram / CPU offload mode (severe performance degradation: 5+ minutes per image).
- Option 2: Allow unrestricted concurrent calls and rely on OS swap (unpredictable latency, desktop freezing).
- Option 3: Implement an asynchronous `VRAMArbiter` mutual exclusion lease protocol with dynamic Ollama model eviction (`keep_alive: 0`).

## Decision Outcome
Chosen: **Option 3 (VRAM Arbiter with Dynamic Eviction)**, because it ensures Fooocus has access to full GPU memory during render passes while restoring conversational models immediately upon render completion.

## Consequences
- Positive: Zero OOM crashes; full SDXL rendering speed (~15-25 seconds per image); stable desktop Hyprland compositor.
- Negative: Re-warming models after render adds ~1.5s latency for the first subsequent LLM token.
- Follow-up: Specified in `02-DOCS/wiki/sdd/specs/03-vram-gpu-arbiter.md`.
