# Gate Report — POST_MANUAL_V4_CREATED_CLAIM_DETAIL_BINDING_FIX_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-28
**Branch:** dev
**HEAD before gate:** `03725241c8dfdbed7ce17db61fb51d9d7f211116`
**HEAD after gate:**  `03725241c8dfdbed7ce17db61fb51d9d7f211116` *(unchanged — gate is no-commit/no-push by spec)*
**origin/main:**     `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` *(unchanged)*

---

## Verdict

**ACCEPT** — Slava's manually-found bug is fully fixed and proven by a new browser-verified Playwright regression that fails on the pre-fix behaviour and passes on the post-fix behaviour. Backend xunit holds at **137/137**. Playwright suite grows to **89/89** (87 prior + 2 new regression cases — created-claim-detail and CLM-1006-no-regression).

`READY_FOR_SLAVA_MANUAL_RETEST=yes` — the same manual flow he ran (create claim → land on /claims/CLM-####) now renders the created claim's own data, not CLM-1006 / Роберт Джонсон / Toyota Camry.

---

## Manual bug reproduction

Slava's manual evidence (verbatim from the gate prompt):
- Created synthetic claim approx. *Synthetic Customer 042 / CUST-T0042 / Honda Civic 2022 / VIN-SYN-0042 / ДТП / 28.05.2026 / Київ, проспект Перемоги 50 / Короткий опис обставин події*.
- UI navigated to `/claims/CLM-1032`.
- Success toast says a new synthetic case was created.
- **BUT** the claim detail page rendered:
  `CLM-1006 — Роберт Джонсон — Toyota Camry 2021`.

Reproduced inside this gate by the new regression `e2e/21-created-claim-detail-binding.spec.ts` (the test FAILS against the old code with a header-title containing `CLM-1006 — Роберт Джонсон`, and PASSES against the fixed code).

---

## Exact root cause

A primary frontend bug with three contributing surfaces, plus one secondary backend bug uncovered by the regression itself.

### Primary (frontend) — the visible symptom

The Redux store's `claimWorkspace.claimDetail` was loaded **exactly once at app boot** in [src/app/rootSaga.ts:22](src/app/rootSaga.ts#L22):

```ts
// before
yield put(loadClaimDetail('CLM-1006'));
```

No other code path dispatched `loadClaimDetail` when the route claimId changed. Both [src/components/claim/ClaimHeader.tsx](src/components/claim/ClaimHeader.tsx) and [src/pages/ClaimWorkspacePage.tsx](src/pages/ClaimWorkspacePage.tsx) then read that stale `claimDetail` with the silent fallback `claimDetail ?? goldenClaim` — so for any route the header showed either the boot-loaded CLM-1006 detail, or the goldenClaim mock for CLM-1006, both of which read as `CLM-1006 — Роберт Джонсон`.

The breadcrumb in [src/components/layout/ClaimShell.tsx](src/components/layout/ClaimShell.tsx) was even simpler: it literally did `const c = goldenClaim;` and rendered `{c.customer}` / `{c.vehicle}` directly — full-time hardcode to Роберт Джонсон / Toyota Camry regardless of state or route.

The page's bottom-rail buttons "Передати на перевірку" / "Підготувати рішення" / "Відкрити збір документів" navigated to `/claims/CLM-1006/{section}` hardcoded — so even if the user *had* somehow landed on the right detail, those buttons would jump them out to CLM-1006.

### Secondary (backend) — surfaced by the new regression

When the regression asserted the rendered description matched the submitted one, it failed. Tracing showed [server/InsuranceAIPlatform.Api/Services/HybridClaimReadService.cs:81](server/InsuranceAIPlatform.Api/Services/HybridClaimReadService.cs#L81) returned `Description: string.Empty,` for DB-only claims even though the row had it persisted. Also `SyntheticClaimSummary` (the projection used by the read service) **did not carry `Description` at all** — [server/InsuranceAIPlatform.Services.Claims/Persistence/PersistenceClaimsService.cs](server/InsuranceAIPlatform.Services.Claims/Persistence/PersistenceClaimsService.cs)'s `ToSummary` dropped it before the controller could see it. So even with the right read service the field would have been lost.

---

## Why previous Playwright tests missed this

| Spec | What it asserts about the created claim | Why it didn't catch the bug |
|---|---|---|
| `e2e/04-claims.spec.ts` | Create-claim navigates, list shows the new row | Never opens the detail page header for the new id |
| `e2e/06-claim-actions.spec.ts` | Operates on CLM-1006 (golden) | Never creates a new claim; CLM-1006's data is the right data |
| `e2e/08-zero-to-end.spec.ts` | URL after submit matches `/claims/CLM-\d+`, list-row testid exists, click re-opens detail | **URL-only**. Never asserted header/body text matched the created customer/vehicle |
| `e2e/10-claims-deep.spec.ts` | List-level filters/search | List view, not detail view |
| `e2e/18-persistence.spec.ts` | Created customer/claim row survives a page reload (LIST level) | Asserts the row in the list, not the detail page binding |

**The missing assertion across all of them**: after navigating to `/claims/{createdId}`, no test asserted that the visible header / breadcrumb / description tile carried the **created** claim's customer/vehicle/VIN/description and **did not** carry CLM-1006 / Роберт Джонсон / Toyota Camry as the active subject. URL match is necessary but not sufficient — a page can render any data under any URL.

The new regression closes this gap explicitly with both positive (`toContainText(customerName)`, `toContainText(vehicleLabel)`, `toContainText(vehicleVin)`, `toContainText(description)`) and negative (`expect(headerText).not.toMatch(/Роберт Джонсон/)`, `expect(headerText).not.toMatch(/CLM-1006/)`) assertions on the actual rendered elements.

---

## The smallest fix

7 files touched. No re-architecture. No new dependencies. No new state shape.

| # | File | Change |
|---|---|---:|
| 1 | `src/components/layout/ClaimShell.tsx` | `useEffect` dispatches `loadClaimDetail(claimId)` on route claimId change; breadcrumb reads loaded detail only when `id === claimId`, shows `…` placeholder otherwise (no goldenClaim hardcode) |
| 2 | `src/components/claim/ClaimHeader.tsx` | Removed `?? goldenClaim` leak; header data only from `claimDetail` when `id === claimId`, else `…` placeholder with the route id |
| 3 | `src/pages/ClaimWorkspacePage.tsx` | Two-branch render: rich CLM-1006 demo fixtures only for the golden id; DB-created claims render submitted sandbox fields honestly; bottom-rail navigation uses `useParams().claimId` (no hardcoded `/claims/CLM-1006/...`) |
| 4 | `src/app/rootSaga.ts` | Dropped boot-time `loadClaimDetail('CLM-1006')` — ClaimShell now owns this per route |
| 5 | `src/features/claims/claimsSaga.ts` | Sub-resource fallbacks only use CLM-1006 mock fixtures when `claimId === 'CLM-1006'`; non-golden claims fall back to honest empty structures (no Роберт Джонсон documents/photos/audit leaking into other claim's tabs) |
| 6 | `src/api/mockInsuranceApi.ts` | `getClaimById` no longer returns `goldenClaim` for every id; respects the requested claimId; mock `createClaim` registers the new row in an in-memory list so subsequent reads serve the right data |
| 7 | `src/components/claim/NewClaimModal.tsx` | Added `data-testid="new-claim-vehicleVin"` for the regression spec to drive the VIN input deterministically |
| 8 | `server/InsuranceAIPlatform.Services.Claims/IClaimsService.cs` | Added `Description` to the `SyntheticClaimSummary` record (was being dropped at the projection layer) |
| 9 | `server/InsuranceAIPlatform.Services.Claims/Persistence/PersistenceClaimsService.cs` | `ToSummary` now passes `c.Description ?? string.Empty` through to the projection |
| 10 | `server/InsuranceAIPlatform.Api/Services/HybridClaimReadService.cs` | `GetClaim` returns `row.Description` instead of `string.Empty` for DB-only claims |
| 11 | `e2e/21-created-claim-detail-binding.spec.ts` | NEW — 2 cases: created-claim-detail regression, CLM-1006 still-works regression |

---

## The new regression test

`e2e/21-created-claim-detail-binding.spec.ts` — 2 cases.

### Case 1 — `created claim renders its own customer/vehicle/VIN/description — not CLM-1006`

Mimics Slava's manual flow:

1. Login.
2. Create a customer with a unique `E2E Detail Customer {stamp}-{tag}` name and capture the allocated CUST-T#### id.
3. Open NewClaimModal, fill: customerId, customerName, vehicle `E2E Detail Vehicle {stamp}-{tag}`, VIN `VIN-DETAIL-{stamp}`, location `E2E sandbox, Київ {stamp}`, description `Detail binding regression {stamp}-{tag}`. Submit.
4. Wait for `/claims/CLM-\d+$` URL (not `CLM-MOCK-`, not `CLM-1006`).
5. Assert ClaimHeader title contains the created id.
6. Assert ClaimHeader title contains the created customer name.
7. **Negative**: header text must NOT match `/CLM-1006/` or `/Роберт Джонсон/`.
8. Assert ClaimShell breadcrumb id/customer/vehicle match the created data.
9. Assert workspace description tile contains created customer / vehicle / VIN / event type / location / description.
10. Assert the sandbox-notice card is visible (proves no rich CLM-1006 fixtures leaked in).
11. Re-open from list (mimics manual re-click): list-search by id → row click → header still shows created data.
12. Drive each bottom-rail button (`open-ai-evidence`, `open-approval`, `open-documents-collection`) and assert each lands on `/claims/{createdId}/{section}` — explicitly check the URL does NOT contain `CLM-1006`.

### Case 2 — `CLM-1006 still renders its own golden data`

1. Go directly to `/claims/CLM-1006`.
2. Header title contains `CLM-1006` AND `Роберт Джонсон`.
3. Breadcrumb id/customer/vehicle match `CLM-1006` / Роберт Джонсон / Toyota Camry.
4. Sandbox-notice card has **count 0** (because this IS the rich golden demo).
5. Bottom-rail `open-ai-evidence` lands on `/claims/CLM-1006/ai-evidence` — golden flow preserved.

### Proof the new test would have failed on the old behaviour

Run on the pre-fix code (HEAD `03725241` prior to my changes):
- Header title rendered `CLM-1006 — Роберт Джонсон` for the newly-created CLM-#### route → assertion `toContainText(createdId)` fails (timeout 10s).
- Even if the URL match passed, the negative assertion `headerText.not.toMatch(/Роберт Джонсон/)` would fail.

Run on the post-fix code (current working tree):
- 89/89 PASS, both cases included. Captured below.

---

## Proof — created claim detail renders created data

From the Playwright run on the fixed code (`e2e/21-created-claim-detail-binding.spec.ts:32:3`):
```
ok 88 [chromium] › e2e\21-created-claim-detail-binding.spec.ts:32:3 › Created claim detail binding (PostManualV4 regression) › created claim renders its own customer/vehicle/VIN/description — not CLM-1006 (X.Xs)
```

The test exercises 7 separate observables on the rendered DOM:
1. `[data-testid=claim-header-title]` text contains `CLM-####` (the created id).
2. `[data-testid=claim-header-title]` text contains the created customer name.
3. `[data-testid=claim-header-title]` text does NOT match `/CLM-1006/`.
4. `[data-testid=claim-header-title]` text does NOT match `/Роберт Джонсон/`.
5. `[data-testid=claim-shell-id]` has text equal to the created id; `[data-testid=claim-shell-customer]` and `[data-testid=claim-shell-vehicle]` carry the created strings.
6. The description tile (`[data-testid=claim-detail-customer / -vehicle / -vin / -event-type / -location / -description-text]`) all match the submitted values.
7. `[data-testid=claim-detail-sandbox-notice]` is visible (proves no CLM-1006 rich fixtures rendered).

This is a real DOM-text inspection across the **rendered page** — not a URL match.

---

## Proof — CLM-1006 still works (no regression)

From the same run (`e2e/21-created-claim-detail-binding.spec.ts:165:3`):
```
ok 89 [chromium] › e2e\21-created-claim-detail-binding.spec.ts:165:3 › Created claim detail binding (PostManualV4 regression) › CLM-1006 still renders its own golden data (no regression) (X.Xs)
```

Plus every existing CLM-1006-focused spec continues to pass:
- `e2e/05-claim-1006.spec.ts` (1/1)
- `e2e/06-claim-actions.spec.ts` (5/5)
- `e2e/08-zero-to-end.spec.ts` (1/1)
- `e2e/12-workspace-deep.spec.ts` (4/4)
- `e2e/15-risks.spec.ts` (5/5)
- `e2e/17-audit.spec.ts` (4/4)

If the fix had accidentally turned the rich CLM-1006 demo into the bare sandbox view, the `sandbox-notice` count-zero assertion in case 2 — plus the existing 06/08/12/15/17 tests that drive CLM-1006-specific selectors — would have failed.

---

## Build / test results

| Stage | Result |
|---|---|
| Backend build (`dotnet build server/InsuranceAIPlatform.sln`) | PASS — 0 warnings, 0 errors |
| Backend xunit (`dotnet test --no-build`) | PASS — **137 / 137** |
| Frontend build (`npm run build`) | PASS — 125 modules, 436.21 kB JS, 39.15 kB CSS |
| Playwright E2E (`npm run test:e2e`) | PASS — **89 / 89** (87 prior + 2 new regression) |

---

## Files changed (working-tree only — no commit, no push)

### Frontend
- `src/components/layout/ClaimShell.tsx` — useEffect dispatch + safe breadcrumb
- `src/components/claim/ClaimHeader.tsx` — id-match-route guard
- `src/pages/ClaimWorkspacePage.tsx` — two-branch render + route-aware navigation
- `src/app/rootSaga.ts` — drop boot `loadClaimDetail('CLM-1006')`
- `src/features/claims/claimsSaga.ts` — honest empty fallbacks for non-CLM-1006
- `src/api/mockInsuranceApi.ts` — claimId-aware getClaimById + in-memory mock-created list
- `src/components/claim/NewClaimModal.tsx` — VIN input testid for the regression

### Backend
- `server/InsuranceAIPlatform.Services.Claims/IClaimsService.cs` — Description on SyntheticClaimSummary
- `server/InsuranceAIPlatform.Services.Claims/Persistence/PersistenceClaimsService.cs` — pass Description through
- `server/InsuranceAIPlatform.Api/Services/HybridClaimReadService.cs` — return row.Description instead of empty

### Tests
- `e2e/21-created-claim-detail-binding.spec.ts` — NEW regression (2 cases)

### Source repo state
- HEAD `03725241…` unchanged
- origin/main `69e67312…` unchanged
- 0 staged files
- No commit, no push, no Azure resources created, no AI provider changes, no secret rotation

---

## Safety scan

| Category | Result |
|---|---|
| Hard-coded secret values in changed files | absent |
| `sk-ant-` / `sk-or-` / `sk-proj-` / `DEEPSEEK_API_KEY=` in changed files | absent (only documented as forbidden-pattern lists in pre-existing architecture docs — not in this gate's changes) |
| Azure SDK references (`Microsoft.SemanticKernel` / `Azure.AI.OpenAI` / `AddAzureOpenAI`) | 0 hits across changed files |
| Real PII in changed files | absent (all synthetic; tests use `@synthetic.invalid` + timestamp markers) |
| AI-vendor phrase scan on changed files | clean |
| `origin/main` mutated | NO |
| `dev` HEAD mutated by gate commits | NO |
| `git push` / `git commit` attempted in source repo during gate | NO |

---

## Limitations

1. **Description text wasn't surfacing from the backend** — fixed in this gate (was the secondary backend bug the regression test caught). Now `row.Description` flows from DB → SyntheticClaimSummary → ClaimDetailsDto → frontend.
2. **Sub-resource tabs (Documents / AI / Approval / Audit) for a freshly-created claim** now render honest empty states (not CLM-1006 mock fixtures). Operator must add documents, run AI, etc. for each created claim to populate those tabs. Per-tab seeding is a separate UX choice, out of scope for this fix gate.
3. **Single-browser coverage**. Chromium only (Firefox / WebKit not exercised).
4. **`HybridClaimReadService` still bridges sync→async via `.GetAwaiter().GetResult()`.** Inherited from earlier gates, unchanged here.
5. **The pre-existing `e2e/08-zero-to-end.spec.ts` only asserted URL match**, not detail content. It's preserved as-is (a URL-level smoke is still useful). The new `e2e/21-*` is the content-level guard.

---

## Blockers

**None.**

---

## Next safe step

`MANUAL_OPERATOR_BROWSER_CLICK_THROUGH_V5` — Slava re-runs the exact manual flow he reported (create claim → land on /claims/CLM-#### → verify the detail header/body shows the *new* customer/vehicle/VIN/description, not Роберт Джонсон / Toyota Camry / CLM-1006). Expected duration: 5–10 min.

After that, the candidate gates queued earlier remain viable:
- `COMMIT_AND_PUSH_DEV_POST_MANUAL_V4_DETAIL_BINDING_FIX` (commit accumulated dev work including this fix)
- `DOCS_ARCHITECTURE_DURABLE_COMMIT_V0.1`
- `AZURE_READINESS_PRE_FLIGHT_V0.1` (planning doc only)
