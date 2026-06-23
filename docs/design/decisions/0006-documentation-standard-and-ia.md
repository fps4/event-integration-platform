---
title: "0006: Documentation standard & two-plane information architecture"
summary: Adopt a two-plane IA (Docs + Delivery) with derive-from-path sections, frontmatter, Mermaid diagrams, and immutable ADRs.
status: accepted
date: 2026-06-23
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/README.md
  - docs/design/decisions/0001-record-architecture-decisions.md
---

# 0006: Documentation standard & two-plane information architecture

## Context

The repository's documentation had grown ad-hoc — a flat `docs/` with loosely-organized component notes, guidelines, and a single placeholder ADR. There was no consistent frontmatter, no convention for where a doc lives, and diagrams were external binary files. As the repo goes public and grows, docs need to be navigable, machine-readable, and consistent for both humans and agents.

## Decision

Adopt a **two-plane information architecture**:

- **Docs plane** (reference) — five shelves: `overview.md`, `product/`, `design/`, `reference/`, `guides/`.
- **Delivery plane** (work) — four areas: `delivery/roadmap/`, `delivery/backlog/`, `delivery/tasks/`, `delivery/issues/`.

Rules:

- **Derive-from-path** — a doc's section is its folder; it is never a frontmatter field.
- **Frontmatter on every doc** — `title`, `summary` (one line), `status`, `last_updated`, `owners`, `related`.
- **Heading order** — Purpose → Behaviour → Interface/API → Dependencies → Configuration → Known limitations (omit what doesn't apply).
- **Diagrams are Mermaid** inside markdown — no external diagram files.
- **ADRs** use Context · Decision · Consequences and are immutable once accepted ([ADR-0001](./0001-record-architecture-decisions.md)).
- **Section READMEs are intros**, not catalogues; the repo-root and `docs/` READMEs are GitHub-facing and not part of the in-product navigation.
- **Docs change in the same PR as the code** they describe.

## Consequences

- The existing `docs/components/`, `docs/guidelines/`, and `docs/decisions/` folders are reorganized into the new shelves; external diagram files are converted to inline Mermaid.
- All docs gain frontmatter and consistent structure; links are repo-relative and must resolve.
- Contributors and agents have one predictable place for each kind of document, and the structure a human navigates is the one an agent writes into.
