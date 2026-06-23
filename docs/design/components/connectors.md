---
title: Connectors
summary: Kafka Connect as the platform-managed connector runtime, plus the in-repo HTTP source/sink services and the shared connector-core package.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/schema-registry.md
  - docs/design/overview.md
  - docs/reference/topic-naming.md
---

# Connectors

## Purpose

Cover ingress and delivery: Kafka Connect as the platform-managed connector runtime, plus the in-repo HTTP source/sink services.

## Kafka Connect (platform-managed)

- Runtime: Kafka Connect, configured via REST.
- Plugin catalogue: community/Confluent Hub connectors (e.g. HTTP, Elastic, PagerDuty).
- Core operations (REST):
  1. `GET /connector-plugins`
  2. `POST /connectors` (name + config: `connector.class`, `tasks.max`, `topics`, plugin settings)
  3. `PUT /connectors/<name>/config`
  4. `POST /connectors/<name>/pause` / `/resume`
  5. `GET /connectors/<name>/status`
  6. `DELETE /connectors/<name>`
- If a connector class is missing, install the plugin in the Connect image and retry.

## HTTP source & sink (repo services)

### Shared package — `packages/connector-core`
Shared HTTP connector primitives and validation:
- Request/response types and telemetry hooks.
- `defaultRetryPolicy`: 3 attempts, exponential-ish backoff up to 2s on 408/429/5xx.
- `validateTopicName(topic)`: trimmed string, alphanumerics plus `._-`, no `.`/`..`, max 249 chars; throws `InvalidTopicNameError`.

### HTTP source — `services/connector-http-source`
- Endpoints: `GET /health`, `POST /ingest/:topic`.
- Requirements: `Content-Type: application/json`; path topic validated; body must be a non-empty JSON object/array.
- Behaviour: propagates/generates `x-request-id`; responds `202 { status: "accepted", topic, requestId }`; centralized 404/500.

### HTTP sink — `services/connector-http-sink`
- Endpoints: `GET /health`.
- Current behaviour: subscribes to Kafka topics listed in `HTTP_SINK_TOPICS` (comma-separated), validates topic names, and logs each received record with request/trace ids. Env: `KAFKA_BROKERS`, `KAFKA_CLIENT_ID`, `KAFKA_GROUP_ID`, `PORT` (default `8086`).
- Future behaviour: POST each record to a configured REST endpoint (config to move to DB-driven settings).
- Uses `connector-core` types, telemetry hooks, and `defaultRetryPolicy`; propagates/generates `x-request-id`.

## Supported connectors (initial)

| Connector | Type | Notes |
|---|---|---|
| HTTP Source (`connector-http-source`) | Source | Webhook-style ingest at `/ingest/:topic`; topic validated via `validateTopicName`. |
| HTTP Sink (Kafka Connect plugin) | Sink | REST delivery using shared retry defaults; configured through Kafka Connect REST. |
| S3 Sink (Kafka Connect plugin) | Sink | Batched delivery to object storage; part of the delivery UX scope. |
