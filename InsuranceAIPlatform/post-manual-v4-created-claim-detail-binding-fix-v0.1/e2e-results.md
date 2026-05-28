# Playwright E2E Results — POST_MANUAL_V4_CREATED_CLAIM_DETAIL_BINDING_FIX_V0.1

**Run date (UTC):** 2026-05-28
**Browser:** Chromium (Playwright v1223)
**Workers:** 1 (serial, shared LocalDB)
**Retries:** 0

## Aggregate

| Outcome | Count |
|---|---:|
| Expected (passed) | **89** |
| Unexpected (failed) | 0 |
| Flaky | 0 |
| Skipped | 0 |

## Per-spec breakdown

| Spec | Cases | Status |
|---|---:|---|
| `01-auth.spec.ts` | 2 | PASS |
| `02-sidebar.spec.ts` | 8 | PASS |
| `03-customers.spec.ts` | 3 | PASS |
| `04-claims.spec.ts` | 2 | PASS |
| `05-claim-1006.spec.ts` | 1 | PASS |
| `06-claim-actions.spec.ts` | 5 | PASS |
| `07-safety.spec.ts` | 10 | PASS |
| `08-zero-to-end.spec.ts` | 1 | PASS |
| `09-dashboard.spec.ts` | 9 | PASS |
| `10-claims-deep.spec.ts` | 5 | PASS |
| `11-customers-deep.spec.ts` | 4 | PASS |
| `12-workspace-deep.spec.ts` | 4 | PASS |
| `13-documents-deep.spec.ts` | 4 | PASS |
| `14-ai-deep.spec.ts` | 5 | PASS |
| `15-risks.spec.ts` | 5 | PASS |
| `16-approval-validation.spec.ts` | 4 | PASS |
| `17-audit.spec.ts` | 4 | PASS |
| `18-persistence.spec.ts` | 2 | PASS |
| `19-negative.spec.ts` | 4 | PASS |
| `20-ui-resilience.spec.ts` | 5 | PASS |
| `21-created-claim-detail-binding.spec.ts` (NEW) | 2 | PASS |
| **Total** | **89** | **89 / 89 PASS** |

## Coverage delta vs prior gate

| Metric | Prior gate V0.1 | This gate V0.1 | Delta |
|---|---:|---:|---:|
| Spec files | 20 | 21 | **+1** |
| Test cases | 87 | 89 | **+2** |
| Pass rate | 87 / 87 (100%) | 89 / 89 (100%) | maintained |

## The 2 new cases (`e2e/21-created-claim-detail-binding.spec.ts`)

### Case 1 — `created claim renders its own customer/vehicle/VIN/description — not CLM-1006`

Drives the exact manual flow Slava reported. Creates a customer + claim with **unique** stamped markers (`E2E Detail Customer {ts}-{tag}`, `E2E Detail Vehicle {ts}-{tag}`, `VIN-DETAIL-{ts}`, `Detail binding regression {ts}-{tag}`, `E2E sandbox, Київ {ts}`).

Asserts on the **rendered DOM**, not just URL:
- `claim-header-title` contains the created `CLM-####` id
- `claim-header-title` contains the created customer name
- `claim-header-title` does NOT contain `CLM-1006` or `Роберт Джонсон`
- `claim-shell-id` / `-customer` / `-vehicle` carry the created data
- `claim-detail-customer` / `-vehicle` / `-vin` / `-event-type` / `-location` / `-description-text` all match the submitted values
- `claim-detail-sandbox-notice` is visible (proves no CLM-1006 fixtures leaked)
- Re-open from list (search by id → row click) — header still shows created data
- Bottom-rail buttons `open-ai-evidence` / `open-approval` / `open-documents-collection` navigate to `/claims/{createdId}/{section}`, NOT `/claims/CLM-1006/{section}`

### Case 2 — `CLM-1006 still renders its own golden data (no regression)`

Direct nav to `/claims/CLM-1006`:
- Header contains `CLM-1006` AND `Роберт Джонсон`
- Breadcrumb carries golden id/customer/vehicle
- Sandbox-notice has count 0 (rich golden render preserved)
- `open-ai-evidence` lands on `/claims/CLM-1006/ai-evidence` (golden navigation preserved)

## Issues encountered + fixes (test-side only)

The first full run surfaced 1 selector bug in the new regression test (NOT in the product):

1. **`e2e/21-*:82`** — used `page.getByDisplayValue('VIN ****0000')` to locate the VIN input by current value. That method does not exist on the `Page` object in this Playwright version (it's on `Locator`). **Fix**: added `data-testid="new-claim-vehicleVin"` to the VIN input in `NewClaimModal.tsx` and updated the test to use it. After the testid fix: 89/89 PASS.

This is identical in spirit to the 3 test-bugs caught last gate — selector mismatches in newly-written tests, not product behaviour. Documented as test-side fixes; no product code touched as part of this resolution beyond adding the testid.

## Screenshots / traces

- HTML report local-only at `playwright-report/`
- JSON results local-only at `playwright-report/results.json`
- `screenshot: only-on-failure` configured; with 0 failures → no artifacts to ship
- `trace: on-first-retry` configured; with `retries: 0` → no traces emitted

## Build verification context

Backend build: 0 warnings / 0 errors / 10 projects. Backend xunit: 137 / 137.
Frontend build: 125 modules, 436.21 kB JS, 39.15 kB CSS, 0 errors.
