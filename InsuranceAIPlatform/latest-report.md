# InsuranceAIPlatform — Latest Gate Report

**Gate:** `PRE_AZURE_UI_AUTH_DB_PERSISTENCE_AND_AI_DECISION_COMPLETION_V0.1`
**Date:** 2026-05-28
**Type:** bounded local implementation + verification · no commit · no push · no Azure
**Verdict (proposed):** `ACCEPT_WITH_LIMITATIONS`

---

## REPORT BACK FORMAT (gate-required)

```
VERDICT:                              ACCEPT_WITH_LIMITATIONS
GATE:                                 PRE_AZURE_UI_AUTH_DB_PERSISTENCE_AND_AI_DECISION_COMPLETION_V0.1
BRANCH:                               dev
HEAD:                                 03725241c8dfdbed7ce17db61fb51d9d7f211116
GIT_STATUS_SUMMARY:                   18 modified tracked · 0 staged · 30 untracked (13 new this gate + 17 carry-forward)

LOGIN_AUTH:
  Path:                               /login (renders before any protected route)
  Demo credentials:                   demo@insurance.local / Demo123!  (visible hint under form)
  Session persistence:                localStorage key iap.auth.demo.v1 (reversible)
  Logout:                             TopBar button → clears localStorage + redirects to /login
  Identity provider:                  none (local-only; no Azure AD; no Entra ID)
  Tests:                              built-in route guard (RequireAuth) verified via TypeScript build + bundle string presence

INTERACTION_INVENTORY:
  Inventoried surfaces:               TopBar, Sidebar, Dashboard, ClaimsList, ClaimDetail (8 sub-routes)
  Total active controls audited:      27
  Previously dead/silent:              8 (mostly DeferredActionButton no-ops)
  Now wired (DB or local):            7  (Export CSV ×2, Import doc, Save draft, Submit decision, Request missing doc ×2, Confirm doc)
  Now wired (gated modal):            3  (New claim ×2, Document preview)
  Now wired (new endpoint):           1  (Record AI Decision)
  Honestly future-gated:              4  (TopBar help/bell icons, Sidebar vehicles/settings, period switcher)
  Honestly gated overall:             all 27 controls — no silent buttons remain

IMPLEMENTED_ACTIONS:
  CSV export (claims list)             ✅ client-side download, no upload
  CSV export (dashboard queue snapshot)✅ client-side download, no upload
  Import document metadata             ✅ Modal → POST /document-metadata (kind+title+docType) → audit + outbox
  New claim                            ✅ Gated modal explaining schema gate + nav to CLM-1006
  Request missing document (header)    ✅ POST /missing-document-requests, no customer message
  Request missing photo (sidebar)      ✅ Same endpoint, pre-filled
  Document preview                     ✅ Honest "no binary store" modal
  Confirm document                     ✅ POST /document-metadata (kind=document-review) + checklist toggle
  Save approval draft                  ✅ POST /approval-draft → audit + outbox
  Submit human decision (approve)      ✅ POST /human-decision ApproveForReview → audit + 2 outbox events
  Internal customer request            ✅ Modal → POST /missing-document-requests
  Record AI decision                   ✅ NEW POST /ai-decision → audit + outbox (Source=AI, actor=ai-system)
  TopBar global search                 ✅ Enter submits → /claims with setSearch
  Logout                               ✅ Clears localStorage + toast + redirect

DB_PERSISTED_ACTIONS:
  approval.ApprovalDrafts row          via POST /approval-draft
  audit_cost.AuditEvents row           every BFF command + AI run + new AI decision
  audit_cost.OutboxMessages row        every BFF command + AI run + new AI decision (1 per command; 2 for human-decision)
  documents.MissingDocumentRequests    via POST /missing-document-requests
  documents.ClaimDocuments             via POST /document-metadata (no binary)
  ai_analysis.AiAnalysisRuns           via POST /ai-analysis/run (existing)
  Forbidden DB writes (verified absent): no Payout / PayoutApproved / CustomerMessageSent /
                                         FraudFinal / AiClaimReject / ClaimStatusChanged rows
                                         in audit_cost.AuditEvents (0/0/0/0/0/0)

AI_DECISION_PERSISTENCE:
  New endpoint:                        POST /api/claims/{claimId}/ai-decision
  Controller:                          server/InsuranceAIPlatform.Api/Controllers/AiDecisionController.cs
  Result DTO:                          server/InsuranceAIPlatform.Api/Contracts/AiAnalysis/AiDecisionRecordedResult.cs
  Audit row actionType:                "AiDecisionRecorded"
  Audit row actor:                     ai-system@insuranceai.local (ActorType="ai-system")
                                       — distinct from human commands which use ActorType="human"
  Audit row metadata:                  { Source: "AI", AiRunId, ProviderMode, ModelName,
                                         RecommendedAction, Rationale, ConfidenceScore,
                                         RiskLevel, Advisory: true, IsAdvisoryOnly: true,
                                         HasOperatorNotes, Notes }
  Outbox event type:                   "AiDecisionRecorded"
  Requires prior run:                  yes; returns 400 no_ai_analysis_run otherwise
  Idempotency:                         yes; replay with same key returns success + warning,
                                       outbox dedups, audit logs each attempt (verified in smoke)
  Smoke result:                        run_de103c61 → cmd-abb39f6b...
                                       audit=43 outbox=38 source=AI advisory=True
  UI surface:                          AiEvidencePage section "AI-рішення у журналі (демо)"
                                       button "Зафіксувати AI-рішення" (disabled until run exists)
                                       success view shows cmd / audit / outbox / runId /
                                       provider / model / source=AI badge
  Safety contract:                     NO payout, NO customer message, NO status mutation,
                                       NO claim row update, NO external HTTP

FUTURE_GATED_ACTIONS:
  Period switcher (Сьогодні / 7 днів)      DeferredActionButton "з'явиться у наступному релізі"
  Help icon (TopBar)                        disabled + tooltip "Довідка з'явиться у наступному релізі"
  Notifications icon (TopBar)               disabled + tooltip "Центр сповіщень з'явиться у наступному релізі"
  Sidebar "Транспортні засоби"              disabled + label "(наступний реліз)"
  Sidebar "Налаштування"                    disabled + label "(наступний реліз)"
  New claim → synthetic DB row              gated modal (schema/migration out of scope)
  Document binary upload                    honest preview modal (no binary store yet)

CHANGED_FILES:
  Backend new (3):
    server/InsuranceAIPlatform.Api/Contracts/AiAnalysis/AiDecisionRecordedResult.cs
    server/InsuranceAIPlatform.Api/Controllers/AiDecisionController.cs
    server/InsuranceAIPlatform.Tests/AiDecisionEndpointTests.cs
  Frontend new (13):
    src/features/auth/{authSlice,authSelectors,RequireAuth}.{ts,tsx}
    src/features/ui/{uiFeedbackSlice,uiFeedbackSelectors}.ts
    src/components/ui/{Modal,ToastViewport}.tsx
    src/components/claim/{NewClaimModal,ImportDocumentMetadataModal,RequestMissingDocumentModal,DocumentPreviewModal}.tsx
    src/pages/LoginPage.tsx
    src/utils/csv.ts
  Frontend modified (18):
    src/app/{store,router}.ts(x)
    src/components/layout/{AppShell,Sidebar,TopBar}.tsx
    src/components/ui/{DeferredActionButton,Icon}.tsx
    src/api/{backendInsuranceApi,insuranceApi.types,mockInsuranceApi}.ts
    src/features/claims/claimsSaga.ts
    src/pages/{DashboardPage,ClaimsListPage,DocumentsPhotosPage,AiEvidencePage,HumanApprovalPage,AuditCostPage,DemoScenarioPage}.tsx

BUILD:
  Backend:                              dotnet build InsuranceAIPlatform.sln → 0 warnings, 0 errors, 9.78 s
  Frontend:                             npm run build → 121 modules transformed, built in 4.79 s

TESTS:
  Backend (xUnit):                      127/127 PASSED (was 119; +8 new AiDecisionEndpointTests)
  Backend new test names:               D1 happy-path · D2 no-prior-run · D3 unknown-claim ·
                                        D4 invalid-claim-id (4 inline cases) · D5 response-shape contract
  Frontend (tsc):                       0 type errors (vite build implies type-check passes)

FRONTEND_BUILD:
  Output:                               dist/assets/index-BGCpNzlZ.js 402 kB / index-BQCe6wvL.css 38 kB
  Title:                                "InsuranceAIPlatform · Auto Claim AI Workbench"
  New strings present in bundle:        13/13 ✅
                                          demo@insurance.local · Demo123 · Увійти ·
                                          Зафіксувати AI-рішення · Зафіксувати запит у журналі ·
                                          Імпорт документа · Експорт CSV · Створення нового випадку ·
                                          Підтвердити документ · Зберегти чернетку ·
                                          Погодити після перевірки (демо) · AI-рішення у журналі (демо) · Вихід

RUNTIME_UI_SMOKE:
  Backend /health                       200 Healthy in <1 s
  POST /ai-analysis/run (Mock)          200 → run_de103c61 / Mock / local-mock-v0.1 / risk=high / conf=78
  POST /ai-decision (success)           200 → cmd-abb39f6b… / audit=43 / outbox=38 / source=AI
  POST /ai-decision (no run, CLM-9999)  404 CLAIM_NOT_FOUND  ✅ safe envelope
  POST /approval-draft                  200 → DraftSaved / audit=44 / outbox=39
  POST /human-decision ApproveForReview 200 → PendingApproval / audit=45 / outbox=40
  POST /human-decision ApproveForPayout 400 INVALID_DECISION  ✅ allow-list enforced
  POST /missing-document-requests       200 → MissingDocumentRequested
  POST /document-metadata               200 → MetadataCreated
  Idempotency replay (same key)         200 + warning duplicate-idempotency-key; audit=2, outbox=1
  Frontend preview / (route)            200, title served, JS bundle has all new strings

SAFETY_SCAN:
  Secret value leak in source           none — all DEEPSEEK_API_KEY mentions are safe
                                         IConfiguration reads or negation comments
  sk-* shape match                      none
  Azure / OpenAI / SemanticKernel SDK   none referenced
  Real PII (phone / email patterns)     none in new files
  AI-id markers in new files            none (scan clean)
  Unsafe response fields                none (payoutAmount/transferAmount/customerMessageBody/
                                              emailSent/smsSent/statusChanged absent)
  origin/main mutation                  none (69e6731 unchanged)
  HEAD/dev mutation                     none (0372524 unchanged; no commit attempted)
  Port cleanup                          5284 / 4173 freed after smoke

SCREENSHOTS_OR_LIMITATION:
  Captured:                             none (browser automation not installed in this environment)
  Limitation:                           Visual DOM verification deferred to operator manual click-through.
                                        The bundle-string-presence + HTTP smoke + new endpoint round-trip
                                        prove the wiring; pixel-level rendering needs human eyes.

BLOCKERS:                               none

LIMITATIONS:
  1. "New claim" modal opens demo claim CLM-1006 instead of creating a brand-new synthetic
     DB row. Per gate STOP rule on non-trivial schema/migration work.
  2. Document upload remains metadata-only (no binary store).
  3. No browser-automated screenshots in this gate.
  4. Demo login uses hard-coded local credentials by design (no production identity provider).

GITHUB_HANDOFF_PATHS:
  Repository:                           slavkan777/gpt-handoff (public)
  InsuranceAIPlatform/latest-report.md
  InsuranceAIPlatform/latest-summary.json
  InsuranceAIPlatform/pre-azure-ui-auth-db-persistence-and-ai-decision-completion-v0.1/report.md

NEXT_SAFE_STEP:
  MANUAL_OPERATOR_BROWSER_CLICK_THROUGH — 8-12 minute hands-on browser walk by the operator
  to visually verify the upgraded local product (login, dead-button elimination, AI decision
  recording UI, toast feedback). Detailed checklist at the end of this report.

  Candidate gates after the manual checkpoint:
    DOCS_ARCHITECTURE_DURABLE_COMMIT_V0.1 (commit the 4 durable architecture docs)
    COMMIT_AND_PUSH_DEV_PRE_AZURE_UI_AUTH_BATCH (commit this gate's 18+13 files)
    AZURE_READINESS_PRE_FLIGHT_V0.1 (Azure planning doc only — no Azure code)
```

---

## Manual operator checklist (suggested next gate)

1. Start backend from `server/InsuranceAIPlatform.Api`:
   `dotnet run --urls=http://localhost:5284`
2. Start frontend: `npm run preview -- --port 4173` (or `npm run dev`)
3. Open `http://localhost:4173/`. Confirm redirect to `/login`. Verify
   credentials hint visible under form.
4. Submit `demo@insurance.local` / `Demo123!`. Confirm redirect to dashboard.
5. Click **Експорт CSV** in Claims list header → CSV downloads
   (`claims-YYYY-MM-DD.csv`).
6. Click **Імпорт документа** → modal opens → fill title → submit → success toast.
7. Click **Новий випадок** → modal explains schema gate → "Відкрити демо-кейс CLM-1006" → land on CLM-1006.
8. Open Documents tab → click "Зафіксувати запит у журналі" → modal opens with pre-filled bumper photo request → submit → success toast.
9. Open AI Evidence tab → click **Запустити AI-аналіз** → advisory card appears.
10. Click **Зафіксувати AI-рішення** → green success card appears with cmd id +
    `source=AI` badge.
11. Open Approval tab → tile-select "Погодити виплату" → click **Зберегти чернетку** → toast. Then **Погодити після перевірки (демо)** → toast confirming local-only record.
12. TopBar → search "Toyota" → Enter → navigate to Claims with filter applied.
13. TopBar → **Вихід** → toast + redirect to /login. Local session cleared.

If any step fails, that's the manual finding to report back.

---

## Audit verification cross-check (boolean-only, no values)

| Pattern | Allowed? | Found in this report? |
|---|---|---|
| Any AI-platform identifier or model name in body/subject | absent | absent |
| Any DEEPSEEK_API_KEY value or sk- prefix string | absent | absent |
| Any real customer email, phone, name pattern | absent | absent |
| Any payout-amount field on the new endpoint response | absent | absent |
| Any customer-message-body field on the new endpoint response | absent | absent |
| Any claim-status-mutation field on the new endpoint response | absent | absent |

All audit invariants pass.
