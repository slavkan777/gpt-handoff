# Full Local Machine Verification (Before DeepSeek) V0.1 — Report

**Gate:** `FULL_LOCAL_MACHINE_VERIFICATION_BEFORE_DEEPSEEK_V0.1` · **Date:** 2026-05-28
**Type:** read-only verification — no source changes, no commit, no push, no real provider call, no Azure.
**Verdict:** **ACCEPT_WITH_LIMITATIONS** — the current local system is ready for `REAL_DEEPSEEK_OPT_IN_LOCAL_INTEGRATION_V0.1`; only the 3 non-blocking notes carried forward from the AI Analysis impl gate remain.

## Git state
| | |
|---|---|
| Branch | `dev` |
| HEAD | `918e8a3aec82462bde9239aeec91d3a46b1082b4` (= `origin/dev`) |
| Last commit | `feat: add AI analysis service guardrails` |
| origin/main | `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` (untouched) |
| ahead/behind dev vs origin/dev | `0 / 0` |
| Source commit this gate | none |
| Source push this gate | none |
| Working tree | only 13 prior-gate planning docs untracked (carried forward across multiple commit/push gates); no other dirty source |

## Phase 1 — Static safety inspection
- **No `Environment.GetEnvironmentVariable("DEEPSEEK_API_KEY")` or `Configuration["DEEPSEEK_API_KEY"]`** anywhere in `server/` or `src/`.
- **No `new HttpClient(`, no `HttpClientFactory`** call sites in implementation; only doc-comment mentions stating "no HttpClient".
- **No provider SDK** in any `.csproj` (no Azure.AI / OpenAI / SemanticKernel / DeepSeek-anything); `Services.AiAnalysis.csproj` packages = EF SqlServer + EF Design + DI Abstractions.
- **No `IFormFile`, `SmtpClient`, `SendGrid`, `Twilio`, `ExecutePayout`, `DisbursePayout`, `ServiceBus`, `EventHub`, `BlobClient`** in source.
- **No `.env`, no secret value** in any tracked file; `appsettings.Development.json` has only `AiProvider:{Mode,RealCallsEnabled,DeepSeek.Model}` (no key) + integrated-auth connection string.
- BFF AI controller injects `IAiAnalysisOrchestrator + IClaimReadService` only — no `DbContext`.
- Service projects: no service-to-service project references.

## Phase 2 — Backend build
| | |
|---|---|
| Command | `dotnet test C:\Projects\InsuranceAIPlatform\server\InsuranceAIPlatform.sln -c Debug --nologo -v minimal` (test run includes build) |
| Exit code | 0 |
| Projects | 9 (BuildingBlocks + 6 services + Api + Tests) |
| Warnings | 0 |
| Errors | 0 |
| Result | **PASS** |

## Phase 3 — Backend tests
| | |
|---|---|
| Command | same `dotnet test` invocation |
| Total | **103** |
| Passed | **103** |
| Failed | 0 |
| Skipped | 0 |
| Result | **PASS** |

## Phase 4 — Frontend build
| | |
|---|---|
| Command | `npm --prefix C:/Projects/InsuranceAIPlatform run build` (`tsc -b && vite build`) |
| Result | **PASS** — 107 modules transformed in 3.57s |

## Phase 5 — Database / migration / seed
DB: `InsuranceAIPlatform` (LocalDB `(localdb)\MSSQLLocalDB`, integrated auth — no password). DevDept untouched.

| Schema | Tables (rows after smoke) |
|---|---|
| `customers_policies` | `SyntheticCustomers=201` (incl. 200 `CUST-T%` + 1 golden), `Policies=201`, `Vehicles=201`, 1 migration |
| `claims` | `Claims=15` (incl. `CLM-1006`), `ClaimStatusHistories=0`, 1 migration |
| `documents` | `ClaimDocuments=15`, `MissingDocumentRequests=2` (after smoke), **2 migrations** |
| `approval` | `ApprovalDrafts=1`, `ApprovalDecisionOptions=4`, 1 migration |
| `audit_cost` | `AuditEvents=18` (after smoke), `OutboxMessages=13` (after smoke), `CostTraces=4`, `TokenUsageTraces=1`, **2 migrations** |
| `ai_analysis` | `AiAnalysisRuns=3` (1 legacy seed + 2 mock smoke), `AiFindings/Evidence/RiskSignals` populated, **2 migrations** including `AddAiAnalysisRunStructuredFields` |

`ai_analysis.AiAnalysisRuns` new columns present (9 / 9): `ModelName`, `Status`, `SummaryText`, `RecommendedActionJson`, `PolicyExplanationText`, `GuardrailFlagsJson`, `RiskLevel`, `CreatedAtUtc`, `CorrelationId`.

## Phase 6 — Backend runtime
| | |
|---|---|
| Command | `dotnet run --project server/InsuranceAIPlatform.Api --no-launch-profile --urls http://localhost:5284` (background) |
| Effective URL | `http://localhost:5284` |
| Startup | ready in ~1s (BFF health probed 200) |
| Correlation headers echoed | `X-Correlation-Id`, `X-Trace-Id`, `X-Bff: api-gateway` |
| Shutdown | clean kill after smoke; no leftover processes |

## Phase 7 — Read endpoint smoke
| Call | HTTP | Notes |
|---|---|---|
| `GET /api/bff/health` | 200 | `service=bff-api-gateway, status=healthy, stage=skeleton-v0.1` |
| `GET /api/bff/demo-status` | 200 | `project=InsuranceAIPlatform, mode=LocalDemo, data=Synthetic, humanApprovalRequired=true` |
| `GET /api/claims` | 200 | list returned with `CLM-1006` first |
| `GET /api/claims/CLM-1006` | 200 | full claim detail incl. customer / policy / vehicle / docs / AI fields |
| `GET /api/claims/CLM-NONEXIST` | 400 (`INVALID_CLAIM_ID`) | safe error shape — rejects non-`CLM-####` ids |
| `GET /api/claims/CLM-9999` (well-formed-but-missing) | — | not exercised on read side (legacy claims controller only checks pattern); AI side correctly returns 404 (see Phase 9) |

## Phase 8 — Write command smoke (4 commands, CLM-1006, synthetic only, unique idempotency keys)
| Call | HTTP | Result |
|---|---|---|
| `POST /api/claims/CLM-1006/approval-draft` (1st) | 200 | `success=true, status=DraftSaved, auditEventId=13, outboxMessageId=8` |
| `POST /api/claims/CLM-1006/approval-draft` (2nd same Idempotency-Key) | 200 | `success=true, outboxMessageId=8 (deduped), warnings=["duplicate…"]` — **idempotency dedup working** |
| `POST /api/claims/CLM-1006/human-decision` (`NeedsMoreInformation`, allowed) | 200 | `success=true, status=AwaitingInformation, auditEventId=15, outboxMessageId=9` |
| `POST /api/claims/CLM-1006/human-decision` (`ApproveAndPay`, forbidden) | **400 `INVALID_DECISION`** | Allowed values enumerated: `ApproveForReview, RejectForReview, NeedsMoreInformation, RequestDocuments` |
| `POST /api/claims/CLM-1006/missing-document-requests` | 200 | `auditEventId=16, outboxMessageId=11` |
| `POST /api/claims/CLM-1006/document-metadata` | 200 | `auditEventId=17, outboxMessageId=12` |

Confirmed during smoke: no payout executed, no customer message sent, no binary uploaded, no claim-status mutation (status transitions go through the `ClaimStatusTransitionRequested` outbox event only), no final AI authority anywhere.

## Phase 9 — AI Analysis mock + disabled-DeepSeek
| Call | HTTP | Result |
|---|---|---|
| `GET /api/claims/CLM-1006/ai-analysis` (initial — pre-existing impl-gate run) | 200 | `runId=run_610e898f, providerMode=Mock, modelName=local-mock-v0.1, status=succeeded` |
| `POST /api/claims/CLM-1006/ai-analysis/run` (new run) | 200 | `runId=run_09d82ca5, providerMode=Mock, modelName=local-mock-v0.1, status=succeeded, riskLevel=high, confidenceScore=78, findings=3, evidence=2, risks=4, costTrace={tokens:4261, estimatedCost:0.0187, currencyCode:"USD"}` |
| `GET /api/claims/CLM-1006/ai-analysis` (after new run) | 200 | latest = `run_09d82ca5` (the new run) |
| `GET /api/claims/CLM-9999/ai-analysis` (well-formed-missing) | **404 `no_ai_analysis_run`** | safe error |
| `POST /api/claims/CLM-9999/ai-analysis/run` (well-formed-missing) | **404 `CLAIM_NOT_FOUND`** | safe error |
| `GET /api/claims/CLM-NOWAY/ai-analysis` (malformed) | 400 `INVALID_CLAIM_ID` | safe error |
| `POST /api/claims/CLM-NOWAY/ai-analysis/run` (malformed) | 400 `INVALID_CLAIM_ID` | safe error |

**Guardrail flags in live response (`run_09d82ca5`):**
- `isAdvisoryOnly=true`, `notice` field present
- `guardrails.advisoryOnly=true`
- `guardrails.requiresHumanReview=true`
- `guardrails.canApprovePayout=false`
- `guardrails.canRejectClaim=false`
- `guardrails.canAccuseFraudFinal=false`
- `guardrails.canSendCustomerMessage=false`
- `guardrails.canChangeClaimStatus=false`

**Disabled-DeepSeek safe path** is covered by the test suite (`AiAnalysisModeTests` M2: configuring `Mode=DeepSeekDisabled` resolves `DisabledDeepSeekAiProvider`, whose `AnalyzeAsync` throws `InvalidOperationException` referencing "never reads DEEPSEEK_API_KEY"; M3: sentinel test asserts no key read). Not exercised live in this gate to avoid changing runtime configuration.

**No `DEEPSEEK_API_KEY` read / log / print occurred** at any point during this verification.

## Phase 10 — Audit / outbox / cost after smoke
| Metric | Before smoke | After smoke | Delta |
|---|---|---|---|
| `audit_cost.AuditEvents` total | 12 | **18** | +6 (4 writes incl. idempotent retry that re-appended audit but deduped outbox; 1 invalid-decision rejected at the controller boundary → no audit; 1 new AI run + the human-decision audit + the missing-doc + the doc-meta) |
| `audit_cost.OutboxMessages` total | 7 | **13** | +6 unique (idempotent retry deduped; human-decision emits 2 outbox events: `HumanDecisionSubmitted` + `ClaimStatusTransitionRequested`) |
| `AuditEvents WHERE ActionType='AiAnalysisCompleted'` | 1 | **2** | +1 |
| `OutboxMessages WHERE EventType='AiAnalysisCompleted'` | 1 | **2** | +1 |
| `documents.MissingDocumentRequests` | 1 | **2** | +1 |
| `ai_analysis.AiAnalysisRuns` for CLM-1006 | 2 | **3** | +1 |
| `ai_analysis.AiAnalysisRuns` ProviderMode for new runs | — | `Mock` (both) | — |

Token + cost metadata persisted on the `AiAnalysisRun` row itself (`Tokens=4261`, `Cost=0.0187`). The legacy `audit_cost.CostTraces` and `TokenUsageTraces` tables remain at their seeded counts (4 / 1) — the AI run path persists on its own service-owned row, which is the documented architecture. No broker integration.

## Phase 11 — Frontend runtime + API seam
| | |
|---|---|
| Command | `npm --prefix C:/Projects/InsuranceAIPlatform run dev` (background) |
| Effective URL | `http://localhost:5173` |
| Startup | dev server ready in ~3-4s |
| `GET /` | HTTP 200, 917 bytes |
| Index sanity | references `src/main.tsx`, `@vite/client`, `@react-refresh`; `<html lang="uk">` |
| API seam | additive `src/api/insuranceApi.types.ts` + `src/api/backendInsuranceApi.ts` include `getClaimAiAnalysis` / `runClaimAiAnalysis` + AI DTO types alongside the 4 prior command client fns |
| UI wiring of AI endpoints | deferred (no `src/features` / `src/pages` / `src/components` changes) — honest |
| Direct frontend-to-service calls | none — frontend talks only through BFF |
| Shutdown | clean kill after probe |

## Phase 12 — Connectivity matrix
| Row | Status | Evidence |
|---|---|---|
| Frontend installed/runnable | **PASS** | `npm run build` 107 modules; `npm run dev` → Vite 5173 200 |
| Backend/BFF installed/runnable | **PASS** | `dotnet test` 0/0 build; `dotnet run` → BFF health 200 |
| SQL Server / LocalDB reachable | **PASS** | `sqlcmd -E` returned rows |
| DB `InsuranceAIPlatform` exists | **PASS** | sqlcmd queries against it succeeded |
| Migrations applied | **PASS** | 8 migrations total across 6 schemas (incl. 2 in `ai_analysis`, 2 in `audit_cost`, 2 in `documents`) |
| Seed data exists | **PASS** | 200 `CUST-T%` + 1 golden + CLM-1006 |
| Read endpoints work | **PASS** | BFF health, demo-status, claims list, claim detail all 200 |
| Write endpoints work | **PASS** | 4 commands + idempotency dedup + invalid-decision 400 |
| AI mock run works | **PASS** | POST `/ai-analysis/run` 200 with structured output + Mock provider |
| Disabled DeepSeek safe path works | **PASS (via tests)** | `AiAnalysisModeTests` M2/M3; no live config change this gate |
| Audit events persist | **PASS** | `AuditEvents` 12 → 18 (incl. 2 `AiAnalysisCompleted`) |
| Outbox messages persist | **PASS** | `OutboxMessages` 7 → 13 (incl. 2 `AiAnalysisCompleted`) |
| Cost / token metadata persists | **PASS** | `AiAnalysisRun.Tokens=4261` and `Cost=0.0187` persisted on each run |
| Safety scan clean | **PASS** | no `DEEPSEEK_API_KEY` runtime read; no `HttpClient` in AI impl; no provider SDK; no payout/messaging/upload; no `.env`/secret in repo |
| Azure absent | **PASS** | no Azure SDK/config/resources; no Azure SDK packages in any csproj |
| Real DeepSeek absent / deferred | **PASS** | provider mode defaults to `Mock`; `DisabledDeepSeekAiProvider` throws without HTTP/key; no real call made |

## Known limitations (carried forward from impl gate — non-blocking)
1. `PersistenceAiAnalysisOrchestrator.BuildResult()` hardcodes `ProviderMode:"Mock"` in the DTO returned to the API. Persistence is correct (uses `_provider.Mode.ToString()`). For Mock mode (the only mode that completes successfully in this gate), DTO and DB agree. Cosmetic fix for any future non-Mock completed mode.
2. Audit `Actor` column persists as `"Synthetic Adjuster (human)"` (the existing AuditCost service's `Name + " (" + Type + ")"` format) rather than the email-form synthetic id `demo.adjuster@insuranceai.local`. The id is correctly defined and passed by the controller. Still synthetic, no real person.
3. Legacy seed run `run_8f3d2a7e` keeps `ProviderMode="Disabled"` and `NULL` values for the 9 new columns. Expected for an additive nullable migration.

## Process notes (this gate only)
- During mid-gate parsing I observed that Git-Bash `/tmp/` paths don't translate to Windows-native Python's CWD interpretation; my interim Python heredoc parses silently fell back to `head -c` raw JSON dumps. Final parses were redone via stdin pipe and verified all expected fields (guardrail flags, provider mode, structured output) — see Phase 9. No functional impact.
- The background API was started with `--no-launch-profile` so `ASPNETCORE_ENVIRONMENT` defaulted to `Production`; `appsettings.Development.json` (AiProvider section) was therefore not loaded — provider mode still resolved correctly to `Mock` via the in-code default (`?? new() { Mode = "Mock" }`), which is defense-in-depth. DB connection succeeded via the in-code LocalDB integrated-auth default in `SeedConstants`.

## Recommended next safe gate
`REAL_DEEPSEEK_OPT_IN_LOCAL_INTEGRATION_V0.1` — opt-in DeepSeek HTTP adapter behind the existing `Services.AiAnalysis.Providers.DisabledDeepSeekAiProvider` namespace, gated by explicit Slava authorization, with local user-secrets for the key (never repo-committed), `IHttpClientFactory` usage, and an isolated test that does not call the real provider. **Not started in this gate.**
