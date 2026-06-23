---
title: "US-0303: DLQ triage & remediation agent"
summary: An agent that explains why dead-letter records failed and proposes a transform/schema fix plus a scoped replay plan, applied by a human.
status: draft
milestone: M3
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/agent-services.md
  - docs/design/components/observability.md
---

# US-0303: DLQ triage & remediation agent

As an **operator**, I want dead-letter failures explained with a proposed fix so that I can recover failed messages without manually decoding each error.

## Acceptance criteria

- **AC-1.** WHEN an operator opens a DLQ record THE SYSTEM SHALL produce a plain-language root-cause explanation from the failure context and the relevant schema/transform.
- **AC-2.** THE SYSTEM SHALL propose a remediation — a transform or schema change and a scoped replay plan.
- **AC-3.** THE SYSTEM SHALL NOT apply a remediation or trigger a replay without explicit human confirmation.
- **AC-4.** WHEN a remediation is applied and replayed THE SYSTEM SHALL record the action in the audit trail.
  ↳ source: PRD-0001 AC-7, NFR-5
