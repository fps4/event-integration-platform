---
title: Personas & responsibilities
summary: The platform's users — integration engineer, operator/on-call, security/compliance — and the ownership split between platform and product teams.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/product/vision.md
  - docs/product/prd/0001-event-integration-platform.md
---

# Personas & responsibilities

## Purpose

Who uses the platform, and where the ownership boundaries sit. The platform team provides capabilities and guardrails; product teams own mappings and activation.

## Personas

### Integration engineer (product team)
- Defines source and destination schemas.
- Authors and versions **Flink SQL** transformations (assisted by the AI authoring copilot).
- Configures destinations (REST/S3).
- Activates / pauses integrations.

### Operator / on-call (platform or shared)
- Monitors consumer lag, failure rates, and DLQ growth.
- Triggers replay or backfill (assisted by the AI DLQ-triage agent).
- Pauses/resumes integrations (circuit breaker).
- Reviews deployment and runtime status.

### Security / compliance
- Reviews schema metadata (PII flags).
- Audits configuration changes, replays, and AI-assist actions.
- Verifies access and RBAC boundaries.

## Ownership boundaries

| Area | Platform team | Product teams |
|---|---|---|
| Kafka cluster | ✅ | ❌ |
| Flink SQL transform runtime | ✅ | ❌ |
| Connectors (HTTP, S3, etc.) | ✅ | ❌ |
| Schema Registry | ✅ | ❌ |
| Web control plane | ✅ | ❌ |
| AI assist layer (runtime + governance) | ✅ | ❌ |
| Transformation logic (Flink SQL) | ❌ | ✅ |
| Topic ownership | ❌ | ✅ |
| Mapping lifecycle (transform versions) | ❌ | ✅ |
| Alerts & SLAs | Shared | Shared |
