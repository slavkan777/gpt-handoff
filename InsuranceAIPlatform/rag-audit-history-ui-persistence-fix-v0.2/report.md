REQUEST_ID: REQ-2026-06-04-insuranceai-rag-audit-history-ui-persistence-fix-v0-2
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-implementation-continuation
PROJECT: InsuranceAIPlatform
GATE: RAG_AUDIT_HISTORY_UI_PERSISTENCE_FIX_AND_PRE_MANUAL_ACCEPTANCE_V0.2
COMPLETED: 2026-06-04
COMPLETED_BY: claude

## Current State

The one accepted limitation from the RAG completion gate — UI audit-history persistence
across a page reload — is now **closed**. A new **Audit History** panel on the Claim Evidence
Intelligence (AI Evidence) page re-hydrates the current claim's persisted RAG audit traces from
the **existing** `GET /api/claims/{claimId}/rag/audit` endpoint on every component mount, so the
history survives a full browser reload. The previously **skipped** Playwright PERSISTENCE test is
now a **real PASS**. This was **frontend + e2e only** — no backend change was needed or made
(the audit endpoint already existed and persists `RagAuditTrace` rows). Product-repo code remains
**uncommitted** on branch `rag/local-foundation-mega-v0.1`.

## DONE criteria vs evidence

| Criterion (from request) | Status | Evidence |
|---|---|---|
| Audit History panel exists | ✅ | `src/components/claim/RagAuditHistoryPanel.tsx` (testid `rag-audit-history`) |
| Loads current claim audit history | ✅ | `useEffect → dispatch(fetchAuditHistory(claimId))` `RagAuditHistoryPanel.tsx:34-36`; saga → `api.ragAudit(claimId, 10)` |
| Reload shows previous RAG trace / metadata | ✅ | E2E test 7: `rag-audit-row` count **2 before AND after** `page.reload()` |
| Playwright persistence test passes (not skipped) | ✅ | `npx playwright test 22-rag-evidence` → **8 passed, 0 skipped**, exit 0 |
| Existing RAG buttons + similar-claims still work | ✅ | tests 1–6, 8 unchanged + green (mechanical / semantic×3 / negative×2 / cross-claim isolation) |
| Build and tests pass | ✅ | `dotnet test` **158/158** 0-skip exit 0 · `npm run build` exit 0 |
| Report published to project paths | ✅ | this report → the 3 paths below |

## What Changed (frontend + e2e only — 8 files)

| File | Change |
|---|---|
| `src/components/claim/RagAuditHistoryPanel.tsx` | **NEW** — Audit History panel; `useEffect` dispatch on mount; testids `rag-audit-history` / `rag-audit-row` / `rag-audit-empty` / `-loading` / `-error`; renders useCase, confidence, date, query + answer snippet |
| `src/features/rag/ragSlice.ts` | `auditHistory` / `auditStatus` / `auditError` state + `fetchAuditHistory` / `auditHistoryReceived` / `auditHistoryFailed` |
| `src/features/rag/ragSaga.ts` | `takeLatest(fetchAuditHistory)` → `ragAudit(claimId,10)` worker |
| `src/features/rag/ragSelectors.ts` | `selectRagAuditHistory`, `selectRagAuditStatus` |
| `src/api/backendInsuranceApi.ts` | `ragAudit` → `GET /api/claims/{claimId}/rag/audit?limit=10` (real DB traces in LIVE mode) |
| `src/api/mockInsuranceApi.ts` | `ragAudit` → 2 **deterministic** synthetic rows (fixed `createdAtUtc`, advisory tone, no fraud words) so MOCK reload shows stable history |
| `src/pages/AiEvidencePage.tsx` | renders `<RagAuditHistoryPanel claimId=… />` after the intelligence panel |
| `e2e/22-rag-evidence.spec.ts` | replaced `test.skip` PERSISTENCE with a real test: asserts 2 rows, `page.reload()`, asserts 2 rows again |

## Verification (personally re-run, not delegated)

| Check | Command | Result |
|---|---|---|
| Backend tests | `dotnet test …InsuranceAIPlatform.Tests` | **Passed 158, Failed 0, Skipped 0**, exit 0 |
| Frontend build | `npm run build` | `✓ built in 6.60s`, exit 0 |
| UI E2E | `npx playwright test 22-rag-evidence --config playwright.mock.config.ts --reporter=list` | **8 passed, 0 skipped**, exit 0 |
| Persistence (test 7) | reload assertion | `rag-audit-row` count = 2 before reload AND after `page.reload()` |
| No backend touched | `find server -name "*.cs" -newermt "15 minutes ago"` | **empty** (0 backend files) |
| Task file scope | `find src e2e -newermt "15 minutes ago"` | exactly the 8 files above |

## Persistence — end-to-end chain (how reload-persistence actually holds)

1. **Backend persists** `RagAuditTrace` rows on every RAG ask (verified prior gate v0.1: DB rows present + `GET /rag/audit` returns them).
2. **Backend API path** (`backendInsuranceApi.ragAudit`) calls the real `GET /api/claims/{claimId}/rag/audit?limit=10` → returns DB-persisted traces in LIVE mode.
3. **Mock API path** (`mockInsuranceApi.ragAudit`) returns 2 deterministic rows, stable across reloads — enables offline/MOCK e2e without a backend.
4. **UI** re-hydrates by dispatching `fetchAuditHistory` in `useEffect` on **every mount**, so a full `page.reload()` re-fetches from the durable source rather than relying on in-memory Redux.
5. **E2E (MOCK)** proves the UI reload-persistence; backend DB persistence was verified in v0.1.

## Screenshots (written by the e2e run, under `test-results/`)

- `rag-08-persistence-before-reload.png` — audit history panel with 2 rows (initial load)
- `rag-09-persistence-after-reload.png` — same 2 rows after full reload

## Boundaries Honored

- **No backend `.cs` changed** (verified by recent-mtime find). No new backend tests needed.
- **No product-repo commit / push** — changes uncommitted on `rag/local-foundation-mega-v0.1`.
- **No Azure / deploy / secrets / PII.** Advisory tone preserved; no fraud-accusation language added.
- **No global `_BRIDGE`, no TwinCore** touched. This report published only to InsuranceAIPlatform project paths via an isolated git worktree (own index).

## Known Limitations

- E2E reload-persistence is demonstrated in **MOCK** mode (deterministic rows). LIVE-backend
  reload-persistence relies on the already-verified `RagAuditTrace` DB persistence (v0.1) plus the
  wired `GET /rag/audit` call; it was not re-run as a live-backend browser e2e in this gate.
- Audit rows display advisory metadata only (useCase, confidence, query/answer snippet, timestamp).

## Next Safe Step

**Pre-manual acceptance:** open CLM-1006 → AI Evidence tab → use a RAG button → scroll to the
**Audit History** panel → reload the page → rows persist. On accept, the full RAG code
(backend + frontend, uncommitted on `rag/local-foundation-mega-v0.1`) is ready for a separate
**commit-only** gate.

## Not Touched

Backend source; global `_BRIDGE`; TwinCore; product-repo git history (no commit/push); Azure; AIKB; secrets.
