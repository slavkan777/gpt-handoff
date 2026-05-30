# InsuranceAIPlatform — Latest gate report

**Gate:** AZURE_SQL_FULL_PERSISTENCE_ALL_PAGES_DEPLOY_PUSH_V0.1
**Type:** deploy Azure SQL + make the previously-broken list/create flows persistent, validate, commit/push (docs)
**Date (UTC):** 2026-05-30
**Verdict:** **SQL_PERSISTENCE_DEPLOYED_PARTIAL_PUSHED** ✅
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

**Source `dev`:** `89e8516` → **`70af774`** (pushed) · **origin/main `69e67312` (untouched)**

**Full report:** [azure-sql-full-persistence-all-pages-v0.1/report.md](azure-sql-full-persistence-all-pages-v0.1/report.md)

---

## Bottom line

The site no longer falls back to mock for its broken surfaces — **customers, the claims list, and creating a synthetic customer are now live from Azure SQL and persist.** The exact bug the user hit (create customer → **"Failed to fetch"**) is **resolved**: `POST /api/customers` returns 200 with the SWA CORS header and the row survives a page refresh. The list endpoints that returned 500 now return 200 from SQL.

**Key discovery:** the EF persistence layer was **already built and committed** (6 service DbContexts, 11 migrations, a `DbMigrator` that migrates + idempotently seeds, and a serverless-SQL bicep module). So this gate needed **no new EF code** — it was deploy + wire + migrate.

**Deployed:** Azure SQL `InsuranceAIPlatform`, **GP_S_Gen5_1 serverless** (auto-pause 60 min, 2 GB), on `iap-sql-r2-6c7g465` in **germanywestcentral** (West + North Europe were capacity-blocked for new SQL servers; `AllowAllAzureServices` reaches it cross-region, ~10 ms). **SQL auth**; the connection string lives **only** as a Container App secret (`sql-connection`) — never in the repo. Migrated + seeded (201 customers/policies/vehicles, 15 claims, + documents/approval/audit/ai-analysis). Container App revision `0000002` picked it up; **no image rebuild, no frontend redeploy** needed.

**Verified:** API `/health` 200 · `/api/customers` 200 (200 rows) · `/api/claims` 200 (15) · `POST /api/customers` 200 + persist · headless-Chromium UI **8/8** (EN default, lists load, create no-"Failed to fetch", persists across refresh). Independent **Opus `/qa-inspector` CLEARED 6/6**.

**Cost:** only Azure SQL added — **no AKS/ACR/AI**; ACA minReplicas=0; SWA Free; serverless auto-pause → realistic **~$1–5/mo, well under $30**.

## Why PARTIAL (honest)

The curated **golden-claim CLM-1006 detail + sub-resources**, the **dashboard summary**, and the **demo scenario** remain **in-memory fixtures by design** (deterministic demo) — not SQL-backed. Write flows beyond customer-create (approval / payout-sim / document-upload) are **DB-backed in code** but not individually e2e-tested this gate. AI stays Mock; demo auth client-side; synthetic data only.

## Commit
| Commit | Message |
|---|---|
| `70af774` | `docs: record Azure SQL persistence deployment` (11 files; no source/infra change — EF layer pre-existed) |

**Next recommended gate:** e2e-verify the remaining write flows (approval / payout-sim / document-upload persistence) and optionally migrate the golden CLM-1006 views to SQL — or a real-AI-provider gate, or `PRODUCT_I18N_QA_POLISH_V0.1`.

---

The full REPORT BACK FORMAT block is in the [full report](azure-sql-full-persistence-all-pages-v0.1/report.md).
