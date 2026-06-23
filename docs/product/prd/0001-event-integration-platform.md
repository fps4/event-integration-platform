---
title: "PRD-0001: Event Integration Platform"
summary: Product requirements for the end-to-end REST → Kafka → Flink SQL → deliver platform, configured via UI, with replay/DLQ, schema governance, and AI assistance.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/product/vision.md
  - docs/product/personas.md
  - docs/design/overview.md
  - docs/design/decisions/0002-flink-sql-as-transformation-engine.md
spec:
  feature: event-integration-platform
  kind: functional_spec
  summary: >
    An integration platform where a product team configures an end-to-end event flow through a
    web UI: a REST endpoint ingests events into Kafka, a versioned Flink SQL transform validates
    and reshapes them against registered schemas, and connectors deliver the result to REST or S3
    destinations. Failures land in a dead-letter queue with full context and can be replayed after
    a fix. Schemas, transforms, and configuration are versioned and audited, and an AI assist layer
    helps author transforms, map fields, and triage failures.
---

# PRD-0001: Event Integration Platform

## Purpose

Define the end-to-end integration experience — defined and operated through a UI — covering REST ingest, schema binding & validation, Flink SQL transformation, multi-destination delivery, and DLQ/replay, with clear ownership boundaries and AI assistance.

## Scope

In scope: the create-integration wizard, schema catalog, replay/DLQ management, runtime visibility, and the requirements they imply. Out of scope: the platform's internal operations tooling (operators use their organization's centralized observability stack — see [observability](../../design/components/observability.md)).

## Core UI flows

### Create integration (wizard)

1. **Source (REST ingest)** — endpoint `POST /ingest/:topic`; topic validated; requires `Content-Type: application/json` and a non-empty JSON object/array; `x-request-id` propagated; responds `202`.
2. **Kafka topics (auto-generated)** — source, transformed, and DLQ topics named per the [topic convention](../../reference/topic-naming.md); retention/compaction/partitioning shown read-only.
3. **Transformation (Flink SQL)** — author or select a versioned **Flink SQL** statement; pin an explicit version; optional PII masking/redaction; destination schema selection; dry-run preview (sample input → output). The AI copilot can draft the SQL from a natural-language description.
4. **Destinations (multi-select)** — REST sink (URL, auth, retry/backoff, timeout, idempotency key, headers) and/or S3 sink (bucket/region, prefix template, format, compression, batch size & flush).
5. **Observability & alerts** — alert routes (Slack/email/PagerDuty) and thresholds (consumer lag, DLQ rate, destination failure rate).
6. **Access & governance** — environment (dev/test/prod) and RBAC (who can edit, replay, approve promotion).

### Schema catalog
Search/filter by name, domain, tags, PII flag; lifecycle Draft → Active → Deprecated; versioning with compatibility policy and diff view; impact visibility (topics and integrations depending on a schema).

### Replay & DLQ management
Select by time window or correlation ID(s); rate-limit; pin target schema/mapping versions; impact preview; production warnings; dry-run validation. The AI DLQ-triage agent explains failures and proposes fixes.

### Runtime visibility
Integration status (active/paused/failed) and a per-message trace across ingress → transform → delivery/DLQ, linking to the schema, transform version, and config snapshot used.

## Acceptance criteria (EARS)

- **AC-1.** WHEN a caller `POST`s to `/ingest/:topic` with `Content-Type: application/json` and a non-empty JSON object/array THE SYSTEM SHALL accept the request, attach or propagate `x-request-id`, and respond `202 Accepted`.
- **AC-2.** WHEN an ingest request omits `Content-Type: application/json` or carries an empty/invalid body THE SYSTEM SHALL reject it with `415` or `400` and not produce to Kafka.
- **AC-3.** WHEN a topic name violates the naming rules THE SYSTEM SHALL reject the request and surface the validation error.
- **AC-4.** THE SYSTEM SHALL attach a schema ID/version to every message produced to an enriched topic.
- **AC-5.** WHEN an active Flink SQL transform processes a source record THE SYSTEM SHALL validate the output against the destination schema and produce to the configured target topic.
- **AC-6.** WHEN a transform or validation fails THE SYSTEM SHALL write the record to the integration's DLQ with failure context (including `x-request-id` and `x-dlq-reason`).
- **AC-7.** WHEN a user replays from a DLQ or time window THE SYSTEM SHALL apply the pinned schema and transform versions, support a dry-run that produces nothing, and record the replay in the audit trail.
- **AC-8.** WHEN a user pins a transform or schema version THE SYSTEM SHALL use that explicit version and never silently resolve to "latest".
- **AC-9.** WHEN a schema change is breaking THE SYSTEM SHALL block it by default and require an explicit override accompanied by a blast-radius view.
- **AC-10.** THE SYSTEM SHALL version and audit every configuration change (schemas, transforms, destinations, access) and make it replayable.
- **AC-11.** WHEN a user requests a transform from a natural-language description THE SYSTEM SHALL produce candidate Flink SQL, present it for review, and never activate it without explicit user confirmation.

## Non-functional requirements (EARS)

- **NFR-1** (security). THE SYSTEM SHALL enforce RBAC per environment and per integration, store secrets centrally, and never expose them in the UI or logs.
- **NFR-2** (reliability). THE SYSTEM SHALL surface ingest success rate, p99 end-to-end latency, max consumer lag, and per-integration DLQ ceiling against defined SLOs.
- **NFR-3** (observability). THE SYSTEM SHALL emit per-message traces keyed by correlation ID and structured logs, and expose consumer lag, DLQ rate, transform-error rate, and destination-failure metrics.
- **NFR-4** (performance/UX). THE SYSTEM SHALL reflect lag and DLQ state in near-real-time and apply configuration changes within seconds.
- **NFR-5** (compliance, AI). THE SYSTEM SHALL audit AI-assist actions, keep the model provider configurable, and apply PII and cost controls to AI calls.

## Demo expectations

The demo shows: creating an integration without writing bespoke code; schema-driven validation and Flink SQL transformation; a failing message landing in the DLQ; the AI agent explaining the failure and proposing a fix; replay after the fix; and end-to-end traceability.
