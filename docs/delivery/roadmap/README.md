---
title: Roadmap
summary: Milestones and sequencing — what each milestone ships and proves, from the Flink SQL transformation core to the web replatform and the AI assist layer.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/delivery/roadmap/m1-flink-transformation-core.md
  - docs/delivery/roadmap/m2-web-replatform.md
  - docs/delivery/roadmap/m3-agentic-assist.md
  - docs/delivery/backlog/README.md
---

# Roadmap

Milestone-level planning: what each milestone ships, what it proves, and how they sequence. Story-level decomposition lives in each milestone's scoping doc and in the [backlog](../backlog/README.md).

## Currently open scoping docs

| Milestone | Doc | Ships | Status |
|---|---|---|---|
| **M1** | [Flink transformation core](./m1-flink-transformation-core.md) | Flink SQL as the single transform runtime | proposed |
| **M2** | [Web replatform](./m2-web-replatform.md) | Web app on Next.js + shadcn/ui + Tailwind | proposed |
| **M3** | [Agentic assist](./m3-agentic-assist.md) | NL→SQL copilot, schema mapping, DLQ triage | proposed |

## Sequencing

M1 establishes the Flink SQL core that M3's AI assist builds on. M2 (web replatform) can proceed in parallel with M1 but its transform UI depends on M1's transform API. M3 follows M1 and consumes the M2 web surface.
