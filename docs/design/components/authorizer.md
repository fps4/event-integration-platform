---
title: Authorizer service
summary: Issues short-lived HS256 JWTs for machine clients and UI sessions, manages workspace membership, and enforces CORS.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/control-api.md
  - docs/design/components/webapp.md
  - docs/design/components/data-models.md
---

# Authorizer service

## Purpose

Supports the UI and Control API auth flows: it issues tokens for `/ingest/*` and `/api/*` so end-users can sign in, register, and manage access, and so backends can enforce authorization.

Issues short-lived JWTs for the platform: machine **clients** calling `/ingest/*`, control API callers hitting `/api/*`, and browser/UI sessions. Stores minimal session metadata for audit/troubleshooting. A single MongoDB database holds workspaces, clients, users, and sessions (no multi-tenant DB split).

## Behaviour

- Manage **clients** (global machine identities); clients carry topic-bound scopes (e.g. `ingest:topic:orders.created`, `api:read`).
- Validate client status and secrets before issuing tokens; workspace context is established through session metadata (derived from pipeline registration).
- Register and authenticate users (operators) and issue UI/control tokens (workspace-agnostic).
- Manage workspace lifecycle and membership **after login**: users create workspaces in the UI, add pipelines and clients, and invite other users. The creator becomes **owner** and can assign **admins** and members.
- Do **not** auto-create a workspace during registration; users can belong to zero or many workspaces.
- Sessions can be user-only until a workspace is selected; once selected, tokens carry that workspace. For client sessions, workspace context comes from pipeline registration.
- Create and persist sessions with TTL; refresh TTL and re-issue JWTs.
- Issue HS256 JWTs with principal claims (client or user), workspace claim, and scope/topic restrictions.
- Enforce CORS via a configured allowlist (no tenant-derived origins).

## Interface / API

Base path: `/auth`.

### Client token (machine-to-machine) — `POST /auth/token`
Issues a token for clients that call `/ingest/*` or `/api/*`.

Request:
```json
{ "client_id": "client id", "client_secret": "client secret", "scopes": ["ingest:topic:orders.created", "api:read"] }
```
Response `201`:
```json
{ "sessionId": "...", "token": "...", "expiresIn": 3600, "client_id": "...", "scopes": ["ingest:topic:orders.created", "api:read"] }
```
Errors: `400` invalid input/scopes · `404` client not found/inactive · `401` secret mismatch · `500` missing `AUTH_JWT_SECRET`/internal.

### UI / user session — `POST /auth/session`
Issues a session for operators (authn mechanism pluggable: password, SSO token, etc.).

Request (example):
```json
{ "username": "operator@example.com", "password": "...", "workspace_id": "..." }
```
(`workspace_id` required only when `AUTH_REQUIRE_USER_WORKSPACE=true`.)

Response `201`:
```json
{
  "sessionId": "...", "token": "...", "expiresIn": 3600,
  "user": { "id": "...", "active_workspace_id": "null until selected",
            "memberships": [ { "workspace_id": "...", "role": "owner|admin|member" } ] }
}
```

### Refresh token — `POST /auth/refresh`
Refreshes the session TTL and issues a new JWT. Accepts `Authorization: Bearer <token>` or `X-Session-Token: <token>`. Returns `200` with a new token, `expiresAt`, and principal details. Errors: `401` missing/invalid/expired · `404` session not found · `500` internal.

## JWT claims

HS256-signed: `sid` (session), `pid` (principal), `ptyp` (`client`|`user`), `wid` (optional active workspace), `scopes` (e.g. `ingest:topic:<name>`, `api:*`, `ui:session`), plus `iss`, `aud`, `exp`, `iat`, `jti`. Clients may also carry `topics` (allowlist usable on `/ingest/:topic`).

On token issuance the service captures user agent / Client Hints and IP, stored under normalized keys (`user_agent`, `ch_ua`, `ip_address`) on the session context.

## Dependencies

- Runtime: Node.js + TypeScript (ESM), Express, MongoDB (single database).
- `@event-integration-platform/data-models`, `@event-integration-platform/logging-utils`.
- Consumed by the [Control API](./control-api.md) for `/api/*` enforcement.

## Configuration

Required: `AUTH_JWT_SECRET`, `MONGO_URI`, `AUTH_DB_NAME`.
Optional: `AUTHORIZER_PORT` (`7305`), `AUTH_JWT_ISSUER` (`authorizer-service`), `AUTH_JWT_AUDIENCE` (`api`), `SESSION_TTL_MINUTES` (`60`), `CORS_ORIGINS` (static allowlist), `AUTH_REQUIRE_USER_WORKSPACE` (`false`), `LOG_PRETTY`.

CORS: if `CORS_ORIGINS` is set, those origins are allowed; in non-production, common localhost origins are allowed; if none are configured, all origins are allowed.
