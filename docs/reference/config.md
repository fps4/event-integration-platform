---
title: Configuration reference
summary: Environment variables and configuration for the platform services — authorizer, control-api, connectors, and the transform runtime.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/authorizer.md
  - docs/design/components/control-api.md
  - docs/guides/setup.md
---

# Configuration reference

Configuration is environment-variable driven. Secrets are injected via secure config and never committed. Hostnames below use placeholders — set them per environment.

## Authorizer

| Variable | Required | Default | Purpose |
|---|---|---|---|
| `AUTH_JWT_SECRET` | yes | — | JWT signing secret |
| `MONGO_URI` | yes | — | MongoDB connection URI |
| `AUTH_DB_NAME` | yes | — | Database name (workspaces, clients, users, sessions) |
| `AUTHORIZER_PORT` | no | `7305` | Listen port |
| `AUTH_JWT_ISSUER` | no | `authorizer-service` | `iss` claim |
| `AUTH_JWT_AUDIENCE` | no | `api` | `aud` claim |
| `SESSION_TTL_MINUTES` | no | `60` | Session lifetime |
| `CORS_ORIGINS` | no | — | Static allowlist (comma-separated) |
| `AUTH_REQUIRE_USER_WORKSPACE` | no | `false` | Require `workspace_id` on `/auth/session` |
| `LOG_PRETTY` | no | — | `false`/`0` for JSON logs |

## Control API

| Variable | Purpose |
|---|---|
| `MONGO_URI` / `MONGO_DB` | Control-plane state |
| `KAFKA_BROKERS` | Broker list (KafkaJS admin) |
| SSL/SASL envs | Broker auth, as applicable |
| `AUTH_JWT_SECRET` | Validate Authorizer-issued tokens |

## Connectors

| Variable | Service | Default | Purpose |
|---|---|---|---|
| `KAFKA_BROKERS` | source/sink | `broker:9092` | Broker list |
| `KAFKA_CLIENT_ID` / `KAFKA_GROUP_ID` | source/sink | — | Kafka identifiers |
| `HTTP_SINK_TOPICS` | http-sink | — | Comma-separated topics to consume |
| `PORT` | http-sink | `8086` | Listen port |

## Transform runtime (Flink)

| Variable | Default | Purpose |
|---|---|---|
| `KAFKA_BROKERS` | `broker:9092` | Source/target/DLQ broker list |
| `SCHEMA_REGISTRY_URL` | `http://schema-registry:8081` | Schema-aware (de)serialization |
| `MONGO_URI` / `MONGO_DB` | — | Read versioned transform configs (control-api store) |
| `WORKSPACE_ID` | — | Optional filter to a single workspace |
| `DLQ_TOPIC` | `<source>.dlq` | Optional DLQ override |
| `LOG_LEVEL`, `NODE_ENV` | — | Logging / environment |
