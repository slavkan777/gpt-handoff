# InsuranceAIPlatform — Latest gate report

**Gate:** AZURE_MINIMAL_DEPLOY_MASTER_V0.1
**Type:** first real Azure minimal deploy (operator-checkpointed)
**Date (UTC):** 2026-05-29
**Verdict:** **DEPLOYED_MINIMAL** ✅ (API live + `/health` 200; idle ~$0)
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

**Source `dev` HEAD:** `ce1a1e5` *(unchanged — no commit this gate)* · **origin/main** `69e67312` *(untouched)*

**Full report:** [azure-minimal-deploy-v0.1/report.md](azure-minimal-deploy-v0.1/report.md)

---

## Bottom line

First real Azure deploy is **live** on the **personal** subscription (switched off the corporate tenant first). Our API image (GHCR, public) runs on Azure Container Apps **scale-to-zero**; **`GET /health` → 200** `{"status":"Healthy","service":"InsuranceAIPlatform.Api","environment":"Production"}` after a cold start. 8 resources in `rg-iap-demo` (westeurope); **SQL + AI deferred, no AKS, no ACR**; SWA (Free) provisioned, content deploy deferred. Cost guardrails all verified — idle ≈ $0. The `enableSql` infra edit + 3 post-deploy docs are **uncommitted** (commit gate next).

**Live:** API `https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io` · SWA `https://kind-meadow-03cf73103.7.azurestaticapps.net` (empty)

---

## REPORT BACK FORMAT

```text
VERDICT: DEPLOYED_MINIMAL
GATE: AZURE_MINIMAL_DEPLOY_MASTER_V0.1
MODEL_USED: Opus 4.8 (1M context) / claude-opus-4-8[1m]
SOURCE_REPO:
  path: C:/Projects/InsuranceAIPlatform
  branch: dev
  head: ce1a1e54721f7487d2b1124ab072806ec1b151fe (unchanged — no source commit this gate)
  origin_dev: ce1a1e5
  origin_main: 69e67312a10cc9bcf28c4e387a126b48c91fb9c5 (untouched)
  working_tree_before: clean except test-results/
  working_tree_after: modified infra/main.bicep (enableSql) + 3 new docs/architecture/azure/*.md (ALL UNCOMMITTED); test-results/ untracked
OPERATOR_APPROVALS:
  login_approved: YES (after switching corporate -> personal account)
  deploy_approved: YES (defer SQL + GHCR)
  resource_creation_approved: YES
AZURE_ACCOUNT:
  login_used: personal account (corporate tenant explicitly switched away); IDs/email redacted
  subscription_selected: "Azure subscription 1" (personal; confirmed has the $30 budget)
  region: westeurope
  budget_verified: operator-confirmed $30/mo + alerts $5/$10/$20/$30/$50
LIVE_PREFLIGHT:
  providers: 7 registered (App, Web, Storage, KeyVault, OperationalInsights, Insights, ManagedIdentity)
  quotas: ok (fresh sub; minimal footprint)
  names: uniqueString-derived (storage/KV) — no collision
  sku_region_availability: westeurope OK for all deployed types
  blockers: none (post account-switch)
IMAGE:
  docker_build: PASS (reproducible)
  registry: GHCR (ghcr.io/slavkan777/insuranceai-api), package made PUBLIC
  tag: ce1a1e5 (digest sha256:3d320fa9…)
  push_result: PUSHED; anonymous pull HTTP 200
DEPLOYMENT:
  attempted: YES
  resource_group: rg-iap-demo (westeurope)
  deployment_name: iap-min-20260529223052
  result: provisioningState Succeeded
  resources_created: 8 (Container App + ACA env + SWA Free + Managed Identity + Key Vault + Storage + Log Analytics + App Insights)
  enable_ai: false
  aks_created: NO
POST_DEPLOY_CONFIG:
  container_apps: image ghcr.io/slavkan777/insuranceai-api:ce1a1e5, minReplicas=0, maxReplicas=2, provisioning Succeeded, running
  sql: not deployed (enableSql=false; output sqlServerFqdn="")
  storage: allowSharedKeyAccess=false; 4 containers; lifecycle TTL
  key_vault: RBAC; no secrets stored
  monitoring: Log Analytics (30d, 1GB/day cap) + App Insights (50% sampling)
  ai_optional: not deployed
SMOKE_TESTS:
  api_health: PASS (HTTP 200, body service=InsuranceAIPlatform.Api, env=Production)
  frontend: DEFERRED (SWA resource live but no content yet)
  auth: n/a (no auth configured this gate)
  notes: /health has no DB dependency; cold start from scale-to-zero handled
COST_GUARDRAILS:
  budget: $30/mo + alerts (operator)
  min_replicas: 0 (scale-to-zero)
  sql_auto_pause: n/a (SQL not deployed)
  blob_ttl: configured
  log_retention: 30d + 1GB/day cap; App Insights 50% sampling
  estimated_idle_risk: ~$0 (residual LAW/Storage < $1-2/mo); no AKS/ACR/always-on
DOCS:
  created_or_updated: docs/architecture/azure/{AZURE_MINIMAL_DEPLOY_V0.1, AZURE_DEPLOYED_RESOURCES_V0.1, AZURE_COST_POST_DEPLOY_CHECK_V0.1}.md + infra/main.bicep enableSql edit
  committed: NO (uncommitted; separate commit gate)
GITHUB_HANDOFF:
  latest_report:  InsuranceAIPlatform/latest-report.md
  latest_summary: InsuranceAIPlatform/latest-summary.json
  task_report:    InsuranceAIPlatform/azure-minimal-deploy-v0.1/report.md
BLOCKERS: none
LIMITATIONS: frontend content not deployed (SWA empty; follow-up swa deploy + SPA->API wiring/linked-backend); SQL deferred; AI Mock-only; first deploy via local az (CI/OIDC path still the guarded workflow)
CLEANUP_PLAN: az group delete -n rg-iap-demo --yes --no-wait
NEXT_RECOMMENDED_GATE: AZURE_MINIMAL_DEPLOY_COMMIT_V0.1 (commit enableSql edit + 3 docs to dev) -> then AZURE_FRONTEND_DEPLOY_V0.1
STOP_LINE_CONFIRMATION: stopping after report; no AI integration, no SQL prod migration, no extra resources, no source commit/push, no AIKB, no PR, no main, resources NOT deleted (await operator for cleanup)
```
