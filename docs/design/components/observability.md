---
title: Observability
summary: End-user, workspace-scoped observability — logs (Loki), integration metrics (Kafka admin + MongoDB), per-message traces, and self-service alerting.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/control-api.md
  - docs/design/components/transform-engine.md
  - docs/reference/topic-naming.md
---

# Observability

## Purpose

The end-user observability architecture for product teams using the platform: logging, metrics, tracing, and alerting — all workspace-scoped.

### End-user focus

Self-service observability through raw service logs (workspace-filtered), integration-level metrics (lag, DLQ counts, success rates, throughput), distributed tracing (correlation ID → full message lifecycle), self-service alerting (Slack/email/PagerDuty), and business-focused dashboards (no Kafka internals required).

> Platform operators monitor infrastructure with their organization's centralized observability tooling (e.g. Datadog, Grafana, ELK). This document covers the **product-facing** observability built into the platform.

## Architecture

### Observability API
A dedicated `services/observability-api` provides workspace-scoped access to logs, metrics, and traces: queries Loki for workspace-filtered logs, aggregates metrics from the Kafka Admin API + MongoDB, reconstructs traces from correlation IDs, serves a REST API for the web app, and enforces workspace isolation via JWT claims. Running it separately keeps user queries off the control-plane path and lets it scale independently.

### Infrastructure
**Loki + Promtail** for log aggregation: Loki is the horizontally-scalable backend; Promtail scrapes container logs and forwards them; workspace-scoped LogQL provides multi-tenancy.

### Service logging standards
All services use **Pino** structured JSON logging. Standard fields: `level`, `time`, `msg`, `service`, `requestId` (`x-request-id`, propagated across services), `workspaceId`, `topic`.

```json
{
  "level": "info",
  "time": "2026-02-08T15:00:00.000Z",
  "service": "connector-http-source",
  "requestId": "req_abc123",
  "workspaceId": "ws_acme",
  "topic": "prod.acme.orders.raw",
  "msg": "Message accepted for topic",
  "statusCode": 202
}
```

`LOG_PRETTY=true` for human-readable dev output; `false` for JSON in production.

## Interface / API

### Logs — `GET /api/logs`
Query params: `workspaceId` (required, validated against JWT), `service`, `level`, `requestId`, `topic`, `since` (required), `until`, `limit` (default 100, max 1000). Translates to workspace-scoped LogQL and queries Loki `/loki/api/v1/query_range`.

### Metrics
Aggregated from the Kafka Admin API (lag, partition offsets, throughput) and MongoDB (run history, DLQ counts, transform success/failure rates):

| Metric | Endpoint |
|---|---|
| Consumer lag | `GET /api/metrics/:pipelineId/lag` |
| DLQ message count | `GET /api/metrics/:pipelineId/dlq` |
| Transform success rate | `GET /api/metrics/:pipelineId/transforms` |
| Delivery rate | `GET /api/metrics/:pipelineId/delivery` |
| Throughput | `GET /api/metrics/:pipelineId/throughput` |
| Pipeline health | `GET /api/metrics/:pipelineId/health` |

### Distributed tracing — `GET /api/traces/:requestId`
`x-request-id` propagation: ingress accepts/generates it → attached as a Kafka message header → read by the transform runtime (in logs and DLQ messages) → propagated to downstream systems. The response reconstructs the message lifecycle across ingress → transform → delivery/DLQ, e.g.:

```json
{
  "requestId": "req_abc123",
  "workspaceId": "ws_acme",
  "pipelineId": "pl_abc123",
  "status": "delivered",
  "events": [
    { "stage": "ingress",   "service": "connector-http-source", "status": "accepted" },
    { "stage": "transform", "service": "transform-runtime", "transformVersion": "v2", "status": "success", "outputTopic": "prod.acme.orders.enriched" },
    { "stage": "delivery",  "service": "connector-http-sink", "status": "delivered", "httpStatus": 201 }
  ]
}
```

Implementation: query Loki for all logs with a matching `requestId` across services; optionally enrich from a MongoDB trace collection; reconstruct the timeline by stage.

### Alerting
Alert rules persist in MongoDB (`pipelineId`, `workspaceId`, `condition` { `metric`, `operator`, `threshold`, `windowMinutes` }, `actions` { slack/email/pagerduty }, `enabled`). A periodic evaluation engine (every 1–5 min) queries current metric values and fires webhooks/emails when a threshold is breached, debouncing repeats. The web app surfaces create-rule, alert history, and a status dashboard.

## Operational playbooks

- **Failed message** — get the correlation ID, query Loki (`{service=~".+"} |= "req_abc123"`), identify the failure stage, inspect the DLQ, fix the transform/schema via the Control API, replay from the web app.
- **High lag** — identify the consumer group, query Loki for transform-runtime errors, check the Kafka consumer group state, scale the runtime or pause the integration.

## Best practices

- Always include `requestId`; log at appropriate levels; put context (topic, workspaceId, pipelineId) in structured fields, not message strings.
- Never log PII, credentials, or full payloads (outside explicit debug); sample hot paths.
- End-user metrics use business terms (aggregated to pipeline level); platform metrics use technical terms (raw Kafka/service data).

## References

- Grafana Loki — https://grafana.com/oss/loki/ · LogQL — https://grafana.com/docs/loki/latest/logql/
- Pino — https://getpino.io/ · Kafka monitoring — https://kafka.apache.org/documentation/#monitoring
