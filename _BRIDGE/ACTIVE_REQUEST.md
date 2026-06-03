REQUEST_ID: REQ-2026-06-03-azure-sql-cost-audit-readonly
STATE: READY_FOR_CLAUDE
TASK_TYPE: audit
PROJECT: InsuranceAIPlatform
TARGET_REPORT_PATH: _BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/azure-sql-cost-audit-readonly-v0.1/report.md
AIKB_UPDATE_REQUIRED: after-approval
CREATED: 2026-06-03T00:00:00+03:00
CREATED_BY: architect-gpt
GATE: external-access-audit / azure-readonly-cost-audit

# AZURE SQL COST AUDIT — READ-ONLY REQUEST

## CURRENT STATE
Slava manually inspected Azure Portal for InsuranceAIPlatform resources.
Observed screenshots show resource group `rg-iap-demo` contains Azure resources including:
- Container App `iap-demo-api`
- Managed Identity `iap-demo-api-mi`
- Application Insights `iap-demo-api`
- Container Apps Environment `iap-demo-cae`
- Log Analytics workspace `iap-demo-law`
- Static Web App `iap-demo-swa`
- SQL server `iap-sql-r2-6c7q465`
- Key Vault `iapdemokv...`
- Storage account `iapdemost...`
- SQL database `InsuranceAIPlatform (...)`

Azure Cost Analysis screenshot shows actual cost around `$1.03`, with almost all cost attributed to **SQL Database**. Storage and Log Analytics appear near zero.

Important: durable AIKB may be stale about Azure because older AIKB says no Azure resources yet. For this task, trust actual Azure portal/CLI evidence over stale AIKB, but do NOT update AIKB. Report the discrepancy only.

## CURRENT GATE
`AZURE_SQL_COST_AUDIT_READONLY_V0.1`

This is a read-only Azure audit / cost investigation gate.
It is NOT a deploy gate.
It is NOT a SQL resize/update gate.
It is NOT a cleanup/delete gate.
It is NOT an implementation gate.

## WHY THIS TASK EXISTS
The SQL Database appears to be consuming money. Slava wants to understand exactly why SQL is charging and whether it is configured for low-cost behavior: Serverless, auto-pause, minimal vCores, no accidental constant wakeups.

## GOAL
Inspect Azure read-only state and produce a sanitized report answering:
1. Which Azure SQL server and database exist for InsuranceAIPlatform?
2. Is the SQL database Serverless or Provisioned?
3. Is auto-pause enabled or disabled?
4. What are min/max vCores / SKU / service tier / compute tier / storage settings?
5. Is the database currently Online, Paused, Resuming, or another status?
6. What Azure service/resource is responsible for the current cost?
7. Is there evidence that something keeps SQL awake?
8. What are the safest cost-reduction options, ranked by risk?
9. What is the next safe manual action for Slava?

## DONE STATE
This task is complete only when Claude publishes a sanitized report to:
- `_BRIDGE/LATEST_REPORT.md`
- and, if possible, `InsuranceAIPlatform/azure-sql-cost-audit-readonly-v0.1/report.md`

The report must include:
- `REQUEST_ID` exactly matching `REQ-2026-06-03-azure-sql-cost-audit-readonly`.
- Azure account/subscription identification at sanitized level only: subscription name/id may be partially masked if shown.
- Resource group inventory for `rg-iap-demo`.
- SQL server/database inventory.
- SQL Database compute/storage configuration evidence.
- Cost analysis evidence if CLI/API access allows it.
- Read-only commands run and their sanitized outputs.
- Clear conclusion: why SQL is costing money.
- Clear options: keep serverless + auto-pause, reduce settings, stop app wakeups, export/delete DB, or defer action.
- Explicit confirmation that no Azure resources were changed.
- Explicit confirmation that no secrets/connection strings were printed.

## RISK PROFILE
RED/YELLOW boundary due to Azure account and billing impact.
Allowed: read-only inspection only.
Forbidden: any mutation, resize, delete, deploy, secret retrieval, or paid-service enablement.

## READ FIRST
Use local/project context if available, but do not rely on stale AIKB for current Azure resource reality.
If local repo exists, you may read only docs/reports that mention Azure resource names, but do not edit files.

Suggested local read-only paths if present:
- `docs/architecture/azure/`
- `docs/reports/`
- `infra/`
- `deploy/`
- `server/`

Do not modify any source repository files.

## READ-ONLY AZURE SCOPE
You may use read-only Azure CLI / portal inspection if access is already authenticated and safe:
- `az account show`
- `az group show --name rg-iap-demo`
- `az resource list --resource-group rg-iap-demo --output table`
- `az sql server list --resource-group rg-iap-demo --output table`
- `az sql db list --resource-group rg-iap-demo --server <server-name> --output table`
- `az sql db show --resource-group rg-iap-demo --server <server-name> --name <db-name> --output json`
- `az monitor metrics list` for the SQL database resource id if available and read-only
- `az costmanagement query` / Cost Management read-only query if permission exists

If Azure CLI is not installed, not logged in, wrong tenant, missing permission, or requires interactive login/2FA:
- STOP the Azure CLI part;
- do not ask for passwords/secrets;
- report exact blocker;
- tell Slava the minimal manual action needed.

## WRITABLE SCOPE
Only gpt-handoff report files:
- `_BRIDGE/LATEST_REPORT.md`
- `InsuranceAIPlatform/azure-sql-cost-audit-readonly-v0.1/report.md`
- optionally `InsuranceAIPlatform/azure-sql-cost-audit-readonly-v0.1/summary.json`

No product repo writes.
No AIKB writes.
No Azure writes.

## FORBIDDEN SCOPE
Strictly forbidden:
- `az login` if it requires Slava credentials in chat.
- Asking for passwords, tokens, API keys, connection strings, or subscription secrets.
- Printing full connection strings.
- Reading Key Vault secret values.
- Changing SQL tier/compute/storage.
- Enabling/disabling auto-pause.
- Resizing SQL.
- Deleting SQL database or server.
- Stopping/deleting Container App.
- Creating/deleting/updating any Azure resource.
- Applying Bicep/Terraform.
- Running deployment commands.
- Running migrations.
- Touching source repo code.
- Commit/push in source repo.
- Force push.
- Cleanup/delete/archive.

## PRESERVE
- Azure account safety.
- Billing safety.
- Secrets safety.
- Read-only evidence.
- Slava final decision authority.
- Gate separation.

## INVESTIGATION MAP

### Phase 1 — Access and identity check
1. Check whether Azure CLI is available.
2. Run `az account show` only if already authenticated.
3. Report sanitized subscription/tenant context.
4. If not authenticated or wrong account, stop and report.

### Phase 2 — Resource inventory
1. Inspect `rg-iap-demo`.
2. List resources by type/name/location.
3. Identify SQL server and SQL database names exactly.
4. Identify whether any other expensive services exist unexpectedly: AKS, ACR, App Service, AI Search, OpenAI, VMs, always-on resources.

### Phase 3 — SQL configuration audit
For the SQL database:
1. Inspect SKU / service tier.
2. Inspect compute tier: Serverless vs Provisioned.
3. Inspect `autoPauseDelay`.
4. Inspect `minCapacity`, max vCores / capacity if available.
5. Inspect status: Online / Paused / Resuming.
6. Inspect storage size/config if available.
7. Determine whether config matches low-cost target.

### Phase 4 — Cost source audit
1. Use Cost Management read-only query if permission exists.
2. Group by Resource / ServiceName if possible.
3. Confirm whether SQL Database is the actual cost source.
4. Report exact limitations if cost API is unavailable.

### Phase 5 — Wake-up / activity hypothesis
Investigate without changing anything:
1. Look for current SQL status and recent metrics if accessible.
2. Check metrics that may indicate activity: CPU, sessions, connections, app CPU billed / allocated storage if available.
3. Infer possible wake-up sources:
   - live API hitting DB;
   - health checks touching SQL;
   - Query Editor / SSMS / Azure Data Studio open sessions;
   - manual portal operations;
   - background app calls;
   - missing auto-pause / provisioned tier.
4. Label each as FACT or INFER.

### Phase 6 — Recommendation matrix
Produce ranked options:
- Option A: Keep DB but set/check Serverless + auto-pause manually later.
- Option B: Stop application wakeups / avoid DB health checks.
- Option C: Export/backup synthetic data, then delete SQL DB if demo persistence not needed.
- Option D: Keep SQL for demo but accept small storage/compute cost.
- Option E: Move demo back to mock/fallback until next demo.

For each option include:
- expected benefit;
- risk;
- whether Azure mutation is required;
- whether separate Slava approval gate is required.

## VERIFICATION MATRIX
Report the following:
- Azure CLI access: PASS / BLOCKED / NOT_AVAILABLE.
- `rg-iap-demo` inventory: PASS / BLOCKED.
- SQL server identified: PASS / BLOCKED.
- SQL database identified: PASS / BLOCKED.
- Compute tier identified: PASS / BLOCKED.
- Auto-pause value identified: PASS / BLOCKED.
- Cost source confirmed: PASS / BLOCKED / LIMITED.
- No Azure changes made: PASS required.
- No secrets printed: PASS required.

## REPORT BACK FORMAT
Write report in this structure:

REQUEST_ID: REQ-2026-06-03-azure-sql-cost-audit-readonly
STATUS: READY_FOR_AUDIT | BLOCKED | PARTIAL
PROJECT: InsuranceAIPlatform
GATE: AZURE_SQL_COST_AUDIT_READONLY_V0.1

## Summary
## Azure account context, sanitized
## Resource inventory: rg-iap-demo
## SQL database configuration
## Cost source evidence
## Activity / auto-pause analysis
## Findings
## Cost-reduction options
## Risks
## Commands run
## Evidence
## Boundaries honored
## Not touched
## Limitations
## Recommended next safe step

## STOP LINE
Stop after publishing the report.
Do not change Azure.
Do not change SQL.
Do not update AIKB.
Do not commit/push product repo.
Do not proceed to a fix without Slava approval.
