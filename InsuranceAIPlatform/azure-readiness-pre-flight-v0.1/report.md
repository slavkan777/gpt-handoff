# Gate Report — AZURE_READINESS_PRE_FLIGHT_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-29
**Type:** planning-only Azure readiness dossier + cost/governance architecture
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

---

## Verdict

**ACCEPT_AZURE_PREFLIGHT_READY** — full Azure plan produced; **no resources created, no `az login`, no secrets, no source commit/push**. Repo-scaffolding gaps (Dockerfile / IaC / CI / SWA config) are documented and assigned to gate 2 (`AZURE_IAC_SKELETON_V0.1`), so they are planned work, not blockers.

## Source repo state (unchanged this gate)

```
path:        C:/Projects/InsuranceAIPlatform
branch:      dev
HEAD:        2e9443a6726f34f20bd26233e840ae8cc982d91a (before == after)
origin/dev:  2e9443a6726f34f20bd26233e840ae8cc982d91a
origin/main: 69e67312a10cc9bcf28c4e387a126b48c91fb9c5 (untouched)
worktree:    test-results/.last-run.json (artifact) + 4 NEW untracked azure planning docs (uncommitted, per gate)
```

## Repo deployment-readiness (SUBPHASE 2)

| Area | State | Azure mapping | Gap (→ gate) |
|---|---|---|---|
| Frontend | Vite SPA → static `dist/` | Static Web Apps | `staticwebapp.config.json` → gate 2 |
| Backend | .NET 9 single Api host referencing 6 service libs in-proc (**microservice-shaped modular monolith**) | 1× Container App (scale-to-zero) | `Dockerfile`/`.dockerignore` → gate 2 |
| Config | `IConfiguration`; conn-string + `DEEPSEEK_API_KEY` via env (never logged); `appsettings.json` clean | Key Vault refs + Managed Identity | wire KV refs → gate 4 |
| DB | SQL Server LocalDB, 6 schemas, DbMigrator | Azure SQL **Serverless** (auto-pause) | connection-string swap only (no code change) → gate 4 |
| AI | `IAiProvider` Mock default / DeepSeek opt-in disabled | + Azure OpenAI provider behind same seam, manual | new provider impl → gate 6 |
| CI/CD | none | GitHub Actions + OIDC | workflow → gate 2 |
| IaC | none | Bicep | `infra/*.bicep` → gate 2 |

`REPO_READINESS: GAPS_FOUND` — app is deploy-shaped; platform scaffolding is intentionally the next gates' work.

## Service matrix (summary — full table in source doc)
Deploy-now: Static Web Apps (Free), Container Apps (`insurance-api`, **minReplicas=0**), Managed Identity, Key Vault, App Insights (sampling, <5 GB free), GitHub Actions (OIDC). Image via **GHCR ($0)** rather than ACR Basic (~$5/mo). Later: Azure SQL **Serverless auto-pause**, Blob + lifecycle TTL, `cleanup-job` ACA Job. Governed-AI (manual, capped): Azure OpenAI/Foundry, Document Intelligence **F0**, AI Search **Free tier**. **AKS explicitly deferred** (24/7 node cost; ACA gives $0-idle scale-to-zero). Cost traps explicitly avoided: AI Search Basic (~$75/mo), ACA minReplicas>0, SQL no-auto-pause, un-sampled logs.

## Cost model
- Real target **$5–10/mo** (often $0–5 with GHCR + SWA Free); comfort cap **$30**; explainable ceiling **~$1000** (AKS + always-on + Basic AI Search + heavy AI).
- Budget alerts **$5/$10/$20/$30/$50** — **notify only, do not stop resources** (architecture enforces cost: scale-to-zero, auto-pause, free tiers, manual AI).
- Top risks → mitigations: SQL not pausing → auto-pause+verify; ACA minReplicas>0 → set 0; AI loops → manual-only/Mock-default; Doc Intelligence batches → F0+caps; AI Search Basic → Free only; log volume → sampling+cap; blob growth → TTL; ACR fixed cost → GHCR.

## Low-traffic production-shaped guarantees
Anonymous = static only; API Container App scales to 0; SQL Serverless auto-pauses; AI only after login + explicit click (no background AI); blob TTL + cleanup job; logs sampled/retention-capped.

## Slava manual setup checklist (gate 1)
**DO:** create/sign-in Azure account ($200/30-day trial + always-free tiers); select subscription (don't paste its ID); **create budget alerts first** ($5–50); choose+record region (West/North Europe or East US); approve naming (`rg-iap-prod`, `iap-prod-*`); ensure GitHub repo reachable for OIDC.
**DO NOT YET:** create any resource manually; create AI deployments; paste secrets/keys/subscription-id; enable paid/background services; `az login` outside a deploy gate.

## Future gate plan (full detail in source doc)
1. `AZURE_ACCOUNT_AND_BUDGET_SETUP_MANUAL_V0.1` (manual; budget alerts; 0 resources)
2. `AZURE_IAC_SKELETON_V0.1` (Bicep + Dockerfile + CI + commit azure docs/IaC to `dev`; no apply)
3. `AZURE_MINIMAL_DEPLOY_V0.1` (SWA + 1 scale-to-zero Container App via GHCR+OIDC; first real spend ≈ $0)
4. `AZURE_SQL_SERVERLESS_INTEGRATION_V0.1` (auto-pause SQL + KV + MI; connection-string swap)
5. `AZURE_BLOB_AND_CLEANUP_V0.1` (Blob + lifecycle TTL + cleanup-job)
6. `AZURE_AI_CONTROLLED_DEMO_V0.1` (governed real AI behind a button; Mock stays default)
7. `PORTFOLIO_INTERVIEW_POLISH_V0.1` (README/diagram/cost screenshot/Q&A)

## Interview story (full in source doc)
One-liner + 60-sec + AKS-vs-Container-Apps + AI-governance + cost-governance + deployed-vs-architecture-ready truth table. Current truth: local product built+verified+pushed (`2e9443a`); **zero Azure resources exist**; deployment not started.

## Local planning docs created (uncommitted — to be committed in gate 2)
- `docs/architecture/azure/AZURE_READINESS_PRE_FLIGHT_V0.1.md`
- `docs/architecture/azure/AZURE_SERVICE_MATRIX_V0.1.md`
- `docs/architecture/azure/AZURE_COST_GOVERNANCE_V0.1.md`
- `docs/architecture/azure/AZURE_GATE_PLAN_V0.1.md`

## Safety
No `az login`; no resources; no secrets read/pasted; `DEEPSEEK_API_KEY` untouched; no product code/test change; no source commit/push; no `main`; no real PII; no real AI call.

## Limitations
1. The 4 local docs are uncommitted (per gate); they get committed in `AZURE_IAC_SKELETON_V0.1`.
2. Cost figures are list-price estimates for a tiny workload; actuals depend on region + real usage — verify against the live budget after gate 3.
3. `test-results/.last-run.json` remains untracked (future housekeeping).

## Next recommended gate
`AZURE_ACCOUNT_AND_BUDGET_SETUP_MANUAL_V0.1` — Slava manual account + budget alerts + region/naming; **0 resources created**.
