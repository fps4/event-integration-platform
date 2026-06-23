---
title: "LIMITATION-0001: Documentation describes the target design"
summary: The docs describe the target architecture (Flink SQL core, new web stack, AI assist); parts of the code still carry the prior design until the roadmap milestones land.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/delivery/roadmap/README.md
  - docs/design/decisions/0002-flink-sql-as-transformation-engine.md
  - docs/design/decisions/0003-web-application-stack.md
---

# LIMITATION-0001: Documentation describes the target design

## Constraint

The documentation describes the **target** design recorded in the ADRs — Apache Flink SQL as the single transform runtime ([ADR-0002](../../design/decisions/0002-flink-sql-as-transformation-engine.md)), the web app on the standard open-source stack ([ADR-0003](../../design/decisions/0003-web-application-stack.md)), and the AI assist layer ([ADR-0004](../../design/decisions/0004-agentic-capabilities.md)).

Parts of the codebase still reflect the prior design (e.g. a JSONata worker, an MUI-based web app) until the corresponding [roadmap](../roadmap/README.md) milestones land. Where names differ, the ADRs and `CODEBASE.md` note the migration.

## Why accepted

The docs are refactored ahead of the implementation so the design, decisions, and delivery plan are agreed and public-ready first. This limitation is `current` until M1–M3 close, at which point the code and docs converge.
