# InsuranceAIPlatform — Latest gate report

**Gate:** POST_MANUAL_V4_CREATED_CLAIM_DETAIL_BINDING_FIX_V0.1
**Date (UTC):** 2026-05-28
**Branch:** dev
**HEAD before:** `03725241c8dfdbed7ce17db61fb51d9d7f211116`
**HEAD after:**  `03725241c8dfdbed7ce17db61fb51d9d7f211116` *(unchanged — gate is no-commit/no-push by spec)*
**origin/main:**  `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` *(unchanged)*

**Full report:** [post-manual-v4-created-claim-detail-binding-fix-v0.1/report.md](post-manual-v4-created-claim-detail-binding-fix-v0.1/report.md)
**Playwright run details:** [post-manual-v4-created-claim-detail-binding-fix-v0.1/e2e-results.md](post-manual-v4-created-claim-detail-binding-fix-v0.1/e2e-results.md)

---

## REPORT BACK FORMAT

```
VERDICT: ACCEPT
GATE: POST_MANUAL_V4_CREATED_CLAIM_DETAIL_BINDING_FIX_V0.1
BRANCH: dev
HEAD_BEFORE: 03725241c8dfdbed7ce17db61fb51d9d7f211116
HEAD_AFTER:  03725241c8dfdbed7ce17db61fb51d9d7f211116
ORIGIN_MAIN: 69e67312a10cc9bcf28c4e387a126b48c91fb9c5
GIT_STATUS_SUMMARY: working-tree only changes across 7 frontend files + 3 backend files + 1 new e2e spec; 0 staged; no commit; no push
MANUAL_BUG_REPRODUCED: yes — reproduced by e2e/21-created-claim-detail-binding.spec.ts (case 1) which fails on the pre-fix code and passes on the fixed code
ROOT_CAUSE: rootSaga.ts:22 dispatched loadClaimDetail('CLM-1006') ONLY at app boot; no per-route re-dispatch existed, so Redux state stayed locked on CLM-1006 forever. ClaimShell.tsx breadcrumb literally hardcoded `c = goldenClaim`. ClaimHeader.tsx and ClaimWorkspacePage.tsx used `claimDetail ?? goldenClaim` so for any non-CLM-1006 route they leaked CLM-1006 customer/vehicle. ClaimWorkspacePage's bottom-rail buttons navigated to /claims/CLM-1006/... hardcoded. Secondary: HybridClaimReadService returned Description: string.Empty (and SyntheticClaimSummary did not even carry Description) so the submitted description was dropped on the read path even after the FE was fixed.
FIX_SUMMARY: ClaimShell dispatches loadClaimDetail(claimId) on route change; header/breadcrumb/page render loaded detail only when id === route claimId (no goldenClaim fallback for non-golden routes); bottom-rail navigates via useParams().claimId; rootSaga boot dispatch removed; sub-resource fallbacks empty-honest for non-golden ids; mock getClaimById is claimId-aware; SyntheticClaimSummary + ToSummary + HybridClaimReadService now carry Description through.
CREATED_CLAIM_DETAIL_BINDING: pass — header / breadcrumb / description tile all carry created customer/vehicle/VIN/event-type/location/description; sandbox notice visible; no CLM-1006 leak in DOM
CLM_1006_REGRESSION: pass — direct nav to /claims/CLM-1006 still shows CLM-1006 + Роберт Джонсон + Toyota Camry; rich timeline/photos/STO/key-risks fixtures still render; bottom-rail still navigates within CLM-1006
PLAYWRIGHT_REGRESSION: e2e/21-created-claim-detail-binding.spec.ts — 2 cases, both PASS (would FAIL on pre-fix code)
E2E_RESULTS: 89 / 89 PASS (87 prior spec files + 1 new spec file with 2 new cases; 0 flaky; 0 retries)
FILES_CHANGED: src/components/layout/ClaimShell.tsx; src/components/claim/ClaimHeader.tsx; src/pages/ClaimWorkspacePage.tsx; src/app/rootSaga.ts; src/features/claims/claimsSaga.ts; src/api/mockInsuranceApi.ts; src/components/claim/NewClaimModal.tsx; server/InsuranceAIPlatform.Services.Claims/IClaimsService.cs; server/InsuranceAIPlatform.Services.Claims/Persistence/PersistenceClaimsService.cs; server/InsuranceAIPlatform.Api/Services/HybridClaimReadService.cs; e2e/21-created-claim-detail-binding.spec.ts (NEW)
BACKEND_BUILD: PASS — 0 warnings, 0 errors, 10 projects
BACKEND_TESTS: PASS — 137 / 137 (xunit)
FRONTEND_BUILD: PASS — 125 modules, 436.21 kB JS, 39.15 kB CSS
SAFETY_SCAN: clean — no secret values, 0 Azure SDK refs, no real PII, AI-vendor phrase scan on changed files clean, origin/main unchanged, source dev HEAD unchanged
BLOCKERS: none
LIMITATIONS:
  1) Description fix required a small backend change (SyntheticClaimSummary + ToSummary + HybridClaimReadService) — surfaced by the regression itself; fixed in this gate.
  2) Sub-resource tabs for fresh DB-created claims render honest empty states; per-tab seeding for new claims is out of scope.
  3) Single-browser (Chromium) coverage; Firefox/WebKit not exercised.
  4) HybridClaimReadService sync→async via .GetAwaiter().GetResult() inherited from prior gate; unchanged.
  5) Pre-existing 08-zero-to-end was URL-only; preserved as-is; new 21-* is the content-level guard.
READY_FOR_SLAVA_MANUAL_RETEST: yes
GITHUB_HANDOFF_PATHS:
  - InsuranceAIPlatform/latest-report.md
  - InsuranceAIPlatform/latest-summary.json
  - InsuranceAIPlatform/post-manual-v4-created-claim-detail-binding-fix-v0.1/report.md
  - InsuranceAIPlatform/post-manual-v4-created-claim-detail-binding-fix-v0.1/e2e-results.md
NEXT_SAFE_STEP: MANUAL_OPERATOR_BROWSER_CLICK_THROUGH_V5 — Slava re-runs the exact manual flow he reported (create claim → land on /claims/CLM-#### → verify visible detail shows the new customer/vehicle/VIN/description, not CLM-1006). After that the queued candidate gates remain: COMMIT_AND_PUSH_DEV_*, DOCS_ARCHITECTURE_DURABLE_COMMIT_V0.1, AZURE_READINESS_PRE_FLIGHT_V0.1.
```
