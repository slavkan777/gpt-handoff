REQUEST_ID: REQ-2026-06-06-insuranceai-azure-dev-deploy-rag-fallback-mega-v0-1
STATUS: BLOCKED
TASK_TYPE: project-azure-dev-deploy-and-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_DEV_DEPLOY_RAG_FALLBACK_MEGA_V0.1
COMPLETED_BY: claude

# Azure dev deploy — BLOCKED before any Azure mutation (RAG SQL-schema prerequisite vs. gate's no-migration boundary)

One-line: the deploy MECHANISM is understood and feasible, but the target **Azure SQL database lacks the RAG schema**, and creating it requires an **EF migration which THIS gate explicitly forbids**. Deploying the RAG image would make `/rag/*` return **500** (missing tables) — a STOP condition — and flipping the frontend would break the currently-working public mock demo. Therefore I stopped **before mutating any Azure resource** (all Azure calls this gate were READ-ONLY). No deploy, no revision change, no frontend flip, no rollback needed.

## Routing Lock Verification
- PROJECT InsuranceAIPlatform; remote `slavkan777/InsuranceAIPlatform`; branch `rag/local-foundation-mega-v0.1`; HEAD `4067591` (pushed, matches preflight). Working tree clean. No source change this gate.

## Starting Repo State
- `git status` clean; HEAD `4067591 feat(rag): add local Ollama reasoning provider`; `origin/rag/local-foundation-mega-v0.1 = 4067591`; `origin/main = 69e6731` (untouched).

## Azure Target Verification (READ-ONLY)
- Logged in (subscription enabled; GUIDs omitted from this public report). RG `rg-iap-demo` (westeurope).
- Container App `iap-demo-api`: image **`ghcr.io/slavkan777/insuranceai-api:14d5c81-cors`**, `registries=null` (anonymous GHCR pull → package is public), **active revision `iap-demo-api--0000002`** (rollback target), minReplicas=0, FQDN `iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io`.
- Static Web App `iap-demo-swa`: Free, mock mode, host `kind-meadow-03cf73103.7.azurestaticapps.net`.
- **No ACR** in the subscription.

## Pre-Deploy Checks
- Secret scan (carried from preflight): clean. No `.env`/secret files committed. Connection string = passwordless localdb (dev). Build: 181/181 on `4067591` (this session). No new migration in this gate's HEAD.
- **Deploy mechanism discovered:** `server/Dockerfile` is tracked (multi-stage .NET 9, context `server/`, runtime :8080 — it is the SAME Dockerfile that produced the current live image). Docker available locally (20.10.23). GHCR auth cached in docker config. Deploy verb would be `az containerapp update -n iap-demo-api -g rg-iap-demo --image ghcr.io/slavkan777/insuranceai-api:<tag>`. **Mechanism is feasible** — it is NOT the blocker.
- **The `.github/workflows/azure-deploy-demo.yml` is a DISABLED skeleton** (build/push/deploy steps commented; "disabled until AZURE_MINIMAL_DEPLOY_V0.1"; GH secrets "not configured"). So CI-based deploy is not wired; a manual `docker build → GHCR push → az containerapp update` is the only working path.

## THE BLOCKER (hard) — Azure SQL is missing the RAG schema, and creating it is forbidden here
Evidence the RAG tables are absent in the Azure SQL `InsuranceAIPlatform` DB:
1. The live backend image is `…:14d5c81-cors` — commit `14d5c81`, which **predates** the RAG migration `20260604161120_AddRagFoundation` (committed in `912e841`, after the Azure SQL persistence deploy `70af774`).
2. The API does **NOT** migrate or seed at startup — `server/.../Api/Program.cs` contains only `builder.Build()` (l.224) and `app.Run()` (l.247); no `Database.Migrate()`, no `RagSeeder`. Schema/seed is applied by the **separate `DbMigrator` project**, which the old deploy ran only up to its commit (pre-RAG).
3. Live proof: `/api/claims` → 200 (Claims schema present), but `/api/.../rag/infrastructure` → 404 on the old image (no RAG endpoint). On the NEW image that endpoint exists and immediately queries `PolicyClauses`/`EvidenceChunks`/`RagAuditTraces`/`RagEvaluationQuestions` — tables that do not exist → **SqlException → HTTP 500**.

Consequence: deploying the RAG image in fallback mode would turn `/rag/*` from 404 into **500** (STOP condition "/rag returns 500" / "RAG endpoints remain 404"). The fix — apply `AddRagFoundation` + run `RagSeeder` against Azure SQL — is an **EF/schema migration**, which this gate **explicitly forbids** ("NO schema/EF migration"). The two requirements are mutually exclusive within this gate.

Additional reason not to proceed: the public SWA demo currently works in mock mode; flipping it to backend mode against a backend whose `/rag` 500s would **break the live portfolio demo**. Not worth it for a predictably-failing RAG path.

## Backend Build/Image/Deploy
- NOT performed (would be wasted — the deploy would fail RAG smoke and require rollback). Mechanism + Dockerfile validated by inspection; image-build risk LOW (feature branch passes `dotnet build` + 181/181; Dockerfile is the proven one).

## Backend Env Settings / Frontend Deploy/Config / Smoke / UI / RAG-fallback / Citation proofs
- NOT performed — gated behind the blocker above. No Azure resource was created/updated/deleted; no frontend redeploy; live SWA + Container App untouched.

## Rollback State
- N/A — nothing was deployed. Live state unchanged: `iap-demo-api--0000002` (image `14d5c81-cors`) still active; SWA still mock. Recorded rollback target for the future gate: revision `iap-demo-api--0000002`.

## Files Changed
- None (no source/config change; no commit this gate).

## Commands Run (all READ-ONLY for Azure)
- `git` state/secret-scan; `az account/resource/staticwebapp/webapp/containerapp/acr/sql show|list` (read-only); `curl` health/endpoint probes; `docker version`; local file reads (Dockerfile, Program.cs, workflow).

## Boundaries Honored
NO Azure deploy · NO Azure create/update/delete (read-only only) · NO main push · NO merge · NO production · NO new resources · NO Qdrant/Ollama Azure · NO paid provider · NO secrets printed/committed · NO schema/EF migration · NO force push · NO fake deploy/smoke status · live demo left intact.

## Cost/Risk Notes
- Zero cost incurred (read-only). The recommended prerequisite (migrate+seed Azure SQL) adds no standing cost; Container App stays scale-to-zero.

## Blockers/Failures
- **BLOCKER:** Azure SQL `InsuranceAIPlatform` DB lacks the RAG schema; the migration to create it is forbidden by this gate's "NO schema/EF migration" boundary. (Strong inference from image tag + Program.cs + migration ordering; not a direct SQL query — Azure SQL firewall/Entra prevents a read-only table list without a firewall/access change, which would itself be a resource mutation.)
- Override path: if the `ai_analysis` schema in Azure SQL was in fact migrated to `AddRagFoundation` + seeded in a step not visible to me, this gate can proceed directly — confirm and I will deploy on the next prompt.

## Slava Manual Azure Checklist (if you want to unblock now)
None required for THIS report. To enable the deploy, the prerequisite gate below needs explicit authorization (it performs an EF migration on the cloud DB — its own risk gate).

## Next Safe Step — proposed prerequisite gate
`AZURE_SQL_RAG_MIGRATE_SEED_V0.1` (separately authorized, because it is a schema migration on a cloud DB):
1. Read-only confirm current `ai_analysis` schema level in Azure SQL (sqlcmd with Entra token + temporary client-IP firewall allow, then revert) — verify RAG tables truly absent.
2. Apply `AddRagFoundation` via `DbMigrator` (or `dotnet ef database update`) against Azure SQL; run `RagSeeder` (synthetic CLM-1006…1011 + CLM-1061). Idempotent.
3. THEN re-run THIS deploy gate (`AZURE_DEV_DEPLOY_RAG_FALLBACK_MEGA_V0.1`) — mechanism already de-risked: `docker build -f server/Dockerfile server` → `docker push ghcr.io/slavkan777/insuranceai-api:<tag>` → `az containerapp update --image … --set-env-vars Rag__QdrantEnabled=false Rag__LocalLlamaEnabled=false` → smoke → revision-rollback to `iap-demo-api--0000002` if needed.

STOP — holding after report. No deploy, no Azure mutation, no migration, no main, no unrelated work.
