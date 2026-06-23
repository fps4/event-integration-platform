---
title: OpenAPI design & architecture
summary: Spec-first, component-based OpenAPI strategy — shared schemas in packages/openapi-components, per-service specs that $ref them, runtime validation, and type/client generation.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/control-api.md
  - docs/design/components/authorizer.md
  - docs/design/components/connectors.md
  - docs/reference/api/README.md
---

# OpenAPI design & architecture

## Purpose

The platform uses a **spec-first, component-based OpenAPI architecture**: shared schemas and components are centralized in `packages/openapi-components`, and each service maintains its own OpenAPI spec that `$ref`s those shared components.

## Architecture

```
packages/openapi-components/
├── connectors.yaml      # shared connector schemas (TopicName, IngestPayload, ...)
├── control-api.yaml     # shared control-plane schemas (Workspace, Pipeline, User, ...)
└── README.md

services/
├── control-api/openapi.yaml          # control paths + $ref to shared components
├── authorizer/openapi.yaml           # auth endpoints + $ref to shared components
└── connector-http-source/openapi.yaml  # ingest endpoints + $ref to connectors.yaml
```

Benefits: DRY shared schemas, cross-service consistency, generated TypeScript types, runtime request/response validation, auto-generated docs, and contract testing.

## Shared components

- **`connectors.yaml`** — data-plane: `TopicName`, `IngestPayload`, `AcceptedResponse`, `HealthResponse`, `ErrorResponse`, `TopicParam`, `RequestId`. Used by `connector-http-source`, `connector-http-sink`.
- **`control-api.yaml`** — control-plane domain models (`Workspace`, `Pipeline`, `Client`, `User`, `FlinkSqlTransform`, `Session`), auth components (`BearerAuth`, `Authorization`), common responses (`ValidationError`, `Unauthorized`, `NotFound`), parameters (`WorkspaceIdParam`, `PipelineIdParam`, `ClientIdParam`), and pagination. Used by `control-api`, `authorizer`, and the web app (client generation).

## Per-service specs

Each service keeps an `openapi.yaml` (`openapi: 3.1.0`) with `info`, `servers`, and `paths` that `$ref` shared components. For example, `control-api/openapi.yaml` documents `GET /health`, the workspaces/pipelines/clients/users/topics endpoints, and `GET|POST /api/workspaces/:id/flink-transforms`, each referencing `control-api.yaml`.

## Integration

- **Runtime validation** — `express-openapi-validator` validates incoming requests and outgoing responses against the spec.
- **Type generation** — `openapi-typescript` generates TS types for compile-time safety.
- **Client generation** — `@hey-api/openapi-ts` (or similar) generates a typed client for the web app.
- **Docs** — Swagger UI / Redocly serves interactive documentation.

## Best practices

- Keep shared components generic (domain concepts, not service internals).
- Use `$ref` for schemas, parameters, responses, and security schemes.
- Version shared components semantically; breaking changes require a major bump.
- Encode validation rules (patterns, min/max, required) in schemas; reuse for UI validation.
- Assign a unique `operationId` per endpoint; provide `examples` and `description`.
- Define `BearerAuth` (HTTP bearer, JWT from the Authorizer) once and apply globally or per-operation.

## Maintenance workflow

- **New endpoint** — add/confirm the domain model in shared components, define the path with `$ref`, lint (`redocly lint`), regenerate types, implement with validation middleware, contract-test.
- **Change shared components** — update the schema, grep for consumers, update affected service specs, regenerate types, run the full test suite.
- **Aggregate (optional)** — bundle service specs into a single portal with Redocly for unified docs.
