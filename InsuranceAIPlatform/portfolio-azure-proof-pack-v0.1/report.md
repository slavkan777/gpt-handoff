# Gate Report — PORTFOLIO_AZURE_PROOF_PACK_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-30
**Type:** portfolio/interview evidence pack from the live Azure deployment (docs + screenshots; no Azure/source changes)
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

---

## Verdict

**PROOF_PACK_READY** ✅ — 5 portfolio docs + 5 verified screenshots created; all live checks passed; an Opus `/qa-inspector` confirmed **no overclaiming** (CLEARED 7/7). No Azure change, no deploy, no source commit. Docs left uncommitted for the next commit gate.

## Honest headline finding (important)

The live demo is presentable end-to-end on the **golden path**, but the **list endpoints `/api/claims` and `/api/customers` return HTTP 500** because **Azure SQL is intentionally deferred** (`enableSql=false`). The SPA **degrades gracefully** to bundled seeded rows, so the queue/customers pages still render — but that list data is mock, not live. The proof pack documents this openly (not hidden). Live: summary metrics, the full golden claim CLM-1006 workspace (8 endpoints), demo scenario, CORS.

## Files created (uncommitted)

| File | Purpose |
|---|---|
| `docs/portfolio/PORTFOLIO_AZURE_PROOF_PACK_V0.1.md` | exec summary, architecture, services, runtime proof, cost/security/CI-IaC/AI models, real-vs-deferred, interview-safe claims, cleanup |
| `docs/portfolio/INTERVIEW_STORY_AZURE_DOTNET_AI_V0.1.md` | 30s pitch, 2-min architecture, .NET/Azure/AI/cost/security narratives, recruiter + senior bullets, avoid-overclaiming rules |
| `docs/portfolio/TECHNICAL_PROOF_MATRIX_V0.1.md` | 20-row capability → evidence → runtime check → LIVE/PARTIAL/DEFERRED → how-to-explain |
| `docs/portfolio/LIVE_DEMO_RUNBOOK_V0.1.md` | warm-up, golden path, mock-vs-live, verify, cost, one-command teardown, troubleshooting |
| `docs/portfolio/README_PORTFOLIO_SECTION_DRAFT_V0.1.md` | copy-paste README section (not applied) |
| `docs/portfolio/screenshots/*.png` | 01-login, 02-overview, 03-claim-workspace, 04-ai-evidence, 05-audit-cost (authenticated, rendered; 286–710 KB each) |

## Live proof (read-only, 2026-05-30)

| Check | Result |
|---|---|
| Frontend | `GET /` → 200 (our app) |
| API health | `GET /health` → 200 Healthy (Production) |
| Live summary | `GET /api/claims/summary` → 200 `{"totalActive":47,"pendingReview":12,"highRisk":8,"avgSlaRemainingHours":14.3,"processedToday":6,"aiAnalysisRunning":2}` — values render in the UI cards (02-overview.png) |
| Golden claim CLM-1006 | 8 endpoints (detail/documents/ai-evidence/risks/policy/customer-vehicle/audit/approval) → **all 200** |
| Demo scenario | `GET /api/demo/scenario` → 200 (7 steps, goldenClaimId CLM-1006) |
| CORS | GET with `Origin: <SWA>` → 200 + ACAO `<SWA>`; no wildcard/credentials |
| List endpoints | `GET /api/claims` & `/api/customers` → **500** (SQL deferred → SPA seed fallback) |
| Inventory / cost | 9 resources; no AKS/ACR/SQL; ACA minReplicas=0/max 2; SWA Free; idle ≈ $0 |

## QA

Opus `/qa-inspector` END gate → **CLEARED 7/7**. Priority was overclaiming: every LIVE claim re-verified true; every deferral (SQL/AI/AKS/ACR + the 500 list endpoints) honestly disclosed; AI consistently marked Mock/PARTIAL; no subscription/tenant/client GUID or token in any doc; screenshots are authenticated rendered content.

---

## REPORT BACK FORMAT

```text
VERDICT: PROOF_PACK_READY
GATE: PORTFOLIO_AZURE_PROOF_PACK_V0.1
SOURCE_REPO:
  path: C:/Projects/InsuranceAIPlatform
  branch: dev
  head: 211d50f8d79cf3075b219f729c7cedcd72136f65
  origin_dev: 211d50f8d79cf3075b219f729c7cedcd72136f65
  origin_main: 69e67312a10cc9bcf28c4e387a126b48c91fb9c5
  working_tree_before: clean except untracked test-results/
  working_tree_after: untracked docs/portfolio/ (5 docs + 5 screenshots) + test-results/ ; no commit
LIVE_PROOF:
  frontend_url: https://kind-meadow-03cf73103.7.azurestaticapps.net
  frontend_http: 200
  api_url: https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io
  api_health: 200 Healthy (Production)
  cors_check: GET from SWA origin -> 200 + access-control-allow-origin=<SWA> (no wildcard/credentials)
  data_endpoint: /api/claims/summary 200 {totalActive:47,...}; golden claim CLM-1006 8 endpoints 200; /api/claims + /api/customers 500 (SQL deferred -> graceful seed fallback)
  azure_resource_count: 9 (no AKS/ACR/SQL)
  cost_guardrails: minReplicas=0/max=2; SWA Free; no SQL/AI/AKS/ACR; idle ~$0
FILES_CREATED_OR_UPDATED:
  proof_pack: docs/portfolio/PORTFOLIO_AZURE_PROOF_PACK_V0.1.md
  interview_story: docs/portfolio/INTERVIEW_STORY_AZURE_DOTNET_AI_V0.1.md
  technical_matrix: docs/portfolio/TECHNICAL_PROOF_MATRIX_V0.1.md
  live_demo_runbook: docs/portfolio/LIVE_DEMO_RUNBOOK_V0.1.md
  readme_draft: docs/portfolio/README_PORTFOLIO_SECTION_DRAFT_V0.1.md
  screenshots: docs/portfolio/screenshots/{01-login,02-overview,03-claim-workspace,04-ai-evidence,05-audit-cost}.png (5, authenticated)
EVIDENCE_SUMMARY:
  azure_services: Container Apps, Static Web Apps (Free), GHCR, Managed Identity, Key Vault (RBAC), Storage (shared-key off), App Insights, Log Analytics
  dotnet_proof: .NET 9 modular-monolith API live on ACA; /health 200; 137/137 tests; multi-stage non-root Docker
  ai_readiness_proof: IAiProvider Mock(default)+DeepSeek opt-in; workbench UI live golden-claim data; AI output Mock/seeded (PARTIAL)
  cost_proof: scale-to-zero, Free tiers, GHCR (no ACR), sampled/capped telemetry, $30/mo budget+alerts, idle ~$0
  security_proof: MI+Key Vault arch, storage shared-key disabled, non-root API, config-driven CORS (no wildcard/credentials), no secrets in repo, synthetic data only
  deferred_items: Azure SQL (list endpoints 500), real AI provider (Mock), AKS/ACR (not used), production auth/SSO (demo client-side)
VALIDATION:
  docs_review: Opus /qa-inspector CLEARED 7/7 — no overclaiming; LIVE claims re-verified; deferrals honest
  links_checked: frontend/API/health/summary/golden-claim/CORS all verified
  screenshots: 5 captured; 02-overview + 04-ai-evidence confirmed authenticated rendered content
  other_checks: live summary values (47/12/6/8/14.3) match UI cards
SAFETY:
  secrets_scan: CLEAN (no tokens/keys/GUIDs in docs)
  azure_resources_changed: NO
  deploy_attempted: NO
  image_pushed: NO
  workflow_run: NO
  source_commit_attempted: NO
  source_push_attempted: NO
  main_touched: NO
  resources_deleted: NO
GITHUB_HANDOFF:
  latest_report: InsuranceAIPlatform/latest-report.md
  latest_summary: InsuranceAIPlatform/latest-summary.json
  task_report: InsuranceAIPlatform/portfolio-azure-proof-pack-v0.1/report.md
BLOCKERS: none
LIMITATIONS: /api/claims + /api/customers 500 (Azure SQL deferred; SPA seed fallback keeps UI complete); AI Mock-only; demo auth client-side (no Entra); CI workflow scaffolded but not run (local az used)
NEXT_RECOMMENDED_GATE: PORTFOLIO_AZURE_PROOF_PACK_COMMIT_V0.1 (commit docs+screenshots) — or AZURE_SQL_OPTIONAL_GATE_V0.1 to make list endpoints live
STOP_LINE_CONFIRMATION: stopping after report; no commit/push, no deploy, no resource create/delete, no AIKB, no PR, no main, no SQL/AI gate started
```
