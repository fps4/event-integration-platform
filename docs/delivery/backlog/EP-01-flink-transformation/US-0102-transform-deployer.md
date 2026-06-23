---
title: "US-0102: Transform deployer & FlinkSqlTransform model"
summary: A control-plane deployer that materializes the active versioned Flink SQL transform per pipeline, backed by the FlinkSqlTransform model.
status: draft
milestone: M1
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/control-api.md
  - docs/design/components/data-models.md
  - docs/delivery/roadmap/m1-flink-transformation-core.md
---

# US-0102: Transform deployer & FlinkSqlTransform model

As an **integration engineer**, I want my versioned Flink SQL transform to deploy automatically when I activate it so that I can roll mappings out and back without bespoke deployment steps.

## Acceptance criteria

- **AC-1.** WHEN a `FlinkSqlTransform` version is set to `active` THE SYSTEM SHALL deploy that statement for the pipeline and stop the previously active version.
- **AC-2.** WHEN an active version is changed THE SYSTEM SHALL redeploy atomically with rollback to the prior active version on failure.
- **AC-3.** THE SYSTEM SHALL treat transform versions as immutable and never resolve a binding to "latest".
- **AC-4.** WHEN a transform or validation fails at runtime THE SYSTEM SHALL write the record to the configured DLQ with `x-request-id` and `x-dlq-reason`.
  ↳ source: PRD-0001 AC-6
