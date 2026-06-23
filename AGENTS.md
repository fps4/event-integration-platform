# Agent guide

What automated agents and contributors may do in this repository, how to run things, and the checks expected before submitting.

## Read first

1. [`CODEBASE.md`](./CODEBASE.md) — the directory map and entry points.
2. [`docs/README.md`](./docs/README.md) — the documentation architecture.
3. [`GLOSSARY.md`](./GLOSSARY.md) — domain terms.

## Working agreement

- **Propose via PR; humans merge.** Work on a branch; never push to the default branch and never merge autonomously.
- **Docs change with code.** A behaviour change that leaves docs stale is incomplete (see [`CONTRIBUTING.md`](./CONTRIBUTING.md)).
- **Follow the documentation standard.** New docs carry frontmatter and live on the correct shelf (the folder is the section). Diagrams are Mermaid. ADRs are immutable once accepted — supersede, don't edit.
- **Respect the boundaries.** Kafka is internal-only (ADR-0005). Transforms are Flink SQL (ADR-0002). The web app uses the stack in ADR-0003. AI features follow ADR-0004 (model-provider-neutral, audited, with PII and cost controls).

## Conventions

- Node.js 20+ / TypeScript for services; match the style of the surrounding code.
- Structured logging via Pino; always propagate `x-request-id`.
- Topic names follow [`docs/reference/topic-naming.md`](./docs/reference/topic-naming.md).
- No secrets in code, logs, or docs. This repository is public — see [`CONTRIBUTING.md`](./CONTRIBUTING.md).

## Before submitting

- Build and tests pass for the affected service/package.
- Linters and any security/secret scans are clean.
- Docs affected by the change are updated in the same PR.
- Repo-relative documentation links resolve.
