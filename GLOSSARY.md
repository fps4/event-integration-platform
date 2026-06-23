# Glossary

Terms where the platform's domain language and the code diverge, or where precision matters. The reference copy lives at [`docs/reference/glossary.md`](./docs/reference/glossary.md); this root file mirrors it for quick agent/contributor access.

- **Workspace** — a tenant. Carries a unique 4-letter `code` used in topic names; owns pipelines, users, and configuration.
- **Pipeline** — a named integration within a workspace: source streams, transforms, and sink connections. Carries its own 4-letter `code`.
- **Stream** — a logical channel within a pipeline, backed by a Kafka topic, with a `variant` (`source|sink|raw|enriched|dlq|retry`).
- **Transform** — a versioned **Flink SQL** statement bound to a source stream and a target stream, with status `draft|active|deprecated`. (Historically a JSONata expression — removed; see ADR-0002.)
- **Client** — a global machine identity that produces events; linked to a workspace through pipeline registration.
- **Connection** — an external sink destination (HTTP endpoint or S3 bucket) linked to a pipeline.
- **DLQ (dead-letter queue)** — the `*.dlq` topic where messages that fail transform or validation land, with failure context (`x-request-id`, `x-dlq-reason`).
- **Replay** — re-processing messages (from a DLQ or a time window) after fixing a mapping or schema.
- **Control plane** — the Control API and web app; configuration and governance, never the data path.
- **Data plane** — ingress, the transform runtime, and connectors; the path events actually travel.
- **Transform runtime** — the Apache Flink deployment that executes versioned Flink SQL transforms.
- **Agent services** — the AI assist layer: NL→Flink-SQL authoring, schema-aware field mapping, and DLQ triage (see ADR-0004).
- **Streaming agent** — an event-driven AI agent that observes a stream, reasons, and acts through tools exposed over MCP.
- **MCP** — Model Context Protocol; how the platform exposes topics and the schema registry as tools to agents.
- **Schema Registry** — the registry (Avro/JSON Schema/Protobuf) that governs message schemas and compatibility.
