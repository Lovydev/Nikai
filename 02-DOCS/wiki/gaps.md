# Knowledge Gaps & Open Inquiries

This document tracks unresolved architectural questions, missing documentation, pending user clarifications, and unverified assumptions discovered during agent development or vault gardening.

## Active Gaps

| ID | Domain | Inquiry / Missing Knowledge | Impact | Status | Next Step |
|:---|:---|:---|:---|:---|:---|
| GAP-001 | Hardware / LAN | Exact Mac Mini M2 Pro static LAN IP and SSH key configuration | Multi-node communication | Open | User input required |
| GAP-002 | Audio / WebRTC | Local audio input/output loopback device on Arch Linux for Pipecat | Voice pipeline testing | Open | Test PipeWire pulse device |

---

## Gap Resolution Workflow

1. **Detection:** When an agent or developer discovers missing specs, ambiguous requirements, or missing environment details, an entry is added to the table above.
2. **Investigation:** Agent checks local codebase, tools, or queries the user if clarification is needed.
3. **Closure:** Once answered or resolved, update status to `Resolved`, link the corresponding spec or ADR, and move to the **Resolved Gaps** archive below.

---

## Resolved Gaps Archive

*(No resolved gaps yet)*
