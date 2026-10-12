# 0005. Karpathy-Wiki Memory Pattern in Obsidian with Two-Tier RAM Cache

- Status: accepted
- Date: 2026-10-12
- Deciders: User / Antigravity

## Context and Problem Statement
Storing long-term agent memories in black-box vector databases makes memories opaque, impossible for the human to edit or prune directly, and introduces search latencies (>800ms) that destroy real-time voice conversational flow.

## Considered Options
- Option 1: Standard vector store (ChromaDB / Pinecone) with chunked embeddings.
- Option 2: Pure in-memory context stuffing (context window limits, amnesia across restarts).
- Option 3: Karpathy-Wiki pattern in an Obsidian Vault (`/mnt/storage/nikai-data/vault/`) combined with a Two-Tier Memory architecture (Tier 1 Core RAM cache <5ms, Tier 2 Deep Vault scan on demand).

## Decision Outcome
Chosen: **Option 3 (Karpathy-Wiki + Two-Tier Cache)**, because it makes memories 100% transparent and editable in Obsidian, preserves sub-400ms voice latency for primary conversation, and provides structured markdown data for offline model fine-tuning.

## Consequences
- Positive: Total human transparency; bidirectional `[[wikilinks]]`; sub-5ms primary context lookup; dataset harvesting for Unsloth.
- Negative: Requires markdown parsing and file organization logic rather than simple vector library calls.
- Follow-up: Specified in `02-DOCS/wiki/sdd/specs/06-hybrid-memory-and-wiki.md`.
