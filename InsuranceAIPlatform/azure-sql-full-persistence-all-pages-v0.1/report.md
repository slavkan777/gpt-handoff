# Gate Report — AZURE_SQL_FULL_PERSISTENCE_ALL_PAGES_DEPLOY_PUSH_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-30
**Type:** deploy Azure SQL + make the previously-broken list/create flows truly persistent, validate, commit/push (docs)
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

---

## Verdict

**SQL_PERSISTENCE_DEPLOYED_AND_PUSHED → PARTIAL** ( `SQL_PERSISTENCE_DEPLOYED_PARTIAL_PUSHED` )

The previously-broken surfaces are now **live from Azure SQL** and verified end-to-end: the **customers directory**, the **claims list**, and **creating a synthetic customer** (the exact "Failed to fetch" flow the user hit) — all return 200 and **persist**. PARTIAL because the curated **golden-claim CLM-1006 detail + sub-resources**, the **dashboard summary**, and the **demo scenario** remain **in-memory fixtures by design** (deterministic demo), and write flows beyond customer-create (approval / payout-sim / document-upload) are DB-backed in code but were not individually e2e-tested this gate.

## Root cause (confirmed) + fix

The list/create handlers query EF Core `DbContext`s over `UseSqlServer`; with no reachable Azure SQL they threw `SqlException` → **500** (the browser showed "Failed to fetch" because the 500 error response lacked the CORS header). CORS preflight, route, and method were all fine. **Fix = deploy SQL + wire the connection** — the EF persistence layer (6 DbContexts, 11 migrations, `DbMigrator`) was **already built and committed**; **no new EF code** was needed.

## What was deployed

| Item | Detail |
|---|---|
| SQL server | `iap-sql-r2-6c7g465.database.windows.net` · rg-iap-demo · **germanywestcentral** |
| Why not West Europe | WE **and** North Europe returned `RegionDoesNotAllowProvisioning` for new SQL servers (capacity); germanywestcentral was the nearest accepting region (~10 ms from the WE Container App). `AllowAllAzureServices` lets the Container App reach it cross-region. |
| Database | `InsuranceAIPlatform` · **GP_S_Gen5_1** (General Purpose **Serverless**, min 0.5 vCore, **auto-pause 60 min**, 2 GB, LRS backup) |
| Auth | **SQL auth** (admin login + generated password). Connection string stored **only** as Container App secret `sql-connection`; env `ConnectionStrings__InsuranceAIPlatform=secretref:sql-connection`. Never in repo, never printed. (The Entra-only `infra/modules/sql-serverless.bicep` is an alternative IaC path, not used — operational constraints; SQL-auth + Container App secret is gate-approved per §7.1.) |
| Migration + seed | `DbMigrator` applied 11 migrations across 6 schemas and idempotently seeded: customers/policies/vehicles 201 each, claims 15, documents 14, approval 1+4, audit_cost 6+4+1, ai_analysis 1+3+2+4. |
| Connection wiring | `az containerapp update --set-env-vars ConnectionStrings__InsuranceAIPlatform=secretref:sql-connection` → revision **iap-demo-api--0000002**. No image rebuild (no code change). |

## Live verification (2026-05-30)

| Check | Result |
|---|---|
| `GET /health` | 200 Healthy (Production) |
| `GET /api/customers?page=1&pageSize=3` | **200** `{"total":200,...}` real SQL rows (CUST-T0001…) |
| `GET /api/claims` | **200** — 15 claims (golden CLM-1006..1010 + synthetic CLM-1011..1020) |
| `POST /api/customers` (Origin = SWA) | **200** `{"success":true,"customerId":"CUST-T0201"}` + `access-control-allow-origin: <SWA>` → **no more "Failed to fetch"** |
| Persistence | `GET /api/customers?search=…` returns the created row; survives page refresh in the UI |
| UI e2e (headless Chromium, live SWA) | **8/8**: English default · customers list from SQL · create-customer succeeds (no Failed to fetch) · **persists across refresh** · claims list loads |

(MECHANICAL / SEMANTIC / PERSISTENCE / NEGATIVE evidence layers all satisfied per the AIKB E2E rule.)

## Backend / frontend

- Backend: **no code change** (persistence layer pre-existed in the committed tree). `dotnet build` Release PASS; **137/137** tests PASS. Existing image reused (no GHCR push).
- Frontend: **no change / no redeploy** — the existing backend-mode SWA bundle works now that the API is SQL-backed. EN default + UA switch intact.

## Cost / resources

Only **Azure SQL** was added. **No AKS, no ACR, no AI resources.** ACA **minReplicas=0 / max=2** preserved; SWA **Free**. Serverless auto-pause → near-$0 idle (storage-only ~$0.06–0.20/mo); realistic incremental **~$1–5/mo, well under the $30/mo stop-line**. Stray Entra-only server from the region-capacity retries was deleted; temp local-IP firewall rule removed.

## Commit / push

`70af774` "docs: record Azure SQL persistence deployment" (11 files: 4 new architecture/cost docs + runbook + README updates + 5 e2e screenshots) → pushed `89e8516..70af774 dev -> dev`. `origin/main` `69e67312` untouched.

## QA

Independent **Opus `/qa-inspector` → CLEARED 6/6**: live SQL endpoints (its own POST persisted), SQL serverless confirmed, no AKS/ACR/AI, minReplicas=0, **connection string only in the Container App secret (not in repo)**, docs honest (SQL vs golden-fixture vs mock), main untouched, EN+UA present. Commit-gate satisfied by a genuine QA-CLEARED log line — **no bypass**.

## Limitations (honest)

- Golden-claim **CLM-1006 detail + sub-resources** (documents, ai-evidence, risks, policy, customer-vehicle, approval, audit), **dashboard summary**, and **demo scenario** remain curated **in-memory fixtures by design** (deterministic demo) — not SQL-backed.
- Write flows beyond customer-create (approval, payout simulation, document upload) are **DB-backed in code** but were not individually e2e-tested this gate.
- AI is **Mock**-only; demo auth is client-side; **synthetic data only** (no real PII).
- SQL is **cross-region** (germanywestcentral) due to West-Europe capacity; **SQL-auth** used instead of Entra-only/MI (operational constraints) — connection secret in Container App, not repo.

## Next recommended gate

E2e-verify the remaining write flows (approval / payout-sim / document-upload persistence) and optionally migrate the golden CLM-1006 views to SQL — or a real-AI-provider gate, or `PRODUCT_I18N_QA_POLISH_V0.1`.

---

## REPORT BACK FORMAT

```text
VERDICT: SQL_PERSISTENCE_DEPLOYED_PARTIAL_PUSHED
GATE: AZURE_SQL_FULL_PERSISTENCE_ALL_PAGES_DEPLOY_PUSH_V0.1
REPORT_STUDIED:
  latest_gate: PRODUCT_I18N_EN_DEFAULT_UA_SWITCH_DEPLOY_AND_PUSH_MASTER_V0.1
  latest_verdict: PRODUCT_I18N_DEPLOYED_PARTIAL_PUSHED
  important_findings: i18n live (EN default + UA); list endpoints 500 due to deferred SQL; EF persistence layer + DbMigrator + sql-serverless.bicep ALREADY built -> this gate = deploy + wire + migrate, no new EF code
SOURCE_REPO:
  path: C:/Projects/InsuranceAIPlatform
  branch: dev
  head_before: 89e8516
  head_after: 70af774
  origin_dev_before: 89e8516
  origin_dev_after: 70af774
  origin_main_before: 69e67312
  origin_main_after: 69e67312 (untouched)
WORKING_TREE:
  before_summary: clean except untracked test-results/
  after_summary: clean except untracked test-results/
  remaining_files: test-results/ (untracked, not committed)
RUNTIME_REPRO:
  health: 200
  claims_list_before: 500
  customers_list_before: 500
  customer_create_before: 500 (browser "Failed to fetch" - 500 response lacked ACAO)
  root_cause: persistence - EF DbContexts over UseSqlServer with no reachable Azure SQL -> SqlException -> 500; CORS/route/method all fine
DATA_SURFACE_MATRIX:
  pages_scanned: 14
  sql_backed_now: customers directory (GET /api/customers), create-customer (POST /api/customers, persists), claims list (GET /api/claims), DB-created claims (CLM-1011+)
  frontend_only_remaining: none (frontend i18n chrome only)
  deferred_remaining: golden CLM-1006..1010 detail + sub-resources, dashboard summary, demo scenario = curated in-memory fixtures BY DESIGN; AI Mock; approval/payout-sim/doc-upload DB-backed in code but not e2e-tested this gate
SQL:
  server: iap-sql-r2-6c7g465.database.windows.net (rg-iap-demo, germanywestcentral; WE+NE blocked new SQL servers; AllowAllAzureServices reaches cross-region)
  database: InsuranceAIPlatform
  sku_tier: GP_S_Gen5_1 (General Purpose Serverless, min 0.5 vCore, auto-pause 60m, 2GB, LRS)
  estimated_incremental_cost: ~$1-5/mo (serverless auto-pause; near-$0 idle, storage-only ~$0.06-0.20); well under $30
  migrations: 11 EF migrations (pre-existing) applied via DbMigrator across 6 service schemas
  seed_strategy: idempotent (stable IDs, guards); 201 customers/policies/vehicles, 15 claims, 14 docs, approval 1+4, audit 6+4+1, ai_analysis 1+3+2+4
  secret_storage: SQL auth; connection string ONLY as Container App secret sql-connection; env ConnectionStrings__InsuranceAIPlatform=secretref:sql-connection; never in repo/printed
  firewall_connectivity: AllowAllAzureServices (0.0.0.0) only; temp local-migration client-IP rule removed post-migration
BACKEND:
  files_changed: none (persistence layer pre-existed in committed source)
  endpoints_fixed: GET /api/customers 200; POST /api/customers 200+persist; GET /api/claims 200
  write_flows: create-customer -> SQL (persist verified); approval/payout-sim/doc-upload DB-backed in code (not e2e-tested this gate)
  audit_persistence: audit_cost schema seeded; runtime audit events DB-backed in code
  build: dotnet build Release PASS (0/0)
  tests: 137/137 PASS
  image: not rebuilt (no code change)
  ghcr_push: NO
  container_app_update: env ConnectionStrings__InsuranceAIPlatform=secretref:sql-connection -> revision iap-demo-api--0000002
  api_health_after: 200
FRONTEND:
  files_changed: none
  customer_create_ui: works (no Failed to fetch) - verified headless Chromium
  claims_list_ui: loads from SQL
  i18n_en_ua: intact (EN default + UA switch)
  build: n/a
  swa_redeploy: NO (existing backend-mode bundle works now that API is SQL-backed)
LIVE_E2E:
  customers_list_after: 200 (total 200 + test creates)
  customer_create_after: 200 success (CUST-T0201 / CUST-T0203)
  created_customer_persists: YES (search returns it; survives page refresh)
  claims_list_after: 200 (15)
  claim_detail_after: golden CLM-1006 in-memory (200); DB-created from SQL
  write_flows_tested: customer create (persist verified)
  audit_visible: golden audit in-memory; audit schema seeded in SQL
  api_cors: POST /api/customers returns ACAO=<SWA>
  ui_smoke: 8/8 (EN default, customers load, create no-Failed-to-fetch, persist-after-refresh, claims load)
COST_GUARDRAILS:
  resource_count_before: ~9 top-level
  resource_count_after: + Azure SQL server + InsuranceAIPlatform DB (only Azure SQL added)
  sql_created: YES (serverless GP_S_Gen5_1)
  ai_created: NO
  aks_created: NO
  acr_created: NO
  container_min_replicas: 0
  swa_sku: Free
  budget_status: existing $30/mo budget + alerts; SQL incremental ~$1-5/mo
DOCS:
  created_or_updated: docs/architecture/azure/AZURE_SQL_PERSISTENCE_V0.1.md + AZURE_COST_POST_SQL_CHECK_V0.1.md; docs/architecture/backend/SQL_PERSISTENCE_MODEL_V0.1.md + DATA_SURFACE_PERSISTENCE_MATRIX_V0.1.md; updated docs/portfolio/LIVE_DEMO_RUNBOOK_V0.1.md + README_PORTFOLIO_SECTION_DRAFT_V0.1.md; +5 sql-0x screenshots
COMMITS:
  commits: 70af774 "docs: record Azure SQL persistence deployment" (11 files, +478/-12)
  hook_results: QA commit-gate PASSED via genuine Opus /qa-inspector CLEARED 6/6 + QA-CLEARED log line (NO bypass)
PUSH:
  target: origin/dev
  result: 89e8516..70af774 dev -> dev
  verified_remote: origin/dev=70af774; origin/main=69e67312 unchanged
SAFETY:
  secrets_scan: CLEAN (connection string only in Container App secret; no password/connstr/subscription/tenant/object-id in repo)
  connection_string_printed: NO
  tokens_printed: NO
  real_pii_used: NO (synthetic only)
  sql_enabled: YES (intended this gate)
  ai_enabled: NO
  workflow_run: NO (azure-deploy-demo.yml is workflow_dispatch-only; push did not trigger it)
  main_touched: NO
  force_push_used: NO
ROLLBACK:
  api_rollback: az containerapp update -g rg-iap-demo -n iap-demo-api --remove-env-vars ConnectionStrings__InsuranceAIPlatform  (or roll back to revision iap-demo-api--0000001)
  sql_cleanup: az sql server delete -g rg-iap-demo -n iap-sql-r2-6c7g465 --yes
  frontend_rollback: n/a (unchanged)
GITHUB_HANDOFF:
  latest_report: InsuranceAIPlatform/latest-report.md
  latest_summary: InsuranceAIPlatform/latest-summary.json
  task_report: InsuranceAIPlatform/azure-sql-full-persistence-all-pages-v0.1/report.md
BLOCKERS: none (West/North Europe SQL capacity blocked new servers -> deployed to germanywestcentral, cross-region ~10ms)
LIMITATIONS: golden CLM-1006 detail/sub-resources + dashboard summary + demo scenario are curated in-memory fixtures by design (not SQL); AI Mock-only; demo auth client-side; write flows beyond customer-create DB-backed in code but not e2e-tested this gate; SQL cross-region (germanywestcentral); SQL-auth used (gate-approved) vs Entra-only bicep
NEXT_RECOMMENDED_GATE: e2e-verify remaining write flows (approval/payout-sim/doc-upload persistence) + optionally migrate golden CLM-1006 views to SQL; or real-AI-provider gate; or PRODUCT_I18N_QA_POLISH_V0.1
STOP_LINE_CONFIRMATION: stopped after report; no real-AI gate, no custom-domain gate, AIKB not updated, no PR, main untouched, no extra resources, no redesign
```
