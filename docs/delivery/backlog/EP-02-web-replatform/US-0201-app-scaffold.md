---
title: "US-0201: Web app scaffold on the standard stack"
summary: Scaffold the web app on Next.js App Router with Tailwind, shadcn/ui, and theming, replacing the MUI/Emotion foundation.
status: draft
milestone: M2
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/webapp.md
  - docs/guides/webapp-adding-new-list-edit-view.md
---

# US-0201: Web app scaffold on the standard stack

As the **platform team**, I want the web app scaffolded on Next.js + Tailwind + shadcn/ui so that all subsequent UI work is built on the standard, public, open-source stack.

## Acceptance criteria

- **AC-1.** THE SYSTEM SHALL build the web app on Next.js (App Router) + React + TypeScript with standalone output.
- **AC-2.** THE SYSTEM SHALL style the app with Tailwind CSS and shadcn/ui components over Radix primitives, with light/dark theming via `next-themes`.
- **AC-3.** THE SYSTEM SHALL contain no MUI, Emotion, admin-theme, or private/internal component packages in its dependency graph.
  ↳ source: ADR-0003 · rationale: public release requires a fully open-source dependency graph
- **AC-4.** WHEN the app is built THE SYSTEM SHALL pass lint and type checks with no private-registry dependencies.
