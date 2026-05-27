# InsuranceAIPlatform — Remaining Read Tabs Integration V0.2 — Handoff

**Gate:** `REMAINING_READ_TABS_INTEGRATION_V0.2` · **Date:** 2026-05-27
**Scope:** wire the remaining read-only UI areas through the existing API facade to the live .NET read-only backend, preserving mock fallback + UI baseline. Local only — no commit/push, no server/DB/EF/Azure/AI/write changes.

## CURRENT STATE
All 11 read areas now render live backend data in backend mode (up from 2), each with a non-lossy mock fallback that keeps mock mode byte-identical to the accepted baseline. The 2 previously-missing client functions (summary, approval-read) were added to both the mock and backend implementations, giving full 11-of-11 endpoint coverage. Frontend build PASS; backend untouched and green. No commit/push.

## IMPLEMENTATION STATUS: implemented (local, uncommitted).

## BRANCH / HEAD
- Branch: `dev`. HEAD before & after (no commit this gate): `3f07df7` (`3f07df7d5cb5c0c557942dc1c2633ae352669990`). `origin/dev` unchanged at `3f07df7`.

## FILES CHANGED (20, all `src/**`)
- API (2): `src/api/mockInsuranceApi.ts`, `src/api/backendInsuranceApi.ts` (added `getClaimsSummary` + `getClaimApproval` to both).
- State (8): `src/app/rootSaga.ts`; `src/features/claims/{claimsSlice,claimsSelectors,claimWorkspaceSlice,claimWorkspaceSelectors,claimsSaga}.ts`; `src/features/demo/{demoSlice,demoSelectors,demoSaga}.ts`.
- Pages (9): `DashboardPage`, `DocumentsPhotosPage`, `AiEvidencePage`, `RisksChecksPage`, `PolicyCoveragePage`, `CustomerVehiclePage`, `HumanApprovalPage`, `AuditCostPage`, `DemoScenarioPage`.

## API FACADE CHANGES
Added the 2 missing read functions to keep the facade (`insuranceApi: typeof mockInsuranceApi`) complete:
- `getClaimsSummary()` → GET `/api/claims/summary` (mockInsuranceApi.ts + backendInsuranceApi.ts).
- `getClaimApproval(claimId)` → GET `/api/claims/{id}/approval` (read model).
Both backend versions are GET-only with typed DTO→type mapping. Write-ish functions (`saveApprovalDraft`, `sendCustomerRequest`, `runMockAiAnalysis`) remain stubbed and never hit the backend.

## BACKEND ENDPOINTS CONSUMED: 11 of 11
summary, claims list, claim detail, documents (+photos), ai-evidence, risks, policy, customer-vehicle, approval (read), audit, demo scenario.

## SCREEN-BY-SCREEN MATRIX (all 11, independently verified by Opus inspector at file:line)
| Screen | Endpoint | Backend-rendered | Mock fallback | Page consume site |
|---|---|---|---|---|
| Dashboard / summary | /api/claims/summary (+ /api/claims) | Yes | Yes (`overviewMetrics`/`claimRows`) | DashboardPage.tsx:40-56 |
| Claims List | /api/claims | Yes | Yes (`claimRows`) | ClaimsListPage.tsx:29 |
| CLM-1006 Workspace + header | /api/claims/{id} | Yes | Yes (`goldenClaim`) | ClaimWorkspacePage.tsx:22 / ClaimHeader.tsx:9 |
| Documents/Photos | /api/claims/{id}/documents | Yes | Yes | DocumentsPhotosPage.tsx:31-35 |
| AI Evidence | /api/claims/{id}/ai-evidence | Yes | Yes | AiEvidencePage.tsx:29-33 |
| Risks | /api/claims/{id}/risks | Yes | Yes | RisksChecksPage.tsx:19-22 |
| Policy/Coverage | /api/claims/{id}/policy | Yes | Yes | PolicyCoveragePage.tsx:15-17 |
| Customer/Vehicle | /api/claims/{id}/customer-vehicle | Yes | Yes | CustomerVehiclePage.tsx:15-17 |
| Human Approval (read) | /api/claims/{id}/approval | Yes | Yes | HumanApprovalPage.tsx:45-59 |
| Audit/Cost | /api/claims/{id}/audit (+ risks.pipeline) | Yes | Yes | AuditCostPage.tsx:30-41 |
| Demo Scenario | /api/demo/scenario | Yes | Yes | DemoScenarioPage.tsx:19-20 |
Every page binds the rendered variable to `selector ?? mock` (or `selector?.x ?? mock`) and the JSX iterates the resolved variable — not the raw mock import. No dead-ends.

## MOCK FALLBACK / LOADING / ERROR / API MODE
Default mode `mock` (no backend needed; baseline-identical). Backend mode: sagas load summary + claim detail + all 8 per-claim sub-resources + demo scenario; success → `apiMode:'backend'`; failure → `apiMode:'mock-fallback'` + `error` (surfaced, not silent) and renders mock. `loading`/`error`/`apiMode` carried per resource in claimsSlice / claimWorkspaceSlice / demoSlice.

## FRONTEND BUILD RESULT
PASS — `npm run build` (`tsc -b && vite build`) → exit 0, 106 modules, `✓ built in ~3.4s`, zero TS errors (independently re-verified).

## BACKEND BUILD / TEST RESULT
Backend source unchanged (`git diff --name-only HEAD -- server` empty). Green at `3f07df7`: `dotnet build` 0/0, `dotnet test` Passed 14 / Failed 0.

## LOCAL BACKEND SMOKE
All 9 newly-relevant endpoints curl'd live (backend started, smoked, stopped): `/api/claims/summary`, `/CLM-1006/{documents,ai-evidence,risks,policy,customer-vehicle,approval,audit}`, `/api/demo/scenario` — camelCase shapes confirmed matching the client DTO mappers.

## FRONTEND/BROWSER SMOKE
Build + type-check only. **No browser/visual test** (no headless browser available). Manual verification: run backend (`dotnet run --project server/InsuranceAIPlatform.Api`), then `VITE_INSURANCE_API_MODE=backend npm run dev`, and walk all 11 screens confirming live data; stop the backend to confirm the mock-fallback indicator.

## SCREENSHOTS
None captured (no headless browser). Manual steps above.

## SAFETY SCAN
Clean — no keys/tokens/passwords/connection strings/real PII/real external URLs in changed `src/**`. Only host referenced is `http://localhost:5284`. No `.env`, no new dependency.

## QA GATE
Independent Opus inspector — **CLEARED 8/8**: build clean; changeset 20 files all `src/`; no dead-ends (per-page render site verified at file:line for all 9 newly-wired pages); facade GET-only with full 11-endpoint coverage; read-only preserved (zero write methods reach backend; write buttons stubbed); mock fallback non-lossy + surfaced; no secrets; no commit/push (HEAD still 3f07df7).

## ISSUES / LIMITATIONS
- **No browser/visual test** (no headless browser): evidence is build + type-check + backend curl + independent code review. A manual browser pass is recommended before any demo (a dedicated MANUAL_BROWSER_VERIFY gate is the natural follow-up).
- Minor: Dashboard narrative copy and a dashboard-level AI-recommendation widget keep static mock text (data values flow through the store); pipeline steps on Audit/Cost are sourced from the `/risks` DTO's `pipeline` array (no separate endpoint). Documented, non-blocking.

## CONFIRMATION — FORBIDDEN SCOPE
No commit · no push · no merge · no `main` · no `server/**` change · no DB/SQL/DevDept · no EF Core/migrations/DbContext · no backend write endpoints · no upload/approval-submit/payout · no `package.json`/lock/new-dependency · no `.env`/`appsettings.Production.json` · no secrets/real PII · no Azure · no AI provider · no real external API calls.

## NEXT SAFE STEP
A separately-approved **`COMMIT_AND_PUSH_DEV_REMAINING_READ_TABS_INTEGRATION_ONLY`** — commit the 20 `src/**` files on `dev` + fast-forward push to `origin/dev` (main untouched). Then a **`MANUAL_BROWSER_VERIFY_FRONTEND_BACKEND_READ_FLOW`** pass before DB/EF/Azure/AI work.
