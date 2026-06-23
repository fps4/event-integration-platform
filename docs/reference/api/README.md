---
title: API reference
summary: Where to find the platform's OpenAPI specifications and how they are organized.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/contracts/openapi.md
---

# API reference

The platform is spec-first. Each service owns an `openapi.yaml` that references shared components in `packages/openapi-components`. See the [OpenAPI design & architecture](../../design/contracts/openapi.md) for the strategy.

## Specifications

| Service | Spec | Surface |
|---|---|---|
| Control API | `services/control-api/openapi.yaml` | `/api/*` — workspaces, pipelines, flink-transforms, topics, clients, connections |
| Authorizer | `services/authorizer/openapi.yaml` | `/auth/*` — token, session, refresh |
| HTTP Source | `services/connector-http-source/openapi.yaml` | `/ingest/:topic` |

## Shared components

| Package file | Scope |
|---|---|
| `packages/openapi-components/connectors.yaml` | Data-plane schemas (TopicName, IngestPayload, …) |
| `packages/openapi-components/control-api.yaml` | Control-plane domain models, auth, common responses |

Generated interactive docs (Swagger UI / Redocly) are served from the aggregated spec; see the contract doc for the bundling workflow.
