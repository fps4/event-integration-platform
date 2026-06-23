---
title: Vision
summary: A Kafka-native integration platform where teams onboard event flows through configuration, Flink SQL transforms, and AI assistance — not bespoke code.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/product/personas.md
  - docs/design/overview.md
  - docs/design/decisions/0002-flink-sql-as-transformation-engine.md
  - docs/design/decisions/0004-agentic-capabilities.md
---

# Vision

## Purpose

Give an organization one Kafka-native, internal-only event streaming and integration platform where product teams onboard new event flows **through configuration, SQL, and AI assistance — without writing bespoke services**. Kafka stays internal; teams integrate through REST ingress and a control plane.

## The shape

A multi-tenant **REST → Kafka → Transform → Kafka → Deliver** platform:

- **Ingest** events via REST (webhook-style) into Kafka topics.
- **Validate + transform** events with **Apache Flink SQL** — the single transformation runtime (see [ADR-0002](../design/decisions/0002-flink-sql-as-transformation-engine.md)). Transforms are versioned SQL bound to source/target topics, supporting field mapping, joins, windows, and stateful enrichment.
- **Deliver** to downstream systems via connectors (Kafka Connect + HTTP/S3 sinks).
- **Assist** with AI: turn natural-language intent into Flink SQL, suggest schema-registry-aware field mappings, and triage dead-letter messages with proposed fixes (see [ADR-0004](../design/decisions/0004-agentic-capabilities.md)).
- **Operate and govern** through a Control API and web app — auditability, replay/DLQ, RBAC, and clear ownership boundaries.

## Principles

- **Configuration over code** — product teams express integrations declaratively (SQL + config), not as deployments.
- **Kafka-native, internal-only** — Kafka is never exposed externally ([ADR-0005](../design/decisions/0005-kafka-internal-only.md)); the only external surfaces are REST ingest and the Control API.
- **Clear separation of responsibilities** — the platform team owns the runtime; product teams own schemas, transforms, and destinations.
- **Fail-fast & observable** — validate early, surface failures to a DLQ with full context, trace every message.
- **Composable** — connectors, transforms, and AI assistance are independent, swappable parts.

## Who it's for

- **Platform team** — owns Kafka, the Flink transform runtime, connectors, the schema registry, runtime operations, guardrails, and observability.
- **Product / integration teams** — own schemas, transform versions, destinations, and activation/rollout.

See [personas](./personas.md) for the full responsibility split.

## What you can build

A centralized integration platform where teams onboard event flows without writing code — a reliable pipeline from webhooks/internal APIs → Kafka → Flink SQL transformations → downstream REST/S3 systems, with governance (schemas, RBAC), versioning, rollback, replay, and on-call-friendly controls, accelerated by an AI assist layer.
