REQUEST_ID: REQ-2026-06-04-insuranceai-local-rag-infra-ui-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-implementation
PROJECT: InsuranceAIPlatform
GATE: LOCAL_RAG_INFRASTRUCTURE_UI_DEMO_V0.1
COMPLETED: 2026-06-04
COMPLETED_BY: claude

## Current State

The AI Evidence page now has an **Evidence Intelligence Stack** panel that shows how the local
RAG pieces fit together as one insurance workflow: **SQL source of truth → evidence memory index
→ retrieval → local reasoning runtime (disabled/mock) → audit history → human review**. The panel
reports live status + counts for each of the three infrastructure layers and exposes two **safe,
local** actions (Check = refresh status, Reindex = deterministically rebuild this claim's embedding
cache). Everything runs **100% locally** — SQL is the source of truth, the embedding index is a
rebuildable cache, and the reasoning runtime is **disabled by default** (mock inference only; no
live or paid model). New backend endpoints were added (read + safe reindex); **no schema change,
no migration**. Product-repo code remains **uncommitted** on branch `rag/local-foundation-mega-v0.1`.

## DONE criteria vs evidence

| Criterion (from request) | Status | Evidence |
|---|---|---|
| Evidence Intelligence Stack panel | ✅ | `src/components/claim/RagInfrastructureStackPanel.tsx` (testid `rag-infra-stack`), rendered on `AiEvidencePage` |
| SQL Source of Truth status | ✅ | `rag-infra-sql` row; live GET → `healthy`, 8 clauses / 50 chunks / 21 eval / 4 audit |
| Evidence Memory Index status | ✅ | `rag-infra-index` row; live GET → `healthy`, CLM-1006 13/13 embedded, model `local-hash-embed-v0.1`, dim 256 |
| Local Reasoning Runtime status | ✅ | `rag-infra-runtime` row; live GET → `disabled`, enabled=false (mock only) |
| Safe local health/check/reindex actions | ✅ | `rag-infra-check-btn` (refresh) + `rag-infra-reindex-btn`; `POST /rag/infrastructure/reindex` re-embeds deterministically (idempotent) |
| Preserve RAG / similar / audit / citations / tests | ✅ | existing 8 e2e + 158 unit tests still green |
| Build, tests, targeted E2E, local smoke | ✅ | see Verification |
| Publish to project report paths | ✅ | this report → the 3 paths below |

## What Changed

**Backend (`server/**`, no schema change, no migration):**
| File | Change |
|---|---|
| `…/Api/Contracts/Rag/RagDtos.cs` | `RagInfrastructureStatusDto` + `RagSqlStatusDto` / `RagIndexStatusDto` / `RagRuntimeStatusDto` |
| `…/Api/Controllers/RagController.cs` | `GET /api/claims/{claimId}/rag/infrastructure` + `POST …/reindex` (reuse `ClaimsControllerBase` validation + 404) |
| `…/Services.AiAnalysis/Rag/IRagService.cs` + `RagService.cs` | `GetInfrastructureStatusAsync` (counts via `IDbContextFactory`; reads `RagOptions`) + `ReindexClaimAsync` (re-embed claim chunks → SaveChanges → refreshed status) |
| `…/Services.AiAnalysis/Rag/Contracts/RagContracts.cs` | domain records for the status |
| `…/Tests/RagInfrastructureTests.cs` | **NEW** — 2 tests (healthy counts + runtime disabled; reindex idempotent + index stays healthy) |

**Frontend (`src/**`, `e2e/**`):**
| File | Change |
|---|---|
| `src/components/claim/RagInfrastructureStackPanel.tsx` | **NEW** — 3 layer rows + status badges + workflow strip + Check/Reindex buttons; `useEffect` fetch on mount; runtime row always shows the disabled/mock note |
| `src/features/rag/{ragSlice,ragSaga,ragSelectors}.ts` | `infrastructure` state + fetch/reindex actions + saga workers + selectors |
| `src/api/{insuranceApi.types,mockInsuranceApi,backendInsuranceApi}.ts` | `RagInfrastructureStatus` type; `ragInfrastructure`/`ragReindex` (real GET/POST + deterministic mock) |
| `src/pages/AiEvidencePage.tsx` | renders `<RagInfrastructureStackPanel/>` |
| `src/i18n/messages/rag.ts` | stack labels (en + uk) |
| `e2e/22-rag-evidence.spec.ts` | **4 new tests** (mechanical / semantic / reindex action / negative-no-live-model) |

## Verification (personally re-run — not delegated)

| Check | Command | Result |
|---|---|---|
| Backend tests | `dotnet test …InsuranceAIPlatform.Tests` | **160/160** passed, 0 skipped, exit 0 (158 + 2 new) |
| Frontend build | `npm run build` | exit 0 |
| UI E2E | `npx playwright test 22-rag-evidence --config playwright.mock.config.ts` | **12 passed, 0 skipped**, exit 0 (8 existing + 4 new infra) |
| **Live smoke (LOCAL_REAL)** | `GET /api/claims/CLM-1006/rag/infrastructure` | **200** — sql `healthy` 8/50/21/4 · index `healthy` 13/13 `local-hash-embed-v0.1`/256 · runtime `disabled` enabled=false |
| **Live reindex (LOCAL_REAL)** | `POST …/rag/infrastructure/reindex` | **200** — idempotent, index stays `healthy` |
| Negative — missing claim | `GET …/CLM-9999/rag/infrastructure` | **404** |
| Negative — no secret leak | grep GET body for endpoint URL | **no endpoint URL** (only `endpointConfigured:true` + model name) |

Provider label for the reasoning runtime: **disabled / mock** (not LIVE_REAL). The infrastructure
status + reindex are **LOCAL_REAL** (real LocalDB, real endpoint, real re-embed).

## Boundaries Honored

- **No commit / push / deploy** — changes uncommitted on `rag/local-foundation-mega-v0.1`.
- **No schema change / no EF migration** — reindex only updates existing `EmbeddingJson` rows.
- **No Azure, no paid AI, no live model** — reasoning runtime disabled by default; CI/build/tests need no running model.
- **No secret / endpoint-URL** placed in any DTO, log, or this report.
- **No other project touched.** Report published only to InsuranceAIPlatform project paths via an isolated git worktree (own index) — global `_BRIDGE` and TwinCore untouched.

## Known Limitations

- **Mock vs live counts differ (cosmetic).** The MOCK api returns illustrative fixed values
  (index 50/50 global, audit 2) so offline e2e is deterministic; the **live** endpoint returns real
  **claim-scoped** counts (CLM-1006: index 13/13, audit 4). Both report `healthy`. The Playwright
  suite asserts the mock shape; the live smoke above confirms the real endpoint. A future polish
  pass can align the mock to claim-scoped numbers.
- **Reasoning runtime is disabled by design.** "available/unavailable" is only reachable if a local
  model is later enabled; this gate ships it `disabled`.
- Reindex is claim-scoped (re-embeds the current claim's chunks), not a global rebuild.

## Next Safe Step

Pre-manual acceptance: open CLM-1006 → AI Evidence → **Evidence Intelligence Stack** → read the 3
layer statuses → click **Reindex** (stays healthy) → confirm runtime shows **disabled / mock**.
On accept, the full RAG code (backend + frontend, uncommitted) is ready for a separate
**commit-only** gate.

## Not Touched

Global `_BRIDGE`; TwinCore; other projects; product-repo git history (no commit/push); EF
migrations / DB schema; Azure; AIKB; secrets.
