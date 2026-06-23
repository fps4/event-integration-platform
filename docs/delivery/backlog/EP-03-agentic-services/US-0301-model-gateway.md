---
title: "US-0301: Provider-neutral model gateway & governance"
summary: A model gateway that routes AI-assist calls to a configurable provider, with audit, kill-switch, and PII/cost controls.
status: draft
milestone: M3
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/agent-services.md
  - docs/design/decisions/0004-agentic-capabilities.md
---

# US-0301: Provider-neutral model gateway & governance

As the **platform team**, I want AI-assist calls to route through a governed, provider-neutral gateway so that the platform isn't locked to one vendor and every AI action is auditable and cost-controlled.

## Acceptance criteria

- **AC-1.** THE SYSTEM SHALL route all AI-assist calls through a gateway whose model provider is configurable.
- **AC-2.** THE SYSTEM SHALL record every AI-assist action in an audit log with principal, inputs summary, and outcome.
- **AC-3.** WHERE a kill-switch is engaged THE SYSTEM SHALL refuse new AI-assist calls.
- **AC-4.** THE SYSTEM SHALL apply per-environment cost limits and PII-handling rules to AI calls.
  ↳ source: PRD-0001 NFR-5
