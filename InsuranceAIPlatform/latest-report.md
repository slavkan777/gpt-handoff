# InsuranceAIPlatform — Latest gate report

**Gate:** AZURE_FRONTEND_DEPLOY_MASTER_V0.1
**Type:** frontend content deploy to existing Azure Static Web App + smoke + docs (uncommitted)
**Date (UTC):** 2026-05-29
**Verdict:** **FRONTEND_DEPLOYED_PARTIAL** ✅
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

**Source `dev` HEAD:** `ae4545c` *(unchanged — no commit this gate)* · **origin/main** `69e67312` *(untouched)*

**Full report:** [azure-frontend-deploy-v0.1/report.md](azure-frontend-deploy-v0.1/report.md)

---

## Bottom line

The frontend is now **live** at **`https://kind-meadow-03cf73103.7.azurestaticapps.net`** — the React/Vite SPA deployed into the existing SWA (Free), serving a fully interactive demo on **mock data**. Deep links work (SPA fallback), assets load, and the API `/health` still returns 200.

**Live-API wiring is deferred (by design):** the API's CORS policy is **hardcoded** to `http://localhost:5173` (no env override), so a browser at the SWA origin is CORS-blocked. A server-side probe shows the backend *does* return real data without SQL (`{"totalActive":47,…}`) but with **no `Access-Control-Allow-Origin`** header — so a backend-wired build would be a broken demo. Mock mode gives a working portfolio URL now; the live-data path is one well-scoped gate away.

Independently verified by an **Opus `/qa-inspector` END gate → CLEARED 9/9**. No new resources (RG count unchanged at 9), SWA **Free**, ACA **minReplicas=0**, no SQL/AI/AKS/ACR, idle ≈ $0. **No source commit/push, no main, no PR**, deployment token never printed.

**Next recommended gate:** `AZURE_FRONTEND_DEPLOY_COMMIT_V0.1` (commit the new doc) → then `AZURE_FRONTEND_CORS_FIX_V0.1` (add the SWA origin to `Program.cs` CORS + redeploy the API image, then backend-wire the SPA).

---

## REPORT BACK FORMAT

```text
VERDICT: FRONTEND_DEPLOYED_PARTIAL
GATE: AZURE_FRONTEND_DEPLOY_MASTER_V0.1
SOURCE_REPO:
  path: C:/Projects/InsuranceAIPlatform
  branch: dev
  head: ae4545c8d7aeafba3cb168de3f2b843e1051b7ef
  origin_dev: ae4545c8d7aeafba3cb168de3f2b843e1051b7ef
  origin_main: 69e67312a10cc9bcf28c4e387a126b48c91fb9c5
  working_tree_before: clean except untracked test-results/
  working_tree_after: untracked docs/architecture/azure/AZURE_FRONTEND_DEPLOY_V0.1.md + test-results/ (dist/ git-ignored); no source code modified
AZURE_LIVE_STATE:
  subscription: personal "Azure subscription 1" (IDs redacted)
  resource_group: rg-iap-demo (westeurope)
  api_url: https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io
  swa_url: https://kind-meadow-03cf73103.7.azurestaticapps.net
  api_health_before: 200 Healthy
  api_health_after: 200 Healthy
FRONTEND_DISCOVERY:
  frontend_root: C:/Projects/InsuranceAIPlatform (root-level Vite SPA)
  package_manager: npm
  build_command: npm run build (tsc -b && vite build)
  output_dir: dist
  api_base_url_mechanism: VITE_INSURANCE_API_MODE (backend|mock; default mock) + VITE_INSURANCE_API_BASE_URL (default http://localhost:5284)
CONFIG_CHANGES:
  files_changed: docs/architecture/azure/AZURE_FRONTEND_DEPLOY_V0.1.md (new, uncommitted); staticwebapp.config.json copied into dist/ (build artifact, git-ignored); NO source code changed
  api_url_wired: NO (mock mode; deferred)
  cors_needed: YES for backend wiring (not for this mock deploy)
  cors_changed: NO
SWA_DEPLOY:
  method: "@azure/static-web-apps-cli 2.0.9 (npx) deploy ./dist --env production; token via env, not argv"
  target_swa: iap-demo-swa (rg-iap-demo), production
  result: deployed OK; SWA picked up staticwebapp.config.json
  token_printed: NO
SMOKE_TESTS:
  swa_http: 200 (our app, not placeholder)
  assets_loaded: JS 200 text/javascript; CSS 200 text/css
  spa_fallback: /claims -> 200 index.html
  api_health: 200
  browser_api_integration: N/A (mock mode); backend CORS lacks ACAO for SWA origin
COST_GUARDRAILS:
  new_resources_created: NO (RG count unchanged = 9)
  sql_deployed: NO
  ai_deployed: NO
  aks_created: NO
  acr_created: NO
  container_min_replicas: 0
  swa_sku: Free
  estimated_idle_risk: ~$0 (SWA Free adds $0)
DOCS:
  created_or_updated: docs/architecture/azure/AZURE_FRONTEND_DEPLOY_V0.1.md
  committed: NO
SAFETY:
  secrets_scan: CLEAN
  deployment_token_printed: NO
  source_commit_attempted: NO
  source_push_attempted: NO
  main_touched: NO
  workflow_run: NO
  resources_deleted: NO
GITHUB_HANDOFF:
  latest_report:  InsuranceAIPlatform/latest-report.md
  latest_summary: InsuranceAIPlatform/latest-summary.json
  task_report:    InsuranceAIPlatform/azure-frontend-deploy-v0.1/report.md
BLOCKERS: none
LIMITATIONS: live-API wiring deferred (API CORS hardcoded to localhost:5173); auth deferred; SQL deferred; AI Mock-only
NEXT_RECOMMENDED_GATE: AZURE_FRONTEND_DEPLOY_COMMIT_V0.1 -> then AZURE_FRONTEND_CORS_FIX_V0.1
STOP_LINE_CONFIRMATION: stopping after report; no source commit/push, no PR, no AIKB, no SQL, no AI, no new resources, no deletes, no next gate started
```
