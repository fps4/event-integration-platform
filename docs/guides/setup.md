---
title: Local setup
summary: Run the platform locally with Docker Compose — the services, supporting infrastructure, and how to verify the ingest → transform → deliver path.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/overview.md
  - docs/reference/config.md
  - docs/design/components/broker.md
---

# Local setup

## Purpose

Run the platform locally via Docker Compose to develop and demo the end-to-end ingest → transform → deliver path.

## Prerequisites

- Docker + Docker Compose.
- Node.js 20+ (for running/building individual services outside containers).

## Stack (Docker Compose)

`compose.yaml` brings up:

- **Kafka platform** — Apache Kafka (`broker`), Schema Registry (`schema-registry`), Kafka Connect (`kafka-connect`).
- **Transform runtime** — Apache Flink (cluster or SQL Gateway) executing versioned Flink SQL transforms.
- **Control plane** — `control-api` (MongoDB-backed), `authorizer` (JWT), `webapp` (control surface), `observability-api`.
- **Data plane** — `connector-http-source` (`POST /ingest/:topic`), `connector-http-sink`.
- **Supporting infra** — `mongodb` (state/auth), `loki` + `promtail` (logs), optional `clickhouse`.

See the [configuration reference](../reference/config.md) for per-service environment variables.

## Bring it up

```bash
docker compose up -d
```

## Verify the path

1. **Ingest** — `POST` a JSON event to `connector-http-source`:
   ```bash
   curl -X POST http://localhost:8085/ingest/dev.abcd.wxyz.orders.source \
     -H 'Content-Type: application/json' \
     -d '{"id":"1","status":"new","amount":42}'
   ```
   Expect `202 Accepted` with a `requestId`.
2. **Transform** — confirm the active Flink SQL transform produced to the `*.enriched` topic (or inspect the topic directly — see [broker](../design/components/broker.md)).
3. **Deliver** — confirm the sink connector consumed/delivered the enriched record.
4. **DLQ** — send a malformed event and confirm it lands in the `*.dlq` topic with `x-dlq-reason`.

## Develop a single service

From a service folder: `npm install`, then `npm run build && npm start` (see each service's README for exact scripts). Point `MONGO_URI`, `KAFKA_BROKERS`, and `SCHEMA_REGISTRY_URL` at the Compose services.
