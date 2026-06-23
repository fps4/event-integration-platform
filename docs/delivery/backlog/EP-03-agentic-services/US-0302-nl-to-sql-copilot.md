---
title: "US-0302: NL → Flink SQL authoring copilot"
summary: A copilot that drafts a Flink SQL transform from a natural-language description and the source/target schemas, for human review before activation.
status: draft
milestone: M3
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/agent-services.md
  - docs/design/components/transform-engine.md
---

# US-0302: NL → Flink SQL authoring copilot

As an **integration engineer**, I want to describe a mapping in natural language and get a candidate Flink SQL transform so that I can author transforms faster without writing SQL from scratch.

## Acceptance criteria

- **AC-1.** WHEN a user submits a natural-language description with the source and target schemas THE SYSTEM SHALL produce a candidate Flink SQL statement.
- **AC-2.** THE SYSTEM SHALL present the candidate for review and editing in the SQL editor with a dry-run preview.
- **AC-3.** THE SYSTEM SHALL NOT activate a generated transform without explicit user confirmation.
  ↳ source: PRD-0001 AC-11 · rationale: human-in-the-loop for changes that affect data flow
- **AC-4.** WHEN the user requests a refinement THE SYSTEM SHALL revise the candidate while preserving the prior version for comparison.
