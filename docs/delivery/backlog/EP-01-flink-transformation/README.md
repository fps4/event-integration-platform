---
title: "EP-01: Flink transformation"
summary: The Apache Flink SQL transformation runtime, its control-plane deployer, the data-model/API migration, and the removal of the legacy workers.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/decisions/0002-flink-sql-as-transformation-engine.md
  - docs/design/components/transform-engine.md
  - docs/delivery/roadmap/m1-flink-transformation-core.md
---

# EP-01: Flink transformation

The capability of transforming events with **Apache Flink SQL** as the single runtime: the execution substrate, the control-plane deployer, versioned `FlinkSqlTransform` configuration, the Control API surface, the SQL editor in the web app, and the removal of the JSONata/ksqlDB/Streams workers.

Stories accumulate here as milestones open; see the [M1 scoping doc](../../roadmap/m1-flink-transformation-core.md) for the current slice.
