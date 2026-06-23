---
title: Topic naming convention
summary: The canonical, env-scoped, multi-tenant Kafka topic naming standard and its validation rules.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/broker.md
  - docs/design/components/connectors.md
---

# Topic naming convention

## Pattern

Env-scoped and multi-tenant:

```
<env>.<workspace_code>.<pipeline_code>.<stream>.<variant>
```

| Segment | Meaning |
|---|---|
| `env` | `dev` \| `test` \| `prod` |
| `workspace_code` | 4-character lowercase code (a–z), generated at workspace creation |
| `pipeline_code` | 4-character lowercase code (a–z), generated at pipeline creation |
| `stream` | business domain/entity name (e.g. `orders`, `payments`) |
| `variant` | `source` (input), `sink` (output), `raw`, `enriched`, `dlq`, `retry` |

### Client scoping

When a client owns a subset, append a client identifier before the variant:

```
<env>.<workspace_code>.<pipeline_code>.client-<clientId>.<stream>.<variant>
```

### Examples

- `dev.abcd.wxyz.orders.source`
- `prod.abcd.wxyz.orders.sink`
- `prod.abcd.wxyz.client-billing.payments.enriched`
- `prod.abcd.wxyz.orders.dlq`

## Validation rules

Enforced by `validateTopicName` in `@event-integration-platform/connector-core`:

- Trimmed string; characters limited to alphanumerics plus `._-`.
- No `.` or `..` path segments.
- Maximum length 249 characters.
- Invalid names throw `InvalidTopicNameError`.

## Notes

- DLQ (`*.dlq`) and retry (`*.retry`) topics are first-class and provisioned alongside source/enriched topics.
- Topics are created only through the Control API, never ad-hoc (see [ADR-0005](../design/decisions/0005-kafka-internal-only.md)).
