---
title: "0001: Record architecture decisions"
summary: We will capture significant architectural decisions as immutable, numbered ADRs.
status: accepted
date: 2026-06-23
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/README.md
---

# 0001: Record architecture decisions

## Context

We need to record the architectural decisions made on this project so that the reasoning behind the design is durable, reviewable, and discoverable — by both humans and agents.

## Decision

We will use Architecture Decision Records (ADRs), as described by Michael Nygard. Each ADR is a numbered, immutable record under `docs/design/decisions/` with the sections **Context · Decision · Consequences**. Once an ADR is `accepted` it is never edited; a later decision supersedes it with a new ADR (and the old one's status becomes `superseded by NNNN`).

## Consequences

- The *why* behind the architecture lives next to the code and the design docs.
- Decisions accrete as a numbered, append-only history; numbers are stable join keys and are never reused.
- Reversing a decision means writing a new ADR, keeping the trail intact.
