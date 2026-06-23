---
title: Web app (control surface)
summary: The operator/engineer control plane — Next.js + shadcn/ui + Tailwind — for workspaces, pipelines, Flink SQL transforms, observability, schema catalog, and replay/DLQ.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/decisions/0003-web-application-stack.md
  - docs/design/components/control-api.md
  - docs/design/components/transform-engine.md
  - docs/guides/webapp-react-flow.md
---

# Web app (control surface)

## Purpose

How operators and integration engineers interact with the platform. Presents the control plane for end-user flows; never on the data path. Companion to the [PRD](../../product/prd/0001-event-integration-platform.md) flows and the [Control API](./control-api.md) surface.

## Stack

Built on the platform's standard web stack (see [ADR-0003](../decisions/0003-web-application-stack.md)): **Next.js (App Router) + React + TypeScript**, **shadcn/ui** components over **Radix** primitives, **Tailwind CSS** for styling with `next-themes` for theming, `lucide-react` icons, `react-hook-form` + `zod` for forms, and native `fetch` against the Control/Observability APIs (no heavy data-fetching or state library). The pipeline builder uses **React Flow** themed with Tailwind/shadcn tokens.

## Behaviour

- Enforce RBAC per environment; audit all mutations (who/when/what); rely on the [Authorizer](./authorizer.md) for tokens.
- Backed by the [Control API](./control-api.md) and [Observability API](./observability.md).
- Drive the wizard: source → topics → transform → destinations → observability → access.

### End-user goals
- **Access** — sign in, register/manage access.
- **Pipelines & workspaces** — create workspaces; create pipelines with source/sink config and a Flink SQL transform; start/stop.
- **Observability** — dashboards, run logs/traces, Kafka metrics, alerts on connector failures.
- **Governance** — browse/search/diff the schema catalog, tag PII, pin schema versions.

## Capabilities

- **Workspaces** — create/edit with codes and allowed origins; list/filter.
- **Pipelines** — create/edit with streams and source/sink connectors; visualize with React Flow; start/stop.
- **Pipeline flow visualization** — interactive drag-and-drop canvas with auto-positioned nodes; positions persist to MongoDB; left-to-right flow (Client → Source Connector → Stream → Transformation → Stream → Sink Connector → Connection).
- **Streams** — create with type selection (source, sink, dlq, replay); color-coded by variant.
- **Clients** — create/edit global authenticated clients; manage allowed scopes/topics.
- **Connections** — create/edit external sink destinations (HTTP/S3); manage type-specific config.
- **Source/Sink connectors** — link clients/connections to pipeline streams; shown in the flow diagram.
- **Transformations (Flink SQL)** — a SQL editor for the pipeline transform: write or AI-draft a Flink SQL statement, pick source/target streams and an optional failure queue (DLQ), run a **dry-run** preview, and pin a version. The AI authoring copilot (see [agent-services](./agent-services.md)) drafts SQL from a natural-language description; visualized as Stream (source) → Transformation → Stream (target) with a dashed line to the failure queue.
- **Topics & broker config** — display retention/partitioning/compaction per the naming convention; read-only guardrails.
- **Observability** — integration status (active/paused/failed), per-message trace (ingest → transform → delivery/DLQ), Kafka metrics, alerts.
- **Schema catalog** — browse, diff, tag (PII), view impact; pin versions to integrations.
- **Replay & DLQ** — select window or IDs, dry-run validation, rate limiting, target schema/mapping versions, audit trail; AI DLQ-triage surfaces likely causes and fixes.
- **Docs** — in-app user documentation and an API reference portal.

## Pipeline flow UX

- **Action buttons** — fixed top row (non-draggable): Add Source Connector, Add Stream, Add Transformation, Add Sink Connector.
- **Column layout** — each node type sits in its own column under its action button.
- **Draggable nodes** — repositionable; positions persist to the backend.
- **Edges** — animated connections show data flow; transformation edges prefer the sink variant for target streams.
- **Shared styling** — consistent node dimensions and Tailwind/shadcn tokens for colors and radii.

See the implementation guide: [webapp-react-flow](../../guides/webapp-react-flow.md).
