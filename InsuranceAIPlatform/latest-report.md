# InsuranceAIPlatform — Latest gate report

**Gate:** PORTFOLIO_AZURE_PROOF_PACK_V0.1
**Type:** portfolio/interview evidence pack from the live Azure deployment (docs + screenshots; no Azure/source changes)
**Date (UTC):** 2026-05-30
**Verdict:** **PROOF_PACK_READY** ✅
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

**Source `dev` HEAD:** `211d50f` *(unchanged — no commit this gate)* · **origin/main** `69e67312` *(untouched)*

**Full report:** [portfolio-azure-proof-pack-v0.1/report.md](portfolio-azure-proof-pack-v0.1/report.md)

---

## Bottom line

Created an interview-ready proof pack for the live Azure deployment: **5 docs + 5 verified screenshots** under `docs/portfolio/` (uncommitted). An Opus `/qa-inspector` verified **no overclaiming** (CLEARED 7/7) — every LIVE claim re-checked true, every deferral disclosed honestly.

**Honest headline:** the golden path is live end-to-end (summary metrics, full CLM-1006 workspace, demo scenario, CORS), but the **list endpoints `/api/claims` + `/api/customers` return 500** because **Azure SQL is deferred** — the SPA gracefully falls back to seeded rows so the UI stays complete. Documented openly across the pack.

Docs: proof pack, interview story (pitches + recruiter/senior bullets + avoid-overclaiming rules), technical proof matrix (20 rows, LIVE/PARTIAL/DEFERRED), live-demo runbook (warm-up, golden path, teardown, troubleshooting), README draft. Screenshots: login → overview → claim workspace → AI evidence → audit (authenticated, rendered).

No Azure change, no deploy, no source commit/push, no secrets.

**Next recommended gate:** `PORTFOLIO_AZURE_PROOF_PACK_COMMIT_V0.1` (commit docs + screenshots) — or `AZURE_SQL_OPTIONAL_GATE_V0.1` to make the list endpoints live (closes the only visible gap).

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
  api_health: 200 Healthy
  cors_check: GET from SWA origin -> 200 + ACAO=<SWA>
  data_endpoint: /api/claims/summary 200 {totalActive:47,...}; CLM-1006 8 endpoints 200; /api/claims + /api/customers 500 (SQL deferred -> seed fallback)
  azure_resource_count: 9 (no AKS/ACR/SQL)
  cost_guardrails: minReplicas=0; SWA Free; idle ~$0
FILES_CREATED_OR_UPDATED:
  proof_pack: docs/portfolio/PORTFOLIO_AZURE_PROOF_PACK_V0.1.md
  interview_story: docs/portfolio/INTERVIEW_STORY_AZURE_DOTNET_AI_V0.1.md
  technical_matrix: docs/portfolio/TECHNICAL_PROOF_MATRIX_V0.1.md
  live_demo_runbook: docs/portfolio/LIVE_DEMO_RUNBOOK_V0.1.md
  readme_draft: docs/portfolio/README_PORTFOLIO_SECTION_DRAFT_V0.1.md
  screenshots: docs/portfolio/screenshots/*.png (5, authenticated)
EVIDENCE_SUMMARY:
  azure_services: ACA, SWA Free, GHCR, Managed Identity, Key Vault, Storage, App Insights, Log Analytics
  dotnet_proof: .NET 9 API live on ACA; /health 200; 137/137 tests
  ai_readiness_proof: IAiProvider Mock+DeepSeek opt-in; workbench live golden data; AI output Mock (PARTIAL)
  cost_proof: scale-to-zero, Free tiers, capped telemetry, $30 budget, idle ~$0
  security_proof: MI+KV arch, storage shared-key off, non-root, config-driven CORS, no repo secrets
  deferred_items: Azure SQL (list 500), real AI (Mock), AKS/ACR (not used), prod auth (client-side demo)
VALIDATION:
  docs_review: Opus /qa-inspector CLEARED 7/7 — no overclaiming
  links_checked: frontend/API/health/summary/golden-claim/CORS verified
  screenshots: 5 captured; authenticated rendered content verified
  other_checks: live summary values match UI cards (47/12/6/8/14.3)
SAFETY:
  secrets_scan: CLEAN
  azure_resources_changed: NO
  deploy_attempted: NO
  image_pushed: NO
  workflow_run: NO
  source_commit_attempted: NO
  source_push_attempted: NO
  main_touched: NO
  resources_deleted: NO
GITHUB_HANDOFF:
  latest_report:  InsuranceAIPlatform/latest-report.md
  latest_summary: InsuranceAIPlatform/latest-summary.json
  task_report:    InsuranceAIPlatform/portfolio-azure-proof-pack-v0.1/report.md
BLOCKERS: none
LIMITATIONS: /api/claims + /api/customers 500 (Azure SQL deferred; SPA seed fallback); AI Mock-only; demo auth client-side; CI workflow scaffolded not run
NEXT_RECOMMENDED_GATE: PORTFOLIO_AZURE_PROOF_PACK_COMMIT_V0.1 (or AZURE_SQL_OPTIONAL_GATE_V0.1)
STOP_LINE_CONFIRMATION: stopping after report; no commit/push, no deploy, no resource create/delete, no AIKB, no PR, no main, no SQL/AI gate started
```
