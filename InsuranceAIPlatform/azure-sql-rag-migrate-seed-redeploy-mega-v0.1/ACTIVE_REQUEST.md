REQUEST_ID: REQ-2026-06-06-insuranceai-azure-sql-rag-migrate-seed-redeploy-mega-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-azure-sql-migrate-seed-deploy-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_SQL_RAG_MIGRATE_SEED_AND_REDEPLOY_MEGA_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/azure-sql-rag-migrate-seed-redeploy-mega-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/azure-sql-rag-migrate-seed-redeploy-mega-v0.1/report.md

OPERATOR NOTE:
This is ONE combined prerequisite + deploy mega-gate. Avoid endless micro-prompts. It explicitly authorizes the minimum Azure SQL schema/seed work needed for the already-pushed RAG feature, then redeploys the existing dev/test backend/frontend and smoke-tests. Slava should only be interrupted for auth/login/firewall/manual confirmation if a tool requires it. Do not ask for passwords/secrets.

CONTEXT FROM ACCEPTED REPORTS:
- Feature branch `rag/local-foundation-mega-v0.1` is pushed to origin at HEAD `4067591`.
- main remains untouched.
- Local state accepted: RAG pipeline + Qdrant local + Ollama local provider + mock fallback, 181 tests passed, 30 scenario sweep 27/30 PASS, 0 leakage.
- Azure preflight accepted: resource group `rg-iap-demo`, Static Web App `iap-demo-swa`, backend Container App `iap-demo-api`, Azure SQL DB `InsuranceAIPlatform`, Key Vault and Managed Identity already exist.
- Previous deploy gate was correctly BLOCKED because Azure SQL likely lacks RAG tables from `AddRagFoundation`; deploying RAG image first would make `/rag/*` return 500.
- Deploy mechanism is known feasible: `server/Dockerfile`, GHCR image push, `az containerapp update` to existing `iap-demo-api`.
- Existing Azure public URL: https://kind-meadow-03cf73103.7.azurestaticapps.net/

GOAL:
Safely migrate/seed the Azure DEV/TEST SQL database with the RAG schema/data, then deploy the already-accepted feature branch to existing Azure dev/test resources in fallback mode, smoke-test the Azure UI/API end-to-end, and publish a complete report. This is dev/test only. No production, no main, no paid provider, no Azure Qdrant, no Azure Ollama.

DONE STATE:
1. Verify routing lock, repo path, branch, HEAD `4067591`, remote, clean/expected tree.
2. Verify Azure target before any mutation:
   - subscription logged in;
   - resource group exactly `rg-iap-demo`;
   - SQL server + DB `InsuranceAIPlatform`;
   - Static Web App `iap-demo-swa`;
   - Container App `iap-demo-api`;
   - current active Container App revision/image for rollback, expected old image around `14d5c81-cors`;
   - public SWA URL loads.
3. Re-run lightweight prechecks:
   - no unrelated local changes;
   - no secrets in changed files/configs;
   - backend build/test or targeted build;
   - frontend build if frontend is redeployed;
   - confirm the migration to be applied is the existing accepted RAG migration, not a new generated migration.
4. Azure SQL schema discovery:
   - connect to Azure SQL using safe existing credentials path: Managed Identity/Key Vault/connection string retrieval if already configured, or Azure CLI/Entra token if available;
   - do NOT print secret values or full connection strings;
   - inspect `__EFMigrationsHistory` and RAG table presence;
   - if client IP firewall allow is required, you MAY add ONE temporary firewall rule scoped to the current client IP only, with a clear name like `tmp-slava-rag-migrate-<timestamp>`, and MUST remove it before final report if technically possible;
   - if SQL access requires a password/secret pasted by Slava, STOP BLOCKED.
5. Migration/seed authorization within this gate:
   - Apply ONLY existing accepted migrations needed up to and including `AddRagFoundation` against Azure SQL `InsuranceAIPlatform` using the repository's existing `DbMigrator` or `dotnet ef database update` path.
   - Run the existing RAG seeding path/idempotent seeder for synthetic demo claims/evidence/evaluation data only.
   - Do NOT generate a new migration.
   - Do NOT drop tables/data.
   - Do NOT modify real PII.
   - If data appears production-like or not synthetic/dev/test, STOP BLOCKED.
6. Verify DB after migration/seed:
   - RAG tables exist: policy clauses / evidence chunks / audit traces / evaluation questions or actual project table names;
   - seeded synthetic CLM-1006 and CLM-1061 data exists;
   - migration history includes RAG migration;
   - record counts without exposing sensitive values.
7. Backend deploy:
   - Build backend Docker image from feature branch HEAD `4067591` using existing `server/Dockerfile` and server context.
   - Tag image clearly, e.g. `ghcr.io/slavkan777/insuranceai-api:rag-fallback-<shortsha>-<timestamp>`.
   - Push to GHCR only if auth already works; if auth blocked, STOP with exact blocker.
   - Update existing `iap-demo-api` Container App ONLY as a new revision/image, no new Container App/resource.
   - Set fallback-mode env vars on the Container App:
     - `Rag__QdrantEnabled=false`
     - `Rag__LocalLlamaEnabled=false`
   - Preserve existing SQL/KV/MI settings; do not print secret values.
8. Frontend deploy/config:
   - Update existing `iap-demo-swa` only, not a new SWA.
   - Build/deploy frontend in backend mode if Vite env vars are build-time:
     - `VITE_INSURANCE_API_MODE=backend`
     - `VITE_INSURANCE_API_BASE_URL=<iap-demo-api FQDN>`
   - Use existing SWA deployment path/token if already configured; if SWA token/auth is missing and would require secret in chat, STOP/PARTIAL after backend smoke and report exact blocker.
9. Azure smoke test end-to-end:
   - backend `/health` 200;
   - backend `/api/claims` 200;
   - backend `/api/claims/CLM-1006/rag/infrastructure` 200, no longer 404/500;
   - expected fallback mode: vector runtime honestly says disabled/skipped/in-memory-hash, not fake qdrant/live;
   - `/api/claims/CLM-1006/rag/ask` 200, `providerMode=Mock`, claim-scoped citations only CLM-1006, advisory wording present, confidence sane;
   - `/api/claims/CLM-1061/rag/ask` cautious insufficient evidence if endpoint/data available;
   - public SWA loads;
   - frontend is backend mode, not mock-only;
   - UI CLM-1006 Evidence Intelligence renders answer/citations/confidence from Azure backend;
   - no fatal console/network errors.
10. Rollback/cleanup behavior:
   - Record previous backend revision/image before deploy.
   - If backend smoke fails critically after Container App update, rollback traffic/revision to previous active revision.
   - If frontend backend-mode deploy fails or breaks UI, restore previous SWA/mock deployment if feasible, or report exact rollback limitation.
   - Remove temporary SQL firewall rule if created.
   - DB migration is forward-only by default; do NOT downgrade/drop. If DB migration causes critical issue, STOP and report restore options instead of destructive rollback.
11. Publish report to all three project-specific paths.

ALLOWED CHANGES:
- Existing DEV/TEST Azure resources ONLY in `rg-iap-demo`:
  - Azure SQL schema migration + synthetic seed for RAG tables in DB `InsuranceAIPlatform`;
  - temporary SQL firewall rule for current client IP only, removed at end;
  - update existing `iap-demo-api` Container App revision/image/env;
  - update existing `iap-demo-swa` deployment/config to backend mode;
  - read Azure SQL/KV/MI settings without exposing secret values.
- GHCR push of backend image for this feature branch.
- Build artifacts/images needed for dev deploy.
- No source code changes unless a tiny deploy config fix is absolutely required; if source/config changes are required, commit locally and report before/after. Prefer no source changes.

STRICT BOUNDARIES:
NO main push. NO merge. NO production. NO new Azure resources except one temporary SQL firewall rule if required and removed. NO Qdrant Azure deployment. NO Ollama Azure deployment. NO paid provider. NO OpenAI/DeepSeek/Azure OpenAI key. NO secrets printed or committed. NO real PII. NO generating new EF migration. NO dropping/destroying DB objects/data. NO force push. NO deleting resources. NO unrelated projects. NO fake migration/deploy/smoke status. NO pretending smoke passed if endpoints/UI fail. If Azure/GitHub/SQL auth requires a manual login, ask Slava to complete the auth flow manually; never ask for passwords.

STOP / ROLLBACK CONDITIONS:
- Target resource group is not exactly `rg-iap-demo` or resource names mismatch.
- SQL DB is not the expected dev/test `InsuranceAIPlatform` DB.
- SQL access requires a pasted password/secret.
- Data appears production-like/real PII.
- Migration would require generating a new migration or destructive DB change.
- Backend cannot build.
- GHCR push/auth blocked.
- Backend health fails after deploy.
- RAG endpoints remain 404 or return 500 after migration+deploy.
- `/rag/ask` returns non-claim-scoped citations or unsafe final payout/fraud wording.
- Frontend cannot reach backend due to CORS/config and cannot be fixed safely within deploy config.
- Azure cost/resource creation would exceed existing dev/test scope.
- Any destructive operation is required.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-06-insuranceai-azure-sql-rag-migrate-seed-redeploy-mega-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | ROLLED_BACK | BLOCKED | FAILED
TASK_TYPE: project-azure-sql-migrate-seed-deploy-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_SQL_RAG_MIGRATE_SEED_AND_REDEPLOY_MEGA_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Starting Repo State, Azure Target Verification, Prechecks, SQL Access Method, Pre-Migration DB State, Migration/Seed Actions, Post-Migration DB Proof, Backend Build/Image/Deploy, Backend Env Settings, Frontend Deploy/Config, Azure Smoke Matrix, UI Smoke Result, RAG Fallback Proof, Citation/Leakage Proof, Console/Network Findings, Rollback/Cleanup State, Files Changed, Commands Run, Boundaries Honored, Cost/Risk Notes, Blockers/Failures, Slava Manual Azure Checklist, Next Safe Step.

STOP after report. Do not continue to Azure Qdrant/Ollama, paid providers, main merge, production, or unrelated work.