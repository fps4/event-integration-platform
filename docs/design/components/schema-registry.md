---
title: Schema Registry integration
summary: How the bundled Schema Registry wires into Kafka Connect, the HTTP connectors, the Flink transform runtime, and the Control API.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/connectors.md
  - docs/design/components/transform-engine.md
  - docs/design/components/control-api.md
---

# Schema Registry integration

## Purpose

How to use the bundled Schema Registry (`schema-registry:8081`) with the Control API, HTTP source/sink connectors, the Flink transform runtime, and Kafka Connect, as wired in `compose.yaml`.

## Topology

- Registry host: `http://schema-registry:8081` (private container network).
- Broker: `broker:9092`.
- Kafka Connect: `kafka-connect:8083`.

## Kafka Connect settings

Switch Connect to Schema Registry-aware converters (override defaults in `compose.yaml` or per-connector config):
- `CONNECT_KEY_CONVERTER=io.confluent.connect.avro.AvroConverter`
- `CONNECT_VALUE_CONVERTER=io.confluent.connect.avro.AvroConverter`
- `CONNECT_KEY_CONVERTER_SCHEMA_REGISTRY_URL=http://schema-registry:8081`
- `CONNECT_VALUE_CONVERTER_SCHEMA_REGISTRY_URL=http://schema-registry:8081`

If using JSON Schema or Protobuf, swap the converter classes accordingly. Ensure the plugins you need are installed in the Connect image and rebuild.

## Example: HTTP source via Kafka Connect

```bash
curl -X POST http://localhost:8083/connectors -H "Content-Type: application/json" -d '{
  "name": "http-source-avro",
  "config": {
    "connector.class": "io.confluent.connect.http.HttpSourceConnector",
    "tasks.max": "1",
    "http.api.url": "http://connector-http-source:8085/ingest/my-topic",
    "kafka.topic": "my-topic",
    "value.converter": "io.confluent.connect.avro.AvroConverter",
    "value.converter.schema.registry.url": "http://schema-registry:8081",
    "key.converter": "io.confluent.connect.avro.AvroConverter",
    "key.converter.schema.registry.url": "http://schema-registry:8081"
  }
}'
```

## Flink transform runtime

The [transform runtime](./transform-engine.md) reads source topics and writes enriched topics with Schema-Registry-aware (de)serialization, so Flink SQL table definitions bind to registered schemas and validate output against the destination schema before producing.

## Custom Node services

- `connector-http-source` validates and logs today; to publish with schemas, add a Kafka producer that serializes via the Schema Registry (e.g. `@kafkajs/confluent-schema-registry`) pointing at `schema-registry:8081`.
- `connector-http-sink` should consume with a Schema-Registry-aware consumer to deserialize Avro/JSON Schema/Protobuf payloads.

## Control API

Today the Control API only manages topics via KafkaJS Admin; no Schema Registry usage is required. If adding producers/consumers later, reuse the same Schema Registry client and converters against `schema-registry:8081`.
