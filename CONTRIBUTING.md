# Contributing

Thanks for contributing to the Event Integration Platform. This guide covers how work flows here for both humans and agents.

## Principles

- **Start from intent.** Significant work begins as a product requirement or a design/ADR, not a diff. See the [Delivery plane](./docs/delivery/roadmap/README.md).
- **Work on a branch, open a PR.** Don't push directly to the default branch.
- **Green before review.** Build, tests, linters, and security/secret scans must pass.
- **Docs change with the code.** A behaviour change that leaves docs stale is incomplete.

## Definition of Done

- [ ] Satisfies the acceptance criteria (tests prove it).
- [ ] Unit / integration tests pass.
- [ ] Linters and dependency/secret scans are clean.
- [ ] Docs affected by the change are updated **in the same PR**.
- [ ] Repo-relative documentation links resolve.
- [ ] A human has reviewed and merged.

## Documentation

- Follow the two-plane IA in [`docs/README.md`](./docs/README.md). A doc's section is its folder — put ADRs in `docs/design/decisions/`, contracts in `docs/design/contracts/`, lookups in `docs/reference/`.
- Every doc opens with frontmatter: `title`, `summary` (one line), `status`, `last_updated`, `owners`, `related`.
- Heading order: Purpose → Behaviour → Interface/API → Dependencies → Configuration → Known limitations (omit what doesn't apply; never write "N/A").
- Diagrams are **Mermaid** blocks inside markdown.
- ADRs use Context · Decision · Consequences and are immutable once `accepted` — write a superseding ADR instead of editing.

## Public repository

This repository is public. Do not commit secrets, credentials, internal hostnames, or references to private/internal systems. Use placeholders (e.g. `auth.example.internal`) and environment variables for anything environment-specific.
