---
title: "Web app: React Flow pipeline builder"
summary: File organization and implementation strategy for the visual pipeline builder — React Flow themed with Tailwind/shadcn, nodes for source/topic/transform/sink.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/webapp.md
  - docs/design/components/transform-engine.md
  - docs/reference/topic-naming.md
---

# Web app: React Flow pipeline builder

## Purpose

The file organization and implementation strategy for the visual pipeline builder, which lets users compose pipelines by connecting sources, transforms, topics, and sinks. It uses **React Flow** themed with the web app's Tailwind/shadcn tokens (see [ADR-0003](../design/decisions/0003-web-application-stack.md)).

- **Location:** `services/webapp/components/pipeline/flow/`.
- **Integration point:** the pipeline detail route renders `<PipelineFlow />` in the main panel.
- **Dependencies:** `reactflow`, Tailwind, shadcn/ui primitives.
- **Data model:** aligns with the Control API pipeline/connector/transform schemas.

## File structure

```
services/webapp/components/pipeline/
├── pipeline-detail.tsx              # renders <PipelineFlow />
└── flow/
    ├── index.ts                     # exports PipelineFlow
    ├── pipeline-flow.tsx            # ReactFlow container + state
    ├── nodes/
    │   ├── index.ts
    │   ├── source-node.tsx          # HTTP / Kafka source connectors
    │   ├── transform-node.tsx       # Flink SQL transform
    │   ├── sink-node.tsx            # HTTP / S3 sink
    │   └── topic-node.tsx           # Kafka topic
    ├── edges/
    │   └── topic-edge.tsx           # styled data-flow edge
    ├── controls/
    │   ├── node-palette.tsx         # draggable node templates
    │   └── flow-toolbar.tsx         # save, validate, layout, export
    ├── templates/
    │   ├── basic-ingest.ts          # HTTP → topic (raw)
    │   ├── transform-pipeline.ts    # source → raw → Flink SQL → enriched → sink
    │   └── multi-sink.ts            # one source → multiple sinks
    ├── utils/
    │   ├── flow-validator.ts        # topology + topic-naming checks
    │   ├── flow-serializer.ts       # flow ↔ Control API format
    │   └── layout-helper.ts         # auto-layout (dagre/elk)
    └── hooks/
        ├── use-pipeline-flow.ts     # nodes/edges state + callbacks
        └── use-flow-persistence.ts  # load/save via Control API
```

## Components

- **`pipeline-flow.tsx`** — renders `<ReactFlow>` with custom node/edge types; manages zoom/pan/selection; integrates the palette, toolbar, MiniMap, Background, and Controls; handles node/edge CRUD; emits save/validate.
- **`nodes/`** — each node renders an icon, label, config summary, status, and input/output handles, styled with Tailwind/shadcn tokens:
  - `source-node` — HTTP/Kafka source; outputs to a topic.
  - `topic-node` — a Kafka topic showing the [naming convention](../reference/topic-naming.md), partitions, retention.
  - `transform-node` — a **Flink SQL** transform: input topic → SQL → output topic; opens the SQL editor with dry-run.
  - `sink-node` — HTTP/S3 sink; inputs from a topic.
- **`edges/topic-edge.tsx`** — animated edge colored by topic variant (raw, enriched, dlq).
- **`controls/node-palette.tsx`** — draggable templates grouped Sources / Topics / Transforms / Sinks.
- **`controls/flow-toolbar.tsx`** — Save (serialize → Control API), Validate (`flow-validator`), Auto-layout, Export/Import, Templates.
- **`utils/flow-validator.ts`** — enforces topic naming, required DLQ/retry topics, connection rules (sources output to topics; transforms need input+output; sinks input from topics), no duplicate names, optional cycle detection. Returns `{ type: 'error'|'warning', node, message }[]`.
- **`utils/flow-serializer.ts`** — `flowToApi(nodes, edges)` and `apiToFlow(pipeline)`; handles positions, metadata, config.
- **`hooks/use-pipeline-flow.ts`** and **`use-flow-persistence.ts`** — state management and load/save against `PUT /api/pipelines/:id`.

## Persistence model

Pipeline flow configuration is stored via the Control API on the `Pipeline` document. Two complementary representations stay in sync on save:

- **Logical** — `streams[]`, `sourceClients[]`, `sinkConnections[]`, `transforms[]` (Flink SQL) — used by the Control API to provision Kafka/Flink resources.
- **Visual** — `nodePositions` / `flowConfig` (nodes, edges, viewport) — used to render and edit the diagram. Optional, so pipelines created without the visual editor still work.

## Node → API mapping

- **HTTP/Kafka source node** → source connector on the pipeline (`sourceClients`).
- **HTTP/S3 sink node** → sink connection on the pipeline (`sinkConnections`).
- **Flink SQL transform node** → a `TransformConfig` entry (`type: 'flink-sql'`, `sql`, `sourceStream`, `targetStream`, `failureQueue`).
- **Topic node** → a stream/topic provisioned by the Control API.

## UX practices

- Drag-to-canvas from the palette; red/green handles for invalid/valid connections.
- Double-click a node to open its config (the transform node opens the Flink SQL editor with dry-run preview).
- MiniMap for large flows; Delete/Backspace to remove a selected node; debounce validation.
- Flow state stays ephemeral until Save; use `react-hook-form` + `zod` for node config dialogs.

## References

- React Flow — https://reactflow.dev/
- Topic conventions — [topic-naming](../reference/topic-naming.md)
- The list/edit pattern — [webapp-adding-new-list-edit-view](./webapp-adding-new-list-edit-view.md)
