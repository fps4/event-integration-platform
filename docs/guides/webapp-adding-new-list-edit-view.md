---
title: "Web app: adding a new list/edit view"
summary: Checklist for adding a new entity list/edit page in the Next.js + shadcn/ui web app — routes, navigation, view component, data wiring, and checks.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/design/components/webapp.md
  - docs/guides/webapp-react-flow.md
---

# Web app: adding a new list/edit view

## Purpose

A checklist for adding a new entity list/edit page on the Next.js App Router + shadcn/ui stack (see [ADR-0003](../design/decisions/0003-web-application-stack.md)). Keep UI simple with placeholders until real data is wired.

## 1) Add routes

- Create App Router segments under `services/webapp/app/<feature>/` — `page.tsx` (list), `[id]/page.tsx` (detail), and `new/page.tsx` / `[id]/edit/page.tsx` as needed.
- Centralize path helpers in `services/webapp/lib/routes.ts` so components import a stable route map.

## 2) Add navigation

- Add a nav item in the sidebar/nav config pointing to the new list route.
- Pick a `lucide-react` icon; keep titles short.

## 3) Build the view component

- Place components under `services/webapp/components/<feature>/`.
- Compose with shadcn/ui primitives: `Card`, `Table` (or a card grid), `Button`, `Badge` for status, `Breadcrumb` for the heading + a primary action (e.g. "New …").
- For placeholders: use inline stub data (id, name, status, timestamps); render disabled/"soon" action buttons; skip filters/sorting until requirements exist.
- Style with Tailwind utility classes and CSS-variable tokens; use `cn()` for conditional classes.

## 4) Wire the page

- Keep `app/<feature>/page.tsx` thin: export `metadata` and render the view component. Logic lives in the component.

## 5) Data & API wiring (when ready)

- Add a typed client under `services/webapp/lib/api/<feature>.ts` using native `fetch` (and the generated OpenAPI client where available).
- Handle loading/empty/error states (skeletons, an empty state, and `sonner` toasts for errors).
- For forms, use `react-hook-form` + `zod` resolvers with shadcn `Form` components under `components/<feature>/form/`.

## 6) State & UX

- Server components for data fetching where possible; client components for interactivity.
- Responsive layout via Tailwind grid utilities; light/dark via `next-themes`.
- Use `next/link` for internal navigation; add `TODO` comments for pending behavior rather than speculative logic.

## 7) Checks

- Run `npm run lint` and `npm run build` for the web app.
- Verify the nav link appears and the page renders without runtime errors.
- Follow the metadata pattern: `export const metadata = { title: '<Feature> | Event Integration Platform' }`.
