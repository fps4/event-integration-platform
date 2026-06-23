---
title: Backlog
summary: Epics and user stories — the structural capability buckets that accumulate stories as milestones open.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/delivery/backlog/EP-01-flink-transformation/README.md
  - docs/delivery/backlog/EP-02-web-replatform/README.md
  - docs/delivery/backlog/EP-03-agentic-services/README.md
  - docs/delivery/roadmap/README.md
---

# Backlog

Epics are product-capability buckets that outlive any one milestone; each story carries a `milestone:` and an EARS-form acceptance set. Milestone slicing happens in the [roadmap](../roadmap/README.md) scoping docs.

## Epics

| Epic | Capability |
|---|---|
| [EP-01: Flink transformation](./EP-01-flink-transformation/README.md) | The Flink SQL transformation runtime and its control-plane integration. |
| [EP-02: Web replatform](./EP-02-web-replatform/README.md) | The web control surface on the standard open-source stack. |
| [EP-03: Agentic services](./EP-03-agentic-services/README.md) | The AI assist layer and its governance. |

## Story lifecycle

`draft → ready → in_progress → done` (with `cancelled` as the other terminal state). `draft → ready` is human-only (scope is locked); `in_progress → done` happens on a green merge.
