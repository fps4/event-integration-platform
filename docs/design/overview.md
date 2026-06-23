---
title: Architecture overview
summary: High-level architecture of the platform — Kafka platform layer, Flink SQL transform runtime, control plane, connectors, and the AI assist layer, with C4 diagrams.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/product/vision.md
  - docs/design/components/transform-engine.md
  - docs/design/components/agent-services.md
  - docs/design/decisions/0002-flink-sql-as-transformation-engine.md
---

# Architecture overview

## Purpose

Describe the high-level architecture and the principles behind it. Component deep-dives live under [`components/`](./components/); cross-cutting decisions under [`decisions/`](./decisions/).

## Principles

- **Configuration over code** — product teams own transforms (Flink SQL) and config, not deployments.
- **Kafka-native, internal-only** — Kafka is never externally exposed ([ADR-0005](./decisions/0005-kafka-internal-only.md)).
- **Single transformation runtime** — Apache Flink SQL ([ADR-0002](./decisions/0002-flink-sql-as-transformation-engine.md)).
- **Fail-fast & observable** — early validation, DLQ on failure, end-to-end tracing.
- **Composable** — connectors, transforms, and AI assistance are independent parts.

## System context (C4 L1)

```mermaid
C4Context
  title Event Integration Platform — system context

  Person(engineer, "Integration engineer", "Configures pipelines, authors Flink SQL transforms.")
  Person(operator, "Operator / on-call", "Monitors lag, DLQ; triggers replay.")
  System_Ext(producer, "Event producer", "Webhook / internal API posting events over REST.")
  System_Ext(downstream, "Downstream system", "REST endpoint or S3 bucket receiving delivered events.")
  System_Ext(model, "Model provider", "Pluggable LLM/ML provider for the AI assist layer.")

  System(eip, "Event Integration Platform", "Ingest → Flink SQL transform → deliver, governed via a control plane.")

  Rel(producer, eip, "POST /ingest/:topic")
  Rel(engineer, eip, "Configures via web app / Control API")
  Rel(operator, eip, "Operates, replays")
  Rel(eip, downstream, "Delivers via connectors (REST/S3)")
  Rel(eip, model, "AI assist calls (authoring, mapping, DLQ triage)")
```

## Containers (C4 L2)

```mermaid
flowchart TB
  subgraph control["Control plane"]
    webapp["webapp<br/>(Next.js + shadcn/ui)"]
    controlapi["control-api<br/>(Node/TS)"]
    authorizer["authorizer<br/>(JWT)"]
    obs["observability-api"]
    agents["agent-services<br/>(AI assist + MCP)"]
  end

  subgraph data["Data plane"]
    source["connector-http-source<br/>POST /ingest/:topic"]
    broker["Apache Kafka"]
    flink["transform-runtime<br/>(Apache Flink SQL)"]
    connect["Kafka Connect<br/>(HTTP/S3 sinks)"]
  end

  subgraph platform["Platform services"]
    registry["Schema Registry"]
    mongo["MongoDB<br/>(config/state)"]
    loki["Loki + Promtail"]
  end

  webapp --> controlapi
  webapp --> obs
  webapp --> agents
  controlapi --> mongo
  controlapi --> broker
  controlapi --> flink
  authorizer --> mongo
  source --> broker
  broker --> flink
  flink --> broker
  flink --> registry
  broker --> connect
  connect --> registry
  agents --> broker
  agents --> registry
  obs --> loki
  obs --> broker
```

## Layers

### Kafka platform layer
Apache Kafka (self-hosted), Schema Registry, and Kafka Connect. Deployed via Docker Compose (local) and Kubernetes (prod). Topics follow the [naming convention](../reference/topic-naming.md); raw, enriched, and DLQ variants are first-class.

### Transformation
The **transform runtime** executes versioned **Flink SQL** statements bound to source/target topics — field mapping, joins, windows, and stateful enrichment — validating output against registered schemas and writing failures to the DLQ. See [transform-engine](./components/transform-engine.md) and [ADR-0002](./decisions/0002-flink-sql-as-transformation-engine.md).

### Control plane
The [Control API](./components/control-api.md) manages workspaces, pipelines, transforms, topics, clients, and connections (MongoDB-backed; KafkaJS admin for broker operations). The [web app](./components/webapp.md) is the operator surface. The [authorizer](./components/authorizer.md) issues JWTs.

### Connectors
[Kafka Connect](./components/connectors.md) is the connector runtime; the repo ships HTTP source/sink services built on `connector-core`. Delivery targets REST and S3.

### AI assist layer
[agent-services](./components/agent-services.md) provides NL→Flink-SQL authoring, schema-registry-aware field mapping, DLQ triage & remediation, in-SQL inference functions, and event-driven streaming agents — exposing topics and the schema registry to agents over MCP. See [ADR-0004](./decisions/0004-agentic-capabilities.md).

### Observability
[observability-api](./components/observability.md) serves workspace-scoped logs (Loki), metrics (Kafka admin + MongoDB), and per-message traces keyed by `x-request-id`.
