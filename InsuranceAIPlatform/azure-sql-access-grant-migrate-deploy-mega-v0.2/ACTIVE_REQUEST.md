REQUEST_ID: REQ-2026-06-06-insuranceai-azure-sql-access-grant-migrate-deploy-mega-v0-2
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-azure-sql-access-migrate-seed-deploy-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_SQL_ACCESS_GRANT_MIGRATE_SEED_DEPLOY_SMOKE_MEGA_V0.2
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/azure-sql-access-grant-migrate-deploy-mega-v0.2/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/azure-sql-access-grant-migrate-deploy-mega-v0.2/report.md

OPERATOR NOTE:
This is ONE combined unblock + migrate + deploy + smoke mega-gate. The previous gates spent too much time discovering blockers. Do not stop at a reversible access blocker if the next safe owner-approved action is clear and within this prompt. Slava wants Claude to execute the whole technical path quickly but safely. Slava should only be interrupted for manual browser/device login, UAC, or an explicit owner approval if a high-impact access change cannot be done safely. Do not ask for passwords/secrets.

CONTEXT FROM ACCEPTED REPORTS:
- Feature branch `rag/local-foundation-mega-v0.1` is pushed to origin at HEAD `4067591`.
- main remains untouched.
- Local state accepted: RAG pipeline + local Qdrant + local Ollama provider + mock fallback; 181 tests passed; 30 scenario sweep 27/30 PASS, 0 leakage.
- Azure preflight accepted: RG `rg-iap-demo`, SWA `iap-demo-swa`, backend Container App `iap-demo-api`, Azure SQL DB `InsuranceAIPlatform`, Key Vault and Managed Identity already exist.
- Previous deploy gate was blocked because Azure SQL likely lacks RAG tables from `AddRagFoundation`.
- Previous migrate+deploy gate was blocked because no secret-safe DDL credential path existed from the workstation: MI-only app auth, no SQL Entra admin, no KV access for the current user, no DB login.
- This V0.2 gate explicitly authorizes safe dev/test SQL access setup needed to continue, without secrets in chat.
- Existing Azure public URL: https://kind-meadow-03cf73103.7.azurestaticapps.net/

GOAL:
Unblock SQL access in the existing dev/test Azure environment without secrets in chat, apply existing RAG migration+seed, deploy the already-accepted feature branch to existing Azure dev/test backend/frontend in fallback mode, smoke-test end-to-end, cleanup temporary access where appropriate, and publish a complete evidence report.

DONE STATE:
1. Verify routing lock, repo path, branch, HEAD `4067591`, remote, clean/expected tree.
2. Verify Azure target before mutation:
   - current logged-in Azure identity;
   - subscription enabled;
   - resource group exactly `rg-iap-demo`;
   - SQL server `iap-sql-r2-6c7g465` and DB `InsuranceAIPlatform`;
   - Static Web App `iap-demo-swa`;
   - Container App `iap-demo-api`;
   - active revision/image rollback target, expected `iap-demo-api--0000002` / `14d5c81-cors`;
   - public SWA URL loads.
3. Lightweight prechecks:
   - no unrelated local changes;
   - no secrets in changed files/configs;
   - backend build/test or targeted build;
   - frontend build if frontend is redeployed;
   - confirm migration to apply is existing accepted `AddRagFoundation`; do NOT generate a new migration.

ACCESS UNBLOCK AUTHORIZATION:
4. Prefer Entra/Azure AD SQL access without passwords:
   - If SQL server has no Entra admin, set the current logged-in owner/admin identity as SQL Entra admin ONLY for this dev/test server.
   - If setting Entra admin requires object ID discovery, use Azure CLI read commands to determine the correct signed-in user/service principal object ID. Do not print tenant/subscription GUIDs unnecessarily.
   - If Azure CLI prompts for login/device code, ask Slava to complete browser/device login manually; do not ask for password.
   - If Entra admin cannot be set because current identity lacks permission, STOP BLOCKED with exact missing RBAC/permission.
5. Add ONE temporary SQL firewall rule for the current client IP only if needed for migration access:
   - name: `tmp-slava-rag-migrate-<timestamp>`;
   - scope: exact current public IP only;
   - remove it before final report if technically possible.
6. Do NOT request or print SQL password/connection string. Do NOT paste secrets into chat/files.
7. Do NOT grant broad Key Vault access unless Entra SQL path fails and KV access is clearly safer/needed. If KV access is used, grant least privilege and do not print secret values. Prefer not to touch KV.

SQL MIGRATION + SEED:
8. Connect to Azure SQL using Entra/AAD token path.
9. Inspect `__EFMigrationsHistory` and RAG table presence.
10. If RAG schema already exists and seed exists, do not rerun destructive work; verify and proceed to deploy.
11. If missing, apply ONLY existing accepted migrations up to and including `20260604161120_AddRagFoundation` using repository's existing `DbMigrator` or `dotnet ef database update` path.
12. Run existing idempotent seeders including `RagSeeder` for synthetic/demo data only.
13. Do NOT generate a new EF migration. Do NOT drop/destroy DB objects/data. Do NOT touch production-like/real PII.
14. Verify post-migration state:
   - migration history includes RAG migration;
   - RAG tables exist using actual table names;
   - synthetic/demo CLM-1006 and CLM-1061 RAG data exists;
   - record counts are reported without sensitive values.

BACKEND DEPLOY:
15. Build Docker image from feature branch HEAD `4067591` using existing `server/Dockerfile` and server context.
16. Tag clearly: `ghcr.io/slavkan777/insuranceai-api:rag-fallback-4067591-<timestamp>`.
17. Push to GHCR only if cached auth works. If auth blocks, STOP/PARTIAL with exact command needed; do not ask for token in chat.
18. Update existing Container App `iap-demo-api` ONLY as a new revision/image. No new Container App/resource.
19. Set fallback-mode env vars:
   - `Rag__QdrantEnabled=false`
   - `Rag__LocalLlamaEnabled=false`
20. Preserve existing SQL/KV/MI settings. Do not print secret values.

FRONTEND DEPLOY/CONFIG:
21. Update existing `iap-demo-swa` only. Do not create a new SWA.
22. Build/deploy frontend in backend mode if Vite env vars are build-time:
   - `VITE_INSURANCE_API_MODE=backend`
   - `VITE_INSURANCE_API_BASE_URL=<iap-demo-api FQDN>`
23. Use existing SWA deployment path/token if already configured/cached. If SWA auth/token is missing and would require a secret in chat, STOP/PARTIAL after backend smoke and report exact blocker.

AZURE SMOKE MATRIX:
24. Backend `/health` returns 200.
25. Backend `/api/claims` returns 200.
26. Backend `/api/claims/CLM-1006/rag/infrastructure` returns 200, not 404/500.
27. Fallback labels are honest: vector runtime disabled/skipped/in-memory-hash, not fake qdrant/live; local Llama disabled, not fake Ollama.
28. Backend `/api/claims/CLM-1006/rag/ask` returns 200, `providerMode=Mock`, claim-scoped citations only CLM-1006, advisory wording present, sane confidence.
29. Backend `/api/claims/CLM-1061/rag/ask` returns cautious insufficient-evidence behavior if endpoint/data available.
30. Public SWA loads.
31. Frontend is backend mode, not mock-only.
32. UI CLM-1006 Evidence Intelligence renders answer/citations/confidence from Azure backend.
33. No fatal console/network errors.

ROLLBACK/CLEANUP:
34. Record previous backend revision/image before deploy.
35. If backend smoke fails critically after update, rollback traffic/revision to previous active revision.
36. If frontend backend-mode deploy fails or breaks UI, restore previous SWA/mock deployment if feasible, or report exact rollback limitation.
37. Remove temporary SQL firewall rule if created.
38. DB migration is forward-only; do NOT downgrade/drop. If DB issue occurs, stop and report restore options instead of destructive rollback.
39. Do not remove SQL Entra admin at the end unless it was clearly created as a temporary test-only admin and removing it will not lock out future maintenance. If left in place, report it explicitly as an owner-approved dev/test access posture change.

ALLOWED CHANGES:
- Existing DEV/TEST Azure resources ONLY in `rg-iap-demo`:
  - set SQL Entra admin on existing dev/test SQL server if missing and needed;
  - add/remove one temporary SQL firewall rule for current client IP;
  - Azure SQL schema migration + synthetic seed for RAG tables in DB `InsuranceAIPlatform`;
  - update existing `iap-demo-api` Container App revision/image/env;
  - update existing `iap-demo-swa` deployment/config to backend mode;
  - read Azure SQL/KV/MI settings without exposing secret values.
- GHCR push of backend image for this feature branch.
- Build artifacts/images needed for dev deploy.
- No source code changes unless a tiny deploy config fix is absolutely required; if source/config changes are required, commit locally and report before/after. Prefer no source changes.

STRICT BOUNDARIES:
NO main push. NO merge. NO production. NO new Azure resources except SQL Entra admin setting on the existing dev/test SQL server and one temporary SQL firewall rule if needed. NO Qdrant Azure deployment. NO Ollama Azure deployment. NO paid provider. NO OpenAI/DeepSeek/Azure OpenAI key. NO secrets printed or committed. NO real PII. NO generating new EF migration. NO dropping/destroying DB objects/data. NO force push. NO deleting business resources. NO unrelated projects. NO fake migration/deploy/smoke status. NO pretending smoke passed if endpoints/UI fail. If Azure/GitHub/SQL auth requires manual login, ask Slava to complete the login flow manually; never ask for passwords.

STOP / ROLLBACK CONDITIONS:
- Target RG is not exactly `rg-iap-demo` or resource names mismatch.
- SQL DB is not expected dev/test `InsuranceAIPlatform` DB.
- Current identity lacks permission to set Entra admin and no other secret-safe path exists.
- SQL access requires a pasted password/secret.
- Data appears production-like/real PII.
- Migration would require generating a new migration or destructive DB change.
- Backend cannot build.
- GHCR push/auth blocked.
- Backend health fails after deploy and rollback fails.
- RAG endpoints remain 404 or return 500 after migration+deploy.
- `/rag/ask` returns non-claim-scoped citations or unsafe final payout/fraud wording.
- Frontend cannot reach backend due to CORS/config and cannot be fixed safely within deploy config.
- Azure cost/resource creation would exceed existing dev/test scope.
- Any destructive operation is required.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-06-insuranceai-azure-sql-access-grant-migrate-deploy-mega-v0-2
STATUS: READY_FOR_AUDIT | PARTIAL | ROLLED_BACK | BLOCKED | FAILED
TASK_TYPE: project-azure-sql-access-migrate-seed-deploy-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_SQL_ACCESS_GRANT_MIGRATE_SEED_DEPLOY_SMOKE_MEGA_V0.2
COMPLETED_BY: claude

Sections: Routing Lock Verification, Starting Repo State, Azure Target Verification, Prechecks, Access Unblock Actions, SQL Access Method, Pre-Migration DB State, Migration/Seed Actions, Post-Migration DB Proof, Backend Build/Image/Deploy, Backend Env Settings, Frontend Deploy/Config, Azure Smoke Matrix, UI Smoke Result, RAG Fallback Proof, Citation/Leakage Proof, Console/Network Findings, Rollback/Cleanup State, Files Changed, Commands Run, Boundaries Honored, Security/Access Changes Left In Place, Cost/Risk Notes, Blockers/Failures, Slava Manual Azure Checklist, Next Safe Step.

STOP after report. Do not continue to Azure Qdrant/Ollama, paid providers, main merge, production, or unrelated work.