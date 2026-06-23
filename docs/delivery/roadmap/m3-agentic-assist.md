---
title: "M3: Agentic assist"
summary: Ship the AI assist layer — NL→Flink-SQL authoring, schema-registry-aware field mapping, DLQ triage & remediation, in-SQL inference, and MCP exposure.
status: proposed
milestone: M3
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/decisions/0004-agentic-capabilities.md
  - docs/delivery/backlog/EP-03-agentic-services/README.md
---

# M3: Agentic assist

## What it ships

The `agent-services` layer per [ADR-0004](../../design/decisions/0004-agentic-capabilities.md), built on the M1 Flink SQL core and surfaced through the M2 web app.

## Deliverables → stories

| Deliverable | Stories |
|---|---|
| Model-provider gateway (provider-neutral) + governance/audit | [US-0301](../backlog/EP-03-agentic-services/US-0301-model-gateway.md) |
| NL → Flink SQL authoring copilot | [US-0302](../backlog/EP-03-agentic-services/US-0302-nl-to-sql-copilot.md) |
| DLQ triage & remediation agent | [US-0303](../backlog/EP-03-agentic-services/US-0303-dlq-triage.md) |
| Schema-registry-aware field mapping | (EP-03) |
| In-SQL inference functions | (EP-03) |
| MCP server exposing topics + schema registry | (EP-03) |

## What it does / does not ship

- **Does:** the three prioritized differentiators (NL→SQL, mapping, DLQ triage) plus in-SQL inference and MCP exposure, all human-in-the-loop.
- **Does not:** autonomous activation of transforms or auto-applied fixes — proposals require human confirmation.

## What it proves

An operator can describe a mapping in natural language and get reviewable Flink SQL, get field-mapping suggestions from the schema registry, and have a dead-letter failure explained with a proposed fix and replay plan.

## Definition of complete

The copilot drafts Flink SQL from NL (confirmed before activation); DLQ triage explains failures and proposes fixes; AI actions are audited; the model provider is configurable.

## Open questions

- Default model provider and cost/PII control thresholds per environment.
