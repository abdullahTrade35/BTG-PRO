# Project Scope — btg-devops

## 1. Purpose

btg-devops is a DevOps intelligence and infrastructure management tool for Bistec Global.

The project focuses on analysing Azure cloud infrastructure, identifying security issues, detecting misconfigurations, supporting cost optimisation, and providing both CLI-based and dashboard-based access to audit results.

This document defines the agreed project scope before development begins. It covers objectives, deliverables, team responsibilities, boundaries, constraints, and success criteria.

---

## 2. Project Objectives

The main objectives of btg-devops are:

1. Build a CLI tool to audit Azure cloud infrastructure across 12 resource types.
2. Identify security, configuration, and cost issues with severity classification (Critical / Warning / Info).
3. Provide a web dashboard with two access modes — Normal Mode for direct audit access and Agent Mode for AI-assisted plain English querying.
4. Expose all 12 analyzers as MCP tools so Claude Agent can call them intelligently from the dashboard.
5. Support Bistec Global's internal Azure adoption by running regular infrastructure reviews.
6. Maintain least-privilege access and safe infrastructure review practices.

---

## 3. Success Metrics

| Success Metric | Target |
|---|---|
| Weekly infrastructure audit | Weekly audit process is live for Bistec Azure subscription |
| Incident resolution | At least 3 infrastructure incidents resolved using btg-devops tooling by Q3 2026 |
| AI tooling cost | Routine AI-assisted audit cost remains below $5/month |
| Documentation quality | README, project scope, ADRs, and findings are maintained in Markdown |
| Operational adoption | Bistec infrastructure team can use the tool for audit and review activities |
| Security practice | All cloud credentials follow least-privilege access principles |

---

## 4. In-Scope Deliverables

### 4.1 CLI Commands

| Command | Version | Purpose |
|---|---|---|
| `btg-devops analyze [module]` | v0.12.0 | Run a best-practice audit for one specific resource type |
| `btg-devops analyze all` | v0.13.0 | Run all 12 analyzers in one command and produce a combined audit report |
| `btg-devops analyze cost` | v0.14.0 | Query Azure Cost Management API and show billing data, spend by service, and top 15 highest-cost resources |

**Module list for `btg-devops analyze [module]`:**

| Module | Resource type |
|---|---|
| `storage` | Azure Storage Accounts |
| `iam` | Azure IAM / RBAC role assignments |
| `nsg` | Azure Network Security Groups |
| `acr` | Azure Container Registry |
| `cosmosdb` | Azure Cosmos DB |
| `keyvault` | Azure Key Vault |
| `functions` | Azure Functions |
| `publicip` | Azure Public IPs |
| `appservice` | Azure App Services |
| `appserviceplan` | Azure App Service Plans |
| `cognitiveservices` | Azure Cognitive Services |
| `resourcegroup` | Azure Resource Groups |

**Full subscription audit example:**
```bash
# Run all 12 analyzers — table output
btg-devops analyze all

# Run all and get one combined JSON
btg-devops analyze all --output json

# Filter all analyzers to one resource group
btg-devops analyze all --resource-group my-rg
```

**Cost report example:**
```bash
# Last 30 days — table output (default)
btg-devops analyze cost

# Last 7 days
btg-devops analyze cost --days 7

# Filter to one resource group
btg-devops analyze cost --resource-group my-rg

# JSON output
btg-devops analyze cost --output json
```

**Global flags available on all commands:**

| Flag | Purpose |
|---|---|
| `--output` | Output format: `table` (default) or `json` |
| `--resource-group` | Scope the audit to a single resource group |
| `--subscription-id` | Override the subscription ID from environment |

---

### 4.2 Cloud Infrastructure Analysis

All 12 analyzers classify findings into three severity levels:

| Severity | Meaning |
|---|---|
| Critical | Security risk — fix immediately |
| Warning | Cost waste or configuration issue — review soon |
| Info | Best practice improvement — low urgency |

In-scope analysis areas:

- Azure IAM / RBAC
- Azure Storage Accounts
- Azure Network Security Groups
- Azure Key Vault
- Azure Cosmos DB
- Azure Functions
- Azure Container Registry
- Azure Public IPs
- Azure Resource Groups
- App Services and App Service Plans
- Azure Cognitive Services
- Cost analysis via Azure Cost Management API

---

### 4.3 Web Dashboard

A Next.js dashboard deployed on Vercel is in scope. The dashboard provides two modes of access to live Azure audit data — no file upload required.

**Tech stack:**

| Layer | Technology |
|---|---|
| Framework | Next.js 14+ (App Router) |
| Language | TypeScript |
| Styling | Tailwind CSS |
| Charts | Recharts |
| Deployment | Vercel |

**Mode 1 — Normal Mode:**
User selects a module and clicks Analyze. The dashboard calls the `HTTP API Server (cmd/server.go)` via REST. Live findings are returned and rendered visually.

```
User clicks Analyze
    ↓
Next.js Dashboard → HTTP GET /analyze/{module} → HTTP API Server
    ↓
12 Analyzer Modules → Azure SDK → Azure
    ↓
JSON findings → Dashboard renders visually
```

**Mode 2 — Agent Mode:**
User types a question in plain English. The dashboard sends the prompt to Claude Agent which calls the `MCP Server (cmd/mcp.go)`, runs the appropriate analyzers, and returns an AI explanation of the findings.

```
User types "Check if my storage accounts are secure"
    ↓
Next.js Dashboard → Claude Agent → MCP Server (cmd/mcp.go)
    ↓
12 Analyzer Modules → Azure SDK → Azure
    ↓
Claude explains findings in plain English → Dashboard displays AI response
```

**Dashboard panels:**

| Panel | Mode | Purpose |
|---|---|---|
| Analyze Controls | Normal | Module selector and Analyze button |
| Summary Cards | Both | Critical / Warning / Info totals |
| Findings Table | Both | Sortable, paginated findings list |
| Filter Controls | Both | Filter by severity, module, resource group |
| AI Chat Interface | Agent | Plain English prompt and AI response |

---

### 4.4 MCP Server Integration

btg-devops exposes all 12 analyzers as MCP tools via `cmd/mcp.go`. Claude Agent uses these tools to answer natural language questions from the dashboard Agent Mode.

**Entry point:** `cmd/mcp.go`
**Transport:** stdio
**Authentication:** Same four Azure env vars as the CLI

| MCP Tool | Maps to |
|---|---|
| `analyze_storage` | btg-devops analyze storage |
| `analyze_iam` | btg-devops analyze iam |
| `analyze_nsg` | btg-devops analyze nsg |
| `analyze_acr` | btg-devops analyze acr |
| `analyze_keyvault` | btg-devops analyze keyvault |
| `analyze_cosmosdb` | btg-devops analyze cosmosdb |
| `analyze_functions` | btg-devops analyze functions |
| `analyze_publicip` | btg-devops analyze publicip |
| `analyze_appservice` | btg-devops analyze appservice |
| `analyze_appserviceplan` | btg-devops analyze appserviceplan |
| `analyze_cognitiveservices` | btg-devops analyze cognitiveservices |
| `analyze_resourcegroup` | btg-devops analyze resourcegroup |

---

### 4.5 CI/CD and Quality

| Deliverable | Purpose |
|---|---|
| Unit tests (`tests/`) | 12 test files — one per module — using testify/assert, no real Azure calls |
| CI pipeline (`ci.yml`) | Runs build → test → lint on every PR — blocks merge on failure |
| `CHANGELOG.md` | Version history starting from v0.12.0 |
| `CONTRIBUTING.md` | Build, test, lint, and PR instructions for contributors |

---

### 4.6 Bistec Azure Adoption

In-scope adoption activities include:

- Weekly automated audit of the Bistec Azure subscription.
- Monthly review of audit quality with the infrastructure team.
- Training session for the infrastructure team.
- Documenting findings and resolution steps.

---

### 4.7 Documentation

The following documentation deliverables are in scope:

- Project scope document.
- Architecture Decision Records (ADRs).
- README updates.
- Markdown feature specs per module.
- Audit findings reports.
- Setup and usage documentation.

---

## 5. Out-of-Scope Items

The following items are not part of the current scope:

- Production infrastructure changes without review.
- Automatic infrastructure fixes without human approval.
- Full multi-cloud support (AWS, GCP) in the initial phase.
- Complete incident management platform replacement.
- Full SLA/SLO monitoring implementation.
- Write-level cloud operations without review and approval.
- Unapproved access to production credentials.

---

## 6. Deferred — Future Scope

The following features are planned after the current build is complete and stable:

| Deferred Feature | Reason |
|---|---|
| Slack integration | Post audit findings and critical anomaly alerts to the DevOps team channel — requires stable audit rules first |
| Drift detection (`btg-devops drift`) | Detect differences between Infrastructure as Code and live Azure configuration — requires IaC inventory |
| Runbook library | Searchable operational runbooks in the dashboard — requires runbook authoring process |
| Audit history | View previous audit results and trend reports in the dashboard — requires persistent storage design |
| Automated alerting pipeline | Requires stable audit rules and notification design |
| Multi-cloud support | AWS and GCP scope must be confirmed before implementation |
| AI-generated runbooks | Requires incident data and review process |

---

## 7. Team Roles and Responsibilities

| Role | Responsibility |
|---|---|
| Dev A | CLI tooling, Go analyzers, HTTP API Server, MCP Server, CI pipeline |
| Dev B | Next.js dashboard, Normal Mode, Agent Mode, Vercel deployment |
| Mentor / Project Owner | Scope approval, technical guidance, infrastructure access confirmation, review and merge approval |
| Infrastructure Team | Provide cloud environment details, review findings, confirm remediation actions |

---

## 8. Key Constraints

1. Azure is the primary cloud environment for the initial phase.
2. AWS and GCP scope must be confirmed before implementation.
3. Agent Mode AI integration uses Claude Agent via the Anthropic API and MCP Server.
4. Routine AI usage must remain below $5/month.
5. Cloud credentials must follow least-privilege access.
6. Infrastructure changes must be reviewed before applying to production.
7. All findings and anomaly investigations must include resolution notes.
8. GitHub changes must be reviewed through pull requests.
9. Developer access depends on GitHub repo access, Azure credentials, and required monitoring tools.

---

## 9. Assumptions

- Azure is the main cloud provider for the initial phase.
- Bistec Global infrastructure details will be clarified by the project owner.
- The team will use GitHub for version control and pull request review.
- The CLI is the first major automation interface — the dashboard is built after core audit workflows are stable.
- Weekly audits will be used as the initial operational cadence.
- Documentation will be updated as the project scope becomes clearer.

---

## 10. Risks

| Risk | Impact | Mitigation |
|---|---|---|
| Cloud scope is unclear | Development may target wrong services | Confirm Azure service list with project owner |
| Limited repo access | Developer cannot push changes directly | Use fork and pull request or request contributor access |
| Missing credentials | Audits cannot run against real infrastructure | Use mock/test data until access is provided |
| AI cost exceeds target | Routine audits become expensive | Monitor Claude API token usage per audit run |
| Poor documentation | New developers cannot understand the project | Maintain README, ADRs, and scope documents |
| Unsafe remediation | Production infrastructure may be affected | Require review before applying infrastructure changes |

---

## 11. Approval Criteria

This scope document should be reviewed and approved by the project owner before major development starts.

Approval should confirm:

- Azure services in scope.
- CLI commands and module list.
- Dashboard modes and tech stack.
- MCP and Claude Agent integration approach.
- Deferred features list.
- Team responsibilities.
- Success metrics.
- Constraints and access requirements.

---

## 12. Summary

btg-devops is a DevOps intelligence and automation tool for Bistec Global. It provides three ways to audit Azure infrastructure — a CLI for engineers, a dashboard Normal Mode for any user, and a dashboard Agent Mode for AI-assisted plain English querying.

The current build covers 12 Azure resource analyzers, a full subscription audit command, a cost report command, a Next.js dashboard with two access modes, an MCP server for Claude Agent integration, and a CI pipeline with full test coverage.

Once the current scope is approved and delivered, the team will extend the tool with Slack notifications, drift detection, runbook library, and audit history.