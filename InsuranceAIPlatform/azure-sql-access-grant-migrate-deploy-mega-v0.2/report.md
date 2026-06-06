REQUEST_ID: REQ-2026-06-06-insuranceai-azure-sql-access-grant-migrate-deploy-mega-v0-2
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-azure-sql-access-migrate-seed-deploy-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_SQL_ACCESS_GRANT_MIGRATE_SEED_DEPLOY_SMOKE_MEGA_V0.2
COMPLETED_BY: claude

# Azure SQL access + migrate + seed + backend/frontend deploy + end-to-end smoke — COMPLETE

One-line: the previous gates' access blocker is resolved (this gate authorized it). I set the dev/test SQL Entra admin + one temp firewall rule, applied the existing `AddRagFoundation` migration + ran `RagSeeder` against Azure SQL, deployed the feature-branch backend image to the existing Container App (RAG fallback mode) and the backend-mode frontend to the existing SWA, and smoke-tested the whole stack end-to-end through the public URL. RAG endpoints went **404 → 200**, citations are claim-scoped, fallback labels are honest, the UI renders the Azure-backed answer, **0 console errors**. The temp firewall rule was removed; the dev/test Entra admin is left in place as an owner-approved access posture change. **Not self-accepted.**

## Routing Lock Verification
- PROJECT InsuranceAIPlatform; SOURCE_REPO `C:/Projects/InsuranceAIPlatform`; remote `slavkan777/InsuranceAIPlatform`; branch `rag/local-foundation-mega-v0.1`; HEAD `4067591` (pushed; `origin/...=4067591`, ahead/behind 0/0). `origin/main = 69e6731` (untouched). Working tree clean.

## Starting Repo State
- `git status` clean; HEAD `4067591 feat(rag): add local Ollama reasoning provider`. No source/config change made to the product repo during this gate.

## Azure Target Verification (read-only, before mutation)
- Logged-in identity `info.msdn.com@gmail.com` (object id `1792fe81-09b2-4450-93b0-b013c24a1071`); subscription "Azure subscription 1" **Enabled**.
- RG `rg-iap-demo` (westeurope, Succeeded). SQL server `iap-sql-r2-6c7g465` (Ready), DB `InsuranceAIPlatform` (serverless GP_S_Gen5_1, was **Paused** → auto-resumed on first connect).
- Container App `iap-demo-api`: active revision `iap-demo-api--0000002`, image `ghcr.io/slavkan777/insuranceai-api:14d5c81-cors`, minReplicas 0, FQDN `iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io`. **Rollback target recorded.**
- SWA `iap-demo-swa` (Free), host `kind-meadow-03cf73103.7.azurestaticapps.net`. Public URL loaded **200**.
- Pre-change: SQL Entra admin `[]` (none); firewall only `AllowAllAzureServices`; backend `/rag/infrastructure` → **404** on old image.

## Prechecks
- Secret scan: clean (carried from accepted push gate; no `.env`/secret files committed; connection string is a runtime secretRef, never in repo).
- Migration to apply confirmed = existing **`20260604161120_AddRagFoundation`** (AiAnalysis context has exactly 3 migrations; it is the last). **No new migration generated.**
- Seeder idempotency verified by code: all 5 already-seeded contexts guard with `if (await db.X.AnyAsync(ct)) return;`; `RagSeeder` is per-key additive idempotent (loads existing ids, inserts only missing; never deletes/updates; all data synthetic, no PII). `DbMigrator` uses `db.Database.MigrateAsync` (respects `__EFMigrationsHistory`).
- CORS: `server/.../appsettings.json` `Cors:AllowedOrigins` already includes `https://kind-meadow-03cf73103.7.azurestaticapps.net` (verified live at smoke). Frontend lives at repo root (Vite); build = `tsc -b && vite build`.
- Backend build: authoritative multi-stage Docker build succeeded (Release; all 7 service projects + API compiled). `dotnet test` 181/181 earlier this session on this exact commit.

## Access Unblock Actions (authorized by this gate)
- Set dev/test SQL **Entra admin** = signed-in identity: `az sql server ad-admin create -g rg-iap-demo -s iap-sql-r2-6c7g465 --display-name info.msdn.com@gmail.com --object-id 1792fe81-...` → `login=info.msdn.com@gmail.com, type=ActiveDirectory`.
- Added **one** temp firewall rule `tmp-slava-rag-migrate-20260606205438` for current IP `176.36.240.192` only. **Removed at cleanup** (firewall back to only `AllowAllAzureServices`).
- Did NOT touch Key Vault (not needed — Entra path worked). No password/secret requested or printed.

## SQL Access Method
- Entra/AAD token path. Acquired token via `az account get-access-token --resource https://database.windows.net/` (value never printed); connected with Windows PowerShell `System.Data.SqlClient` + `.AccessToken` (inspection) and `DbMigrator` with connection string `Authentication=Active Directory Default` (migration+seed) after clearing interfering `AZURE_*` env vars so the credential chain resolved to the logged-in az identity. Connected as `live.com#info.msdn.com@gmail.com` to DB `InsuranceAIPlatform`.

## Pre-Migration DB State (direct query)
- Per-context schema-qualified `__EFMigrationsHistory`. `ai_analysis` schema had only: `__EFMigrationsHistory, AiAnalysisRuns, AiEvidenceReferences, AiFindings, AiRiskSignals` — **no RAG tables**. `ai_analysis.__EFMigrationsHistory` = `[InitialAiAnalysisPersistence, AddAiAnalysisRunStructuredFields]` → **`AddRagFoundation` pending**. Other 5 contexts fully migrated (consistent with live `/api/claims=200`).

## Migration/Seed Actions
- Ran `DbMigrator` against Azure SQL. Output: `[ai_analysis] Migration applied`; 5 other contexts no-op migrate + idempotent seeders short-circuited (existing rows: 20 claims, 207 customers, etc.). RagSeeder inserted the RAG corpus. No drops, no destructive ops, no new migration.

## Post-Migration DB Proof (direct query)
- `ai_analysis.__EFMigrationsHistory` now includes **`20260604161120_AddRagFoundation`**.
- RAG tables created: `PolicyClauses=8`, `EvidenceChunks=50`, `RagEvaluationQuestions=21`, `RagAuditTraces=0` (runtime-written).
- Evidence is claim-scoped (`ClaimId`): CLM-1006=13, CLM-1007=6, CLM-1008=6, CLM-1009=7, CLM-1010=10, CLM-1011=8. **CLM-1006 = 13 chunks** (grounded), **CLM-1061 = 0** (not a seeded Claim — see smoke). All synthetic (ClauseId CLA-AC-*, POL-2025-*), no PII.
- After smoke, `RagAuditTraces=3` (CLM-1006×2, CLM-1012×1) — proves the deployed app's SQL identity has **write** access to the new tables.

## Backend Build / Image / Deploy
- `docker build -f server/Dockerfile server` → exit 0, image `ghcr.io/slavkan777/insuranceai-api:rag-fallback-4067591-20260606210103` (sha256 `8d71ce01…`).
- `docker push` → GHCR digest `sha256:bc9e155a…` (only the app layer uploaded; base layers existed).
- `az containerapp update -n iap-demo-api --image … --set-env-vars Rag__QdrantEnabled=false Rag__LocalLlamaEnabled=false` → new revision **`iap-demo-api--0000003`**, provisioning Succeeded, now **Healthy / Running / 100% traffic**. No new resource created.

## Backend Env Settings
- Added: `Rag__QdrantEnabled=false`, `Rag__LocalLlamaEnabled=false`.
- Preserved on the new revision: `ASPNETCORE_URLS=http://+:8080`, `APPLICATIONINSIGHTS_CONNECTION_STRING`, `AZURE_CLIENT_ID=845a1a1d-…` (MI), `AiProvider__Mode=Mock`, `ConnectionStrings__InsuranceAIPlatform` → secretRef `sql-connection` (value never read/printed in deploy).

## Frontend Deploy / Config
- Built backend-mode bundle: `VITE_INSURANCE_API_MODE=backend VITE_INSURANCE_API_BASE_URL=https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io npm run build` (shell env overrides `.env.local`'s mock default; `.env.local` left untouched). Bundle `dist/assets/index-B8iViJhY.js` contains the backend FQDN, **0** `localhost:5284` leftovers.
- Installed `@azure/static-web-apps-cli` 2.0.9; `swa deploy ./dist --deployment-token <az-retrieved, redacted> --env production` → deployed to `https://kind-meadow-03cf73103.7.azurestaticapps.net`. Copied `staticwebapp.config.json` into `dist/` for SPA routing parity. Token retrieved via `az staticwebapp secrets list` (never printed; no secret in chat).
- Live verification: SWA now serves `index-B8iViJhY.js`; that live bundle contains the backend FQDN, 0 `localhost` → **live SWA is backend mode**.

## Azure Smoke Matrix (against deployed stack)
| # | Check | Result |
|---|---|---|
| 24 | backend `/health` | **200** |
| 25 | backend `/api/claims` (+`Origin: SWA`) | **200**, `access-control-allow-origin: https://kind-meadow-03cf73103.7.azurestaticapps.net` (CORS OK) |
| 26 | `/api/claims/CLM-1006/rag/infrastructure` | **200** (was 404) |
| 27 | fallback labels honest | vectorRuntime `disabled`/`in-memory-hash`/`reachable:false`; localReasoning `disabled`/`reachable:false`; counts 8/50/21 match SQL |
| 28 | `/api/claims/CLM-1006/rag/ask` | **200**, `providerMode:Mock`, citations all `CLM-1006-*`, `advisoryOnly:true`, confidence **62** (clean question) |
| 29 | insufficient-evidence | CLM-1061 → **404 CLAIM_NOT_FOUND** (not a seeded Claim). Demonstrated on valid 0-evidence claim **CLM-1012** → `Mock`, confidence 0, **0 citations**, answer "Недостатньо релевантних доказів… Рекомендується перегляд людиною" |
| 30 | public SWA loads | **200** |
| 31 | frontend backend mode | **31** Azure-backend calls during UI smoke (not mock); live bundle carries backend FQDN |

## UI Smoke Result (headless Chromium against the public SWA)
- Login (demo creds) OK; `/claims/CLM-1006` shows customer **Роберт Джонсон** (Azure-served).
- `/claims/CLM-1006/ai-evidence`: `rag-panel` mounted; clicked `rag-btn-coverage` → **`rag-answer-card` rendered** with advisory banner. Answer is a real grounded Ukrainian analysis with citations [1]–[4] (driver statement, claim application, STO repair detail — part #521190X910, 8h catalog vs 14h billed).
- **31** backend calls, **all HTTP 200** across 15 distinct endpoints incl. `GET /rag/infrastructure`, `GET /rag/audit`, `POST /rag/ask`. Screenshot captured (full claim workspace + RAG panel + confidence bar + citation cards).
- **0 console errors, 0 failed requests.**

## RAG Fallback Proof
- `/rag/infrastructure`: `vectorRuntime.status=disabled, backend=in-memory-hash, reachable=false`; `localReasoningRuntime.status=disabled, reachable=false`. `/rag/ask` `providerMode=Mock`. No fake qdrant/live, no fake Ollama. Embedding model `local-hash-embed-v0.1` (256-dim) — honest deterministic fallback.

## Citation / Leakage Proof
- CLM-1006: every citation chunkId prefixed `CLM-1006-` (application, approval-summary, coverage-check, invoice / photo-front, policy-terms, repair-detail). CLM-1012: 0 citations (insufficient). **Zero cross-claim leakage.** Advisory disclaimer present in every answer.

## Console / Network Findings
- 0 console errors; 0 request failures; no CORS errors (allow-origin header present). No 4xx/5xx from the backend during the UI flow.

## Rollback / Cleanup State
- Temp firewall rule **removed** (firewall = only `AllowAllAzureServices`).
- Rollback target retained: inactive revision `iap-demo-api--0000002` (image `14d5c81-cors`). Rollback = `az containerapp revision activate --revision iap-demo-api--0000002` or redeploy that image. Not needed — smoke passed.
- DB migration is forward-only; nothing dropped. Live demo healthy.

## Files Changed
- **Product repo: none** (no source/config commit; `.env.local` untouched). Local build artifacts only: rebuilt `dist/` (backend mode) and copied `staticwebapp.config.json` into `dist/` — neither committed.

## Commands Run (high level)
- `git` state/scan; `az` (account/group/sql server+db/ad-admin create+list+delete/firewall-rule create+list+delete/containerapp show+update+revision list+show/staticwebapp show+secrets); PowerShell `System.Data.SqlClient` AAD queries; `dotnet run DbMigrator`; `docker build/push`; `npm run build`; `swa deploy`; headless Playwright smoke; `curl` endpoint probes.

## Boundaries Honored
NO main push · NO merge · NO production · NO new Azure resources except the authorized dev/test SQL Entra admin + one (now-removed) temp firewall rule · NO Qdrant/Ollama in Azure · NO paid provider · NO OpenAI/DeepSeek/Azure OpenAI key · NO secret printed or committed (SQL secret never read during deploy; SWA token & DB access token used in-process, redacted) · NO real PII (all synthetic) · NO new EF migration · NO dropped/destroyed DB objects · NO force push · NO deleted business resources · NO fake status.

## Security / Access Changes Left In Place
- **SQL Entra admin** `info.msdn.com@gmail.com` on dev/test server `iap-sql-r2-6c7g465` — left in place (gate step 39) as an **owner-approved dev/test access posture change** so future maintenance/migrations don't re-block. Revoke anytime with `az sql server ad-admin delete -g rg-iap-demo -s iap-sql-r2-6c7g465` if you prefer it removed.

## Cost / Risk Notes
- Negligible cost: SQL is serverless (auto-pauses), Container App minReplicas=0 (scales to zero), SWA Free. One extra GHCR image tag. No standing compute added.

## Blockers / Failures
- **None.** The gate completed end-to-end. The only deviation: `CLM-1061` is not a seeded Claim entity (404), so the insufficient-evidence case was demonstrated on valid 0-evidence claim `CLM-1012` (same cautious behavior).

## Slava Manual Azure Checklist
- **None required.** Optional: (a) revoke the dev/test SQL Entra admin if you don't want it persisted; (b) decide later whether to wire a real vector store / managed LLM behind the provider seam (separate gate).

## Next Safe Step
- Architect GPT audit via «отчёт». Optional future gate (separately authorized): swap the honest fallback for a managed vector store + managed LLM behind the existing `IGroundedAnswerGenerator`/Qdrant seam — no schema change needed.

STOP — holding after report. No main merge, no production, no paid providers, no Azure Qdrant/Ollama, no unrelated work.
