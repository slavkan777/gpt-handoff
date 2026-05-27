# InsuranceAIPlatform — Frontend↔Backend Read-Flow Integration V0.1 — Handoff

**Gate:** `FRONTEND_BACKEND_READ_FLOW_INTEGRATION_V0.1` · **Date:** 2026-05-27
**Scope:** wire the frontend read flow to the local .NET read-only API with an API mode switch + safe mock fallback, preserving the UI baseline. Local only — no commit/push, no DB/EF/Azure/AI/write endpoints, no server changes.

## CURRENT STATE
A frontend↔backend read integration layer is in place on `dev` (local, uncommitted). A mode-switching API facade routes the app's data calls to either the live .NET read-only API (`http://localhost:5284`) or the existing mock, defaulting to **mock** so the build and app always work with no backend running. The **primary CLM-1006 flow — claims list + claim workspace (overview body + header) — now renders backend data in backend mode**, with a non-lossy mock fallback that keeps mock mode byte-identical to the accepted baseline. Frontend build PASS; backend untouched and still green (14/14). No commit/push.

## IMPLEMENTATION STATUS: implemented (local, uncommitted).

## BRANCH / HEAD
- Branch: `dev`. HEAD before & after (no commit this gate): `7a9d637` (`7a9d637d4e92ce67a90d983ff5a2dae929b20113`). `origin/dev` unchanged at `7a9d637`.

## API MODE DESIGN
- `src/api/insuranceApi.ts` — facade. `API_MODE` from `import.meta.env.VITE_INSURANCE_API_MODE` (**default `'mock'`**; set `backend` in `.env.local`, never committed). Exposes `insuranceApi: typeof mockInsuranceApi` so the backend impl is forced (compile-time) to match every mock signature; callers/sagas import one stable seam.
- `src/api/backendInsuranceApi.ts` — `fetch`-based client. Base URL `VITE_INSURANCE_API_BASE_URL` (default `http://localhost:5284`). **GET-only / read-only** (no method/body anywhere). Parses camelCase JSON; maps backend DTOs → existing frontend types (date/relative-time/severity→tone/pipeline-status mappers). Non-2xx (incl. 400 `INVALID_CLAIM_ID` / 404 `CLAIM_NOT_FOUND`) → typed `BackendApiError` (never raw stack to UI). Write-ish functions (`runMockAiAnalysis`, `saveApprovalDraft`, `sendCustomerRequest`) are stubbed and do NOT hit the backend.
- Mock fallback that does not hide errors: in backend mode, if a backend call fails, the saga sets `apiMode:'mock-fallback'` + `error` in state and renders mock; a small degraded-mode badge shows on the claims list. A 4xx is surfaced as a typed error, not a silent swap.

## FILES CHANGED (12, all `src/**`)
**Created (3):** `src/api/backendInsuranceApi.ts`, `src/api/insuranceApi.ts`, `src/features/claims/claimsSaga.ts`.
**Modified (9):** `src/app/rootSaga.ts` (fork claimsSaga + boot dispatch loadClaimsQueue/loadClaimDetail), `src/features/approval/approvalSaga.ts` (import via facade), `src/features/claims/claimsSlice.ts` + `claimsSelectors.ts` (loading/error/apiMode + load actions + `selectClaimsQueue`), `src/features/claims/claimWorkspaceSlice.ts` + `claimWorkspaceSelectors.ts` (claimDetail/loading/error/apiMode + `selectClaimDetail`), `src/pages/ClaimsListPage.tsx` (table from `selectClaimsQueue`, mock fallback), `src/pages/ClaimWorkspacePage.tsx` (`c = claimDetail ?? goldenClaim`), `src/components/claim/ClaimHeader.tsx` (header from `selectClaimDetail`, mock fallback).

## BACKEND ENDPOINTS CONSUMED
The client implements GET calls for **9 of the 11** read endpoints: `/api/claims`, `/api/claims/{id}`, `/api/claims/{id}/documents` (serves both documents and photos), `/ai-evidence`, `/risks`, `/policy`, `/customer-vehicle`, `/audit`, `/api/demo/scenario`. Not wired to a frontend function: `/api/claims/summary` and `/api/claims/{id}/approval` (read) — no current consumer. Of the 9, **2 flows are actually rendered in the UI in backend mode**: the claims list and the CLM-1006 workspace (overview body + header). The other 7 client functions exist and are facade-routed but their pages still read direct mock (see below).

## FRONTEND SCREENS VERIFIED (11 render; build proves compile)
| Screen | Backend-mode source | Status |
|---|---|---|
| Claims List | `selectClaimsQueue` ← GET /api/claims | **backend-wired** |
| CLM-1006 Workspace (overview + header) | `selectClaimDetail` ← GET /api/claims/CLM-1006 | **backend-wired** |
| Dashboard | mostly direct mock metrics | mock fallback |
| Documents/Photos | direct mock | mock fallback (client fn ready) |
| AI Evidence | direct mock | mock fallback (client fn ready) |
| Risks | direct mock | mock fallback (client fn ready) |
| Policy/Coverage | direct mock | mock fallback (client fn ready) |
| Customer/Vehicle | direct mock | mock fallback (client fn ready) |
| Human Approval | read mock; writes via facade (stay mock) | mock fallback |
| Audit/Cost | direct mock | mock fallback (client fn ready) |
| Demo Scenario | direct mock (demoSaga) | mock fallback (client fn ready) |

## MOCK FALLBACK / LOADING / ERROR
- Default mock mode: data from mock; `apiMode:'mock'`; no network; baseline-identical.
- Backend mode: `loadClaimsQueue` + `loadClaimDetail` fetch live; success → `apiMode:'backend'`; failure → `apiMode:'mock-fallback'` + `error` in slice state + degraded badge on the list; 4xx surfaced as typed error (not silent). `loading`/`error`/`apiMode` added to `claimsSlice` and `claimWorkspaceSlice`. Non-lossy fallbacks (`storeRows.length>0 ? storeRows : claimRows`; `claimDetail ?? goldenClaim`) prevent null-flash and keep mock mode identical.

## BUILD RESULT
Frontend: PASS — `npm run build` (`tsc -b && vite build`) → exit 0, 105 modules, `✓ built in ~4.4s`, zero TS errors (independently re-verified).

## BACKEND TEST RESULT
Backend unchanged (`git diff HEAD -- server` empty). Re-verified green: `dotnet build` 0/0; `dotnet test` Passed 14 / Failed 0.

## LOCAL INTEGRATION SMOKE
Backend started locally; `curl` confirmed camelCase shapes match the client DTOs: `/api/claims/CLM-1006` (id, customer "Роберт Джонсон", eventDate "2026-05-18", slaDeadline ISO, riskScore 82…), `/api/claims` (list, documentsCount "6/7"), `/api/claims/CLM-1006/risks` (score 82, threshold, factors), `/api/demo/scenario`. Backend process stopped after smoke.

## SCREENSHOTS
None captured — no headless browser available in this environment. Manual verification: run backend (`dotnet run --project server/InsuranceAIPlatform.Api`), then `VITE_INSURANCE_API_MODE=backend npm run dev`, open the claims list + CLM-1006 workspace, confirm live data (and stop the backend to see the mock-fallback badge).

## SAFETY SCAN
Clean — no API keys/tokens/passwords/connection strings/real PII/real external URLs in changed `src/**`. Only host referenced is `http://localhost:5284`. No `.env` created/committed. No new npm dependency (native `fetch`).

## QA GATE
Independent Opus inspector reviewed, returned **REWORK** (the selectors were dead-ended in the store — no page consumed them), the rework was applied (2 pages + the `ClaimHeader` sub-component now consume `selectClaimsQueue`/`selectClaimDetail` with mock fallback), and the inspector **re-verified CLEARED 4/4** at file:line. Read-only client, typed mock-fallback, backend untouched + green all re-confirmed.

## CONFIRMATION — FORBIDDEN SCOPE
No commit · no push · no merge · no `main` · no DB/SQL/DevDept · no EF Core · no migrations · no DbContext · no backend write endpoints · no `server/**` change · no `package.json`/lock change · no new dependency · no `.env` with secrets · no `appsettings.Production.json` · no secrets/real PII · no Azure · no AI provider · no real external API calls.

## ISSUES / LIMITATIONS
- **Scope (V0.1, agreed):** only the primary CLM-1006 list→detail+header flow renders backend data; 7 detail tabs + dashboard + demo remain on compatible mock fallback (client functions exist, facade-routed; wiring their pages needs new sagas/slices = a future gate). This satisfies the gate's item-7 OR-clause.
- **No browser/visual test:** verification is build + type-check + backend curl + code review; the live UI was not visually exercised headlessly (no screenshots). A manual browser pass is recommended before demo.
- `/api/claims/summary` and `/api/claims/{id}/approval` (read) have no frontend consumer yet.

## NEXT SAFE STEP
Separately-approved **`COMMIT_AND_PUSH_DEV_FRONTEND_BACKEND_READ_FLOW_INTEGRATION_ONLY`** — commit the `src/**` integration on `dev` and fast-forward push to `origin/dev` (main untouched). A later gate can wire the remaining detail tabs and add a visual/browser verification pass.
