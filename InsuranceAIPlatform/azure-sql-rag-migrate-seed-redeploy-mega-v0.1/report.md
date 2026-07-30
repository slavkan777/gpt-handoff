REQUEST_ID: REQ-2026-06-06-insuranceai-azure-sql-rag-migrate-seed-redeploy-mega-v0-1
STATUS: BLOCKED
TASK_TYPE: project-azure-sql-migrate-seed-deploy-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_SQL_RAG_MIGRATE_SEED_AND_REDEPLOY_MEGA_V0.1
COMPLETED_BY: claude

# Azure SQL RAG migrate+seed+redeploy — BLOCKED on SQL access (no Azure mutation performed)

One-line: the migration cannot start because **no allowed, secret-safe credential path grants me DDL access to the dev/test Azure SQL DB**. The backend authenticates to SQL via **Managed Identity** (usable only by the app's MI, not from my workstation), the SQL server has **no Entra admin**, my Azure identity has **no Key Vault access** and **no SQL DB login**. Per the gate's own STOP condition ("SQL access requires a pasted password/secret → STOP BLOCKED"), I stopped **before any change** — all Azure calls this gate were READ-ONLY; **no firewall rule was added, no migration run, no deploy**. The blocker is a one-time access grant only you can authorize.

## Routing Lock Verification
- PROJECT InsuranceAIPlatform; remote `slavkan777/InsuranceAIPlatform`; branch `rag/local-foundation-mega-v0.1`; HEAD `4067591` (pushed). Tree clean. No source change.

## Starting Repo State
- HEAD `4067591`; `origin/rag/local-foundation-mega-v0.1 = 4067591`; `origin/main = 69e6731` (untouched).

## Azure Target Verification (READ-ONLY)
- Subscription logged in as `info.msdn.com@gmail.com` (my Azure identity). RG `rg-iap-demo` ✓. SQL server `iap-sql-r2-6c7g465.database.windows.net`, DB `InsuranceAIPlatform` ✓ (state Ready). SWA `iap-demo-swa` (mock). Container App `iap-demo-api`, active revision `iap-demo-api--0000002`, image `…:14d5c81-cors` (rollback target). Public SWA URL loads (200).

## SQL Access Method — the blocker (all evidence READ-ONLY, no secret value printed)
1. **Backend uses Managed Identity for SQL.** The Container App env has `AZURE_CLIENT_ID` (= user-assigned MI `iap-demo-api-mi`) and `ConnectionStrings__InsuranceAIPlatform` → Container App secret `sql-connection`. I retrieved that secret's value and classified it WITHOUT printing: length 173, **no `Password=`, no `Authentication=`, no `Active Directory` keyword** → it is a passwordless, token-(MI)-authenticated connection string. The MI token can only be acquired by the MI identity inside the app, not from my workstation.
2. **No Entra admin on the SQL server** (`az sql server ad-admin list` → `[]`) → I cannot connect via Active Directory auth as my own identity, and there is no AAD principal with DDL rights I can use.
3. **No Key Vault access for me** (`az keyvault secret list` on `iapdemokv6c7g465vrcfi4` → denied) → I cannot retrieve any alternate SQL admin connection string/password.
4. **No SQL-auth password available** anywhere I can read; the gate forbids you pasting a secret into chat.
5. Firewall currently allows only `AllowAllAzureServices` (0.0.0.0); my client IP `176.36.240.192` is not allowed (a temp rule was permitted, but it is moot without working credentials, so I did NOT create one).

Net: every credential path the gate authorizes — Managed Identity / Key Vault / configured connection string / Azure CLI Entra token — is unavailable to me for DDL. Tooling itself is ready (`dotnet-ef` + `sqlcmd` installed, `DbMigrator` exists).

## Prechecks (done where possible)
- Tree clean; HEAD `4067591`; backend 181/181 on this commit (this session); the migration to apply is the EXISTING `20260604161120_AddRagFoundation` (NOT a newly-generated one — confirmed).

## Pre-Migration DB State
- Could not be inspected (no DB access). Strong prior evidence (preflight gate) that RAG tables are absent: live image predates `AddRagFoundation`; API does not migrate at startup.

## Migration/Seed Actions
- NONE. No connection established; `DbMigrator`/`dotnet ef` NOT run against Azure SQL.

## Post-Migration DB Proof / Backend Deploy / Frontend Deploy / Smoke / RAG Fallback / Citation proofs
- NONE — all gated behind the SQL-access blocker. No Container App revision created, no image pushed, no SWA change.

## Rollback/Cleanup State
- Nothing to roll back (no mutation). No temporary firewall rule created (so none to remove). Live demo + backend untouched. Rollback target recorded for the future deploy: revision `iap-demo-api--0000002`.

## Files Changed
- None.

## Commands Run (all READ-ONLY for Azure)
- `az account/sql server (show, ad-admin list, firewall-rule list)/keyvault secret (list)/containerapp (show, secret show)` — read-only; secret value classified, never printed.
- `git` state; tooling version checks; public-IP lookup.

## Boundaries Honored
NO Azure mutation (read-only only) · NO firewall rule created · NO migration run · NO deploy · NO new resources · NO main/merge/prod · NO Qdrant/Ollama Azure · NO paid provider · NO secret printed/committed (SQL secret retrieved for classification only, never displayed) · NO new EF migration · NO destructive op · NO fake status.

## Cost/Risk Notes
- Zero cost (read-only). No security posture changed (I deliberately did NOT set an Entra admin / grant myself access — that is your decision).

## Blockers/Failures
- **BLOCKER (access):** no secret-safe DDL credential path to Azure SQL `InsuranceAIPlatform` from my workstation (MI-only app auth + no Entra admin + no KV access + no DB login). This is a one-time access/ownership decision, not a technical defect.

## Slava Manual Azure Checklist — pick ONE to unblock (no secret in chat needed)
**Option 1 (recommended — you run the migration; you already own the DB).** From your machine/portal, apply the existing migration + seed:
```
# connection string with YOUR admin access (portal Query editor, or AAD as server admin, or SQL admin login)
$env:ConnectionStrings__InsuranceAIPlatform = "<your Azure SQL admin connection string>"
dotnet run --project server/InsuranceAIPlatform.DbMigrator
```
(Idempotent: applies `AddRagFoundation` + runs all seeders incl. `RagSeeder`; no drops.) Then fire the next «промт» and I complete the build → GHCR push → `az containerapp update` → smoke.

**Option 2 (grant me access, then I do everything next prompt).** Either:
- set an Entra admin on the SQL server and add my identity as a DDL user:
  `az sql server ad-admin create -g rg-iap-demo -s iap-sql-r2-6c7g465 --display-name slava --object-id <your-aad-object-id>` (then create a contained DB user for `info.msdn.com@gmail.com` with `db_ddladmin`), **or**
- grant my identity Key Vault data-plane read if a SQL admin connection string is stored there:
  `az role assignment create --assignee info.msdn.com@gmail.com --role "Key Vault Secrets User" --scope <kv resource id>`.
Either way I add a temp client-IP firewall rule, migrate+seed, deploy, smoke, and remove the rule.

## Next Safe Step
After SQL is migrated/seeded (Option 1 or 2), the rest is low-risk and ready: `docker build -f server/Dockerfile server` → `docker push ghcr.io/slavkan777/insuranceai-api:rag-fallback-<sha>` → `az containerapp update -n iap-demo-api -g rg-iap-demo --image … --set-env-vars Rag__QdrantEnabled=false Rag__LocalLlamaEnabled=false` → smoke `/rag/infrastructure` (in-memory-hash, Llama disabled) + `/rag/ask` (Mock, claim-scoped) → SWA backend-mode → rollback to `iap-demo-api--0000002` if needed.

STOP — holding after report. No Azure mutation, no migration, no deploy, no security/access change, no main, no unrelated work.
