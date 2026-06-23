---
title: "0005: Kafka stays internal-only; integrate via APIs and the control plane"
summary: Kafka is never exposed externally; the only external surfaces are REST ingest and the Control API, with all topic/ACL changes via the control plane.
status: accepted
date: 2026-06-23
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/product/vision.md
  - docs/design/overview.md
  - docs/design/components/broker.md
---

# 0005: Kafka stays internal-only; integrate via APIs and the control plane

## Context

A multi-tenant streaming platform must decide how external systems interact with it. Exposing Kafka directly (brokers, raw protocol, ad-hoc topic creation) leaks internal topology, complicates authn/authz and multi-tenancy, and makes governance and auditing far harder. The platform's principle is *configuration over code* with clear ownership boundaries.

## Decision

**Kafka is internal-only.** It is never exposed directly to external callers. The only external surfaces are:

- **REST ingest** (`POST /ingest/:topic`) for producing events.
- **The Control API** (driven by the web app) for configuration and governance.

All topics and ACLs are created and changed through the control plane — never ad-hoc — and follow the platform [topic naming convention](../../reference/topic-naming.md).

## Consequences

- **Tenancy and governance are enforceable** — every produce/consume path is scoped and audited; topic creation is centralized.
- **The broker can evolve** (scaling, rebalancing, version upgrades) without breaking external contracts, which depend only on REST + the Control API.
- **Producers integrate over HTTP**, not the Kafka protocol — simpler for callers, at the cost of an ingress hop.
- **Direct broker access is an operator-only, internal concern** (e.g. `docker exec` for inspection), never a product surface.
