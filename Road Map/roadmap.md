# btg-devops — Project Roadmap

This roadmap shows the three delivery phases for btg-devops. Each phase must be completed before the next begins.

---

```mermaid
graph TD
    classDef phase1 fill:#D6EAF8,stroke:#2E86C1,color:#1A5276
    classDef phase2 fill:#FDEBD0,stroke:#E67E22,color:#784212
    classDef phase3 fill:#F2F3F4,stroke:#7F8C8D,color:#2C3E50
    classDef milestone fill:#1168BD,stroke:#0B4884,color:#ffffff

    subgraph P1["① Phase 1 — Engineering Quality · v0.13.0"]
        T1[Unit Tests\ntests/ — 12 files one per module]
        T2[CI Pipeline\n.github/workflows/ci.yml]
        T3[CHANGELOG.md]
        T4[CONTRIBUTING.md]
        T5[Docs Verification]
        T6[btg-devops analyze all\ncmd/analyze_all.go]
        T1 --> T2 --> T3 --> T4 --> T5 --> T6
    end

    subgraph P2["② Phase 2 — New Features · v0.14.0"]
        T7[btg-devops analyze cost\ncmd/analyze_cost.go]
        T8[HTTP API Server\ncmd/server.go]
        T9[MCP Server\ncmd/mcp.go]
        T10[Next.js Dashboard\nNormal Mode + Agent Mode]
        T11[Deploy to Vercel]
        T7 --> T8 --> T9 --> T10 --> T11
    end

    subgraph P3["③ Phase 3 — Future Scope"]
        T12[Slack Integration]
        T13[Drift Detection\nbtg-devops drift]
        T14[Runbook Library]
        T15[Audit History]
    end

    T6 --> M1([🏷️ Tag v0.13.0])
    M1 --> T7
    T11 --> M2([🏷️ Tag v0.14.0])
    M2 --> T12

    class T1,T2,T3,T4,T5,T6 phase1
    class T7,T8,T9,T10,T11 phase2
    class T12,T13,T14,T15 phase3
    class M1,M2 milestone
```

---

## Phase Summary

### Phase 1 — Engineering Quality (v0.13.0)
Adds the safety net the project needs before new features. No new functionality — only tests, CI, and documentation.

| Task | Description |
|---|---|
| Unit Tests | 12 test files — one per analyzer module |
| CI Pipeline | Auto build → test → lint on every PR |
| CHANGELOG.md | Version history from v0.12.0 |
| CONTRIBUTING.md | Build, test, lint, PR guide for contributors |
| Docs Verification | Confirm all 12 module docs match the code |
| `analyze all` | Run all 12 analyzers in one command |

### Phase 2 — New Features (v0.14.0)
Extends the tool with cost reporting, a REST API, MCP integration, and a live web dashboard.

| Task | Description |
|---|---|
| `analyze cost` | Azure Cost Management billing report |
| HTTP API Server | REST endpoints for dashboard Normal Mode |
| MCP Server | Exposes 12 analyzers as MCP tools for Agent Mode |
| Next.js Dashboard | Two-mode web UI — Normal Mode and Agent Mode |
| Deploy to Vercel | Public dashboard URL |

### Phase 3 — Future Scope
Planned after Phase 2 is stable.

| Task | Description |
|---|---|
| Slack Integration | Post audit findings to DevOps team channel |
| Drift Detection | IaC vs live Azure configuration comparison |
| Runbook Library | Searchable operational runbooks in dashboard |
| Audit History | View previous audit results and trends |