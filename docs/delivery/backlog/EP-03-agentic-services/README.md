---
title: "EP-03: Agentic services"
summary: The AI assist layer — model gateway, NL→SQL copilot, DLQ triage, schema mapping, in-SQL inference, MCP exposure — and its governance.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/decisions/0004-agentic-capabilities.md
  - docs/design/components/agent-services.md
  - docs/delivery/roadmap/m3-agentic-assist.md
---

# EP-03: Agentic services

The capability of AI assistance across the platform ([ADR-0004](../../../design/decisions/0004-agentic-capabilities.md)): a provider-neutral model gateway with governance, the NL→Flink-SQL authoring copilot, DLQ triage & remediation, schema-registry-aware field mapping, in-SQL inference functions, and the MCP server exposing topics and the schema registry. All capabilities keep a human in the loop for changes that affect data flow.

See the [M3 scoping doc](../../roadmap/m3-agentic-assist.md) for the current slice.
