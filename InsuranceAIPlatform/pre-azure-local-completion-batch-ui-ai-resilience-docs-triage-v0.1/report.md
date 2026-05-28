# InsuranceAIPlatform · Pre-Azure Local Completion Batch — UI · AI · Resilience · Docs Triage V0.1 — Report

**Gate:** `PRE_AZURE_LOCAL_COMPLETION_BATCH_UI_AI_RESILIENCE_DOCS_TRIAGE_V0.1` · **Date:** 2026-05-28
**Type:** consolidated bounded local implementation + verification / no commit / no push / no Azure / `main` untouched.
**Verdict:** **ACCEPT** (independent inspector CLEARED 12/12 ✅, 0 ⚠️, 0 ❌).

## Goal

Three bounded scopes completed in one batch to close the remaining pre-Azure local gaps:

A. **Frontend AI Analysis UI wiring** — connect the existing React AI page to the existing BFF advisory-only AI seam.
B. **Backend DeepSeek provider resilience** — bounded retry around the real HttpClient call (Mock untouched).
C. **Docs / reports triage** — classify untracked working-tree artifacts without deletion.

## Git state (unchanged this gate)

| Field | Value |
|---|---|
| Branch | `dev` |
| HEAD | `1b824b012f5d7075ce07d626dd07c4f9ddff42e7` |
| origin/dev | `1b824b012f5d7075ce07d626dd07c4f9ddff42e7` (matches HEAD) |
| origin/main | `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` (unchanged) |
| Source commit created this gate | **No** |
| Source push this gate | **No** |
| main touched | **No** |

## Files changed (modified tracked, 8)

### Backend (3)
1. `server/InsuranceAIPlatform.Services.AiAnalysis/Configuration/AiProviderOptions.cs` — added `MaxAttempts` (default 2) + `RetryBaseDelayMs` (default 200) to `DeepSeekOptions`.
2. `server/InsuranceAIPlatform.Services.AiAnalysis/Providers/RealDeepSeekAiProvider.cs` — wrapped the existing HttpClient send + status triage in a bounded retry loop; `RetryableStatusCodes = {408,429,500,502,503,504}`; `TaskCanceledException` (timeout) also retried; non-retryable statuses + retries-exhausted throw safely with no body/key leak; `HttpRequestMessage` + `StringContent` constructed inside the loop (single-use per attempt).
3. `server/InsuranceAIPlatform.Tests/RealDeepSeekProviderTests.cs` — added 6 new tests (R6-R11) covering retry semantics.

### Frontend (5)
4. `src/api/mockInsuranceApi.ts` — added `getClaimAiAnalysis` + `runClaimAiAnalysis` stub methods returning a synthetic `AiAnalysisDto` (so the `typeof mockInsuranceApi` facade satisfies the new saga calls in Mock mode).
5. `src/features/aiReview/aiReviewSlice.ts` — added `lastRun: AiAnalysisDto | null` + `lastRunStatus` to state; new actions `loadLatestAiAnalysis(claimId)`, `aiAnalysisLoaded`, `aiAnalysisLoadFailed`; `runAiAnalysis` now takes a `{ claimId }` payload; `aiAnalysisSucceeded` now stores the returned DTO.
6. `src/features/aiReview/aiReviewSaga.ts` — real saga workers call `insuranceApi.getClaimAiAnalysis` and `insuranceApi.runClaimAiAnalysis` via the facade; short UX progress animation retained.
7. `src/features/aiReview/aiReviewSelectors.ts` — added `selectAiLastRun`, `selectAiLastRunStatus`.
8. `src/pages/AiEvidencePage.tsx` — added top-of-page advisory card rendering all `AiAnalysisDto` fields with all 7 guardrail pills; `useEffect` dispatches `loadLatestAiAnalysis(c.id)` on mount; run button dispatches `runAiAnalysis(c.id)`; removed hardcoded `Azure OpenAI` provider-name string from this page; recommendedAction rendered as read-only text+rationale (NOT a decision button).

### New untracked this gate (1)
9. `docs/reports/pre-azure-local-completion-batch-ui-ai-resilience-docs-triage-v0.1/triage-notes.md` — classification document for the 17 carry-forward untracked items.

### Carry-forward untracked (NOT changed this gate)
- 9 architecture planning docs in `docs/architecture/`
- 8 prior per-run report dirs in `docs/reports/`

## A. Frontend AI Analysis UI wiring

| Requirement | Status |
|---|---|
| UI appears in claim/review screen (existing AiEvidencePage) | ✅ |
| Uses existing BFF/API seam via `insuranceApi` facade | ✅ (no direct service calls) |
| Loads latest AI analysis for the active claim on mount | ✅ (`useEffect` → `loadLatestAiAnalysis(c.id)`) |
| Run action goes through BFF endpoint | ✅ (`runClaimAiAnalysis(claimId)`) |
| Clearly shows advisory-only status | ✅ (`Advisory-only AI` label + advisory notice text) |
| Provider mode displayed (Mock / DeepSeek / Unknown chip) | ✅ |
| Model name displayed | ✅ |
| Summary text displayed | ✅ |
| Recommended action displayed read-only (NOT a decision button) | ✅ |
| Policy coverage explanation displayed | ✅ |
| Risk level + confidence score displayed | ✅ |
| Findings / evidence / risks displayed | ✅ |
| Cost / token metadata displayed | ✅ |
| Guardrail flags displayed — every Can-* must read `false` | ✅ — all 7 pills rendered with safety colour-coding |
| Safe loading / empty / error states | ✅ (loading chip, "no runs yet" empty state, danger card on failure) |
| No auto-approve / reject / payout / message / status-change actions added | ✅ |
| No API keys displayed or requested in UI | ✅ |
| Feature-flag pattern | Reuses existing `VITE_INSURANCE_API_MODE` env-driven facade (mock/backend) |

## B. Backend DeepSeek provider resilience

| Requirement | Status |
|---|---|
| Mock path unaffected | ✅ (only `RealDeepSeekAiProvider.AnalyzeAsync` changed) |
| Real DeepSeek remains explicit opt-in only | ✅ (Mode + RealCallsEnabled + key) |
| No provider SDK package added | ✅ (only Microsoft.Extensions.* + EF; inline retry logic) |
| No Azure | ✅ |
| No secret logging | ✅ |
| No raw provider response logging | ✅ |
| Retry only on safe transient: 408/429/500/502/503/504 + timeout | ✅ (`RetryableStatusCodes` HashSet + `TaskCanceledException` catch) |
| Do NOT retry 400/401/403 | ✅ (non-retryable branch throws immediately) |
| Bounded attempts (max 2 total) | ✅ (`MaxAttempts=2` default; configurable via `AiProvider:DeepSeek:MaxAttempts`) |
| Bounded timeout preserved | ✅ (`TimeoutSeconds` still drives `httpClient.Timeout`) |
| Bounded short backoff with deterministic small jitter | ✅ (`base * attempt + (attempt*37) % (base/2)`) |
| Error message sanitized | ✅ (only `HTTP {status}` / `timed out after {N}s.` — no body, no key) |
| Tests: retry occurs for 503 / 429 / timeout | ✅ R6 / R7 / R8 (each asserts `callCount=2`) |
| Tests: NO retry for 401 / 403 / 400 | ✅ R9 / R10 / R11 (each asserts `callCount=1`) |
| Tests: no Mock fallback faked as DeepSeek success | ✅ — orchestrator unchanged; G5 fingerprint separation preserved |
| Tests: guardrails still applied after provider success | ✅ — orchestrator's `AdvisoryOnlyGuardrailEvaluator` path unchanged |

## C. Docs / reports triage (no deletion)

Triage document published at `docs/reports/pre-azure-local-completion-batch-ui-ai-resilience-docs-triage-v0.1/triage-notes.md`. Summary:

| Bucket | Count | Future action |
|---|---|---|
| `COMMIT_LATER_SOURCE_DOC` | 4 architecture docs | Commit to dev in a docs-only gate |
| `DEFER` (architecture) | 5 architecture docs | Revisit at next architecture cleanup |
| `ARCHIVE_LATER` (report dirs) | 8 prior per-run reports | Move to archive or commit as historical evidence |
| `DEFER` (report dirs) | 1 (this gate's own) | Reclassify after gate closes |

No files deleted, moved, or staged.

## Verification

| Step | Result |
|---|---|
| Backend build (`dotnet build server/InsuranceAIPlatform.sln -c Release --nologo`) | **PASS** · 0 warnings · 0 errors · ~15s |
| Backend tests (`dotnet test ... --no-build --nologo --verbosity quiet`) | **PASS** · 119 / 119 / 0 · ~1s (113 prior + 6 new) |
| Frontend build (`npm run build`) | **PASS** · 108 modules · ~3.4s |

## Runtime smoke

| Surface | Result |
|---|---|
| `GET /health` on port 5284 | **200** (`Healthy`) |
| Frontend preview on port 4173 | **200** — `InsuranceAIPlatform · Auto Claim AI Workbench` served |
| `POST /api/claims/CLM-1006/ai-analysis/run` in default Mock mode | **200** · `run_6a22cf95` · providerMode=`Mock` · modelName=`local-mock-v0.1` · cost=`0.0187` · conf=`78` · all Can-* `false` · correlation echoed |
| `GET /api/claims/CLM-1006/ai-analysis` after the run | **200** · returns `run_6a22cf95` (same as POST result) |
| JS bundle contains new advisory UI strings | ✅ `Guardrails (порадницький режим)`, `Останній прогон AI-аналізу`, `canApprovePayout`, `loadLatestAiAnalysis`, `Запустити AI-аналіз` |

## Optional real DeepSeek opt-in smoke (provider available — single attempt)

Started backend with `--AiProvider:Mode=DeepSeek --AiProvider:RealCallsEnabled=true`; startup log: **`AI provider resolved to: RealDeepSeek (opt-in)`**.

| Field | Value |
|---|---|
| Attempts | **1** (200 on first attempt; retry logic NOT exercised live this gate) |
| HTTP status | **200** |
| Elapsed | ~6.7s |
| Provider mode | **DeepSeek** (NOT Mock) |
| Model name | **deepseek-v4-flash** (NOT `local-mock-v0.1`) |
| Tokens | 708 |
| Estimated cost (response) | 0.00019116 |
| Confidence score | 75 |
| Risk level | high |
| Guardrails — all advisory-only | true |
| `canApprovePayout` / `canRejectClaim` / `canAccuseFraudFinal` / `canSendCustomerMessage` / `canChangeClaimStatus` | all **false** |
| Persisted run | `run_a88dc9d0` |
| Key value in any log | **NO** |
| Key value in any exception text | **NO** |

The new retry logic was exercised by the 6 new unit tests (R6-R11). The live smoke happened to receive 200 on first attempt; if a 503 had occurred, the loop would have produced a single bounded retry — verified deterministically by R6.

## G2 external-call evidence triple

| Part | Status | Source |
|---|---|---|
| (a) process log host | **PASS** | `DeepSeek AI provider sending request. Host: api.deepseek.com Path: /v1/chat/completions` + `Received HTTP response headers after Xms - 200` in `api-pre-azure-real.stdout.log` |
| (b) DB row fingerprint | **PASS** | `run_a88dc9d0`: ProviderMode=`DeepSeek`, ModelName=`deepseek-v4-flash`, Tokens=`708`, Cost=`0.0002` (DB-precision), ModelConfidence=`75` — all distinct from Mock fingerprint values |
| (c) shape evidence | **PASS** | Tokens=708 differs from prior DeepSeek runs (688/695/700) proving real LLM non-determinism; ~6.7s round-trip vs synchronous Mock; non-default cost 0.00019116 |

## G5 stub-fallback fingerprint absence

**PASS** — `run_a88dc9d0` carries no Mock fingerprint values (`local-mock-v0.1`, `4261`, `0.0187`, `78`); orchestrator + new retry logic preserve fingerprint separation.

## DB state (post-gate)

| Query | Result |
|---|---|
| `SELECT COUNT(*) FROM ai_analysis.AiAnalysisRuns` | **12** (prior 10 + 2 new this gate) |
| `... GROUP BY ProviderMode` | `Mock=7`, `DeepSeek=4`, `Disabled=1` |
| `audit_cost.AuditEvents WHERE ActionType='AiAnalysisCompleted'` | **11** (+2 this gate) |
| `audit_cost.OutboxMessages WHERE EventType='AiAnalysisCompleted'` | **11** (+2 this gate) |
| Audit + outbox correlation match for `run_a88dc9d0` | ✅ both rows reference `corr-pre-azure-real` |

## Safety / forbidden-scope scan (Subphase 9)

| Check | Result |
|---|---|
| `sk-` shape in any of the 8 changed files | **PASS** — 0 hits |
| `DEEPSEEK_API_KEY` literal-assignment in any changed file | **PASS** — 0 hits |
| Key value in `appsettings.Development.json` | **PASS** — only Mode/RealCallsEnabled/Model/Endpoint/TimeoutSeconds |
| Provider SDK in any csproj across the repo | **PASS** — only Microsoft.Extensions.* + EF + xunit + Swashbuckle |
| Azure resources / config / deployment | **PASS** — none |
| Real PII | **PASS** — synthetic CLM-1006 only |
| Payout execution / customer messaging / binary upload added | **PASS** — none |
| Any guardrail authority flag flipped to `true` | **PASS** — `grep -rE "Can*=true"` returns 0 hits |
| `main` branch touched | **NO** (origin/main = `69e6731` unchanged) |
| Force push | **NO** |
| Commit / push performed | **NO** (by gate design) |

## AI-identification check

| Surface | Result |
|---|---|
| `Co-Authored-By:` footer anywhere | absent |
| `Generated with ...` footer anywhere | absent |
| Model name string in any of the 8 changed files | absent (the only `sk-` literal is in test assertions: `Assert.DoesNotContain("sk-", ex.Message)`) |

## Independent inspector review

| Check | Result |
|---|---|
| Claims verified | **12/12** |
| Warnings | 0 |
| Failures | 0 |
| Key value in any output | **NO** |
| Key value in any tracked file | **NO** |
| Repository leakage | **NO** |
| `main` touched | **NO** |
| Commit created | **NO** |
| AI authority flag flipped to `true` | **NO** |
| Provider SDK added | **NO** |
| Azure resources / config added | **NO** |
| **Final inspector verdict** | **CLEARED** |

### Inspector non-binding recommendations (out of scope; flagged for later cleanup)

- Legacy `'Azure OpenAI'` literal still lives at `src/api/mockInsuranceApi.ts:94` inside the unrelated `getAuditTrace` mock — replace in a future cleanup gate.
- Consider logging `lastFailureCategory` + final attempt number at WARN level on the defensive `response is null` branch so a real outage shows up in observability.
- DB `Cost` column truncation displays `.0002`; future smoke reports could query with `CAST(Cost AS decimal(18,8))` to make the recorded `0.00019116` explicitly visible.

## Screenshots / limitation

No browser automation in this environment. Runtime verification used:
1. Frontend preview HTTP 200 + correct title served (`InsuranceAIPlatform · Auto Claim AI Workbench`).
2. JS bundle string-presence check for the 5 new advisory-only UI labels.
3. Backend Mock + real DeepSeek opt-in HTTP smoke through the BFF.

DOM-level visual verification deferred to a separate operator manual click-through gate.

## Known limitations carry-forward

- `AuditEvent.Actor` formatted as `"Synthetic Adjuster (human)"` by existing AuditCost service Name+Type formatting; still synthetic, no real person.
- Legacy seed run `run_8f3d2a7e` retains `ProviderMode='Disabled'` + NULL on the 9 new nullable columns; expected for the additive nullable migration.
- Legacy `'Azure OpenAI'` literal in `mockInsuranceApi.getAuditTrace` mock (unrelated audit-trace, not AI analysis) — flagged for separate cleanup.

## Blockers

**None.**

## Recommended next safe gate (operator choice)

1. **`COMMIT_AND_PUSH_DEV_PRE_AZURE_LOCAL_COMPLETION_BATCH_ONLY`** — commit exactly the 8 modified tracked files + the triage-notes.md to `dev`; suggested commit message: `feat: wire AI analysis UI + add DeepSeek provider resilience`; FF-push `dev` only; `main` untouched; no force.
2. **`MANUAL_OPERATOR_CLICK_THROUGH_SMOKE`** — operator does a hands-on browser walk of the AI Analysis page in both Mock mode and DeepSeek opt-in mode to confirm visual rendering before commit.
3. **`DOCS_ARCHITECTURE_DURABLE_COMMIT_V0.1`** — commit the 4 durable architecture planning docs to `dev` in a separate docs-only gate (per triage classification).

No gate started; awaiting operator selection.

## What was NOT touched

- `main` branch — no merge, push, or force.
- DevDept folder — untouched.
- Local secrets files — never read.
- Tracked production code outside the 8 listed files — unchanged.
- DB schema — no migration created (only new rows from runtime smoke).
- No Azure / no provider SDK / no real PII / no payout / no customer messaging / no binary upload.
- No force push / no rebase / no amend / no merge / no tag push.
