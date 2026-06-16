# Project Initiation — btg-devops

## 1. Purpose

This document formally initiates the **btg-devops** project for Bistec Global.

The purpose of the initiation phase is to make sure the project owner, mentor, developers, and infrastructure team have the same understanding of what will be built, what will not be built in the current scope, how the system will be designed, and what must be completed before full development starts.

---

## 2. Project Summary

btg-devops is an Azure infrastructure audit and DevOps intelligence tool.

The project provides one shared analysis engine with three access paths:

1. **CLI Path** — DevOps engineers run `btg-devops analyze [module]` from the terminal.
2. **Dashboard Normal Mode** — users select a module in a Next.js dashboard and receive live findings through the Go HTTP API server.
3. **Dashboard Agent Mode** — users ask plain English questions; Claude Agent calls MCP tools and explains the findings.

The initial focus is Azure infrastructure auditing across 12 resource types. AWS, GCP, Slack alerts, drift detection, runbook library, and audit history are deferred until the current scope is approved and stable.

---

## 3. Initiation Objectives

The objectives of the initiation phase are:

- Confirm the Azure-focused project scope.
- Confirm the 12 analyzer modules and severity model.
- Document the final architecture using C4 container and component diagrams.
- Record architecture decisions in ADR format.
- Create a full Jira-ready task list for v0.13.0 and v0.14.0.
- Confirm current-scope items and deferred features.
- Prepare all documents for Confluence and project-owner review.
- Start development only after scope, architecture, and task list approval.

---

## 4. Initiation Deliverables

| Deliverable | File | Status |
|---|---|---|
| Project Scope | `docs/project-scope.md` | Prepared |
| Architecture Design | `docs/architecture-design.md` | Prepared |
| Full Task List | `docs/full-task-list.md` | Prepared |
| Architecture Decision Records | `docs/ADR.md` | Prepared |
| Sequence Diagrams | `docs/seq.md` | Prepared |
| Roadmap | `docs/roadmap.md` | Prepared |
| C4 Diagrams | `docs/diagrams/` | Prepared |
| README Index | `README.md` | Prepared |

---

## 5. Linked Documents

| Document | Purpose |
|---|---|
| `docs/project-scope.md` | Defines objectives, deliverables, boundaries, constraints, and success criteria |
| `docs/architecture-design.md` | Explains containers, components, data flow, integrations, and deployment |
| `docs/full-task-list.md` | Breaks implementation into Phase 1, Phase 2, and future scope tasks |
| `docs/ADR.md` | Records accepted decisions and rejected alternatives |
| `docs/seq.md` | Shows runtime sequence flows and error paths |
| `docs/roadmap.md` | Shows version-by-version delivery order |

---

## 6. High-Level Architecture Summary

| Layer / Area | Description |
|---|---|
| CLI Interface | Go/Cobra commands used by DevOps engineers |
| HTTP API Server | Go REST server used by Dashboard Normal Mode |
| MCP Server | Go MCP server exposing all 12 analyzers as tools for Claude Agent |
| Analysis Engine | Shared analyzer logic for all three paths |
| Azure SDK Layer | Uses `azidentity` and Azure ARM SDK clients to fetch live resource data |
| Output Formatter | Prints CLI findings as table or JSON |
| Next.js Dashboard | Browser UI with Normal Mode and Agent Mode |
| Claude Agent | AI layer that chooses MCP tools and explains findings |
| GitHub Actions CI | Build, test, lint, and release automation |
| Vercel | Hosts the Next.js dashboard |

---

## 7. Work Tracks

| Track | Owner | Responsibility |
|---|---|---|
| CLI / Backend Track | Dev A | Go analyzers, CLI commands, HTTP API Server, MCP Server, tests, CI |
| Dashboard Track | Dev B | Next.js dashboard, Normal Mode, Agent Mode, Vercel deployment |
| Review / Approval Track | Mentor / Project Owner | Scope approval, architecture review, access confirmation, merge approval |
| Infrastructure Review Track | Infrastructure Team | Provide Azure details, review findings, confirm remediation actions |

---

## 8. Key Dependencies

| Dependency | Reason |
|---|---|
| GitHub repo access | Required for branches, pull requests, CI, and release tags |
| Azure read-only credentials | Required to run real infrastructure audits |
| Azure Cost Management Reader role | Required for `btg-devops analyze cost` |
| Claude / Anthropic API access | Required for Dashboard Agent Mode |
| Vercel access | Required to deploy the Next.js dashboard |
| Project owner approval | Required before major development starts |

---

## 9. Acceptance Criteria Mapping

| Acceptance Criteria | Evidence |
|---|---|
| Scope document written and ready for approval | `docs/project-scope.md` |
| Architecture documented with component relationships | `docs/architecture-design.md`, `docs/diagrams/` |
| Full task list created and ready for Jira | `docs/full-task-list.md` |
| ADRs recorded for major architecture decisions | `docs/ADR.md` |
| Documents ready for Confluence | `README.md` and all `docs/` files |

---

## 10. Next Steps

1. Upload the final documents to Confluence.
2. Link the documents to the Jira parent story.
3. Ask the project owner / mentor to review the scope.
4. Confirm Azure credentials and GitHub access.
5. Start Phase 1 tasks only after approval.
6. Start Phase 2 only after Phase 1 is released as v0.13.0.

---

## 11. Summary

The initiation phase confirms that btg-devops will be built as an Azure-focused audit tool with one shared analyzer engine and three access paths: CLI, Dashboard Normal Mode, and Dashboard Agent Mode. The final documents, diagrams, ADRs, roadmap, and task list are ready for project-owner review.
