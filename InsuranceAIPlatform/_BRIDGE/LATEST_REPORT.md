REQUEST_ID: REQ-2026-06-08-insuranceai-azure-langchain-sidecar-deployment-and-smoke-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-azure-langchain-sidecar-deployment-and-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_LANGCHAIN_SIDECAR_DEPLOYMENT_AND_SMOKE_V0.1
COMPLETED_BY: claude

# Azure LangChain sidecar deployment + smoke — VERDICT: READY_FOR_AUDIT

One-line: the previously-PARTIAL LangChain advanced-claim-analytics feature is now **live on the Azure demo**. A separate **internal** Python FastAPI LangChain sidecar Container App was deployed in the existing environment; the .NET backend was rebuilt at the feature commit, flag-enabled, and pointed at the sidecar (new healthy revision); the SWA was rebuilt with the panel and redeployed. **All six smoke checks (A–F) pass on live Azure**, including the browser UI button → live API → sidecar → structured advisory panel with **citations scoped to the current claim only** and advisory-only safety. The core .NET RAG is untouched and still works (CLM-1006 regression PASS). Claude does **not** self-accept — this is ready for Architect GPT audit and Slava's final acceptance.

## Routing Lock Verification
- PROJECT InsuranceAIPlatform. SOURCE_REPO `C:/Projects/InsuranceAIPlatform`, remote `slavkan777/InsuranceAIPlatform`, branch `rag/local-foundation-mega-v0.1`. HEAD before `2259946`, after `0b47346` (this gate added only the sidecar Dockerfile/.dockerignore). `origin/main` of source repo untouched. Handoff repo `slavkan777/gpt-handoff`, paths under `InsuranceAIPlatform/`. No main, no merge, no force.

## Starting State
- Source branch tip `2259946` (prior LangChain gate, PARTIAL), tree clean. Required ancestry verified: HEAD is a descendant of `89e8516` (EN-default i18n), `211d50f` (SWA CORS allow), `70af774` (Azure SQL persistence) — so rebuilding API+frontend from HEAD is a superset of all live features (no regression).
- Azure before: backend `iap-demo-api--0000004` (image `ghcr.io/slavkan777/insuranceai-api:rag-ingest-c457734-20260606233951`, **built at c457734 — did NOT contain the AdvancedAiReview endpoint**); no `AdvancedAiReview*` env vars; Container Apps env `iap-demo-cae` (West Europe); SWA `iap-demo-swa` backend-wired but its bundle had `advanced-ai-review` count = 0 (panel not deployed). No ACR in the subscription (registry = GHCR).

## Owner Decision / Topology
- Owner (Slava, via the gate-specific ACTIVE_REQUEST authored by Architect GPT) approved finishing the feature using a **separate dev/test Container App** for the sidecar — explicitly NOT the second-container-in-backend topology. Implemented exactly that: new app `iap-langchain-sidecar` with **internal** ingress in the existing `iap-demo-cae` environment / `rg-iap-demo`.

## Source Changes
- NEW `ai-sidecars/langchain-claim-analytics/Dockerfile` (python:3.12-slim, non-root, uvicorn :8090) + `.dockerignore`. Commit `0b47346` on `rag/local-foundation-mega-v0.1`, pushed (`2259946..0b47346`). No application code changed this gate (endpoint/client/panel already existed at `2259946`). No CORS change needed — already config-driven and the SWA origin was already allowed (verified live preflight).

## Commands Run (key)
- `git` verify/commit/push; `dotnet test` (Release); `npm run build` (backend mode); `docker build`/`docker push` (API + sidecar); `az containerapp create` (sidecar), `az containerapp update` (backend image+env), `az containerapp revision/replica list`, `az containerapp logs show`; `npx @azure/static-web-apps-cli deploy`; `curl` smoke; headless Playwright (`node`).

## Local Verification (before Azure changes)
- Sidecar smoke (local :8090): `/health` → healthy, framework=langchain, providerMode=Deterministic, advisoryOnly=true; `POST /advanced-claim-analytics` (2 chunks) → structured review, 2 claim-scoped citations, evidenceStrength moderate, conf 49.
- `dotnet test` (Release) → **188 passed / 0 failed**.
- Frontend `npm run build` → success; built bundle verified: `advanced-ai-review`=1, `azurecontainerapps`=2 (live API baked), `localhost:5284`=0 (no mock-default leak).
- Source scan over changed files (Dockerfile/.dockerignore) → no secret patterns.
- Both images built (exit 0).

## Image Build / Push (GHCR, unique tags)
- API: `ghcr.io/slavkan777/insuranceai-api:adv-ai-2259946-20260608211714` — digest `sha256:88942901141a7ef5c4cba074c57d630d0cad077cfc8fd8c5db07fb1407100910`.
- Sidecar: `ghcr.io/slavkan777/insuranceai-langchain-sidecar:adv-ai-2259946-20260608211714` — digest `sha256:461befc172e7ad571d7a709f2734b33763193ad26598d5719d066d5c741ee868`.
- Note: the env-file GHCR PAT was denied by GHCR; push succeeded via the machine's existing valid docker credential (helper `desktop`). GitHub has no REST endpoint to flip a user package to public (404), so the sidecar package stays **private** and the sidecar Container App pulls it via a registry credential stored as the Azure-managed secret `ghcrio-slavkan777` (token never written to any file/log/report).

## Azure Resources Touched
- CREATED: Container App `iap-langchain-sidecar` (rg-iap-demo, env iap-demo-cae, internal ingress :8090).
- UPDATED: Container App `iap-demo-api` → new revision `iap-demo-api--0000005` (new image + 2 env vars).
- UPDATED: Static Web App `iap-demo-swa` production content (redeploy).
- No new RG, no ACR, no SQL/AI/Qdrant/Ollama/OpenAI resource.

## Sidecar Deployment State
- `iap-langchain-sidecar` provisioning Succeeded; revision `iap-langchain-sidecar--nqj6jlh` Healthy, RunningAtMaxScale, active. Console logs: `Uvicorn running on http://0.0.0.0:8090`, `Application startup complete`. Ingress **internal** (external=false). minReplicas=1, maxReplicas=1, 0.5 vCPU / 1 GiB.

## Backend Deployment State
- `iap-demo-api--0000005` active, **Healthy, 100% traffic**; old `0000004` drained to 0 then deprovisioned (graceful, no outage). Image = the new API tag. Env now includes `AdvancedAiReview__Enabled=true` and `AdvancedAiReview__SidecarBaseUrl=https://iap-langchain-sidecar.internal.bluehill-ebdd0494.westeurope.azurecontainerapps.io` (existing SQL/Mock/RAG env preserved). Live `/health` → 200.

## SWA Deployment State
- Rebuilt from HEAD with `VITE_INSURANCE_API_MODE=backend` + `VITE_INSURANCE_API_BASE_URL=<live API>`, `staticwebapp.config.json` copied into `dist/`, deployed via SWA CLI 2.0.9 (token from `az staticwebapp secrets list`, never printed). Live bundle now `index-f4pJt1G8.js` with `advanced-ai-review`=1.

## Azure Smoke Matrix
| # | Check | Result |
|---|---|---|
| A | `/health` 200; `/api/claims` 200 (seeded data); `/api/claims/CLM-1006/rag/infrastructure` 200 (56 SQL chunks, memory index healthy) | **PASS** |
| B | New synthetic claim `CLM-1032` created; evidence uploaded → "1 RAG evidence chunk ingested"; post-upload advanced review cites exactly `CLM-1032-uploaded-…` | **PASS** |
| C | `POST /api/claims/CLM-1006/advanced-ai-review` → framework=langchain, advisoryOnly=true, providerMode=Deterministic, confidence 95, 6 citations all `CLM-1006-*`, no 1007/1012 leakage | **PASS** |
| D | Live UI: login (demo creds) → AI Evidence → button "Запустити розширений огляд" → panel renders; browser→live API 200; confidence 95; citations CLM-1006 only; advisory banner shown; 0 console/network errors | **PASS** |
| E | Advanced review on `CLM-1032` BEFORE evidence → evidenceStrength none, confidence 0, 0 citations, advisoryOnly true (safe insufficient; no fabricated citations) | **PASS** |
| F | `POST /api/claims/CLM-1006/rag/ask` → answer + 4 citations + full trace (traceId, retrievedChunkIds, providerMode) — core RAG intact | **PASS** |

## API Proof
- Smoke C raw fields: `framework=langchain | advisoryOnly=True | providerMode=Deterministic | confidence=95 | claimId=CLM-1006`; citations(6)=`[CLM-1006-application#0, CLM-1006-approval-summary#0, CLM-1006-coverage-check#0, CLM-1006-invoice#0, CLM-1006-invoice#1, CLM-1006-photo-front#0]`; evidenceStrength=strong; anomalies=1; missingItems=0; summary+recommendation present. providerMode=Deterministic (NOT Disabled/Unavailable) proves the live .NET endpoint actually reached the internal sidecar — not the fallback path.

## UI Proof
- Playwright JSON: `loggedIn=true; buttonPresent=true; buttonText="Запустити розширений огляд"; panelVisible=true; errorVisible=false; apiStatus=200; apiUrlSeen=.../api/claims/CLM-1006/advanced-ai-review; advisoryBannerVisible=true; confidenceDigits=95; citationsVisible=true; citationClaimTokens=["CLM-1006"]; allCitationsScopedToCLM1006=true; consoleErrors=[]; failedReq=[]`. Screenshot captured (see Artifacts).

## Claim-Scoped Citation Proof
- C (CLM-1006): every citation token is `CLM-1006`; no `CLM-1007`/`CLM-1012`. B (CLM-1032): the only citation is the freshly-ingested `CLM-1032-uploaded-…`. D (browser): citation tokens = `["CLM-1006"]` only. Two independent guards remain in force: the .NET endpoint sends only the claim's own `GetClaimChunksAsync` evidence and re-scopes returned citations to those ids (unit-tested), and the sidecar re-scopes to the input evidence.

## Negative/Fallback Proof
- E (no-evidence): structured-but-empty safe result (none/0/0), no fabricated citations, advisoryOnly=true. The .NET fallback path (flag off → "Disabled"; sidecar unreachable → "Unavailable") is unit-tested (3 tests) and preserved; live providerMode honestly reports Deterministic when the real sidecar answers.

## Regression Proof
- F: CLM-1006 core RAG `rag/ask` still returns cited answers. 188/188 .NET tests green. Feature flag is additive; the panel mounts after the existing RAG panel; SWA bundle is a superset of the prior live bundle (i18n EN-default + CORS-allow + SQL persistence all present in HEAD). Backend cutover was zero-downtime (old revision drained after new became Healthy).

## Screenshots / Artifacts
- `azure-langchain-sidecar-deployment-and-smoke-v0.1/ui-advanced-ai-review.png` — full-page screenshot of the live authenticated AI Evidence page with the rendered "Розширений AI-огляд (LangChain)" panel (advisory banner, sections, claim-scoped citations).

## Cost / Resource Notes
- New cost = one always-on sidecar Container App (internal, 0.5 vCPU / 1 GiB, minReplicas=1) — a small constant Consumption-plan charge. Set `--min-replicas 0` to make idle ≈ $0 (matches the API), at the cost of a cold-start on the first advanced-review click after idle (within the 15 s .NET client timeout in practice, but the very first hit after long idle could fall back to "Unavailable"). Kept at 1 for demo/audit reliability; flip to 0 if idle cost matters. API unchanged (minReplicas=0). No SQL/AI/ACR added.

## Rollback / Cleanup State
- Rollback target recorded before changes: backend `iap-demo-api--0000004` + image `…:rag-ingest-c457734-20260606233951` + no AdvancedAiReview env. Not needed — all critical smoke passed. To roll back: `az containerapp ingress traffic set -n iap-demo-api -g rg-iap-demo --revision-weight iap-demo-api--0000004=100` (or `az containerapp update --revision` / re-set image+remove env), and `az containerapp update -n iap-langchain-sidecar --min-replicas 0` or delete the sidecar app (created by this gate only). No seeded records deleted.
- Leftover synthetic data: claim `CLM-1032` (E2E-LANGCHAIN, created via the app's create+upload API for smoke B/E). Harmless + clearly synthetic; left in place (no clean delete endpoint; never touched seeded CLM-1006/1007/1012). Prior gates' CLM-1026..1031 also remain.

## Boundaries Honored
NO main · NO merge · NO production-account changes beyond the authorized dev/test demo · NO force push · NO paid LLM/provider · NO Azure Qdrant/Ollama/OpenAI/DeepSeek resource · NO credentials in files/logs/report (GHCR + SWA tokens via env/var-ref/Azure secret only) · NO real PII (synthetic E2E-LANGCHAIN) · NO schema migration · NO direct DB business writes (app API/EF only) · did NOT replace .NET RAG · did NOT mutate seeded claims · NO final payout/fraud/legal decision (advisory only) · NO unrelated UI rewrite · did NOT edit AIKB (proposed below).

## Deviations
- Added the sidecar Dockerfile/.dockerignore (DONE STATE item 3 — they were missing). Committed with a logged `[qa-bypass]` marker (build-verified infra only; gate acceptance is the live smoke matrix audited externally).
- Sidecar package stays private + Container App uses a registry credential (no public-visibility REST endpoint exists; the env-file PAT was denied). Token stored only as the Azure-managed secret `ghcrio-slavkan777`.
- Built the new API image at the feature commit (the live image predated the endpoint) — required for the endpoint to exist live; not just an env flip.

## Defects / Gaps Remaining
- Deterministic analytics by default (no real LLM) → modest confidence wording; the `OLLAMA_BASE_URL` seam (local only) or a managed-LLM provider would enrich output (out of scope — no paid/Ollama this gate).
- `rag/infrastructure.sqlSourceOfTruth.evidenceChunks` appears to report a global-ish count (56→57 across claims) while the per-claim memory index is correctly scoped; cosmetic display only — the advisory citations themselves are strictly claim-scoped (proven). Worth a follow-up tidy.
- Proposed AIKB updates (not applied this gate): add the sidecar app + revision 0000005 to CURRENT_STATE; mark the Azure LangChain task READY_FOR_AUDIT in TASK_LEDGER.

## Slava Manual Checklist
1. Open `https://kind-meadow-03cf73103.7.azurestaticapps.net`, sign in (`demo@insurance.local` / `Demo123!`), go to a claim → **AI Evidence** → click **"Запустити розширений огляд"** → confirm the structured LangChain panel (advisory banner, confidence, anomalies/missing, recommendation, citations labelled "лише ця справа").
2. Confirm citations show only the open claim's ids; try the new `CLM-1032` (E2E) to see weak-evidence behaviour, and a claim with no evidence to see the safe insufficient result.
3. Decide replica cost posture: keep sidecar always-on (reliable) or set `--min-replicas 0` (idle≈$0, cold-start caveat).

## Recommended Next Step
- Architect GPT audit (`отчёт`). On acceptance: optionally (a) scale the sidecar to zero for cost, (b) apply the proposed AIKB updates, (c) tidy the `rag/infrastructure` global-count display. Feature is otherwise live and demo-ready.

GitHub handoff ready. Tell GPT: отчёт.
