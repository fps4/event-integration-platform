---
title: Tasks
summary: The in-flight delivery loop — gates, PRs, and merges. Task state is event-sourced, not stored as files.
status: current
last_updated: 2026-06-23
owners: [platform-team]
related:
  - docs/delivery/backlog/README.md
  - docs/delivery/roadmap/README.md
---

# Tasks

The in-flight delivery loop: a user story becomes a task that moves through spec → design → build → merge. Task state is event-sourced (PRs, CI, merges) rather than kept as checked-in files, so this folder holds only this intro.

A task is `active`, `blocked`, `cancelled`, or `done`. The driving work item is a user story in the [backlog](../backlog/README.md); the milestone plan is in the [roadmap](../roadmap/README.md).
