---
title: Control API service
summary: The control-plane API behind the web app — workspaces, pipelines, Flink SQL transforms, topics, clients, connections, and observability surfaces.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/data-models.md
  - docs/design/components/transform-engine.md
  - docs/design/components/authorizer.md
  - docs/design/contracts/openapi.md
---

# Control API service

## Purpose

The control-plane API used by the web app and automation to power end-user flows: workspaces, pipelines, connectors, transforms, schemas, and observability. It is **not** on the data path — no ingestion or delivery flows through it.

## Behaviour

- Present the control plane; persist state in MongoDB; interact with the broker via KafkaJS admin.
- Expose APIs for authentication, workspace/pipeline lifecycle, connector/source/sink config, Flink SQL transforms, observability (stats, logs, traces), and schema catalog access.

## Interface / API (representative)

- `GET /health`, `GET /api/health` — liveness/health.
- **Users / auth** — integrates with Authorizer-issued tokens; user registration/auth via `/api/users`.
- **Workspaces** — `GET /api/workspaces`, `POST /api/workspaces`.
- **Pipelines** — `GET /api/pipelines`, `GET /api/pipelines/:pipelineId`, `POST /api/pipelines` (requires `workspaceId`), `PUT /api/pipelines/:pipelineId` (status/streams/clients/transform), `POST /api/pipelines/:id/start`, `POST /api/pipelines/:id/stop`.
- **Clients** — `GET /api/clients`, `GET /api/clients/:id`, `POST /api/clients`, `PUT /api/clients/:id`. Global entities; workspace association via pipeline registration (`sourceClients`).
- **Connections** — `GET|POST /api/connections`, `GET|PUT|DELETE /api/connections/:id`. External sink destinations (HTTP endpoints, S3 buckets); linked to pipelines through `sinkConnections`.
- **Users** — `GET /api/workspaces/:id/users`, `POST /api/workspaces/:id/users`.
- **Topics** (KafkaJS admin) — `POST /api/topics`, `GET /api/topics`, `GET /api/topics/:name/metrics` (per-partition offsets/lag).
- **Run logs & traces** — `GET /api/pipelines/:id/logs`, `GET /api/pipelines/:id/traces`.
- **Stats** — `GET /api/connectors/:id/stats`, `GET /api/kafka/stats`.
- **Flink SQL transforms** — `GET /api/workspaces/:id/flink-transforms`, `POST /api/workspaces/:id/flink-transforms` (versioned SQL statements bound to source/target topics, optional schema IDs, status `draft|active|deprecated`). See [transform-engine](./transform-engine.md). `POST /api/workspaces/:id/flink-transforms/:id/validate` runs a dry-run against sample input.

## Dependencies

- **MongoDB** via `@event-integration-platform/data-models` — shared Mongoose models for multi-tenant state:
  - `Workspace` — tenants with status and allowed origins.
  - `Client` — global machine identities (secret hash/salt, allowed scopes/topics); linked via `Pipeline.sourceClients`.
  - `Connection` — external sink destinations (HTTP/S3); linked via `Pipeline.sinkConnections`.
  - `User` — per-workspace users with roles and credentials.
  - `Session` — issued tokens with scopes/topics and expiry.
  - `FlinkSqlTransform` — versioned Flink SQL bound to source/target topics (plus schema IDs) for the transform runtime.
  - Access patterns: workspace scoping, `active` status checks, audit on mutations.
- **Kafka** via KafkaJS admin — topic creation/listing and offset metrics; configured by `KAFKA_BROKERS`, SSL/SASL envs.
- **Authorizer** — issues JWTs for callers; the Control API validates bearer tokens on `/api/*`.

## Known limitations

- Security: validation + CORS allowlist; no secrets echoed.
- Reliability: fail-fast validation; structured error responses.
- Observability: structured logs with `x-request-id`; per-request logging.
- Performance: lightweight admin calls; topic metrics are per-partition offset snapshots.
