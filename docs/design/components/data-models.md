---
title: Data models package
summary: Shared Mongoose schemas/models for multi-tenant state — Workspace, Pipeline (with Flink SQL transforms), Client, Connection, User, Session, Notification, Contact.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/control-api.md
  - docs/design/components/authorizer.md
  - docs/design/components/transform-engine.md
---

# Data models package

## Purpose

Shared Mongoose schemas and model factories that all services use for tenant-scoped data. Package name: `@event-integration-platform/data-models`.

## Behaviour

- Mongoose schemas and models for `Workspace`, `Pipeline`, `Client`, `Connection`, `User`, `Session`, `Notification`, and `Contact`.
- `makeModels(conn)` registers all models on a given `mongoose.Connection`.
- Dual CJS/ESM build with emitted typings via `tsup`.

## Schema highlights

- **Workspace** — `_id`, `name`, `code` (4 lowercase letters, unique), `status` (`active`/`inactive`), `allowedOrigins`; status indexed.
- **Pipeline** — `_id`, `workspaceId` (ref, indexed), `name`, `code` (4 lowercase letters), `status`, `streams` (array of StreamDefinition: topic/type/description where type is `source|sink|dlq|replay`), `sourceClients` (ClientConfigRef: clientId/role/connectorType/streamName/description), `sinkConnections` (ConnectionConfigRef: connectionId/connectorType/streamName/description), `transforms` (array of **TransformConfig**: `type` (`flink-sql`), `sourceStream`, `targetStream`, `failureQueue`, `sql`, `description`, `isPaused`, with a unique `targetStream` constraint), `nodePositions` (map of nodeId → {x, y} for UI layout).
- **Client** — `_id`, `name`, `status` (indexed), `secretHash`/`secretSalt`, `allowedScopes`, `allowedTopics`; global entity used as authenticated source producers; linked via `Pipeline.sourceClients`.
- **Connection** — `_id`, `name`, `type` (`HTTP`/`S3`), `status` (indexed), `config` (HTTP: url/method/headers; S3: bucket/region/accessKeyId/secretAccessKey); external sink destinations linked via `Pipeline.sinkConnections`.
- **User** — `_id`, `workspaceId` (ref, indexed), `username` (unique), password hash/salt, `roles`, `status` (indexed); virtual `workspace`.
- **Session** — `_id` (UUID), `workspaceId` (ref, indexed), `principalId`, `principalType` (`client`/`user`, indexed), `scopes`, `topics`, `context`, `status` (`active`/`revoked`, indexed), `expiresAt` (indexed).
- **Notification** — channel `slack|email` (indexed), `type` (indexed), `correlationId` (indexed), optional `tenantId`/`contactId`, channel-specific `target`, `payload`, `status` (`queued`/`sent`/`failed`, indexed), optional `error`, `deliveredAt`.
- **Contact** — optional `email` (unique when present), `phone` (indexed), `role` (`admin`/`member`/`agent`/`viewer`, indexed), `status` (indexed), verification flags, `tags`, `attributes`, `source`, `lastSeenAt`.

> **FlinkSqlTransform** is the versioned transform record (succeeding the removed `JsonataTransform`; see [ADR-0002](../decisions/0002-flink-sql-as-transformation-engine.md)): a Flink SQL statement bound to source/target topics with status `draft|active|deprecated`. The inline `TransformConfig` on `Pipeline` carries the SQL used by a pipeline's transform node.

## Usage

```ts
import mongoose from 'mongoose';
import { makeModels } from '@event-integration-platform/data-models';

const conn = await mongoose.createConnection(mongoUri).asPromise();
const models = makeModels(conn);

const workspace = await models.Workspace.create({ _id: 'ws_1', name: 'Acme' });
```
