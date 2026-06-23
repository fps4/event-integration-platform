---
title: "US-0101: Flink execution substrate"
summary: Stand up an Apache Flink runtime in the platform so versioned Flink SQL transforms can execute against Kafka topics.
status: draft
milestone: M1
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/transform-engine.md
  - docs/delivery/roadmap/m1-flink-transformation-core.md
---

# US-0101: Flink execution substrate

As the **platform team**, I want an Apache Flink runtime available in the platform so that versioned Flink SQL transforms can execute against Kafka topics with Schema-Registry-aware (de)serialization.

## Acceptance criteria

- **AC-1.** WHEN the platform starts via Docker Compose THE SYSTEM SHALL bring up a Flink runtime (cluster or SQL Gateway) reachable by the control-plane deployer.
- **AC-2.** WHEN a Flink SQL statement defines tables over source and target topics THE SYSTEM SHALL bind them to registered schemas via the Schema Registry.
- **AC-3.** WHILE a stateful statement (join/window) runs THE SYSTEM SHALL checkpoint state using per-environment defaults.
- **AC-4.** WHEN the runtime cannot deploy a statement THE SYSTEM SHALL report an unhealthy/not-ready status with the error.
