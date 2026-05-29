# Gate Report — AZURE_MINIMAL_DEPLOY_MASTER_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-29
**Type:** first real Azure minimal deploy (operator-checkpointed)
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

---

## Verdict

**PREFLIGHT_ONLY_OPERATOR_ACTION_REQUIRED** — Phases 1–2 (repo truth + local validation) passed; reached the mandatory **Phase-3 login checkpoint**. The operator has **not** typed the required `LOGIN_APPROVED` phrase, so **no `az login`, no subscription change, no resources, no deploy, no image push** were performed. **Nothing was created; $0.**

## Phase 1–2 (safe, local) — PASS

```
repo: C:/Projects/InsuranceAIPlatform   branch: dev
HEAD: ce1a1e5 (== origin/dev)   origin/main: 69e67312 (untouched)   staged: 0   tree: clean except test-results/
```
| Check | Result |
|---|---|
| Backend unit tests | 137 passed / 0 failed / 0 skipped (3 s) |
| Frontend build | PASS → dist/ |
| Docker build | PASS (reproducible image hash; committed files unchanged) |
| Bicep build | PASS, 0/0, ARM 1322 lines |
| Secret scan | clean |
| Workflow guard | `workflow_dispatch`-only, deploy steps commented (cannot auto-deploy) |

## ⚠️ CRITICAL pre-login finding — wrong Azure account is currently active

A read-only `az account show` (NOT `az login`) shows an **existing az session under a corporate-domain Entra account** (email **redacted** — not logged to an external repo per privacy rules). This is **almost certainly NOT** the personal Azure subscription where the operator created the **$30 portfolio budget**. **Deploying into the corporate subscription would be wrong** (wrong tenant, employer resources, budget/alerts not applicable).

➡️ Before any deploy, the operator must confirm/switch to the **correct personal subscription**, then approve.

## Required operator actions (in this session)

1. Confirm the **correct personal Azure account/subscription** (and `az login`/`az account set` to it if the active corporate session is wrong).
2. Type **`LOGIN_APPROVED`** → executor verifies subscription (read-only) + runs live preflight (providers/SKU/region/name availability) + prints the exact resource plan.
3. Type **`DEPLOY_APPROVED`** → only then are resources created.

No secrets/tokens/subscription IDs are to be pasted into chat.

## Deploy plan preview (to be finalized at Phase 5 after correct-account login)

- **Region:** westeurope (unless changed). **RG:** `rg-iap-demo` (subscription-scope `main.bicep`).
- **Recommended first-deploy path:** operator `az login` to the personal sub → executor runs **`az deployment sub create`** locally (no OIDC/Actions needed for a first manual deploy).
- **Two open plan items** (executor will resolve in the Phase-5/6 plan, with explicit approval before any infra edit):
  1. **SQL admin** — committed `main.bicep` deploys Azure SQL but passes the SQL module an Entra-admin **placeholder objectId (all-zeros)** and does not surface it as a deploy parameter → deploying as-is **would fail at SQL creation**. Recommended minimal path: **defer SQL** (API runs without DB — Mock AI + in-memory reads; `/health` has no DB dependency; no startup migration), which needs a small `enableSql`-style infra toggle (proposed + approved before editing), **or** the operator supplies a real Entra-group objectId.
  2. **API image** — first deploy can (a) stand up infra on the **public placeholder image** (`containerapps-helloworld`, $0) to prove Container Apps, then swap in our image; or (b) push our image to **GHCR** first (needs a token with `write:packages`, or the Actions workflow with `GITHUB_TOKEN`).
- **Cost shape:** Container Apps minReplicas=0 (scale-to-zero), SWA Free, Storage + KeyVault + monitoring (sampled/capped), SQL Serverless auto-pause (if deployed), AI **off** (`enableAi=false`), **no AKS**. Target idle ~$0; real ~$5–10/mo.

## Boundary confirmations (this turn)

| Boundary | Status |
|---|---|
| `az login` | NO (only read-only `az account show`) |
| Subscription selected/changed | NO |
| Resources created / deploy / image push / workflow run / OIDC | NO |
| Source commit/push / main / PR | NO |
| Secrets printed / pasted | NO (corporate email redacted from this report) |
| AIKB updated | NO |

## Cleanup plan

N/A — nothing was created.

## Next step

Operator confirms correct personal subscription + types `LOGIN_APPROVED` (then `DEPLOY_APPROVED` after the plan). Executor resumes the same gate live. If the operator declines, this PREFLIGHT report is the gate conclusion.
