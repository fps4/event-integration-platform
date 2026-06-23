---
title: "0004: Agentic capabilities & architecture"
summary: Add a model-provider-neutral AI assist layer — NL→Flink-SQL authoring, schema-aware mapping, DLQ triage, in-SQL inference, and streaming agents over MCP — with governance.
status: accepted
date: 2026-06-23
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/agent-services.md
  - docs/design/components/transform-engine.md
  - docs/design/decisions/0002-flink-sql-as-transformation-engine.md
---

# 0004: Agentic capabilities & architecture

## Context

With Flink SQL as the transformation core ([ADR-0002](./0002-flink-sql-as-transformation-engine.md)), the platform can host AI capabilities that are otherwise hard to retrofit. A scan of the current integration-platform market shaped the scope:

- **Baseline (table stakes).** Major streaming platforms now offer model inference callable from streaming SQL, in-engine anomaly detection/forecasting, event-driven "streaming agents", and exposure of streaming context to agents over the **Model Context Protocol (MCP)**. To stay credible we should match this baseline.
- **Gaps to win on.** Three use-cases are weak or absent across the field: (1) a copilot that *writes and edits* the transform from natural language, (2) **schema-registry-aware** field mapping (only a few mapping-heavy iPaaS vendors do sample-based mapping), and (3) **AI dead-letter triage & remediation** of runtime data errors (almost nobody productizes this).
- **Constraints.** AI features touch customer data and cost money; they must be governed, auditable, provider-neutral, and never act without human confirmation on changes that affect data flow.

## Decision

Add an **`agent-services`** layer offering:

1. **NL → Flink SQL authoring copilot** — natural language + schemas → candidate Flink SQL, reviewed and confirmed by a human before activation.
2. **Schema-registry-aware field mapping** — suggest field mappings and the SQL that realizes them, from registered schemas and sampled payloads.
3. **DLQ triage & auto-remediation** — explain dead-letter failures and propose a transform/schema fix plus a scoped replay plan (proposed, human-applied).
4. **AI inference functions in Flink SQL** — model inference (classification, embedding, redaction, etc.) callable from a transform.
5. **Event-driven streaming agents** — agents that observe a stream, reason, and act through tools.
6. **Agent governance** — audit of every AI action, a kill-switch, model-provider neutrality, and PII + cost controls.

Topics and the Schema Registry are exposed to agents as **MCP** tools (RBAC'd and audited). The model provider is **pluggable**; no single vendor is assumed. Capabilities (1)–(3) are the prioritized differentiators.

## Consequences

- **New service** (`agent-services`) and a new [component design](../../design/components/agent-services.md); a new MCP server exposing topics + schema registry.
- **Transform engine integration** — in-SQL inference functions and the authoring/mapping output target the [transform runtime](../../design/components/transform-engine.md).
- **Governance surface** — audit log, kill-switch, and provider configuration become first-class; PII handling and cost limits are enforced and recorded as NFRs (PRD NFR-5).
- **Human-in-the-loop is a hard rule** — AI never activates a transform or applies a fix autonomously (PRD AC-11).
- **Provider neutrality** keeps the platform from being locked to one model vendor and keeps the public repo free of vendor-specific assumptions.
