---
title: "M2: Web replatform"
summary: Rebuild the web app on Next.js + shadcn/ui + Tailwind, dropping MUI/Emotion and private dependencies for the public release.
status: proposed
milestone: M2
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/decisions/0003-web-application-stack.md
  - docs/delivery/backlog/EP-02-web-replatform/README.md
---

# M2: Web replatform

## What it ships

The web control surface rebuilt on the standard stack — Next.js (App Router) + React + TypeScript, shadcn/ui over Radix, Tailwind CSS — per [ADR-0003](../../design/decisions/0003-web-application-stack.md).

## Deliverables → stories

| Deliverable | Stories |
|---|---|
| App scaffold (App Router, Tailwind, shadcn, theming) | [US-0201](../backlog/EP-02-web-replatform/US-0201-app-scaffold.md) |
| Port list/edit views and forms (react-hook-form + zod) | (EP-02) |
| Re-theme React Flow pipeline builder onto Tailwind tokens | (EP-02) |
| Remove MUI/Emotion and private packages | (EP-02) |

## What it does / does not ship

- **Does:** a fully open-source web dependency graph; parity with current control-plane flows.
- **Does not:** the Flink SQL editor backend (M1) or AI copilot UI (M3) — it provides the surfaces they plug into.

## What it proves

The web app runs on the standard, public, open-source stack with no private dependencies, ready for public release.

## Definition of complete

No MUI/Emotion/private packages remain; control-plane flows work on the new stack; the pipeline builder renders with Tailwind/shadcn theming.

## Open questions

- Reuse vs rebuild of the React Flow node components during the theme migration.
