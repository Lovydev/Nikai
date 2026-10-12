# 01-TOOLS: Sovereign External Hands

This directory holds the external interfaces, APIs, and hardware integrations for the agent.

Each tool lives in its own dedicated subfolder following a strict isolation and safety pattern:

```
01-TOOLS/
├── <tool-name>/
│   ├── README.md              # Purpose, vendor docs link, danger level
│   ├── .env.example           # Variables and keys required
│   ├── CREDENTIALS.md         # Instructions on how to obtain access safely
│   └── test_connection.py     # Deterministic script proving connectivity
```

## Safety Principle
Before any agent runs operations against an external API or database:
1. The agent must verify that `01-TOOLS/<tool>/test_connection.py` exits with code `0`.
2. For read-only connections, `test_connection.py` must also assert that write operations are rejected.
3. Secrets are NEVER committed; they live in gitignored `.env` files.
