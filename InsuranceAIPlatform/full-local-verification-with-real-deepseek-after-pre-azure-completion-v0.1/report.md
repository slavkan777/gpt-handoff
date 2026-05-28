# InsuranceAIPlatform · Full Local Verification with Real DeepSeek (after Pre-Azure Completion) V0.1 — Report

**Gate:** `FULL_LOCAL_VERIFICATION_WITH_REAL_DEEPSEEK_AFTER_PRE_AZURE_COMPLETION_V0.1` · **Date:** 2026-05-28
**Type:** molecular full local verification / no implementation / no commit / no push / no Azure.
**Verdict:** **ACCEPT**.

## Why this gate existed

GPT accepted the pre-Azure commit gate (`COMMIT_AND_PUSH_DEV_PRE_AZURE_LOCAL_COMPLETION_BATCH_ONLY` → `dev` @ `0372524`). Before any owner-simulation, manual operator test, or Azure planning, the local system must be verified as a fully connected product baseline against the freshly-committed source.

## Git state (unchanged this gate)

| Field | Value |
|---|---|
| Branch | `dev` |
| HEAD | `03725241c8dfdbed7ce17db61fb51d9d7f211116` |
| origin/dev | `03725241c8dfdbed7ce17db61fb51d9d7f211116` (matches HEAD) |
| origin/main | `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` (unchanged) |
| Modified tracked | 0 |
| Staged | 0 |
| Untracked total | 17 (carry-forward only — 9 architecture docs + 8 prior per-run reports per triage) |

## Phase 1 — Source safety scan (before runtime)

| Check | Result |
|---|---|
| `DEEPSEEK_API_KEY` literal assignment in tracked source | **PASS** — 0 hits |
| Key value in `appsettings*.json` | **PASS** — 0 hits |
| `.env` or secrets file staged | **PASS** — none |
| Provider SDK in any csproj (Azure.AI / OpenAI / SemanticKernel / DeepSeek SDK) | **PASS** — none |
| `Can-*=true` authority flip in repo | **PASS** — 0 hits |
| Newly introduced payout / customer messaging / binary upload | **PASS** — none |

## Phase 2 — Build / test / frontend

| Step | Result |
|---|---|
| `dotnet build server/InsuranceAIPlatform.sln -c Release --nologo` | **PASS** · 0 warnings · 0 errors · 17.36s |
| `dotnet test ... --no-build --nologo --verbosity quiet` | **PASS** · 119 / 119 / 0 · ~1s |
| `npm run build` (root Vite app) | **PASS** · 108 modules · ~3.9s |

## Phase 3 — DB / migration / seed baseline

| Query | Result |
|---|---|
| `customers_policies.SyntheticCustomers WHERE Id LIKE 'CUST-T%'` | **200** synthetic users |
| `customers_policies.SyntheticCustomers` total | 201 (200 + 1 golden customer) |
| `claims.Claims WHERE ClaimId='CLM-1006'` | **1** golden claim present |
| `documents.ClaimDocuments WHERE ClaimId='CLM-1006'` | **9** documents |
| `approval.ApprovalDrafts WHERE ClaimId='CLM-1006'` | **1** draft |
| `customers_policies.Policies` | 201 |
| `customers_policies.Vehicles` | 201 |
| `ai_analysis.AiAnalysisRuns` total (pre-gate) | 12 |
| Service schemas with own `__EFMigrationsHistory` | 6 (`ai_analysis`, `approval`, `audit_cost`, `claims`, `customers_policies`, `documents`) |
| `ai_analysis` migrations applied | 2 (`InitialAiAnalysisPersistence` + `AddAiAnalysisRunStructuredFields`) |
| DB name | `InsuranceAIPlatform` (not `DevDept`) ✅ |

## Phase 4 — Backend / BFF startup

- API spawned on `http://localhost:5284` with `ASPNETCORE_ENVIRONMENT=Development`.
- `GET /health` → **200** in 1s (`Healthy`, service `InsuranceAIPlatform.Api`).
- No secrets in startup log.
- Default provider = `Mock` (verified via Phase 7 smoke).

## Phase 5 — Read API smoke (11 endpoints)

| Endpoint | Status |
|---|---|
| `GET /api/claims` | **200** |
| `GET /api/claims/CLM-1006` | **200** |
| `GET /api/claims/CLM-1006/customer-vehicle` | **200** |
| `GET /api/claims/CLM-1006/documents` | **200** |
| `GET /api/claims/CLM-1006/risks` | **200** |
| `GET /api/claims/CLM-1006/policy` | **200** |
| `GET /api/claims/CLM-1006/audit` | **200** |
| `GET /api/claims/CLM-1006/approval` | **200** |
| `GET /api/claims/CLM-1006/ai-analysis` (latest) | **200** |
| `GET /api/demo/scenario` | **200** |
| `GET /api/claims/summary` | **200** |
| `GET /api/claims/CLM-9999` (well-formed unknown) | **404** (expected) |

Correlation echo verified: `X-Correlation-Id: corr-readsmoke-001` round-tripped identically.

## Phase 6 — Safe write command smoke (4 commands + idempotency)

| Command | Result |
|---|---|
| `POST /api/claims/CLM-1006/approval-draft` | success=true · cmd=`cmd-b29840a9` · auditEvent=`28` · outboxMessage=`23` |
| `POST /api/claims/CLM-1006/human-decision` | success=true · cmd=`cmd-2b604b96` · status=`AwaitingInformation` |
| `POST /api/claims/CLM-1006/missing-document-requests` | success=true · cmd=`cmd-cb84831e` |
| `POST /api/claims/CLM-1006/document-metadata` (NO binary upload) | success=true · cmd=`cmd-2cec0fad` |
| Idempotency replay (repeat draft with same Idempotency-Key) | success=true with warning `duplicate-idempotency-key: outbox row 23 already exists` (correctly deduped) |

- No payout executed.
- No customer message sent (missing-document-requests records the request internally only).
- No binary upload attempted.
- AI did not change final claim status (only `human-decision` transitions status, and only to allowed enum values).

## Phase 7 — Mock AI smoke

| Field | Value |
|---|---|
| Endpoint | `POST /api/claims/CLM-1006/ai-analysis/run` |
| HTTP | **200** |
| `runId` persisted | `run_d0e3c4e7` |
| `providerMode` | **Mock** |
| `modelName` | `local-mock-v0.1` |
| Tokens | 4261 |
| Estimated cost | 0.0187 USD |
| Confidence / risk | 78 / high |
| `findings` / `evidence` / `risks` | 3 / 2 / 4 |
| `advisoryOnly` | true |
| `canApprovePayout` / `canRejectClaim` / `canAccuseFraudFinal` / `canSendCustomerMessage` / `canChangeClaimStatus` | all **false** |
| `correlationId` | `corr-fv-mock-ai-001` (echoed) |

## Phase 8 — Real DeepSeek opt-in smoke

Spawned API on `http://localhost:5285` with `--AiProvider:Mode=DeepSeek --AiProvider:RealCallsEnabled=true`. Startup log: **`AI provider resolved to: RealDeepSeek (opt-in)`**.

**Secret pathway (booleans only — no values disclosed):**
- `key_present` = true
- `key_source_kind` = user-scope-env-registry
- `key_shape_plausible` = true
- `auth_result` = accepted (zero 401/403)

**Single attempt (max-time 60s):**

| Field | Value |
|---|---|
| HTTP | **200** |
| Elapsed | ~7s |
| `runId` persisted | `run_3c3c4919` |
| `providerMode` | **DeepSeek** (NOT Mock) |
| `modelName` configured | `deepseek-chat` |
| `modelName` returned | **`deepseek-v4-flash`** (server alias) |
| Tokens | 706 |
| Estimated cost | 0.00019062 USD |
| Confidence / risk | 75 / high |
| Status | succeeded |
| `findings` / `evidence` / `risks` | 2 / 2 / 2 |
| Summary language | **English** (Mock is Ukrainian) |
| Summary snippet | `Claim CLM-1006 involves a 2021 Toyota Camry in a road traffi...` |
| `recommendedAction.action` snippet | `Request the missing rear bumper photo before proceeding. Com...` |
| `advisoryOnly` / `requiresHumanReview` | true / true |
| All `Can-*` flags | **false** |
| `correlationId` | `corr-fv-real-001` (echoed) |
| Retry observed | **NO** — 200 on first attempt (retry logic covered by unit tests R6-R11) |

**Process log evidence:**
```
DeepSeek AI provider sending request. Host: api.deepseek.com Path: /v1/chat/completions
Received HTTP response headers after 512.0449ms - 200
DeepSeek response received. Model: deepseek-v4-flash, Tokens: 706, ContentLength: 2128
```

**Log key-leak scan:** 0 sk-shape matches, 0 `DEEPSEEK_API_KEY` mentions.

**Provider classification:** `available — 200 on first attempt`.

## G2 external-call evidence triple

| Part | Status | Source |
|---|---|---|
| (a) process log host | **PASS** | `Host: api.deepseek.com Path: /v1/chat/completions` + `Received HTTP response headers after 512.0449ms - 200` |
| (b) DB row fingerprint | **PASS** | `run_3c3c4919` — ProviderMode=`DeepSeek`, ModelName=`deepseek-v4-flash`, Tokens=706, Cost=0.0002, ModelConfidence=75 — all distinct from Mock |
| (c) shape evidence | **PASS** | Tokens=706 differs from Mock (4261), prior DeepSeek runs (688/695/700/708), AND this gate's parallel Mock smoke (4261) — proves real LLM non-determinism; ~7s round-trip vs Mock's synchronous response; English summary; non-default cost 0.00019062 |

## G5 stub-fallback fingerprint absence

**PASS** — `run_3c3c4919` row carries zero Mock fingerprint values (no `local-mock-v0.1`, no `4261`, no `0.0187`, no `78`); orchestrator + retry logic preserve fingerprint separation.

## Phase 9 — Frontend local verification

| Check | Result |
|---|---|
| `npm run preview` server on port 4173 | **PASS** |
| `GET /` from preview | **200** |
| Served `<title>` | `InsuranceAIPlatform · Auto Claim AI Workbench` |
| JS asset | `assets/index-DqfPgff0.js` |
| Advisory-only UI strings in JS bundle | **10 / 10 present** (Guardrails / Останній прогон / Advisory-only AI / canApprovePayout / canAccuseFraudFinal / canSendCustomerMessage / canChangeClaimStatus / loadLatestAiAnalysis / Запустити AI-аналіз / Поліс / покриття) |
| Stale `Azure OpenAI` literal in bundle | 1 hit (legacy `getAuditTrace` mock at `mockInsuranceApi.ts:94` — flagged for separate cleanup) |
| Browser automation available | **No** — visual DOM rendering deferred to operator manual click-through |

## Phase 10 — Audit / outbox consistency

**Post-gate counts:**
- AI runs total: **14** (was 12; +2 this gate) · Mock=8, DeepSeek=5, Disabled=1
- `audit_cost.AuditEvents WHERE ActionType='AiAnalysisCompleted'`: **13** (+2)
- `audit_cost.OutboxMessages WHERE EventType='AiAnalysisCompleted'`: **13** (+2)

**Audit events from this gate (6, correlation matched):**
- `corr-fv-real-001` → `AiAnalysisCompleted`
- `corr-fv-mock-ai-001` → `AiAnalysisCompleted`
- `corr-fv-draft-001` → `ApprovalDraftSaved`
- `corr-fv-docmeta-001` → `DocumentMetadataCreated`
- `corr-fv-missing-001` → `MissingDocumentRequested`
- `corr-fv-decision-001` → `HumanDecisionSubmitted`

**Outbox messages from this gate (correlation matched):**
- Same 6 IDs + 1 extra `ClaimStatusTransitionRequested` for the human-decision command (by design — single command emits two distinct outbox messages).

**Forbidden event check:** 0 audit events match `Payout` / `CustomerMessage` / `FraudFinal` / `Reject` ✅

## Phase 11 — Final safety scan after runtime smoke

| Check | Result |
|---|---|
| Git staged files | **0** |
| Modified tracked files | **0** |
| HEAD unchanged | ✅ (`0372524`) |
| `origin/main` unchanged | ✅ (`69e6731`) |
| `sk-` shape in tracked source | **0 hits** |
| `sk-` shape in API logs from this gate | **0 hits** |
| `DEEPSEEK_API_KEY` mentions in API logs | **0 hits** |
| Azure resources / config added | **none** |
| Provider SDK added | **none** |
| Real PII | **none** (synthetic CLM-1006 only) |
| Force push | **no** |
| Commit / push occurred | **no** |
| Aggregate | **PASS** |

## Screenshots / limitation

No browser automation in this environment — no screenshots captured. Frontend verification used:
1. Preview HTTP 200 + correct title served.
2. JS bundle string-presence for the 10 advisory-only UI labels.
3. Backend Mock + real DeepSeek HTTP smoke through the BFF — proving the upstream contract that the UI consumes is sound.

DOM-level visual rendering of the new AI advisory card + guardrail pills + provider/risk chips is deferred to a separate operator manual click-through gate.

## Blockers

**None.**

## Known limitations (carry-forward — non-blocking)

- `AuditEvent.Actor` formatted as `"Synthetic Adjuster (human)"` by existing AuditCost service Name+Type formatting; still synthetic, no real person.
- Legacy seed run `run_8f3d2a7e` retains `ProviderMode='Disabled'` + NULL on the 9 new nullable columns; expected for the additive nullable migration.
- Legacy `'Azure OpenAI'` literal in `src/api/mockInsuranceApi.ts:94` inside the unrelated `getAuditTrace` mock — flagged by prior inspector; out of scope for this gate.
- No browser-automation visual smoke; DOM-level rendering of the AI advisory card is deferred.
- 9 untracked architecture planning docs + 8 untracked prior per-run report dirs remain in working tree per the triage classification.

## Recommended next safe gate (operator choice)

1. **`AUTOMATED_OWNER_SIMULATION_DRY_RUN_V0.1`** — script-driven owner-simulation walk that exercises the AI advisory card + safe write commands + DeepSeek opt-in via the BFF, producing a structured transcript without requiring the operator to be present.
2. **`MANUAL_OPERATOR_CLICK_THROUGH_SMOKE`** — Slava does a hands-on browser walk of the AI Analysis page in both Mock and DeepSeek opt-in modes to confirm visual rendering of the advisory card and all 7 guardrail pills.
3. **`AZURE_READINESS_PRE_FLIGHT_V0.1`** — bounded Azure-readiness planning doc (no Azure code yet) classifying what changes when the app moves from LocalDB + `DEEPSEEK_API_KEY` env-var to Azure SQL + Key Vault + App Configuration.

No gate started; awaiting operator selection.

## What was NOT touched

- `main` branch — verified before AND after smoke as unchanged `69e6731`.
- DevDept folder — untouched.
- Local secrets files — never read.
- Tracked production code — zero changes.
- DB schema — no migration created or changed (only new runtime rows from smoke).
- No Azure / no provider SDK / no real PII / no payout / no customer messaging / no binary upload.
- No force push / no merge / no rebase / no amend / no tag push / no commit / no push.
