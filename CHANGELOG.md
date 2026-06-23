# Changelog

All notable changes to this project are documented here. The format is based on
[Keep a Changelog](https://keepachangelog.com/), and the project aims to follow
[Semantic Versioning](https://semver.org/).

## [Unreleased]

### Added
- Two-plane documentation architecture (Docs + Delivery) with frontmatter, Mermaid diagrams, and immutable ADRs (see [`docs/README.md`](./docs/README.md) and [ADR-0006](./docs/design/decisions/0006-documentation-standard-and-ia.md)).
- ADRs recording the platform's direction: Apache Flink SQL as the single transformation engine ([ADR-0002](./docs/design/decisions/0002-flink-sql-as-transformation-engine.md)), the web application stack ([ADR-0003](./docs/design/decisions/0003-web-application-stack.md)), the agentic AI assist layer ([ADR-0004](./docs/design/decisions/0004-agentic-capabilities.md)), and Kafka internal-only ([ADR-0005](./docs/design/decisions/0005-kafka-internal-only.md)).
- Delivery roadmap and backlog covering the Flink transformation core, the web replatform, and agentic services.

### Changed
- Transformation is centred on **Apache Flink SQL**; JSONata, ksqlDB, and Kafka Streams runtimes are removed from the design.
- The web application targets Next.js + shadcn/ui + Tailwind, replacing MUI/Emotion and private component packages.
