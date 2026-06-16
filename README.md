# btg-devops — Project Documentation

btg-devops is a DevOps intelligence tool for Bistec Global. It audits Azure infrastructure, classifies findings as **Critical / Warning / Info**, and exposes the same analyzer engine through three access paths: CLI, Dashboard Normal Mode, and Dashboard Agent Mode.

## Main Documents

| Document | Purpose |
|---|---|
| [`docs/project-initiation.md`](docs/project-initiation.md) | Starts the project formally and maps the initiation deliverables |
| [`docs/project-scope.md`](docs/project-scope.md) | Final Yomal scope file: objectives, deliverables, boundaries, constraints, and success criteria |
| [`docs/architecture-design.md`](docs/architecture-design.md) | Final architecture design aligned with the C4 diagrams and ADRs |
| [`docs/full-task-list.md`](docs/full-task-list.md) | Final Yomal task list: v0.13.0 quality phase, v0.14.0 features, and future scope |
| [`docs/ADR.md`](docs/ADR.md) | Accepted architecture decisions for the three-path model, HTTP API, MCP/Claude, and dashboard stack |
| [`docs/seq.md`](docs/seq.md) | Mermaid sequence diagrams for CLI, dashboard normal mode, agent mode, and error paths |
| [`docs/roadmap.md`](docs/roadmap.md) | Version roadmap and delivery order |
| [`docs/file-explanation.md`](docs/file-explanation.md) | Simple explanation of every file in this documentation pack |

## Diagrams

| Diagram | PNG Preview | Editable Draw.io File |
|---|---|---|
| Final Component Diagram | [`docs/diagrams/component-final.png`](docs/diagrams/component-final.png) | [`docs/diagrams/component-final.drawio`](docs/diagrams/component-final.drawio) |
| Final Container Diagram | [`docs/diagrams/container-final.png`](docs/diagrams/container-final.png) | [`docs/diagrams/container-final.drawio`](docs/diagrams/container-final.drawio) |
| Final Three-Path Flow Diagram | [`docs/diagrams/flow-final.png`](docs/diagrams/flow-final.png) | [`docs/diagrams/flow-final.drawio`](docs/diagrams/flow-final.drawio) |
| CLI-only Component Diagram | [`docs/diagrams/component-cli-only.png`](docs/diagrams/component-cli-only.png) | [`docs/diagrams/component-cli-only.drawio`](docs/diagrams/component-cli-only.drawio) |
| CLI-only Container Diagram | [`docs/diagrams/container-cli-only.png`](docs/diagrams/container-cli-only.png) | [`docs/diagrams/container-cli-only.drawio`](docs/diagrams/container-cli-only.drawio) |
| How It Works Flow | [`docs/diagrams/flow-how-it-works.png`](docs/diagrams/flow-how-it-works.png) | PNG only |

## Final File Decision

- `project-scope.md` uses Yomal’s Azure-focused scope.
- `full-task-list.md` uses Yomal’s task list.
- `architecture-design.md` is rewritten to match the final three-path architecture.
- `ADR.md` is kept as the decision record.
- `roadmap.md` is kept separately.
- Old broad files that used `btg-ops`, React + Vite, Slack, drift, and runbook as current-scope items are not used as final scope.

