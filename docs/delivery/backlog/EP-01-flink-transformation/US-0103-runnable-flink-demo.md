---
title: "US-0103: Runnable Flink SQL demo with a custom UDF"
summary: A one-command, end-to-end demo that ingests, transforms with a stateful Flink SQL statement plus a custom UDF, delivers, and routes a failure to the DLQ — with a walkthrough guide.
status: draft
milestone: M1
last_updated: 2026-06-24
owners: [platform-team]
related:
  - docs/design/components/transform-engine.md
  - docs/delivery/backlog/EP-01-flink-transformation/US-0101-flink-runtime.md
  - docs/delivery/backlog/EP-01-flink-transformation/US-0102-transform-deployer.md
  - docs/delivery/roadmap/m1-flink-transformation-core.md
  - docs/delivery/issues/LIMITATION-0001-design-blueprint-stage.md
---

# US-0103: Runnable Flink SQL demo with a custom UDF

As a **reviewer or interviewer evaluating the platform**, I want to start the
platform from a clean checkout and watch a single command drive an event through
the full REST → Kafka → Flink SQL → deliver path — including a stateful operation
and a custom function — so that the Flink transformation runtime is demonstrable,
not only described (closes the gap behind LIMITATION-0001 for the transform path).

## Acceptance criteria

- **AC-1.** WHEN an operator runs the documented demo command (e.g. `make demo`) against a clean checkout THE SYSTEM SHALL register the source and target schemas, deploy the active demo `FlinkSqlTransform`, and start the HTTP source and sink connectors with no manual code edits.
  ↳ verify: demonstration · source: US-0101, US-0102
- **AC-2.** WHEN a sample event is POSTed to the HTTP source THE SYSTEM SHALL ingest it to the source topic, transform it with the active Flink SQL statement, and deliver the enriched event to the sink within the demo's stated latency budget.
  ↳ verify: test
- **AC-3.** THE demo Flink SQL statement SHALL exercise at least one stateful operation — a windowed aggregation or a stream–stream join — so the demo proves stateful stream processing, not only stateless field mapping.
  ↳ verify: test · source: transform-engine.md
- **AC-4.** THE demo SHALL register and invoke at least one custom user-defined function (a Flink scalar/aggregate UDF) from within the transform SQL, packaged and loaded by the runtime.
  ↳ verify: test · rationale: the COE-architect role this platform showcases requires demonstrable custom Flink functions, not only built-ins.
- **AC-5.** WHEN a sample event fails validation against the target schema THE SYSTEM SHALL route it to the DLQ with `x-request-id` and `x-dlq-reason`, so the demo shows the failure path alongside the happy path.
  ↳ verify: test · source: PRD-0001 AC-6
- **AC-6.** THE repository SHALL include a walkthrough guide under `docs/guides/` that an operator can follow end-to-end, listing each command and its expected output, runnable in under ten minutes.
  ↳ verify: inspection

## Non-functional

- **NFR-1.** THE demo SHALL run on a single developer machine via Docker Compose with no cloud dependency, so it is reproducible offline.
  ↳ verify: demonstration · priority: should
- **NFR-2.** THE demo SHALL use only assistive, human-initiated steps — it SHALL NOT activate any transform automatically without the operator running the documented command; the AI-assisted authoring path (NL → Flink SQL) stays out of scope here and is owned by M3.
  ↳ priority: must · rationale: keeps the demo's AI posture honest — assist is human-in-the-loop (M3), the demo proves the deterministic runtime only.

## Dependencies

- **US-0101** (Flink execution substrate) — the runtime this demo deploys onto.
- **US-0102** (transform deployer & `FlinkSqlTransform` model) — the activation path AC-1 drives.
- Existing `connector-http-source` / `connector-http-sink` services and the Schema Registry (already in `compose.yaml`).

## Out of scope

- AI authoring / mapping / DLQ-triage assist (M3, [EP-03](../EP-03-agentic-services/README.md)).
- The web SQL editor (M2-dependent, tracked in EP-01 / M2).
- Removing the legacy `worker-jsonata` (tracked separately in the M1 deliverables).
