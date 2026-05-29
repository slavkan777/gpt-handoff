# Gate Report — AZURE_IAC_SKELETON_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-29
**Type:** Azure IaC (Bicep) skeleton + deployment docs + offline validation
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

---

## Verdict

**ACCEPT_IAC_SKELETON_READY** — a Bicep IaC skeleton + deployment docs + a disabled CI workflow were authored and **validated offline** (`az bicep build` → **0 errors / 0 warnings**, 1291-line ARM template). **No `az login`, no resources, no deploy, no secrets, no source commit/push.** All files left **uncommitted** for the commit gate.

## Source repo state (unchanged this gate)

```
branch: dev   HEAD: 2e9443a6726f34f20bd26233e840ae8cc982d91a (before == after)
origin/dev: 2e9443a…   origin/main: 69e67312… (untouched)
staged: 0
working tree: + 17 NEW uncommitted files (this gate) + 4 uncommitted pre-flight docs (prior gate) + test-results/.last-run.json
```

## Azure context (from operator)
Budget created manually: **$30/mo**, alerts **$5/$10/$20/$30/$50**, spend **$0**. Real target $5–10/mo · comfort cap $30 · ceiling ~$1000. Region: default `westeurope` (confirm at deploy).

## IaC decision: Bicep-first
Subscription-scope `main.bicep` creates the RG and wires RG-scoped modules. Chosen over Terraform (no multi-cloud need / no state backend to run) and over azd-only (azd uses Bicep underneath; a clean modular tree can be wrapped later).

## Files created (17 this gate — all uncommitted)
**infra/ (12):** `main.bicep`, `README.md`, `parameters/demo.bicepparam`, `modules/{monitoring,identity,key-vault,storage,container-apps,sql-serverless,static-web-app,ai-optional}.bicep`, `modules/budgets-notes.md`.
**docs/architecture/azure/ (4 new):** `AZURE_IAC_SKELETON_V0.1.md`, `AZURE_DEPLOYMENT_RUNBOOK_V0.1.md`, `AZURE_RESOURCE_NAMING_V0.1.md`, `AZURE_INTERVIEW_STORY_V0.1.md`. *(+ 4 pre-flight docs from the prior gate, still uncommitted — all commit together next gate.)*
**.github/workflows/ (1):** `azure-deploy-demo.yml` — **`workflow_dispatch` only** (no push trigger), deploy job guarded by `confirm == 'DEPLOY'` + `environment: azure-demo`, az/deploy steps **commented out** until the deploy gate, OIDC placeholders only, **no secrets**.

## Resource model (cost-shaped, in the templates)
| Resource | Cost-critical setting |
|---|---|
| Static Web Apps | `sku: Free` — public static, $0 |
| Container Apps `insurance-api` | **`minReplicas: 0`** scale-to-zero; HTTP scale; user-assigned MI |
| Azure SQL | `GP_S_Gen5_1`, **`autoPauseDelay: 60`**, `azureADOnlyAuthentication: true` (no password) |
| Storage | `allowSharedKeyAccess: false`; 4 containers; **lifecycle TTL** delete after `blobTtlDays` |
| Key Vault | `enableRbacAuthorization: true`; Secrets User → app MI; **no secret values** |
| Managed Identity | user-assigned; role assignments (KV Secrets User, Blob Data Contributor) |
| Monitoring | LAW `retention 30d` + `dailyQuotaGb 1`; App Insights `SamplingPercentage 50` |
| AI (optional) | only if `enableAi=true` (default **false**); OpenAI S0 + Document Intelligence **F0** + AI Search **Free**; `disableLocalAuth: true` |
| AKS | **deferred** — not modelled |

## Validation (SUBPHASE 8)
```
az bicep install                       → Bicep CLI 0.43.8
az bicep build --file infra/main.bicep → EXIT 0, 0 errors, 0 warnings, ARM emitted (1291 lines)
```
Offline only — no `az login`, no `what-if` (needs login), no `az deployment`. **Full compile validation, not just static review.**

## Safety (SUBPHASE 9)
- Secret-value scan over `infra/`, `docs/architecture/azure/`, `.github/` → **no matches**.
- GUID scan → only public Azure built-in **role definition IDs** (KV Secrets User, Blob Data Contributor, Cognitive Services OpenAI User) + an **all-zeros placeholder** for `sqlAdminObjectId`. **No real subscription/tenant ids, no keys, no passwords, no connection strings.**
- `allowSharedKeyAccess:false` + SQL Entra-only + Key Vault RBAC → the design itself is passwordless.

## Boundary confirmations
| Boundary | Status |
|---|---|
| `az login` | NO |
| Azure resources created / deployed | NO |
| `what-if` / `az deployment` | NO |
| Source commit / push | NO |
| `main` / PR | NO |
| Secrets / subscription ids | NONE |
| Product code / tests changed | NO |
| AI provider activated | NO (templates default `enableAi=false`; app `AiProvider__Mode=Mock`) |

## Next recommended gate
`AZURE_IAC_SKELETON_COMMIT_V0.1` (or combined `COMMIT_AND_PUSH_AZURE_IAC_SKELETON_V0.1`) — commit `infra/` + the 8 `docs/architecture/azure/*` + the disabled workflow to `dev` (the commit gate runs the QA-inspector path). Then `AZURE_MINIMAL_DEPLOY_V0.1` (Dockerfile + GHCR image + OIDC + first real deploy ≈ $0).

## Limitations
1. All files uncommitted (commit gate next).
2. `bicep build` validates syntax + bundled type schemas offline — it does **not** verify live Azure quota / region SKU availability / model availability; that surfaces at `what-if`/deploy.
3. Deploy-time inputs deliberately deferred: `insuranceApiImage` (GHCR tag), `sqlAdminObjectId` (Entra group oid) — supplied at deploy, never committed.
4. Dockerfile + GHCR publish + OIDC federation are deploy-gate deliverables (not in this skeleton).
