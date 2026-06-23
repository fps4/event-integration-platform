---
title: Event Integration Platform overview
summary: A Kafka-native integration platform with Apache Flink SQL at the centre of transformation and an AI assist layer for authoring, mapping, and DLQ triage.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/overview.md
  - docs/product/vision.md
  - docs/delivery/roadmap/README.md
---

# Event Integration Platform

The Event Integration Platform lets teams move events through a governed **REST → Kafka → Transform → Kafka → Deliver** pipeline without writing bespoke services, configuring everything through a Control API and a web control plane. Kafka stays internal-only; **Apache Flink SQL** is the single transformation runtime, so mappings, joins, windows, and stateful enrichment are expressed as versioned SQL bound to source and target topics. An **AI assist layer** turns natural-language intent into Flink SQL, suggests schema-registry-aware field mappings, and triages dead-letter messages with proposed fixes. Replay/DLQ, schema governance, RBAC, and end-to-end tracing are first-class. The platform team owns the runtime (Kafka, Flink, connectors, schema registry); product teams own schemas, transforms, and destinations.

**Status:** design blueprint — the architecture, decisions, and delivery plan are documented here; implementation tracks against the [roadmap](./delivery/roadmap/README.md).

Read next: the **[Docs plane](./README.md)** (overview · product · design · reference · guides) or the **[Delivery plane](./delivery/roadmap/README.md)** (roadmap · backlog · issues).
