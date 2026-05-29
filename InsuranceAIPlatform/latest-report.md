# InsuranceAIPlatform — Latest gate report

**Gate:** AZURE_FRONTEND_CORS_FIX_V0.1
**Type:** config-driven CORS fix + API image redeploy + frontend backend-mode rewire
**Date (UTC):** 2026-05-29
**Verdict:** **LIVE_API_WIRED** ✅
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

**Source `dev` HEAD:** `14d5c81` *(unchanged — no commit this gate)* · **origin/main** `69e67312` *(untouched)*

**Full report:** [azure-frontend-cors-fix-v0.1/report.md](azure-frontend-cors-fix-v0.1/report.md)

---

## Bottom line

The live demo is now wired to the **live API** end-to-end. The SPA at `https://kind-meadow-03cf73103.7.azurestaticapps.net` (backend-mode bundle) fetches real data from `https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io`, and CORS is fixed.

- **CORS** is now **config-driven** (`Cors:AllowedOrigins`, fallback localhost, no wildcard, no credentials). Source: `Program.cs` + `appsettings.json` (tracked, **uncommitted** — next gate commits).
- **API** rebuilt (`ghcr.io/slavkan777/insuranceai-api:14d5c81-cors`) + pushed to GHCR, Container App updated → revision `0000001` (100% traffic), **minReplicas=0 preserved**, `/health` 200, **137/137** tests.
- **Frontend** rebuilt in backend mode (live API URL baked) + redeployed to the existing SWA.
- **CORS smoke:** preflight + GET from the SWA origin both return `access-control-allow-origin: <SWA>`; localhost still works (no dev regression); browser-equivalent GET returns `200` JSON.
- Independently verified: **Opus `/qa-inspector` CLEARED 8/8**. No new/deleted resources (count 9), SWA Free, no SQL/AI/AKS/ACR, idle ≈ $0. No source commit/push, main untouched, no secrets/token printed.

**Next recommended gate:** `AZURE_FRONTEND_CORS_FIX_COMMIT_V0.1` — commit `Program.cs` + `appsettings.json` (+ the CORS-fix doc) to `dev`.

**Rollback:** `az containerapp update -g rg-iap-demo -n iap-demo-api --image ghcr.io/slavkan777/insuranceai-api:ce1a1e5` + rebuild frontend without the backend env and redeploy.

---

## REPORT BACK FORMAT

```text
VERDICT: LIVE_API_WIRED
GATE: AZURE_FRONTEND_CORS_FIX_V0.1
SOURCE_REPO:
  path: C:/Projects/InsuranceAIPlatform
  branch: dev
  head: 14d5c8126a4ecd51122557520facb63224e7fe44
  origin_dev: 14d5c8126a4ecd51122557520facb63224e7fe44
  origin_main: 69e67312a10cc9bcf28c4e387a126b48c91fb9c5
  working_tree_after: M Program.cs + M appsettings.json (tracked) + untracked AZURE_FRONTEND_CORS_FIX_V0.1.md + test-results/ (UNCOMMITTED)
AZURE_LIVE_STATE:
  subscription: personal "Azure subscription 1" (IDs redacted)
  resource_group: rg-iap-demo (westeurope)
  api_url: https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io
  swa_url: https://kind-meadow-03cf73103.7.azurestaticapps.net
  api_health_before: 200
  api_health_after: 200
CORS_FIX:
  root_cause: hardcoded WithOrigins("http://localhost:5173"); SWA origin got no ACAO
  files_changed: server/InsuranceAIPlatform.Api/{Program.cs, appsettings.json}
  allowed_origins: http://localhost:5173, https://kind-meadow-03cf73103.7.azurestaticapps.net
  config_driven: YES (env-overridable Cors__AllowedOrigins__N; fallback localhost; no wildcard, no AllowCredentials)
  preflight_before: 204 with NO access-control-allow-origin
  preflight_after: 204 + ACAO=<SWA> + access-control-allow-methods: GET
BACKEND:
  build: PASS (Release)
  tests: 137/137 passed
  docker_image: ghcr.io/slavkan777/insuranceai-api:14d5c81-cors (digest sha256:208f9cca…)
  ghcr_push: PUSHED (public; manifest OK)
  container_app_update: PASS (minReplicas=0/max=2 preserved; Succeeded)
  revision: iap-demo-api--0000001 (Active, 100% traffic); old --mjdxllx deprovisioned
FRONTEND:
  build_mode: backend
  api_base_url: https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io
  build_result: PASS (bundle index-BJlHvw5H.js; live API URL baked; secret scan clean)
  swa_redeploy: PASS (SWA CLI 2.0.9; token via env; production)
SMOKE_TESTS:
  api_health: 200
  api_data_endpoint: 200 JSON {"totalActive":47,...}
  cors_preflight: 204 + ACAO for SWA origin + allow-methods GET
  swa_http: 200
  assets_loaded: JS 200 text/javascript; CSS 200
  spa_fallback: /claims 200
  browser_api_integration: HTTP-level CONFIRMED — GET from SWA origin 200 + ACAO; bundle targets live API (no headless browser)
COST_GUARDRAILS:
  new_resources_created: NO (count unchanged = 9)
  resource_count: 9
  sql_deployed: NO
  ai_deployed: NO
  aks_created: NO
  acr_created: NO
  container_min_replicas: 0
  swa_sku: Free
  estimated_idle_risk: ~$0
DOCS:
  created_or_updated: docs/architecture/azure/AZURE_FRONTEND_CORS_FIX_V0.1.md
  committed: NO
SAFETY:
  secrets_scan: CLEAN
  tokens_printed: NO
  source_commit_attempted: NO
  source_push_attempted: NO
  main_touched: NO
  workflow_run: NO
  resources_deleted: NO
ROLLBACK_PLAN: az containerapp update -g rg-iap-demo -n iap-demo-api --image ghcr.io/slavkan777/insuranceai-api:ce1a1e5 ; rebuild frontend mock + redeploy
GITHUB_HANDOFF:
  latest_report:  InsuranceAIPlatform/latest-report.md
  latest_summary: InsuranceAIPlatform/latest-summary.json
  task_report:    InsuranceAIPlatform/azure-frontend-cors-fix-v0.1/report.md
BLOCKERS: none
LIMITATIONS: live-data verified at HTTP/CORS level (no headless browser); source uncommitted (next gate); SQL deferred (seeded in-memory data); AI Mock
NEXT_RECOMMENDED_GATE: AZURE_FRONTEND_CORS_FIX_COMMIT_V0.1
STOP_LINE_CONFIRMATION: stopping after report; no source commit/push, no AIKB, no PR, no main, no SQL/AI, no new/deleted resources, no next gate
```
