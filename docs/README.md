# Documentation

This repository's documentation follows a **two-plane** information architecture: a **Docs** plane you read for reference, and a **Delivery** plane you track for work. A document's section is derived from its folder — the folder *is* the shelf.

> This index is for GitHub/IDE orientation. The product landing is [`overview.md`](./overview.md).

## Docs plane (reference)

| Shelf | Path | What lives here |
|---|---|---|
| **Overview** | [`overview.md`](./overview.md) | One short product landing. |
| **Product** | [`product/`](./product/) | Intent, vision, personas, and product requirements. |
| **Design** | [`design/`](./design/) | Architecture, component designs, contracts, and ADRs. |
| **Reference** | [`reference/`](./reference/) | The lookup surface — glossary, topic naming, config, API specs. |
| **Guides** | [`guides/`](./guides/) | How-to, setup, and contributor guidelines. |

## Delivery plane (work)

| Area | Path | What lives here |
|---|---|---|
| **Roadmap** | [`delivery/roadmap/`](./delivery/roadmap/) | Milestones and sequencing. |
| **Backlog** | [`delivery/backlog/`](./delivery/backlog/) | Epics → user stories. |
| **Tasks** | [`delivery/tasks/`](./delivery/tasks/) | The in-flight delivery loop. |
| **Issues** | [`delivery/issues/`](./delivery/issues/) | Defects and accepted limitations. |

## Conventions

- Every doc opens with frontmatter (`title`, `summary`, `status`, `last_updated`, `owners`, `related`).
- Architecture diagrams are **Mermaid** blocks inside markdown — no external diagram files.
- ADRs are immutable once accepted; supersede rather than edit.
- Section READMEs are **intros**, not catalogues.
