---
title: "0002: Apache Flink SQL as the transformation engine"
summary: Apache Flink SQL is the single transformation runtime; JSONata, ksqlDB, and Kafka Streams are removed from the design.
status: accepted
date: 2026-06-23
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/transform-engine.md
  - docs/design/components/data-models.md
  - docs/design/components/control-api.md
  - docs/design/decisions/0004-agentic-capabilities.md
---

# 0002: Apache Flink SQL as the transformation engine

## Context

The platform's earlier design carried three transformation runtimes: a JavaScript **JSONata** worker (the initial implementation), with **ksqlDB** and **Kafka Streams** workers on the roadmap. This created three problems:

- **JSONata is too limited.** It handles field reshaping but cannot do joins, windows, stateful enrichment, or aggregation — the operations real integrations need. It is also a niche expression language with a small talent pool.
- **Three runtimes is three of everything.** Three execution models, three sets of operational behaviour, three things to document, secure, and support — for a platform whose principle is *configuration over code*.
- **The market has converged on streaming SQL.** Comparable platforms express transformations as SQL and increasingly call model inference, anomaly detection, and forecasting as SQL functions inside the stream. A SQL-first core is both the industry baseline and the foundation for the AI assist layer ([ADR-0004](./0004-agentic-capabilities.md)).

We need one runtime that covers stateless mapping through stateful, windowed, joined enrichment; integrates with the Schema Registry; is widely known; and can host AI inference functions.

## Decision

**Apache Flink SQL is the single transformation runtime.** A transform is a versioned Flink SQL statement bound to a source stream and a target stream, with status `draft|active|deprecated`. The platform runs a Flink execution substrate (a Flink cluster or SQL Gateway) and a thin control-plane deployer that materializes the active statement per pipeline. Output is validated against the destination schema; failures land in the DLQ with context. AI model inference is exposed as Flink SQL functions.

JSONata, ksqlDB, and Kafka Streams are **removed** from the design.

### Considered and rejected

- **JSONata (status quo)** — rejected: no joins/windows/state; niche language; can't host SQL-native inference.
- **ksqlDB** — rejected: narrower than Flink SQL, weaker stateful/processing story, and a smaller, more vendor-tied ecosystem.
- **Kafka Streams (Java)** — rejected: it's *code*, not configuration; it pushes transformation ownership back onto engineering and breaks the configuration-over-code principle for product teams.

## Consequences

- **Removed:** the `worker-jsonata`, `worker-ksqldb`, and `worker-streams` services; the `JsonataTransform` model.
- **Added:** a `transform-runtime` service over Apache Flink; a control-plane transform deployer.
- **Data model:** `JsonataTransform` → `FlinkSqlTransform`; the inline `TransformConfig.expression` becomes `TransformConfig.sql`; `type` is `flink-sql`.
- **Control API:** `*/jsonata-transforms` endpoints become `*/flink-transforms`, plus a dry-run/validate endpoint.
- **Web app:** the transform UI is a Flink SQL editor with dry-run preview, not a JSONata expression box.
- **DLQ semantics preserved:** failure handling, headers, and replay are unchanged.
- **New operational surface:** Flink brings state backends, checkpointing, and cluster sizing to operate — sane per-environment defaults mitigate this.
- **Enables the AI layer:** NL→SQL authoring and in-SQL inference build directly on this core.
