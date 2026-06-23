---
title: Design — architecture & decisions
summary: The how & why-decided — architecture overview, component designs, contracts, and architecture decision records.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/overview.md
  - docs/design/decisions/0001-record-architecture-decisions.md
---

# Design

The **how & why-decided** of the platform.

- **Overview** — the high-level architecture, principles, and C4 diagrams.
- **Components** (`components/`) — per-service/per-package designs.
- **Contracts** (`contracts/`) — API and integration contracts (OpenAPI strategy).
- **Decisions** (`decisions/`) — architecture decision records (ADRs), immutable once accepted.

## How to read this

Start with the **overview**, then drill into a **component**. Cross-cutting choices (Flink SQL, web stack, agentic capabilities) are recorded as **ADRs** — read those to understand *why* the design is the way it is.
