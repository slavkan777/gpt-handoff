REQUEST_ID: REQ-2026-06-06-insuranceai-azure-dev-deploy-rag-fallback-mega-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-azure-dev-deploy-and-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_DEV_DEPLOY_RAG_FALLBACK_MEGA_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/azure-dev-deploy-rag-fallback-mega-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/azure-dev-deploy-rag-fallback-mega-v0.1/report.md

OPERATOR NOTE:
This is ONE combined Azure dev/test deploy mega-gate. Avoid endless micro-prompts. Execute the complete safe dev deploy path if possible, then smoke-test and report. Slava should only be interrupted for auth/UAC/manual browser confirmations if a tool requires it. Do not ask for passwords/secrets.

CONTEXT FROM ACCEPTED PREFLIGHT:
- Feature branch `rag/local-foundation-mega-v0.1` is pushed to origin at expected HEAD `4067591`.
- main remains untouched.
- Existing Azure dev/test resource group: `rg-iap-demo`.
- Existing Static Web App: `iap-demo-swa`, currently frontend mock mode.
- Existing backend Container App: `iap-demo-api`, currently live but old image; `/health` and `/api/claims` work; `/api/.../rag/infrastructure` returns 404 because image predates RAG.
- Existing Azure SQL DB and Key Vault/Managed Identity already exist.
- No Qdrant or Ollama exists in Azure.
- Recommended Option A: cheapest dev demo using existing infra, RAG fallback mode only: `Rag__QdrantEnabled=false`, `Rag__LocalLlamaEnabled=false`, Mock grounded generator, in-memory-hash retrieval.

GOAL:
Deploy the already-accepted feature branch to the existing Azure DEV/TEST environment using the cheapest safe architecture: existing Static Web App + existing backend Container App + existing Azure SQL/KV/MI, with RAG fallback mode only. Then smoke-test from Azure URL as a user would and publish a complete report. No production, no main, no paid provider, no Qdrant/Ollama Azure deployment in this gate.

DONE STATE:
1. Verify routing lock, current repo, branch, HEAD, remote state, and clean/expected tree.
2. Reconfirm target Azure resources read-only before changes:
   - subscription logged in;
   - resource group `rg-iap-demo`;
   - static web app `iap-demo-swa`;
   - container app `iap-demo-api`;
   - previous revision/image and current FQDN;
   - rollback target/current active revision.
3. Re-run lightweight pre-deploy checks:
   - no unrelated local changes;
   - no secrets in changed files/configs;
   - backend build/test or targeted build if full suite is too expensive;
   - frontend build if frontend will be deployed;
   - no new EF/schema migration in this deploy step.
4. Build and deploy backend from `rag/local-foundation-mega-v0.1` to existing `iap-demo-api` ONLY as a new Container App revision/image, using the existing deployment mechanism/registry/workflow/scripts if present. If registry/workflow auth is missing, stop with exact blocker. Do not create new Azure resources.
5. Configure backend env/app settings for DEV fallback mode only:
   - `Rag__QdrantEnabled=false`;
   - `Rag__LocalLlamaEnabled=false`;
   - preserve existing SQL/KV/MI settings;
   - no API keys;
   - no paid providers.
6. Deploy/update frontend to existing `iap-demo-swa` so it calls the backend Container App, not mock-only mode. If Vite env vars are build-time, rebuild with:
   - `VITE_INSURANCE_API_MODE=backend`;
   - `VITE_INSURANCE_API_BASE_URL=<iap-demo-api FQDN>`.
   Use existing SWA deployment path if present. Do not create a new SWA.
7. Smoke test Azure end-to-end:
   - SWA URL loads: `https://kind-meadow-03cf73103.7.azurestaticapps.net/`;
   - frontend is backend mode, not mock-only;
   - backend `/health` 200;
   - `/api/claims` 200;
   - `/api/claims/CLM-1006/rag/infrastructure` no longer 404;
   - expected in fallback mode: vector backend/status honestly shows in-memory/fallback or qdrant disabled, not qdrant/live;
   - `/api/claims/CLM-1006/rag/ask` returns 200, `providerMode=Mock`, claim-scoped citations only CLM-1006, advisory banner/wording;
   - UI CLM-1006 Evidence Intelligence renders answer/citations/confidence from Azure backend;
   - CLM-1061 insufficient evidence still cautious if available;
   - no fatal console/network errors.
8. Rollback behavior:
   - record previous backend revision/image before changes;
   - if smoke fails critically, rollback Container App traffic to previous revision and restore SWA/mock or previous frontend deployment if feasible;
   - report exactly what was rolled back or why rollback was not needed.
9. Publish report to all three project paths.

ALLOWED CHANGES:
- Existing dev/test Azure resources in `rg-iap-demo` only:
  - update `iap-demo-api` Container App revision/image/env;
  - update `iap-demo-swa` deployment/config to backend mode;
  - read Azure SQL/KV/MI settings without exposing secret values.
- Build artifacts/images required for this dev deploy.
- No source code changes unless a tiny deploy config fix is absolutely required; if source/config changes are required, commit locally and report before/after. Prefer no source changes.

STRICT BOUNDARIES:
NO main push. NO merge. NO production. NO new Azure resources unless explicitly unavoidable and then STOP for Slava approval. NO Qdrant Azure deployment. NO Ollama Azure deployment. NO paid provider. NO OpenAI/DeepSeek/Azure OpenAI key. NO secrets printed or committed. NO real PII. NO schema/EF migration. NO force push. NO deleting resources. NO unrelated projects. NO fake deploy status. NO pretending smoke passed if endpoints/UI fail. If Azure/GitHub auth is required, ask Slava to complete the auth flow manually; never ask for passwords.

STOP / ROLLBACK CONDITIONS:
- Target resource group is not `rg-iap-demo` or resource names mismatch.
- Deployment path would touch production or main.
- A secret would need to be pasted into chat or committed.
- Backend cannot build.
- Backend health fails after deploy.
- RAG endpoints remain 404 after deploy.
- `/rag/ask` returns 500 or non-claim-scoped citations.
- Frontend cannot reach backend due to CORS/config and cannot be fixed safely within deploy config.
- Azure cost/resource creation would exceed existing dev/test scope.
- Any destructive operation is required.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-06-insuranceai-azure-dev-deploy-rag-fallback-mega-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | ROLLED_BACK | BLOCKED | FAILED
TASK_TYPE: project-azure-dev-deploy-and-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_DEV_DEPLOY_RAG_FALLBACK_MEGA_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Starting Repo State, Azure Target Verification, Pre-Deploy Checks, Backend Build/Image/Deploy, Backend Env Settings, Frontend Deploy/Config, Azure Smoke Matrix, UI Smoke Result, RAG Fallback Proof, Citation/Leakage Proof, Console/Network Findings, Rollback State, Files Changed, Commands Run, Boundaries Honored, Cost/Risk Notes, Blockers/Failures, Slava Manual Azure Checklist, Next Safe Step.

STOP after report. Do not continue to Qdrant/Ollama Azure, paid providers, main merge, or unrelated work.