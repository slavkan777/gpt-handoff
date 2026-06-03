REQUEST_ID: REQ-2026-06-03-azure-sql-cost-audit-readonly
STATUS: READY_FOR_AUDIT
TASK_TYPE: audit
PROJECT: InsuranceAIPlatform
GATE: AZURE_SQL_COST_AUDIT_READONLY_V0.1
COMPLETED: 2026-06-03 (read-only)
COMPLETED_BY: claude

# Azure SQL Cost Audit — Read-Only Report

## Summary

The SQL resource flagged in Cost Analysis is database `InsuranceAIPlatform` on server
`iap-sql-r2-6c7g465` (germanywestcentral). It is **General Purpose Serverless
(`GP_S_Gen5_1`)** with **auto-pause enabled at 60 min** (the Azure minimum), **min 0.5 /
max 1 vCore**, **2 GB max size (~9 MB actually used)**, **Local (LRS) backup**, and it is
**currently Paused** (since 2026-06-03 01:07 UTC).

This is already at — or one destructive step away from — the lowest-cost configuration Azure
SQL offers. The ~**$1.03 month-to-date** (operator Portal Cost Analysis screenshot) is
dominated by SQL only because every *other* resource in the group is Free or near-zero:
Static Web App = Free, Storage = Standard_LRS (tiny), Log Analytics = tiny ingestion,
Container App = scale-to-zero. There is no AKS / ACR / App Service plan / AI Search /
Azure OpenAI / VM / always-on compute anywhere in the group.

Metrics show **no constant wake-up**: successful connections are 0 in almost every 6-hour
window over the last 3 days, with only 3 small bursts (11, 7, 1) that line up with 3 short
serverless-compute charges. The Container App is at **0 replicas** (scale-to-zero) and
therefore cannot be hammering the DB. The compute cost is brief, intentional/manual
activity — not a runaway loop.

**No Azure resource was changed. No secrets/connection strings were printed. Key Vault
secret values were not read.**

## Azure account context, sanitized

- Environment: `AzureCloud`
- Subscription: "Azure subscription 1" — id `858c258c-…` (masked), state **Enabled**
- Tenant: `eb75bfdb-…` (masked), domain `…onmicrosoft.com` (personal MSDN/PAYG-style tenant, masked)
- Signed-in identity: account owner (email masked), type `user`
- Azure CLI: `2.77.0` — already authenticated; **no `az login` performed**

## Resource inventory: rg-iap-demo

RG `rg-iap-demo` · location **westeurope** · provisioningState Succeeded ·
tags `{env:demo, project:InsuranceAIPlatform, managedBy:bicep, costCenter:portfolio-demo}`.

| # | Resource | Type | Location | SKU / cost note |
|---|---|---|---|---|
| 1 | iap-demo-law | Log Analytics workspace | westeurope | pay-per-GB ingestion (tiny) |
| 2 | iap-demo-swa | Static Web App | westeurope | **Free** |
| 3 | iap-demo-api-mi | User-assigned Managed Identity | westeurope | free |
| 4 | iapdemost… | Storage account | westeurope | Standard_LRS (StorageV2) |
| 5 | iapdemokv… | Key Vault | westeurope | ~free (op-priced; not read) |
| 6 | iap-demo-appi | Application Insights | westeurope | ingestion (tiny) |
| 7 | iap-demo-cae | Container Apps Environment | westeurope | consumption |
| 8 | iap-demo-api | Container App | westeurope | **minReplicas 0** (scale-to-zero), max 2, HTTP rule |
| 9 | Application Insights Smart Detection | Action group | global | free |
| 10 | iap-sql-r2-6c7g465 | **SQL server** | **germanywestcentral** | v12.0 |
| 11 | …/master | SQL system DB | germanywestcentral | system serverless |
| 12 | …/InsuranceAIPlatform | **SQL database** | germanywestcentral | **GP_S_Gen5_1 serverless** |

No unexpected/expensive services present.

⚠️ Minor: SQL server + DB are in **germanywestcentral** while the rest of the group is in
**westeurope** (cross-region app↔DB path — negligible cost at this scale, small added latency).

## SQL database configuration

Server `iap-sql-r2-6c7g465` (FQDN `…database.windows.net`) · publicNetworkAccess Enabled ·
minimal TLS 1.2 · admin login present (username masked; **password NOT retrieved**) · state Ready.

| Property | Value |
|---|---|
| Edition / tier | GeneralPurpose |
| Service objective | `GP_S_Gen5_1` |
| Compute tier | **Serverless** (kind `v12.0,user,vcore,serverless`) |
| autoPauseDelay | **60 min** (= Azure minimum → most aggressive auto-pause possible) |
| minCapacity | **0.5 vCore** |
| max vCore (capacity) | **1** |
| maxSizeBytes | 2 GB |
| Data space used (storage metric) | **~9 MB** (8.5–9.0 MB across 3 days) |
| Backup redundancy | **Local (LRS)** — cheapest option |
| zoneRedundant | false |
| readScale | Disabled |
| Elastic pool | none |
| Geo-replication / failover group | none |
| **Status** | **Paused** (since 2026-06-03 01:07:42 UTC) |

Effectively the minimum-cost serverless GP configuration.

## Cost source evidence

- **Operator evidence (authoritative):** Azure Portal Cost Analysis screenshot ≈ **$1.03 MTD**,
  almost entirely **SQL Database**; Storage and Log Analytics near zero. (Provided by operator.)
- **Consumption line items (`az consumption usage list`):** 10 line items spanning exactly
  **3 services** — `Microsoft.Sql` (4 lines), `Microsoft.Storage` (4), `microsoft.operationalinsights`
  (2). **No** Container App / SWA / Key Vault / Managed Identity / App Insights billing lines.
  (Monetary fields returned null via the legacy CLI consumption API on this subscription, so
  per-line $ amounts are unavailable from CLI; the **service attribution** is valid.)
- **Cost Management CLI:** `az costmanagement query` unavailable — extension not installed
  (not installed under the read-only scope of this gate).
- **Interpretation:** SQL is the cost "source" because it is the only resource with non-trivial
  *compute* billing; everything else is Free/near-zero. The absolute amount (~$1) is already minimal.

## Activity / auto-pause analysis

`app_cpu_billed` (serverless billed compute) — 3-day window, PT6H buckets, nonzero only:
- 2026-06-01 ~10:58 UTC → 8486
- 2026-06-02 ~10:58 UTC → 3412
- 2026-06-02 ~16:58 UTC → 2857
- all other buckets → empty (DB paused, $0 compute)

`connection_successful` — same window:
- 2026-06-01 10:58 → 11 · 2026-06-02 10:58 → 7 · 2026-06-02 16:58 → 1 · **all other buckets → 0**

- **FACT:** DB currently Paused; auto-pause = 60 min (minimum); compute billed only during 3 short
  midday bursts; Container App at 0 replicas now.
- **FACT:** No background/constant connection pattern — most 6h windows show 0 connections.
- **INFER:** Bursts correspond to manual dev/test activity (Query Editor / SSMS / a handful of API
  calls during working sessions), not an automated health-check or always-on caller.
- **INFER:** Nothing is "keeping SQL awake" — the 60-min auto-pause is working as intended.

## Findings

1. SQL DB is already Serverless + auto-pause at the 60-min floor + min vCore + LRS backup +
   2 GB/~9 MB — near the cheapest possible Azure SQL config. **(FACT)**
2. SQL dominates cost only in relative terms; absolute cost ≈ **$1 MTD**. (operator portal + service attribution)
3. No constant wake-up; app scales to zero; DB currently paused. **(FACT, metrics)**
4. No unexpected/expensive resources in the group. **(FACT, inventory)**
5. Minor: SQL in germanywestcentral vs rest in westeurope — cross-region, negligible cost. **(FACT/INFER)**

## Cost-reduction options

| Opt | Action | Expected benefit | Risk | Azure mutation? | Separate approval gate? |
|---|---|---|---|---|---|
| **A** | Keep Serverless + auto-pause (already set; 60-min floor can't be lowered) | none beyond current — already optimal | none | No (already configured) | No |
| **B** | Stop app wakeups / avoid DB health checks | marginal (already ~0 background hits) | none | No (verify only) | No |
| **C** | Export bacpac → **delete** SQL DB until next demo | → **~$0** (removes compute + storage) | demo loses persistence; must re-deploy (Bicep exists) before next demo | **Yes (destructive)** | **Yes — required** |
| **D** | Keep SQL, accept ~$1/mo | zero effort; demo always ready | ongoing ~$1–3/mo | No | No |
| **E** | Switch demo to mock/fallback between demos; delete/pause DB in between | ~$0 between demos | must re-enable real DB for live demo; mock less convincing on inspection | App config (and possibly DB delete) | Depends (DB delete → yes) |

## Risks

- Options C / E (delete) are **irreversible for current data** (synthetic/seedable, but still a
  destructive Azure mutation) → require a separate gate + Slava approval. Out of scope for this
  read-only gate.
- Lowering auto-pause below 60 min is **not possible** (platform floor).
- Options A / B / D carry **no risk** — they require no Azure change.

## Commands run (all read-only GET/list)

- `az version` · `az account show`
- `az group show --name rg-iap-demo`
- `az resource list --resource-group rg-iap-demo`
- `az sql server list -g rg-iap-demo`
- `az sql db show -g rg-iap-demo -s iap-sql-r2-6c7g465 -n InsuranceAIPlatform`
- `az sql elastic-pool list -g rg-iap-demo -s iap-sql-r2-6c7g465`
- `az monitor metrics list … --metric app_cpu_billed | connection_successful | storage`
- `az consumption usage list`
- `az containerapp show …` · `az containerapp replica list …`

No create / update / delete / login command was run.

## Evidence

Sanitized key values are in the **SQL database configuration** and **Activity / auto-pause**
tables above (service objective, autoPauseDelay=60, minCapacity=0.5, maxSize=2 GB, used ~9 MB,
status=Paused, LRS backup; connection bursts 11/7/1 with all other windows 0; app minReplicas=0,
0 active replicas).

## Boundaries honored

- No Azure resource created / updated / deleted.
- No SQL tier / compute / storage / auto-pause change.
- No `az login` with credentials — used the existing authenticated session.
- No secrets / connection strings printed; Key Vault secret values NOT read.
- No product-repo writes; no AIKB writes.

## Not touched

- Key Vault secrets, SQL admin password, connection strings.
- Container App (not stopped / scaled / changed).
- Bicep / Terraform, deployments, migrations.
- Any source-repository file; any commit/push in a product repo.

## Limitations

- CLI **dollar** figures unavailable: `az costmanagement` extension not installed, and
  `az consumption usage list` returned null monetary fields on this subscription. The dollar
  amount relies on the operator's Portal Cost Analysis screenshot (~$1.03 MTD).
- `app_cpu_billed` → $ conversion not computed (would be INFER); the metric is used only to locate
  *when* compute was billed, not to assert an exact cost.
- AIKB may be stale (older AIKB reportedly said "no Azure resources yet"). Per request, live Azure
  evidence was trusted and **AIKB was NOT updated** — discrepancy reported here only.

## Recommended next safe step

**No Azure change under this gate.** The DB is already at the practical cost floor for Azure SQL
serverless and absolute spend is ~$1 MTD.

- If Slava accepts ~$1/mo for an always-ready demo → **Option D (do nothing)** — recommended default.
- If Slava wants literal **$0** → open a **separate destructive gate** for **Option C**
  (export bacpac → delete DB; re-provision via existing Bicep before next demo). Do not proceed
  without that explicit approval.

## VERIFICATION MATRIX

| Check | Result |
|---|---|
| Azure CLI access | **PASS** |
| rg-iap-demo inventory | **PASS** |
| SQL server identified | **PASS** (`iap-sql-r2-6c7g465`) |
| SQL database identified | **PASS** (`InsuranceAIPlatform`) |
| Compute tier identified | **PASS** (Serverless `GP_S_Gen5_1`) |
| Auto-pause value identified | **PASS** (60 min) |
| Cost source confirmed | **LIMITED** (service attribution PASS via consumption + portal; per-line $ from CLI unavailable) |
| No Azure changes made | **PASS** |
| No secrets printed | **PASS** |

## STOP LINE

Report published. No Azure/SQL change, no AIKB update, no product-repo commit. Awaiting Architect
GPT audit («отчёт») and Slava decision before any fix.
