REQUEST_ID: REQ-2026-06-06-insuranceai-push-pr-azure-preflight-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-remote-branch-and-azure-preflight
PROJECT: InsuranceAIPlatform
GATE: PUSH_PR_AZURE_PREFLIGHT_V0.1
COMPLETED_BY: claude

# Feature-branch push + Azure dev/test preflight

One-line: feature branch `rag/local-foundation-mega-v0.1` pushed to origin (feature branch only — main untouched, no force, no deploy); the existing Azure dev/test stack inspected read-only; deployment options + a recommended next gate produced. No Azure resource was created/updated/deleted.

## Routing Lock Verification
- PROJECT InsuranceAIPlatform; SOURCE_REPO_REMOTE `slavkan777/InsuranceAIPlatform`; branch `rag/local-foundation-mega-v0.1`; HEAD `4067591`.
- Edits: NONE this gate (push + read-only inspection only). No unrelated project touched.

## Starting Repo State
- Branch `rag/local-foundation-mega-v0.1`, HEAD `4067591 feat(rag): add local Ollama reasoning provider`. Working tree clean. 25 ahead / 0 behind origin/main.
- `4067591` confirmed ancestor of HEAD (accepted Ollama commit present).

## Pre-Push Checks
- **Secret scan** across all 25 pushed commits (`git log origin/main..HEAD -p`): no real secret values. All matches were documentation about secret hygiene, the env-var NAME `DEEPSEEK_API_KEY` (name-only), sentinel test keys, or NEGATIVE safety-test assertions. No committed `.env`/`secrets.json`/`.pfx`/`.pem`/`.key` files.
- Connection string in `appsettings.Development.json` = local `(localdb)` with `Trusted_Connection=True` (no password). Safe.
- **EF/schema migration:** this gate's commit (`4067591`) added ZERO migrations. Migrations present in the branch are from PRIOR accepted gates (Claims/Documents/Approval/AuditCost/CustomersPolicies/AiAnalysis incl. `AddRagFoundation`), already part of the accepted local state.
- **Build:** clean (0 `error CS`) once the running dev API's file lock was released; matches the earlier `dotnet test` 181/181 on this exact commit. No unrelated files.
- `.github/workflows/azure-deploy-demo.yml` present in range (flagged as a potential PAT `workflow`-scope blocker — push succeeded, so the PAT had scope).

## Branch Push Result
- `git push -u origin rag/local-foundation-mega-v0.1` → **`* [new branch] rag/local-foundation-mega-v0.1 -> rag/local-foundation-mega-v0.1`** (non-force, upstream set).
- Verified remote refs: `refs/heads/rag/local-foundation-mega-v0.1 = 4067591` (== local HEAD); `refs/heads/main = 69e6731` (**unchanged — main NOT pushed**).

## PR Result
- **Not created — no working programmatic auth path.** `gh` CLI not installed; github MCP returned `Bad credentials`. Per gate boundaries, not inventing status.
- Create manually (one click): **https://github.com/slavkan777/InsuranceAIPlatform/pull/new/rag/local-foundation-mega-v0.1**
- Or once `gh` is installed + authed: `gh pr create --repo slavkan777/InsuranceAIPlatform --base main --head rag/local-foundation-mega-v0.1 --draft --title "Local RAG: Qdrant + Ollama (dev/test)" --body-file <notes>`

## Azure URL Inspection (read-only)
- `https://kind-meadow-03cf73103.7.azurestaticapps.net/` → HTTP 200. Title "InsuranceAIPlatform · Auto Claim AI Workbench".
- Frontend JS (`/assets/index-*.js`) default `ApiMode:"mock"` (also `mock-fallback` present) → the deployed SWA runs **frontend-only, mock mode**. The bundle references a `*.westeurope.azurecontainerapps.io` host (the existing Container App backend base URL) but is not serving backend data by default.

## Azure CLI Read-Only Discovery
- `az` 2.77.0, **logged in** (subscription "Azure subscription 1", state Enabled; subscription/tenant GUIDs intentionally omitted from this public report).
- Single resource group **`rg-iap-demo`** (westeurope). READ-ONLY `az resource list` / `staticwebapp list` / `webapp list` / `containerapp list` / `sql server list` — NO create/update/delete.

## Current Azure Topology (already deployed — richer than frontend-only)
| Resource | Type | State / note |
|---|---|---|
| `iap-demo-swa` | Static Web App (Free) | frontend, mock mode |
| `iap-demo-api` | **Container App** | **LIVE** backend — `/health` 200, `/api/claims` 200, but **`/api/.../rag/infrastructure` 404** (image predates RAG) |
| `iap-demo-cae` | Container Apps Environment | hosts the API |
| `iap-sql-r2-…` | Azure SQL Server (germanywestcentral) | DBs `master` + **`InsuranceAIPlatform`** |
| `iapdemokv…` | Key Vault | secrets store |
| `iap-demo-api-mi` | User-assigned Managed Identity | API → KV/SQL |
| `iap-demo-appi` / `iap-demo-law` | App Insights / Log Analytics | observability |
| `iapdemost…` | Storage account | SWA/functions backing |
- **No Qdrant, no Ollama in Azure.** App Service list empty (backend is Container Apps, not App Service).
- **Key gap:** the live Container App serves the older API WITHOUT the RAG endpoints. The pushed feature branch must be built into a new image and deployed to bring RAG to Azure.

## Deployment Options
**A — Cheapest dev demo (RECOMMENDED).** Rebuild `iap-demo-api` Container App image from `rag/local-foundation-mega-v0.1`; run RAG in **fallback mode** (`Rag__QdrantEnabled=false` → in-memory-hash retrieval; Mock generator → grounded deterministic answers). Flip `iap-demo-swa` to backend mode pointing at the Container App FQDN. Reuses ALL existing infra (Container App + Azure SQL + KV + MI + App Insights). **No new paid service. No Qdrant/Ollama cost.** RAG answers = deterministic mock (grounded, claim-scoped, advisory) — same guardrails proven locally.

**B — Dev live vector + (optional) local LLM.** Add a small **Qdrant Container App** to the existing environment (`Rag__QdrantEnabled=true`) for real vector retrieval. Ollama in Azure is **not recommended now**: Container Apps has no GPU; a CPU-only 1.5B model is slow and memory-hungry (cost/latency risk). If live generation is wanted, prefer Option C over CPU-Ollama.

**C — Production-like later (separate owner approval + paid).** Plug Azure OpenAI (or an OpenAI-compatible managed provider) into the SAME `IGroundedAnswerGenerator` seam (a new provider class, config-selected). Key in Key Vault via Managed Identity. Requires a paid provider + explicit owner approval — out of scope until a dedicated gate.

## Recommended Dev/Test Architecture
**Option A.** Next gate = "build + deploy feature-branch image to the existing `iap-demo-api` Container App (revision), RAG in fallback mode, flip SWA to backend, verify `/rag/infrastructure` + one `/rag/ask` live, then rollback-ready." Cheapest, reuses everything, brings RAG to Azure honestly (mock generation labelled as such). Qdrant (Option B) and managed LLM (Option C) are follow-on gates.

## Required Env Vars / Secrets (for Option A)
- App config on the Container App: `Rag__LocalLlamaEnabled=false`, `Rag__QdrantEnabled=false` (fallback mode), `ASPNETCORE_ENVIRONMENT=Production` (or Development for swagger).
- SQL connection string: via **Key Vault + Managed Identity** (already provisioned `iapdemokv…` + `iap-demo-api-mi`) — never in repo/config.
- Frontend SWA: `VITE_INSURANCE_API_MODE=backend`, `VITE_INSURANCE_API_BASE_URL=https://iap-demo-api.bluehill-…westeurope.azurecontainerapps.io`.
- No DeepSeek/OpenAI key needed for Option A (Mock generator).

## Cost / Risk Notes
- Option A: ~zero incremental (reuses Free SWA + existing Container App + existing SQL). Main standing cost = Azure SQL + Container App min replicas. Set Container App **min-replicas=0** (scale-to-zero) to cut idle cost.
- Option B (+Qdrant CA): +1 small Container App (scale-to-zero capable). Modest.
- Risk: deploying a new image to the live `iap-demo-api` replaces the current revision — mitigate with Container Apps **revision-based rollback** (keep prior revision, traffic-split or instant revert).

## Rollback / Stop-Cost Plan
- Container Apps keeps revisions: revert by shifting 100% traffic to the previous revision (`az containerapp revision` — a later, separately-authorized deploy gate).
- Stop-cost: Container App min-replicas=0; pause/scale SQL to lowest tier or stop when idle; SWA Free has no cost.
- Git rollback: feature branch is isolated; `main` untouched. Nothing to revert in the repo.

## Files Changed
- None (this gate = push + read-only inspection). The pushed content is the already-accepted feature branch (HEAD `4067591`).

## Commands Run
- `git rev-list/log/diff` (state + secret scan), `git push -u origin rag/local-foundation-mega-v0.1`, `git ls-remote` (verify).
- `dotnet build` (clean after API lock released).
- `curl` on the SWA URL + the Container App FQDN (read-only probes).
- `az account/resource/staticwebapp/webapp/containerapp/sql ... list/show` (READ-ONLY).

## Boundaries Honored
NO main push (main stayed `69e6731`) · NO merge · NO force-push · NO Azure deploy · NO Azure create/update/delete (read-only only) · NO production change · NO paid provider · NO OpenAI/DeepSeek key · NO secrets in chat/files · NO real PII · NO schema/EF migration added · NO unrelated projects · did NOT fake PR/login/deploy status.

## Blockers
- PR auto-creation blocked (no `gh`, github MCP `Bad credentials`) → manual URL/command provided above. Push itself succeeded.

## Next Deploy Gate Proposal
`AZURE_DEV_DEPLOY_RAG_FALLBACK_V0.1` (separately authorized): build the feature-branch image → deploy as a NEW Container App revision of `iap-demo-api` (RAG fallback mode) → flip SWA to backend → verify `/rag/infrastructure` (vector=in-memory-hash, localReasoning=disabled) + one live `/rag/ask` (providerMode=Mock, claim-scoped) → keep prior revision for instant rollback. Explicit STOP/rollback conditions; one owner manual checkpoint before traffic shift.

STOP — holding after report. No deploy, no merge, no Azure resource change, no unrelated work.
