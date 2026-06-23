---
title: "M1: Flink transformation core"
summary: Make Apache Flink SQL the single transformation runtime — runtime, control-plane deployer, data-model and API migration, and a SQL transform UI.
status: proposed
milestone: M1
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/decisions/0002-flink-sql-as-transformation-engine.md
  - docs/delivery/backlog/EP-01-flink-transformation/README.md
---

# M1: Flink transformation core

## What it ships

Apache Flink SQL as the single transformation runtime, replacing the JSONata worker ([ADR-0002](../../design/decisions/0002-flink-sql-as-transformation-engine.md)).

## Deliverables → stories

| Deliverable | Stories |
|---|---|
| Flink execution substrate (cluster/SQL Gateway) in Compose | [US-0101](../backlog/EP-01-flink-transformation/US-0101-flink-runtime.md) |
| Control-plane transform deployer + `FlinkSqlTransform` model | [US-0102](../backlog/EP-01-flink-transformation/US-0102-transform-deployer.md) |
| Control API `*/flink-transforms` + dry-run validate | (EP-01) |
| Web app Flink SQL editor with dry-run preview | (EP-01, depends on M2) |
| Remove `worker-jsonata` / `worker-ksqldb` / `worker-streams` | (EP-01) |

## What it does / does not ship

- **Does:** versioned Flink SQL transforms bound to source/target topics; schema-registry validation; DLQ on failure; dry-run.
- **Does not:** AI authoring of SQL (M3); the full web replatform (M2).

## What it proves

A pipeline can ingest, transform with Flink SQL, validate against a schema, deliver, and route failures to a DLQ — all configured, no bespoke code.

## Definition of complete

The JSONata worker is removed; an end-to-end pipeline runs on Flink SQL with dry-run and DLQ; the data model and Control API use `FlinkSqlTransform`/`flink-transforms`.

## Open questions

- Flink deployment shape locally vs prod (SQL Gateway vs full cluster) and state-backend defaults.
