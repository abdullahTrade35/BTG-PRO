# btg-devops — Task List

**Project:** btg-devops  
**Team:** Dev A (CLI / Backend) · Dev B (Dashboard / Frontend)  
**Last updated:** 2026-06-11

---

## Phase 1 — Engineering Quality · v0.13.0

> Complete all Phase 1 tasks before starting Phase 2.

---

### Task 1 — Unit Tests

- **Assignee:** Dev A
- **File:** `tests/` directory at repo root
- **Status:** `[ ] To Do`

**What to do:**
- Create one test file per analyzer module — 12 files total
- Start with `tests/iam_test.go` — it has the most complex logic
- Use Go standard `testing` package + `testify/assert`
- Follow table-driven pattern for all test cases
- No real Azure calls — mock all Azure SDK calls

**Each test file must cover:**
- Severity classification — Critical / Warning / Info thresholds trigger correctly
- Output format — correct table headers and valid JSON schema
- Edge cases — empty resource list, nil pointer values, malformed API response

**File list:**
```
tests/iam_test.go
tests/storage_test.go
tests/nsg_test.go
tests/acr_test.go
tests/cosmosdb_test.go
tests/keyvault_test.go
tests/functions_test.go
tests/publicip_test.go
tests/appservice_test.go
tests/appserviceplan_test.go
tests/cognitiveservices_test.go
tests/resourcegroup_test.go
```

**Done when:** `go test ./...` passes with zero failures.

---

### Task 2 — CI Pipeline

- **Assignee:** Dev A
- **File:** `.github/workflows/ci.yml`
- **Status:** `[ ] To Do`
- **Depends on:** Task 1 (write tests first — CI will fail with no tests)

**What to do:**
- Create `.github/workflows/ci.yml`
- Trigger on every push to `main` and every pull request
- Run three steps in order:

```
go build ./...  →  go test ./...  →  golangci-lint run
```

- After merging, enable branch protection rule on `main` — require CI to pass before any PR can merge

**Done when:** A push to `main` triggers the workflow and all three steps pass.

---

### Task 3 — CHANGELOG.md

- **Assignee:** Dev A + Dev B
- **File:** `CHANGELOG.md` at repo root
- **Status:** `[ ] To Do`

**What to do:**
- Create `CHANGELOG.md` using conventional commits format
- Add a `[v0.12.0]` baseline entry listing all 12 existing modules
- Add a `[v0.13.0]` entry just before tagging — list everything added in this phase

**Done when:** Both version entries exist and are accurate.

---

### Task 4 — CONTRIBUTING.md

- **Assignee:** Dev A + Dev B
- **File:** `CONTRIBUTING.md` at repo root
- **Status:** `[ ] To Do`

**What to do:**
- Create `CONTRIBUTING.md` covering:
  - Prerequisites (Go 1.23.6, golangci-lint)
  - Build commands for Windows and Mac/Linux
  - Test command (`go test ./...`)
  - Lint command (`golangci-lint run`)
  - Azure credentials setup (4 env vars)
  - PR checklist
  - Release process (tagging)

**Done when:** A new developer can follow the guide to build, test, and open a PR without asking anyone.

---

### Task 5 — Docs Verification

- **Assignee:** Dev A + Dev B
- **Location:** `docs/` folder
- **Status:** `[ ] To Do`

**What to do:**
- Open the `docs/` feature index and check every link resolves to an existing file
- For each of the 12 module spec docs — verify the severity rules documented match the actual code
- Fix any broken links or outdated content
- Log any discrepancies that cannot be fixed immediately

**Done when:** All 12 module docs exist, all links are valid, and spec content matches the code.

---

### Task 6 — Full Subscription Audit (`analyze all`)

- **Assignee:** Dev A
- **File:** `cmd/analyze_all.go`
- **Version:** Feature 013
- **Status:** `[ ] To Do`

**What to do:**
- Create `cmd/analyze_all.go` as a new Cobra subcommand
- Run all 12 existing analyzer modules in sequence
- Combine all findings into one unified output (table or JSON)
- Support `--output json` and `--resource-group` flags

**Commands to implement:**
```bash
btg-devops analyze all
btg-devops analyze all --output json
btg-devops analyze all --resource-group my-rg
```

**Azure Permission Required:** Reader role on the subscription

**Done when:** All four acceptance criteria pass:
- [ ] Runs all 12 analyzers in sequence
- [ ] Combined output renders as a single table or JSON array
- [ ] `--resource-group` scopes all 12 analyzers correctly
- [ ] `--output json` produces a valid combined JSON array

---

### Task 7 — Release Tag v0.13.0

- **Assignee:** Dev A + Dev B
- **Status:** `[ ] To Do`
- **Depends on:** Tasks 1 – 6 all merged to `main`

**What to do:**
```bash
git tag v0.13.0
git push origin v0.13.0
```

This triggers the existing `release.yml` — cross-platform binaries are built and published to GitHub Releases automatically.

**Done when:** GitHub Releases page shows v0.13.0 with binaries for all five platforms.

---

## Phase 2 — New Features · v0.14.0

> Start Phase 2 only after v0.13.0 is tagged and released.

---

### Task 8 — Cost Report (`analyze cost`)

- **Assignee:** Dev A
- **File:** `cmd/analyze_cost.go`
- **Version:** Feature 014
- **Status:** `[ ] To Do`

**What to do:**
- Create `cmd/analyze_cost.go` as a new Cobra subcommand
- Query the Azure Cost Management API
- Display total spend, cost broken down by service, and top 15 highest-cost resources
- Default time period is last 30 days

**Commands to implement:**
```bash
btg-devops analyze cost
btg-devops analyze cost --days 7
btg-devops analyze cost --days 90
btg-devops analyze cost --resource-group my-rg
btg-devops analyze cost --output json
```

**Azure Permission Required:** Cost Management Reader role on the subscription

**Done when:**
- [ ] Returns real billing data from Azure Cost Management API
- [ ] Output shows total spend, cost by service, and top 15 resources
- [ ] `--days` flag controls the time period correctly
- [ ] Missing permissions produces a clear error message

---

### Task 9 — HTTP API Server

- **Assignee:** Dev A
- **File:** `cmd/server.go`
- **Status:** `[ ] To Do`
- **Depends on:** Task 8

**What to do:**
- Create `cmd/server.go` as a new entry point started by `cmd/root.go`
- Expose one REST endpoint per module:

```
GET /analyze/storage
GET /analyze/iam
GET /analyze/nsg
... (one per module)
```

- Used by Dashboard Normal Mode — reuses the same `12 Analyzer Modules (internal/analyzers)`
- Authentication via the same four Azure env vars as the CLI
- Configure CORS to allow requests from the dashboard origin

**Done when:**
- [ ] `GET /analyze/{module}` returns live findings as JSON for all 12 modules
- [ ] Missing credentials produce a clear JSON error response
- [ ] Server starts without conflicting with existing CLI commands

---

### Task 10 — MCP Server

- **Assignee:** Dev A
- **File:** `cmd/mcp.go`
- **Status:** `[ ] To Do`
- **Depends on:** Task 9

**What to do:**
- Create `cmd/mcp.go` — registers btg-devops as an MCP server
- Wire all 12 analyzers as individually callable MCP tools
- Transport: stdio
- Authentication: same four Azure env vars

**MCP tools to expose:**
```
analyze_storage · analyze_iam · analyze_nsg · analyze_acr
analyze_keyvault · analyze_cosmosdb · analyze_functions
analyze_publicip · analyze_appservice · analyze_appserviceplan
analyze_cognitiveservices · analyze_resourcegroup
```

**Done when:**
- [ ] All 12 analyzers are callable as MCP tools via stdio transport
- [ ] Claude Agent can call any tool and receive structured findings

---

### Task 11 — Next.js Dashboard Scaffold

- **Assignee:** Dev B
- **Status:** `[ ] To Do`
- **Depends on:** Task 9 (HTTP API Server must be running)

**What to do:**
- Scaffold a new Next.js 14+ project with App Router
- Set up TypeScript, Tailwind CSS, and Recharts
- Create the project structure:

```
dashboard/
├── app/
│   ├── page.tsx          (main dashboard page)
│   └── api/
│       └── analyze/      (API route — calls HTTP API Server)
├── components/
│   ├── SummaryCards.tsx
│   ├── FindingsTable.tsx
│   ├── FilterControls.tsx
│   └── AgentChat.tsx
└── ...
```

**Done when:** `npm run dev` starts the dashboard locally without errors.

---

### Task 12 — Dashboard Normal Mode

- **Assignee:** Dev B
- **Status:** `[ ] To Do`
- **Depends on:** Task 11

**What to do:**
- Build the module selector and Analyze button (`AnalyzeControls` component)
- Create the Next.js API route (`/api/analyze`) that calls the HTTP API Server
- Build `SummaryCards` — shows Critical / Warning / Info totals
- Build `FindingsTable` — sortable, paginated findings list
- Build `FilterControls` — filter by severity, module, resource group

**Flow:**
```
User clicks Analyze
    ↓
Next.js Dashboard → HTTP GET /analyze/{module} → HTTP API Server
    ↓
JSON findings → SummaryCards + FindingsTable render
```

**Done when:**
- [ ] Clicking Analyze fetches live findings from the HTTP API Server
- [ ] Summary cards show correct Critical / Warning / Info counts
- [ ] Findings table renders all findings with correct columns
- [ ] Filters work correctly for severity, module, and resource group

---

### Task 13 — Dashboard Agent Mode

- **Assignee:** Dev B
- **Status:** `[ ] To Do`
- **Depends on:** Task 12, Task 10 (MCP Server must be running)

**What to do:**
- Build the AI chat interface (`AgentChat` component)
- Connect to Claude Agent via Anthropic API from the dashboard server side
- Claude Agent calls the MCP Server tools based on the user's prompt
- Display the AI explanation and findings in the dashboard

**Flow:**
```
User types "Check if my storage is secure"
    ↓
Next.js Dashboard → Claude Agent → MCP Server
    ↓
12 Analyzer Modules → Azure → findings
    ↓
Claude explains in plain English → Dashboard displays response
```

**Done when:**
- [ ] User can type a prompt and receive an AI explanation
- [ ] Claude Agent correctly calls the right MCP tools based on the prompt
- [ ] AI response and findings are displayed clearly in the dashboard

---

### Task 14 — Deploy Dashboard to Vercel

- **Assignee:** Dev B
- **Status:** `[ ] To Do`
- **Depends on:** Tasks 12 and 13

**What to do:**
- Connect the dashboard repository to Vercel
- Set environment variables on Vercel:
  - `ANTHROPIC_API_KEY` — for Claude Agent (Agent Mode)
  - `API_SERVER_URL` — URL of the running HTTP API Server
- Deploy and verify both modes work on the live URL

**Done when:**
- [ ] Dashboard is accessible via a public Vercel URL
- [ ] Normal Mode fetches live findings on the deployed URL
- [ ] Agent Mode works with Claude Agent on the deployed URL

---

## Phase 3 — Future Scope

> These tasks are planned after Phase 2 is complete and stable.

| Task | Assignee | Description |
|---|---|---|
| Slack Integration | TBD | Post audit findings and alerts to DevOps team channel |
| Drift Detection | Dev A | `btg-devops drift` — IaC vs live Azure config comparison |
| Runbook Library | Dev B | Searchable operational runbooks in the dashboard |
| Audit History | Dev B | View previous audit results and trends in the dashboard |

---

## Progress Summary

| Phase | Tasks | Done |
|---|---|---|
| Phase 1 — v0.13.0 | 7 | 0 |
| Phase 2 — v0.14.0 | 7 | 0 |
| Phase 3 — Future | 4 | 0 |
| **Total** | **18** | **0** |