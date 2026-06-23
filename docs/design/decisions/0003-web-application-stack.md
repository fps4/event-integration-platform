---
title: "0003: Web application stack"
summary: The web app uses Next.js + React + TypeScript with shadcn/ui, Radix, and Tailwind CSS, replacing MUI/Emotion and any private component packages.
status: accepted
date: 2026-06-23
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/webapp.md
  - docs/guides/webapp-react-flow.md
---

# 0003: Web application stack

## Context

The web app was built on Material UI (v7) with Emotion and a third-party admin theme, pulling in private/internal component packages. Two pressures motivate a change:

- **Public release.** The repository is going public; private/internal dependencies (closed component libraries, vendored SDKs, internal theme packages) cannot ship in a public repo.
- **Standardization.** We want the web app to match the organization's reference web stack so engineers move between products without relearning the UI toolchain, and so the UI is built on widely-adopted open-source primitives.

## Decision

The web app is built on:

- **Next.js (App Router) + React + TypeScript** — file-based routing, server/client components, standalone output.
- **shadcn/ui** components composed over **Radix UI** primitives — owned in-repo, not a closed dependency.
- **Tailwind CSS** for styling, with `next-themes` for light/dark theming and CSS variables as design tokens.
- **lucide-react** for icons; **react-hook-form** + **zod** for forms and validation.
- **Native `fetch`** against the Control and Observability APIs — no Redux/React Query unless a concrete need arises.
- **React Flow** for the pipeline builder, themed with Tailwind/shadcn tokens.

MUI, Emotion, the admin theme, and all private component packages are removed.

## Consequences

- **The web app is rebuilt** on the new stack; this is a replatforming effort tracked in the backlog, not a doc-only change.
- **Private dependencies are dropped** — the dependency graph is fully open-source, satisfying the public-release constraint.
- **React Flow is re-themed** from MUI styling onto Tailwind/shadcn tokens; the [pipeline-builder guide](../../guides/webapp-react-flow.md) is rewritten accordingly.
- **Styling model changes** from CSS-in-JS (Emotion) to utility-first Tailwind with CSS-variable tokens; component variants use `class-variance-authority`.
- Forms standardize on `react-hook-form` + `zod`; the existing form patterns are ported.
