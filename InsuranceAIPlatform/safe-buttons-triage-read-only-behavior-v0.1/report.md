# InsuranceAIPlatform — Safe Buttons Triage (Read-Only Behavior) V0.1 — Handoff

**Gate:** `SAFE_BUTTONS_TRIAGE_READ_ONLY_BEHAVIOR_V0.1` · **Date:** 2026-05-27
**Scope:** audit every visible frontend button/action across all 11 screens, classify each, and make the UI honest — disable/defer write-looking or dead actions with clear tooltips, keep safe navigation + local-UI working — WITHOUT adding any real backend write behavior. Local only: no commit/push, no `server/**`, no DB/EF/Azure/AI/write endpoints, no new deps.

## CURRENT STATE
All visible actions are inventoried and classified. Write-looking and dead buttons are now `disabled`/deferred with explanatory tooltips (and a small `demo` badge on the prominent ones); safe navigation and local-UI controls remain functional; the AI run is an explicit local mock that never hits a provider; one previously-dead button was wired to a safe navigation; and one misleading caption was corrected. A small reusable `DeferredActionButton` (disabled, tooltip, optional badge — mirroring the existing disabled-nav pattern in `Sidebar`) backs the deferred treatment. Frontend build PASS; backend untouched. Implemented locally, uncommitted.

## IMPLEMENTATION STATUS: implemented (local, uncommitted).

## BRANCH / HEAD
- Branch: `dev`. HEAD unchanged: `b1f832f` (`b1f832f519fdd021c68169422dfd437ae40601db`). No commit/push this gate.

## FILES CHANGED (9, all `src/**`) — +116 / −67 (modified) + 1 new (42 lines)
- NEW: `src/components/ui/DeferredActionButton.tsx` (reusable disabled/deferred button).
- MOD pages (7): `DashboardPage`, `ClaimsListPage`, `ClaimWorkspacePage`, `DocumentsPhotosPage`, `AiEvidencePage`, `CustomerVehiclePage`, `HumanApprovalPage`.
- MOD layout (1): `src/components/layout/TopBar.tsx`.

## ACTION INVENTORY MATRIX (all 11 screens + global chrome)
| Screen | Action | Classification | Treatment |
|---|---|---|---|
| Dashboard | row click, "Переглянути всі/AI-аналіз/деталі/журнал" | SAFE_NAVIGATION | kept |
| Dashboard | "Сьогодні", "7 днів", "Експорт", "+ Створити випадок" | DEFERRED_DISABLED | DeferredActionButton + hint (+badge on Export/Create) |
| Dashboard | queue segment chips | DEFERRED_DISABLED | disabled + tooltip → points to Claims List filters |
| Claims List | segment filter (setSegment), row click | SAFE_NAV / LOCAL_UI | kept (functional) |
| Claims List | "Експорт CSV", "Імпорт документів", "+ Новий випадок" | DEFERRED_DISABLED | DeferredActionButton + hint + badge |
| Claim Workspace | 4 nav buttons (list / ai-evidence / approval / documents) | SAFE_NAVIGATION | kept |
| Claim Workspace | "Наступна дія" caption + button | SAFE_NAVIGATION | caption corrected (no false SMS+email send); button relabelled "Відкрити збір документів" to match its `navigate` |
| Documents/Photos | "Запросити у клієнта", "Запросити фото", "Переглянути оригінал", "Підтвердити документ" | DEFERRED_DISABLED | DeferredActionButton + hint |
| Documents/Photos | selectDocument, toggleReviewed | SAFE_READ_ONLY_LOCAL_UI | kept |
| AI Evidence | "Перезапустити mock-аналіз" | MOCK_ONLY_EXPLICIT | kept functional; tooltip "локальний демо-прогон, реальний AI-провайдер не підключений" (dispatches a stub, never hits backend) |
| AI Evidence | evidence tabs, confidence slider | SAFE_READ_ONLY_LOCAL_UI | kept |
| Risks | 3 nav buttons | SAFE_NAVIGATION | kept |
| Policy/Coverage | (no actions — pure display) | — | none |
| Customer/Vehicle | "→ Деталі" | SAFE_NAVIGATION | wired to `/claims/CLM-1006/policy` (was dead) |
| Human Approval | decision options, notes, checklist | SAFE_READ_ONLY_LOCAL_UI | kept (local draft building) |
| Human Approval | "Зберегти чернетку", "Надіслати запит клієнту", "Погодити після перевірки" | DEFERRED_DISABLED | DeferredActionButton + hint (+badge on approve) |
| Audit/Cost | (no actions — pure display) | — | none |
| Demo Scenario | start/stop demo, step cards | SAFE_NAV / LOCAL_UI | kept (local guided-tour player) |
| TopBar (global) | demo run | SAFE_READ_ONLY_LOCAL_UI | kept |
| TopBar (global) | search input | MOCK_ONLY_EXPLICIT | "read-only demo" tooltip |
| TopBar (global) | Help, Notifications icons | DEFERRED_DISABLED | disabled + tooltip |
| Sidebar (global) | "Транспортні засоби", "Налаштування" | DEFERRED_DISABLED | already disabled pre-gate — preserved |

## CLASSIFICATION COUNTS (logical actions; multi-instance groups counted once)
- SAFE_NAVIGATION: ~16
- SAFE_READ_ONLY_LOCAL_UI: ~11
- DEFERRED_DISABLED: 17 new (+2 pre-existing in Sidebar, preserved)
- MOCK_ONLY_EXPLICIT: 2 (AI mock run, search)
- REMOVE_OR_DEEMPHASIZE: 0 (preferred disable/explain over deletion — no layout churn)

## WRITE-BACK SAFETY
Verified clean. Fresh grep across changed `src/**` for `method:(POST|PUT|PATCH|DELETE)`, `axios.(post|put|patch|delete)`, and two-arg `fetch(url, {...})` → none. Deferred buttons dispatch nothing (`DeferredActionButton` is `disabled` with no `onClick`); the removed write-looking dispatches (`saveDraft`, `sendRequestToCustomer`, `requestMissingPhoto`) are no longer wired. `src/api/backendInsuranceApi.ts` is untouched and GET-only. The AI run dispatches `runMockAiAnalysis`, which is a stub returning a canned object (no `apiFetch`) — it does not reach the backend even in backend mode.

## FRONTEND BUILD RESULT
PASS — `npm run build` (`tsc -b && vite build`) → exit 0, 107 modules (+1 for the new component), `✓ built` in ~3.6s, zero TS errors (independently re-verified by the Opus inspector).

## BACKEND BUILD / TEST RESULT
Backend source unchanged (`git diff --name-only HEAD -- server` empty; no `server/**` in changeset). Green at `b1f832f` (re-verified earlier this session): `dotnet build` 0/0; `dotnet test` Passed 14/14.

## FRONTEND SMOKE
Build + type-check only. No browser/visual test (no headless browser available) — deferred to the later manual checkpoint per Conveyor Mode. Manual verification: run backend (`dotnet run --project server/InsuranceAIPlatform.Api`), `VITE_INSURANCE_API_MODE=backend npm run dev`, then walk all 11 screens — confirm deferred buttons are visibly dimmed + show a tooltip on hover, safe navigation works, the AI mock run animates locally, and CustomerVehicle "→ Деталі" opens the Policy tab.

## SCREENSHOTS
None captured (no headless browser). Manual steps above.

## SAFETY SCAN
Clean — no keys/tokens/passwords/connection strings/Azure secrets/AI-provider config/real PII/real external URLs in changed `src/**`. No `.env`, no new dependency. Only host referenced anywhere in the app remains `http://localhost:5284` (untouched API client).

## QA GATE
Independent **Opus** inspector — **CLEARED after 1 rework**. Iteration 0 returned REWORK for two missed honesty defects: (1) `ClaimsListPage` had 3 live dead/write buttons ("Експорт CSV", "Імпорт документів", "+ Новий випадок"); (2) `ClaimWorkspacePage` caption falsely claimed a button "надсилає запит клієнту через SMS + email" while it only navigated. Both fixed and re-verified: full independent census of all 19 raw `<button>` tags confirms every one has `onClick` (nav/local) or `disabled`; zero live dead/write buttons remain; build exit 0; no write-back path; changeset 9 `src/**` files; HEAD still `b1f832f` (no commit/push).

## ISSUES / LIMITATIONS
- No browser/visual test (no headless browser): evidence is build + type-check + independent code review + button census. A manual browser pass is recommended before any demo.
- Date-range presets (Dashboard "Сьогодні"/"7 днів") and the Dashboard queue segment chips are deferred rather than wired to real filtering (the working segment filter lives on the Claims List screen). Documented, non-blocking.

## CONFIRMATION — FORBIDDEN SCOPE
No commit · no push · no merge · no `main` · no PR · no `server/**` change · no `src/api/**` change · no DB/SQL/DevDept · no EF Core/migrations/DbContext · no backend write endpoints · no approval-submit/payout/upload/customer-message-send · no claim-status mutation · no `package.json`/lock/new-dependency · no `.env`/`appsettings.Production.json` · no secrets/real PII · no Azure · no AI provider · no real external API calls.

## NEXT SAFE STEP
A separately-approved **`COMMIT_AND_PUSH_DEV_SAFE_BUTTONS_TRIAGE_ONLY`** — commit the 9 `src/**` files on `dev` + fast-forward push to `origin/dev` (main untouched). Then a **`PRE_AZURE_FULL_LOCAL_MANUAL_ACCEPTANCE_CHECKPOINT`** (manual browser walk of all 11 screens) before any DB/EF/Azure/AI work.
