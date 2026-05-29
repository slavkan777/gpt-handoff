# Gate Report — AZURE_FRONTEND_CORS_FIX_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-29
**Type:** config-driven CORS fix + API image redeploy + frontend backend-mode rewire (existing resources only)
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

---

## Verdict

**LIVE_API_WIRED** ✅ — the live demo now uses the **live API** end-to-end. CORS is config-driven and allows the SWA origin; the rebuilt API image is live on the existing Container App (scale-to-zero preserved); the SPA was rebuilt in backend mode against the live API URL and redeployed to the existing SWA. Independently verified by an Opus `/qa-inspector` END gate → **CLEARED 8/8**. No new/deleted resources, no SQL/AI/AKS/ACR, no source commit/push.

## What changed (source — uncommitted, tracked, for the next commit gate)

| File | Change |
|---|---|
| `server/InsuranceAIPlatform.Api/Program.cs` | CORS origins **config-driven** — reads `Cors:AllowedOrigins` (fallback `http://localhost:5173`); no `AllowCredentials`, no wildcard |
| `server/InsuranceAIPlatform.Api/appsettings.json` | `Cors:AllowedOrigins = [localhost:5173, <SWA origin>]` |

Origins live in the **tracked base** `appsettings.json` (not `appsettings.Production.json`, which `server/.gitignore` ignores → wouldn't be reproducible). Env-overridable via `Cors__AllowedOrigins__N`. A transient Production file was created then removed. `dotnet test -c Release` → **137/137**. No business logic touched.

## API redeploy

- Image `ghcr.io/slavkan777/insuranceai-api:14d5c81-cors` (digest `sha256:208f9cca…`) built + pushed to GHCR (public, manifest OK).
- Container App `iap-demo-api` updated → revision **`iap-demo-api--0000001`** (Active, 100% traffic); old `…--mjdxllx` (`:ce1a1e5`) deprovisioned. **minReplicas=0 / max=2 preserved.**

## CORS smoke (after) — PASS

| Request (Origin = SWA) | Result |
|---|---|
| `OPTIONS …/api/claims/summary` | `204` + `access-control-allow-origin: <SWA>` + `access-control-allow-methods: GET` + `vary: Origin` |
| `GET …/api/claims/summary` | `200` + ACAO `<SWA>` + JSON `{"totalActive":47,…}` |
| `GET` Origin `http://localhost:5173` | `200` + ACAO localhost (no dev regression) |
| `GET /health` | `200` |

## Frontend (backend mode)

- Built `VITE_INSURANCE_API_MODE=backend` + `VITE_INSURANCE_API_BASE_URL=<live API>`. Bundle `assets/index-BJlHvw5H.js` — live API URL baked, dead `localhost:5284` eliminated, secret scan clean.
- Redeployed `./dist` (+ `staticwebapp.config.json`) to `iap-demo-swa` production via SWA CLI 2.0.9 (token via env, never printed).

## Frontend integration smoke — PASS

- SWA root `200`, references `index-BJlHvw5H.js`; `/claims` deep link `200` (SPA fallback); JS/CSS `200`; deployed JS contains the live API URL.
- **End-to-end:** browser-equivalent `GET …/api/claims/summary` with `Origin: <SWA>` → `200` JSON + ACAO `<SWA>`. *(HTTP/CORS-level verification; no headless browser in this environment.)*

## Cost / resources

Count **9** (unchanged — only a revision swapped). SWA **Free**. ACA **minReplicas=0**. No SQL/AI/AKS/ACR. Idle ≈ $0.

## Rollback

```bash
az containerapp update -g rg-iap-demo -n iap-demo-api --image ghcr.io/slavkan777/insuranceai-api:ce1a1e5
# + rebuild frontend without the VITE_ backend env and redeploy ./dist to iap-demo-swa
```

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
  working_tree_before: clean except untracked test-results/
  working_tree_after: M Program.cs + M appsettings.json (tracked) + untracked AZURE_FRONTEND_CORS_FIX_V0.1.md + test-results/ (ALL UNCOMMITTED)
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
  config_driven: YES (Cors:AllowedOrigins; env-overridable Cors__AllowedOrigins__N; fallback localhost; no wildcard, no AllowCredentials)
  preflight_before: 204 with NO access-control-allow-origin
  preflight_after: 204 + ACAO=<SWA> + access-control-allow-methods: GET
BACKEND:
  build: PASS (Release)
  tests: 137/137 passed
  docker_image: ghcr.io/slavkan777/insuranceai-api:14d5c81-cors (digest sha256:208f9cca…)
  ghcr_push: PUSHED (public; manifest OK)
  container_app_update: PASS (image swapped; minReplicas=0/max=2 preserved; Succeeded)
  revision: iap-demo-api--0000001 (Active, 100% traffic); old --mjdxllx deprovisioned
FRONTEND:
  build_mode: backend
  api_base_url: https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io
  build_result: PASS (bundle index-BJlHvw5H.js; live API URL baked; localhost fallback eliminated; secret scan clean)
  swa_redeploy: PASS (SWA CLI 2.0.9; token via env; production)
SMOKE_TESTS:
  api_health: 200
  api_data_endpoint: 200 JSON {"totalActive":47,...}
  cors_preflight: 204 + ACAO for SWA origin + allow-methods GET
  swa_http: 200
  assets_loaded: JS 200 text/javascript; CSS 200
  spa_fallback: /claims 200
  browser_api_integration: HTTP-level CONFIRMED — GET from SWA origin 200 + ACAO for SWA; deployed bundle targets live API (no headless browser run)
COST_GUARDRAILS:
  new_resources_created: NO (count unchanged = 9; only a revision changed)
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
  secrets_scan: CLEAN (changed source + dist; no values/token)
  tokens_printed: NO
  source_commit_attempted: NO
  source_push_attempted: NO
  main_touched: NO
  workflow_run: NO
  resources_deleted: NO
ROLLBACK_PLAN: az containerapp update -g rg-iap-demo -n iap-demo-api --image ghcr.io/slavkan777/insuranceai-api:ce1a1e5 ; rebuild frontend without VITE_ backend env + redeploy ./dist
GITHUB_HANDOFF:
  latest_report: InsuranceAIPlatform/latest-report.md
  latest_summary: InsuranceAIPlatform/latest-summary.json
  task_report: InsuranceAIPlatform/azure-frontend-cors-fix-v0.1/report.md
BLOCKERS: none
LIMITATIONS: live-data verified at HTTP/CORS level (no headless browser); source changes uncommitted (next gate); SQL deferred (seeded in-memory data); AI Mock
NEXT_RECOMMENDED_GATE: AZURE_FRONTEND_CORS_FIX_COMMIT_V0.1
STOP_LINE_CONFIRMATION: stopping after report; no source commit/push, no AIKB, no PR, no main, no SQL/AI, no new/deleted resources, no next gate
```
