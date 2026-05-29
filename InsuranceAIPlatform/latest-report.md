# InsuranceAIPlatform — Latest gate report

**Gate:** AZURE_READINESS_PRE_FLIGHT_V0.1
**Type:** planning-only Azure readiness dossier + cost/governance architecture
**Date (UTC):** 2026-05-29
**Verdict:** **ACCEPT_AZURE_PREFLIGHT_READY** ✅ (no resources, no `az login`, no secrets, no source commit/push)
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

**Source `dev`:** `2e9443a` *(unchanged)* · **origin/main** `69e67312` *(untouched)*

**Full report:** [azure-readiness-pre-flight-v0.1/report.md](azure-readiness-pre-flight-v0.1/report.md)

---

## Bottom line

Produced a complete, interview-grade Azure plan for a production-*shaped* deployment at hobby cost (~$5–10/mo real, $30 cap, ~$1000 ceiling). Core idea: **public is static+free; compute and AI sleep until a human logs in and clicks.** App is deploy-shaped (static SPA + stateless .NET API + connection-string-swappable DB + provider-agnostic AI); platform scaffolding (Docker/IaC/CI/SWA config) is the next gates' work. 4 planning docs written under `docs/architecture/azure/` (uncommitted). Nothing deployed; zero Azure resources exist.

---

## REPORT BACK FORMAT

```text
VERDICT: ACCEPT_AZURE_PREFLIGHT_READY
GATE: AZURE_READINESS_PRE_FLIGHT_V0.1
MODEL_USED: Opus 4.8 (1M context) / claude-opus-4-8[1m]
SOURCE_REPO:
  path: C:/Projects/InsuranceAIPlatform
  branch: dev
  head: 2e9443a6726f34f20bd26233e840ae8cc982d91a (unchanged — no source commit)
  origin_dev: 2e9443a6726f34f20bd26233e840ae8cc982d91a
  origin_main: 69e67312a10cc9bcf28c4e387a126b48c91fb9c5 (untouched)
  working_tree_status: 4 new untracked azure planning docs + test-results/.last-run.json; 0 staged; no commit/push
REPO_READINESS:
  frontend: Vite SPA -> static dist/  => Azure Static Web Apps (gap: staticwebapp.config.json -> gate 2)
  backend: .NET 9 single Api host referencing 6 service libs in-proc (microservice-shaped modular monolith) => 1 Container App scale-to-zero (gap: Dockerfile -> gate 2)
  containers: NONE present (no Dockerfile) -> gate 2 deliverable
  config: IConfiguration; conn-string + DEEPSEEK_API_KEY via env (never logged); appsettings.json clean => Key Vault + Managed Identity
  ai_mode: IAiProvider Mock default / DeepSeek opt-in disabled => add Azure OpenAI provider behind same seam, manual (gate 6)
  db_mode: SQL Server LocalDB, 6 schemas, DbMigrator => Azure SQL Serverless auto-pause, connection-string swap only (gate 4)
AZURE_ARCHITECTURE:
  public_layer: Static Web Apps (CDN SPA) — no backend/DB/AI on anonymous load
  auth_layer: SWA built-in auth / Entra External ID — compute+AI only after login
  api_layer: Container Apps insurance-api, minReplicas=0 (cold-start on first authed call)
  microservices: insurance-api (monolith now) + ai-worker (split later) + cleanup-job (ACA Job cron)
  db: Azure SQL Database Serverless, auto-pause 1h
  storage: Azure Blob (Hot/Cool) for artifacts
  cleanup: Blob lifecycle TTL + cleanup-job + SQL auto-pause
  secrets_identity: Key Vault + Managed Identity (passwordless; no conn-string passwords)
  observability: App Insights/Monitor/Log Analytics, sampling + 30-day retention + daily cap (free <5GB)
  ai_services: Azure OpenAI/Foundry + Document Intelligence F0 + AI Search Free — manual-trigger, token/page caps, Mock default, advisory-only + audit
  ci_cd: GitHub Actions, build/test/containerize/deploy via OIDC federated identity (no stored secret)
  iac: Bicep (infra/*.bicep) — gate 2
COST_MODEL:
  real_target: $5-10/month (often $0-5 with GHCR + SWA Free)
  comfort_cap: $30/month
  architecture_ceiling: ~$1000/month (AKS + always-on + AI Search Basic + heavy AI)
  alerts: $5/$10/$20/$30/$50 (NOTIFY only — do not stop resources)
  top_risks: SQL not pausing; ACA minReplicas>0; AI loops; Doc Intelligence batches; AI Search Basic (~$75/mo); log volume; blob growth; ACR fixed ~$5/mo
  mitigations: scale-to-zero; SQL auto-pause; manual AI + Mock default; F0/Free AI tiers; sampling+cap; blob TTL; GHCR instead of ACR; one RG tear-down; monthly review
SLAVA_SETUP:
  required_manual_steps: create/sign-in Azure account; select subscription; create budget alerts $5-50 FIRST; choose+record region (W/N Europe or East US); approve naming (rg-iap-prod, iap-prod-*); ensure GitHub repo reachable for OIDC
  do_not_do_yet: create any resource manually; create AI deployments; paste secrets/keys/subscription-id; enable paid/background services; az login outside a deploy gate
LOCAL_DOCS:
  created: yes (uncommitted; to be committed in gate 2)
  paths: docs/architecture/azure/AZURE_READINESS_PRE_FLIGHT_V0.1.md, AZURE_SERVICE_MATRIX_V0.1.md, AZURE_COST_GOVERNANCE_V0.1.md, AZURE_GATE_PLAN_V0.1.md
GITHUB_HANDOFF:
  latest_report:  InsuranceAIPlatform/latest-report.md
  latest_summary: InsuranceAIPlatform/latest-summary.json
  task_report:    InsuranceAIPlatform/azure-readiness-pre-flight-v0.1/report.md
BLOCKERS: none
LIMITATIONS: 4 local docs uncommitted (committed in gate 2); cost figures are list-price estimates for a tiny workload (verify vs live budget after gate 3); test-results/ not gitignored
NEXT_RECOMMENDED_GATE: AZURE_ACCOUNT_AND_BUDGET_SETUP_MANUAL_V0.1 (Slava manual; budget alerts; 0 resources created)
STOP_LINE_CONFIRMATION: stopping after report; no az login / resources / deploy / secrets / product code / source commit / push / main / PR / real AI call
```
