# Event Integration Platform

A Kafka-native event streaming & integration platform where Kafka stays internal-only and teams integrate through APIs and a control plane — **configuration over code**, with **Apache Flink SQL** at the centre of transformation and an **AI assist layer** for authoring, mapping, and dead-letter triage.

> **REST → Kafka → Transform (Flink SQL) → Kafka → Deliver**, operated through a Control API and a web control plane, with first-class replay/DLQ, schema governance, and end-to-end observability.

## Quick links

- **Start here:** [`docs/overview.md`](./docs/overview.md) — the product landing.
- **Docs index:** [`docs/README.md`](./docs/README.md)
- **Architecture:** [`docs/design/overview.md`](./docs/design/overview.md)
- **Decisions (ADRs):** [`docs/design/decisions/`](./docs/design/decisions/)
- **Local setup:** [`docs/guides/setup.md`](./docs/guides/setup.md)
- **For agents:** [`CODEBASE.md`](./CODEBASE.md) · [`AGENTS.md`](./AGENTS.md) · [`GLOSSARY.md`](./GLOSSARY.md)

## What it is

This repository packages the building blocks needed to run a multi-tenant integration platform:

- **Ingest** events via REST (webhook-style) into Kafka topics.
- **Validate + transform** events with **Apache Flink SQL** — the single, versioned transformation runtime.
- **Deliver** to downstream systems via connectors (Kafka Connect + HTTP/S3 sinks).
- **Assist** operators with AI: natural-language → Flink SQL authoring, schema-registry-aware field mapping, and dead-letter triage & remediation.
- **Operate and govern** integrations through a Control API and web app — auditability, replay/DLQ, and clear ownership boundaries.

## Documentation

Documentation follows a two-plane information architecture (a **Docs** plane you read and a **Delivery** plane you track). See [`docs/README.md`](./docs/README.md) for the full map and [`docs/guides/documentation-standards`](./docs/guides/) for conventions.

## Contributing

See [`CONTRIBUTING.md`](./CONTRIBUTING.md). Docs change in the same PR as the code they describe.
