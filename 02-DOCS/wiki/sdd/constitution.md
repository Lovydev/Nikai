# Project Constitution: Non-Negotiable Principles

*Every feature specification, technical plan, task list, and diff in this repository must satisfy these numbered principles. A change that violates any principle is broken by definition.*

---

## 1. Sovereign Local Execution
No proprietary cloud inference APIs (OpenAI, Anthropic, etc.) are called in production runtime. All LLMs, vision, embeddings, and speech run on local hardware (Ollama, ONNX, PyTorch, Fooocus).

## 2. Hardware Budget & Memory Safety
Hardware constraints (e.g. 10 GB VRAM limit on NVIDIA RTX 3080) are strictly respected. Heavy services (Ollama and Fooocus SDXL) must never run concurrently without mutual exclusion leases (`keep_alive: 0` before SDXL renders).

## 3. Storage Hierarchy Discipline
Code lives in the repository. Heavy model weights, LoRA checkpoints, generated renders, and the master Obsidian knowledge vault reside on dedicated fast storage (`/mnt/storage`).

## 4. Spec-Driven Development (SDD) Discipline
The artifact is the contract. No non-trivial code is written without an approved specification in `wiki/sdd/specs/` and a verifiable technical plan in `wiki/sdd/plans/`.

## 5. Automated Verification Floor
Every component, tool connector, and service must expose a deterministic verification command or test suite that exits with code `0`. Tests are asserted against observed reality, never against intention.
