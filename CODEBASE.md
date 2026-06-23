# Codebase orientation

The first file to read for a map of this repository. For documentation conventions see [`docs/README.md`](./docs/README.md); for what agents may and may not do see [`AGENTS.md`](./AGENTS.md).

## What this product does

A Kafka-native event integration platform: ingest events over REST, transform them with **Apache Flink SQL**, and deliver them to downstream systems via connectors — all configured through a Control API and a web control plane, with an AI assist layer for authoring, mapping, and dead-letter triage.

## Directory map

```
.
├── docs/                       # two-plane documentation (see docs/README.md)
├── packages/                   # shared libraries
│   ├── connector-core/         # HTTP connector primitives, validateTopicName, retry policy
│   ├── openapi-components/     # shared OpenAPI schemas referenced by service specs
│   ├── data-models/            # shared Mongoose schemas/models (multi-tenant state)
│   └── logging-utils/          # structured logging (Pino) helpers
└── services/                   # deployable services
    ├── control-api/            # control plane: workspaces, pipelines, transforms, topics
    ├── authorizer/             # JWT issuance/refresh for clients and UI sessions
    ├── connector-http-source/  # REST ingress: POST /ingest/:topic
    ├── connector-http-sink/    # delivery (consumer skeleton today)
    ├── transform-runtime/      # Apache Flink SQL transformation runtime (see ADR-0002)
    ├── agent-services/         # AI assist layer (see ADR-0004)
    ├── observability-api/      # workspace-scoped logs/metrics/traces
    └── webapp/                 # web control plane (see ADR-0003 for the stack)
```

> Service/package names reflect the **target** design recorded in the ADRs. Where the current tree still carries legacy names (e.g. a JSONata worker), the ADRs note the migration.

## Entry points

- **Ingest:** `connector-http-source` — `POST /ingest/:topic`.
- **Control:** `control-api` — `/api/*` (workspaces, pipelines, transforms, topics, connections).
- **Auth:** `authorizer` — `/auth/token`, `/auth/session`, `/auth/refresh`.
- **Transform:** `transform-runtime` — deploys versioned Flink SQL statements per pipeline.

## Key conventions

- **Topic naming:** `<env>.<workspace_code>.<pipeline_code>.<stream>.<variant>` — see [`docs/reference/topic-naming.md`](./docs/reference/topic-naming.md).
- **Correlation:** `x-request-id` propagated end-to-end (HTTP → Kafka headers → logs → DLQ).
- **Transforms:** versioned Flink SQL with status `draft|active|deprecated`.
- **Runtime:** Node.js 20+ / TypeScript for services; Apache Flink for the transform runtime.

## Out of scope

- Kafka is internal-only; it is never exposed directly to external callers (see ADR-0005).
