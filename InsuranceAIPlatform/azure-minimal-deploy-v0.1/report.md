# Gate Report — AZURE_MINIMAL_DEPLOY_MASTER_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-29
**Type:** first real Azure minimal deploy (operator-checkpointed)
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

---

## Verdict

**DEPLOYED_MINIMAL** ✅ — the API is live on Azure Container Apps (scale-to-zero) on the **personal** subscription, `/health` returns 200, all cost guardrails verified. Image via GHCR (public). SQL + AI deferred. No AKS, no ACR. Source docs + the `enableSql` infra edit left **uncommitted** (separate commit gate next).

## Operator checkpoints honored

- Switched off the corporate tenant → personal account; `LOGIN_APPROVED` then `DEPLOY_APPROVED` both given explicitly.
- Decisions: **defer SQL**, **GHCR** image path.

## What is live

- **API:** `https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io`
  - `GET /health` → **200** `{"status":"Healthy","service":"InsuranceAIPlatform.Api","environment":"Production",...}` (our image, after cold start).
- **SWA:** `https://kind-meadow-03cf73103.7.azurestaticapps.net` (Free; content deploy deferred).

## Resources created — `rg-iap-demo` / westeurope (8)

`iap-demo-api` (Container App, minReplicas=0, image `ghcr.io/slavkan777/insuranceai-api:ce1a1e5`), `iap-demo-cae` (ACA env), `iap-demo-swa` (SWA Free), `iap-demo-api-mi` (Managed Identity), `iapdemokv…` (Key Vault RBAC), `iapdemost…` (Storage, shared-key disabled, TTL), `iap-demo-law` (Log Analytics, capped), `iap-demo-appi` (App Insights, sampled).

**Not deployed (by design):** Azure SQL (`enableSql=false`, output `sqlServerFqdn=""`), AI (`enableAi=false`), AKS, ACR.

## Validation evidence

| Step | Evidence |
|---|---|
| Local | tests 137/137, frontend build OK, docker build reproducible, bicep 0/0 |
| Image | pushed to GHCR (digest `sha256:3d320fa9…`); made public; anonymous pull HTTP **200** |
| Deploy | `az deployment sub create` → `provisioningState: Succeeded` |
| ACA | image correct, minReplicas=0, provisioning Succeeded, runningStatus Running |
| Smoke | `/health` HTTP 200, body identifies `InsuranceAIPlatform.Api` (not placeholder) |
| Cost | 8 resources; no AKS/ACR in sub; SWA Free; storage shared-key=false; LAW capped; AI/SQL off |

## Cost guardrails

- Container Apps **minReplicas=0** (scale-to-zero) · SWA **Free** · App Insights sampled · Log Analytics 30d + 1 GB/day cap · Storage TTL.
- **Idle ≈ $0** (residual LAW/Storage well under $1–2/mo). Budget $30/mo + alerts $5–50 (operator). Real target $5–10/mo.

## Boundary confirmations

| Boundary | Status |
|---|---|
| Personal subscription (not corporate) | YES — switched |
| AKS / ACR / always-on compute | NONE |
| AI provider activated | NO (Mock) |
| Secrets printed / tokens in chat | NO |
| `main` touched / PR / force | NO |
| Source commit/push | NO (docs + enableSql edit uncommitted) |
| AIKB updated | NO |

## Uncommitted source changes (for the next commit gate)

- `infra/main.bicep` — `enableSql` toggle (param + `if(enableSql)` guard + conditional output).
- `docs/architecture/azure/{AZURE_MINIMAL_DEPLOY_V0.1, AZURE_DEPLOYED_RESOURCES_V0.1, AZURE_COST_POST_DEPLOY_CHECK_V0.1}.md`.

## Cleanup / rollback

```bash
az group delete -n rg-iap-demo --yes --no-wait
```

## Limitations / next

1. **Frontend content not deployed** — SWA resource live but empty. Follow-up: `swa deploy ./dist` with the SWA token (or Actions), + wire SPA→API (`VITE_INSURANCE_API_MODE=backend` + SWA **linked backend** for same-origin, no CORS code change).
2. SQL deferred — a later SQL gate (real Entra admin objectId + `DbMigrator` run).
3. AI stays Mock until an explicit AI controlled-demo gate.
4. First deploy was a **local `az deployment`** (no OIDC/Actions needed); CI deploy path remains the guarded workflow.

## Next recommended gate
`AZURE_MINIMAL_DEPLOY_COMMIT_V0.1` — commit the `enableSql` edit + 3 post-deploy docs to `dev`. Then `AZURE_FRONTEND_DEPLOY_V0.1`.
