# Gate Report — AZURE_MINIMAL_DEPLOY_PREP_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-29
**Type:** deploy-prep — Docker/GHCR/OIDC/SWA/Bicep readiness + offline validation (no deploy)
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

---

## Verdict

**ACCEPT_DEPLOY_PREP_READY** — all deploy-prep artifacts authored and **validated with real builds** (Docker image built, 137/137 tests, frontend + bicep compile). No Azure login, no resources, no deploy, no image push, no OIDC creation, no secrets. All files left **uncommitted** for the commit gate. `dev` baseline `a70071d` unchanged.

## Source repo (unchanged this gate)

```
branch: dev   HEAD: a70071d (== origin/dev)   origin/main: 69e67312 (untouched)
staged: 0
working tree before: clean except test-results/ (untracked)
working tree after:  +4 new + 2 modified (all UNCOMMITTED) + test-results/
```

## Files created / updated (uncommitted)

**New (4):** `server/Dockerfile`, `server/.dockerignore`, `staticwebapp.config.json`, `docs/architecture/azure/AZURE_MINIMAL_DEPLOY_PREP_V0.1.md`.
**Modified (2):** `.github/workflows/azure-deploy-demo.yml` (guarded GHCR build+push + OIDC deploy blueprint; still `workflow_dispatch`-only, deploy steps commented; `packages: write` added), `infra/modules/container-apps.bicep` (Liveness/Readiness/Startup probes → `/health`).

## Validation — all green (Docker available, full not limited)

| Check | Command | Result |
|---|---|---|
| Docker image | `docker build -f server/Dockerfile server` | **EXIT 0** — `insurance-api:preptest` `sha256:d2dda6f2…`; 8 projects restored, `dotnet publish -c Release`, exported; context 503 KB |
| Backend build | docker publish + `dotnet test` build | OK — `InsuranceAIPlatform.Api.dll` + 6 services + BuildingBlocks |
| Backend tests | `dotnet test …Tests -c Release` | **137 passed / 0 failed / 0 skipped** (6 s, no DB) |
| Frontend | `npm run build` | **EXIT 0** → `dist/` (js 436 KB / gzip 131 KB) |
| Bicep | `az bicep build --file infra/main.bicep` | **EXIT 0**, 0/0, ARM 1322 lines |
| Secret scan | rg over created/edited files | **clean** |

## Container image design (interview-defensible)

- Multi-stage: `mcr.microsoft.com/dotnet/sdk:9.0` (restore-cached by copying csproj first) → `mcr.microsoft.com/dotnet/aspnet:9.0`.
- Runs **non-root** (`USER $APP_UID`), `/p:UseAppHost=false`, `ASPNETCORE_ENVIRONMENT=Production` (Swagger off), `EXPOSE 8080` matching Container Apps ingress.
- No secrets baked in — DB connection string + AI keys injected at runtime (env / Managed Identity / Key Vault). AI defaults to **Mock**.

## Readiness summary

| Area | State |
|---|---|
| Backend container | **READY** — image builds + tests pass |
| Frontend (SWA) | **READY** — `dist/` builds; SPA fallback config added |
| Bicep infra | **READY** — compiles 0/0 with `/health` probes |
| Workflow | **READY** — guarded blueprint, disabled on push |
| GHCR publish | **PLAN** — image not pushed (deploy gate) |
| OIDC federation | **PLAN** — operator creates app + federated credential |
| Azure SQL/Storage/KV params | **READY** — placeholders; `sqlAdminObjectId` supplied at deploy |

## Known gaps (documented; product code untouched)

1. **CORS** — API allows only `localhost:5173`. Preferred fix = SWA **linked backend** (`/api/*` proxied same-origin → no code change). Alt = env-driven CORS (separate code gate).
2. **SQL wiring** — `INSURANCEAI_CONNECTION_STRING` (Entra/MI passwordless) + one-time `DbMigrator` run; deferred to a SQL gate. Minimal deploy runs without DB (Mock AI + in-memory reads; `/health` has no DB dep; no startup migration → cold-start safe).
3. **GHCR visibility** — make the package public after first push (anonymous pull) or add a pull credential.
4. **`sqlAdminObjectId`** — real Entra oid supplied at deploy, never committed.

## Operator checklist (manual — before/at deploy gate)

1. `az login` (deploy gate only). 2. Confirm subscription. 3. Confirm region `westeurope`. 4. Confirm $30 budget/alerts active. 5. Entra group → `sqlAdminObjectId`. 6. OIDC: app + federated credential (`repo:slavkan777/InsuranceAIPlatform:environment:azure-demo`) + Contributor + repo Actions secrets `AZURE_CLIENT_ID/TENANT_ID/SUBSCRIPTION_ID` (via GitHub UI). 7. Approve first `az deployment sub create`. 8. Set GHCR package visibility after first push.

**Do NOT paste (chat/committed):** GitHub PAT, any API key, `DEEPSEEK_API_KEY`, SQL passwords (none — Entra-only), client secrets. Sub/tenant/client **IDs** → GitHub Actions secrets via UI, not chat.

## Boundary confirmations

| Boundary | Status |
|---|---|
| `az login` / resources / deploy / what-if | NO |
| GHCR image pushed / workflow run / OIDC created | NO |
| Source commit / push / main / PR | NO |
| Secrets / real PII | NONE |
| Product business logic changed | NO (only Docker/SWA/infra/workflow/doc) |
| AI provider activated | NO (Mock default) |

## Next recommended gate

`AZURE_MINIMAL_DEPLOY_V0.1` — needs `az login` (operator) and **creates Azure resources**: activate GHCR build/push + OIDC `az deployment sub create` of `main.bicep` (+ image tag + `sqlAdminObjectId`); verify `/health`; confirm ~$0 idle. (A commit gate for these prep files should run first — they are currently uncommitted.)

## Limitations

1. All prep files uncommitted (commit gate next).
2. `bicep build` offline — no live quota/region/SKU/model check (surfaces at deploy).
3. Image built locally only (`insurance-api:preptest`, removed after validation) — not pushed to GHCR.
4. CORS + SQL are deploy/post-deploy concerns, not solved here.
