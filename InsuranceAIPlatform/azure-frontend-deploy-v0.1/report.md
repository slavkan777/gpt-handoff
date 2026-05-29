# Gate Report — AZURE_FRONTEND_DEPLOY_MASTER_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-29
**Type:** frontend content deploy to existing Azure Static Web App + smoke + docs (uncommitted)
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

---

## Verdict

**FRONTEND_DEPLOYED_PARTIAL** ✅ — the SPA is **live** on the existing SWA and fully interactive on **mock data**; **live-API wiring is deferred** (the API CORS policy is hardcoded to `localhost:5173`, so a browser at the SWA origin is CORS-blocked). Independently verified by an Opus `/qa-inspector` END gate → **CLEARED 9/9**. No source commit, no new resources, no SQL/AI/AKS/ACR, no secrets exposed.

## What is live

- **Frontend:** `https://kind-meadow-03cf73103.7.azurestaticapps.net` → **200**, serves our app (`InsuranceAIPlatform · Auto Claim AI Workbench`), deep links work (SPA fallback).
- **API:** `https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io` → `/health` **200** (unaffected).

## How it was deployed

- Build: `npm run build` (Vite, **mock mode** — the app's default self-contained synthetic-data mode). `dist/` = `index.html` + `assets/` + `staticwebapp.config.json` (copied in for SPA navigation fallback).
- Deploy: Azure Static Web Apps CLI **2.0.9** (`npx @azure/static-web-apps-cli deploy ./dist --env production`). Deployment token pulled from `az staticwebapp secrets list` into an env var **inline** — never printed, logged, stored, or committed.

## Smoke tests (all PASS)

| Check | Result |
|---|---|
| SWA root | 200; our app (title/`#root`/hashed assets), not the Azure placeholder |
| SPA deep link `/claims` | 200 → `index.html` (navigation fallback) |
| JS / CSS assets | 200 `text/javascript` (~443 KB) / 200 `text/css` |
| API `/health` after | 200 Healthy (`service=InsuranceAIPlatform.Api`) |
| Browser ↔ live API | N/A — mock mode makes no cross-origin calls (no CORS error in the running demo) |

## Why mock mode (live-API wiring deferred)

- API CORS is **hardcoded** `WithOrigins("http://localhost:5173")` (`Program.cs`); the SWA origin is not allowed and there's **no env-var override**.
- Probe: `GET /api/claims/summary` with the SWA `Origin` → **200 + real data** (`{"totalActive":47,…}`) but **no `Access-Control-Allow-Origin`** header → backend serves data **without SQL**, yet a browser would block it.
- So backend-wiring would render a **broken** demo. Mock mode gives a fully working portfolio demo now. Live API URL is **not** baked into the bundle (verified).

## Cost / resources

RG resource count **unchanged (9)** → no new resources. SWA **Free** ($0). Container App **minReplicas=0**. No SQL/AI/AKS/ACR. Idle ≈ $0.

## Git / safety

- Branch `dev`, HEAD `ae4545c` (unchanged), origin/dev `ae4545c`, origin/main `69e67312` — **no commit, no push, no main, no PR, no force**.
- Working tree: untracked `docs/architecture/azure/AZURE_FRONTEND_DEPLOY_V0.1.md` (new doc) + `test-results/`; `dist/` git-ignored; **no source code modified**.
- Secrets: dist + doc scan clean; deployment token never printed/committed; no SQL/AI deployed; no image pushed; no workflow run; no resource deleted.

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
  api_url_wired: NO (mock mode; live-API wiring deferred to CORS-fix gate)
  cors_needed: YES for backend wiring (not for this mock deploy)
  cors_changed: NO
SWA_DEPLOY:
  method: "@azure/static-web-apps-cli 2.0.9 (npx) deploy ./dist --env production; token via SWA_CLI_DEPLOYMENT_TOKEN env (inline), not argv"
  target_swa: iap-demo-swa (rg-iap-demo), production env
  result: deployed OK; SWA picked up staticwebapp.config.json
  token_printed: NO
SMOKE_TESTS:
  swa_http: 200 (our app, not placeholder)
  assets_loaded: JS 200 text/javascript; CSS 200 text/css
  spa_fallback: /claims -> 200 index.html
  api_health: 200
  browser_api_integration: N/A (mock mode, no cross-origin calls); backend CORS lacks ACAO for SWA origin -> deferred
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
  secrets_scan: CLEAN (dist + doc; no key values/GUIDs; dead localhost:5284 only)
  deployment_token_printed: NO
  source_commit_attempted: NO
  source_push_attempted: NO
  main_touched: NO
  workflow_run: NO
  resources_deleted: NO
GITHUB_HANDOFF:
  latest_report: InsuranceAIPlatform/latest-report.md
  latest_summary: InsuranceAIPlatform/latest-summary.json
  task_report: InsuranceAIPlatform/azure-frontend-deploy-v0.1/report.md
BLOCKERS: none
LIMITATIONS: live-API wiring deferred (API CORS hardcoded to localhost:5173, no env override -> needs source change + image redeploy); auth deferred; SQL deferred; AI Mock-only
NEXT_RECOMMENDED_GATE: AZURE_FRONTEND_DEPLOY_COMMIT_V0.1 (commit the new doc) -> then AZURE_FRONTEND_CORS_FIX_V0.1 (add SWA origin to Program.cs CORS + redeploy image, then backend-wire the SPA)
STOP_LINE_CONFIRMATION: stopping after report; no source commit/push, no PR, no AIKB, no SQL, no AI, no new resources, no deletes, no next gate started
```
