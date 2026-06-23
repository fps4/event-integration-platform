---
title: Broker (Kafka) service
summary: The platform-operated Kafka cluster — topic/ACL governance, retention/compaction policy, DLQ/retry provisioning, and broker observability.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/reference/topic-naming.md
  - docs/design/overview.md
  - docs/design/components/transform-engine.md
---

# Broker (Kafka) service

## Purpose

The Kafka cluster operated by the platform team. All topics and ACLs are created via the control plane, never ad-hoc.

## Behaviour

- Run Kafka brokers with replication, retention, and compaction policies defined per domain.
- Enforce platform topic conventions and ACLs for workspaces/clients.
- Expose metrics for broker health, topic storage, partition skew, and consumer lag.
- Provide DLQ/retry topics per integration with clear ownership.

Topic names follow the convention defined in [topic-naming](../../reference/topic-naming.md).

## Best practices

- **Partitioning** — size partitions to keep consumer lag within SLO; avoid over-partitioning small streams.
- **Retention** — per-topic (raw shorter, DLQ longer for replay); compaction only where idempotent keys exist.
- **DLQ/retry** — always provision `dlq` (and optional `retry`) topics alongside source/transformed topics.
- **ACLs** — producers limited to their workspace/client topics; consumers restricted to needed streams; block wildcard access.
- **Schema binding** — all produce/consume paths register schemas in the Schema Registry; enforce compatibility (`BACKWARD`/`FULL`).
- **Observability** — export partition sizes, ISR health, controller status, and consumer lag; alert on lag, DLQ rate, offline partitions.
- **Change management** — topic creation/update only via the Control API; audit who/when/what.

## Manual topic read (docker exec)

- Get a shell into the broker container: `docker exec -it <broker-container-name> bash`.
- The Apache image ships CLI tools under `/opt/kafka/bin`.
- List topics to confirm naming: `/opt/kafka/bin/kafka-topics.sh --bootstrap-server localhost:9092 --list`.
- Read from a topic without reusing application consumer groups (avoids stealing offsets):

  ```bash
  /opt/kafka/bin/kafka-console-consumer.sh \
    --bootstrap-server localhost:9092 \
    --topic dev.abcd.wxyz.orders.raw \
    --from-beginning \
    --group tmp-inspect-$(date +%s) \
    --property print.key=true \
    --property print.timestamp=true \
    --timeout-ms 10000
  ```

- For a quick peek at only the newest records, drop `--from-beginning` and `--timeout-ms`.
