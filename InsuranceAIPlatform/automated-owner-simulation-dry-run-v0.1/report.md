# InsuranceAIPlatform · Automated Owner-Simulation Dry Run V0.1 — Report

**Gate:** `AUTOMATED_OWNER_SIMULATION_DRY_RUN_V0.1` · **Date:** 2026-05-28
**Type:** automated owner/user simulation dry run / molecular walkthrough / no implementation / no commit / no push / no Azure.
**Verdict:** **ACCEPT_WITH_LIMITATIONS** (single limitation = no browser DOM automation; visual pixel rendering deferred to operator manual click-through).

## Why this gate existed

Before the operator does a manual local acceptance walkthrough, the system was simulated end-to-end in an owner persona — so the operator does not become the first person to discover broken flows. Companion transcript: `automated-owner-simulation-dry-run-v0.1/transcript.md`.

## Git state (unchanged this gate)

| Field | Value |
|---|---|
| Branch | `dev` |
| HEAD | `03725241c8dfdbed7ce17db61fb51d9d7f211116` |
| origin/dev | `03725241c8dfdbed7ce17db61fb51d9d7f211116` (matches HEAD) |
| origin/main | `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` (unchanged) |
| Modified tracked | 0 |
| Staged | 0 |
| Untracked | 17 (carry-forward only — 9 architecture docs + 8 prior reports per triage) |

## Startup evidence

| Surface | Result |
|---|---|
| Backend `GET /health` (default Mock mode, port 5284) | **200** in 1s — service `InsuranceAIPlatform.Api` healthy |
| Frontend preview `GET /` (port 4173) | **200** · served title `InsuranceAIPlatform · Auto Claim AI Workbench` |
| Secrets in startup log | NO |

## Owner walkthrough — read flow (Phase 3 + 4)

| Step | Result |
|---|---|
| `GET /api/claims` (queue) | 5 claims; CLM-1006 visible with `customer='Роберт Джонсон' risk='Високий' aiStatus='AI-перевірено'` |
| `GET /api/claims/summary` | totalActive=47 · pendingReview=12 · highRisk=8 · avgSlaRemainingHours=14.3 · processedToday=6 · aiAnalysisRunning=2 |
| `GET /api/demo/scenario` | 7 demo steps (portfolio storyline anchor) |
| `GET /api/claims/CLM-1006` and 5 sub-routes (`/customer-vehicle`, `/policy`, `/risks`, `/approval`, `/audit`) | **all 200** |
| Claim detail key fields | Toyota Camry 2021 · POL-2025-AC-4421 · ДТП 2026-05-18 · status `В роботі` · risk 82/conf 78 · estimate $2720 vs benchmark $1970 · deductible $500 · recommended payout $1800 · docs 6/7 · missing: rear bumper photo |
| `GET /api/claims/CLM-9999` (well-formed unknown) | **404** (safe) |
| `GET /api/claims/INVALID` (malformed) | **400** (format validation rejects before DB) |
| `X-Correlation-Id` echo | PASS |

## Document flow (Phase 5)

| Field | Value |
|---|---|
| `GET /api/claims/CLM-1006/documents` | **200** · 7 items returned |
| Breakdown | 4 documents + 3 photos + 1 missing (rear bumper photo) |
| Binary upload attempted | NO |
| Real PII | NO — synthetic only |

## Safe write flow (Phase 6) — 4 commands + idempotency replay

| Command | Result |
|---|---|
| `POST /approval-draft` | `success=true cmd=cmd-ee3c961e audit=35 outbox=30` |
| `POST /human-decision` (NeedsMoreInformation) | `success=true cmd=cmd-02d2cc1b status=AwaitingInformation` |
| `POST /missing-document-requests` | `success=true cmd=cmd-b92469db` — internal log only |
| `POST /document-metadata` (NO binary upload) | `success=true cmd=cmd-ef40db4c` |
| **Idempotency replay** of draft (same Idempotency-Key) | `success=true` with warning `duplicate-idempotency-key: outbox row 30 already exists` |

**Idempotency design note:** audit table contains 2 rows for `owner-sim-draft-001` (both attempts recorded) while outbox contains 1 row (side effect published once). Intentional pattern: full audit of attempts + at-most-once-side-effect.

## Mock AI flow (Phase 7)

| Field | Value |
|---|---|
| Endpoint | `POST /api/claims/CLM-1006/ai-analysis/run` |
| HTTP | **200** |
| `runId` | `run_d72d9196` |
| `providerMode` / `modelName` | **Mock** / `local-mock-v0.1` |
| Tokens / Cost / Conf / Risk | 4261 / 0.0187 / 78 / `high` |
| findings / evidence / risks | 3 / 2 / 4 |
| `recommendedAction.action` | `Запросіть відсутнє фото бампера.` (advisory read-only text) |
| `advisoryOnly` / `requiresHumanReview` | true / true |
| All `Can-*` | **false** |
| `notice` | `AI output is advisory only — human decision is final.` |

## Real DeepSeek opt-in flow (Phase 8)

| Field | Value |
|---|---|
| Endpoint | `POST /api/claims/CLM-1006/ai-analysis/run --AiProvider:Mode=DeepSeek --AiProvider:RealCallsEnabled=true` |
| Key — `present` / `source` / `shape_plausible` | true / `user-scope-env-registry` / true |
| Resolved provider startup log | `AI provider resolved to: RealDeepSeek (opt-in)` |
| Attempts / HTTP / elapsed | 1 / **200** / 6s |
| `runId` | `run_6fcc493e` |
| `providerMode` / `modelName` returned | **DeepSeek** / `deepseek-v4-flash` |
| Tokens / Cost / Conf / Risk | **702** / 0.00018954 / 75 / `high` |
| findings / evidence / risks | 2 / 2 / 2 |
| Summary language | **English** (Mock = Ukrainian) — `Claim CLM-1006 involves a 2021 Toyota Camry in a road traffi...` |
| `recommendedAction.action` | `Request the missing rear bumper photo before proceeding. Com...` |
| `advisoryOnly` / `requiresHumanReview` | true / true |
| All `Can-*` | **false** |
| `correlationId` echoed | `owner-sim-real-001` |
| Process log host hit | `Host: api.deepseek.com Path: /v1/chat/completions` · `Received HTTP response headers after 538.5364ms - 200` |
| Key value in log | NO (0 `sk-` shape · 0 `DEEPSEEK_API_KEY` mentions) |
| Retry observed live | NO — 200 on first attempt; retry logic covered by unit tests R6-R11 |
| Provider classification | **available — 200 on first attempt** |

### G2 evidence triple

| Part | Status |
|---|---|
| (a) process log host | **PASS** |
| (b) DB row fingerprint | **PASS** — `run_6fcc493e` carries DeepSeek/deepseek-v4-flash/702/conf 75 — distinct from all Mock fingerprints |
| (c) shape evidence | **PASS** — 702 tokens unique across all 6 historical real DeepSeek runs; English summary; 6s round-trip |

### G5 stub-fallback check
**PASS** — `run_6fcc493e` contains zero Mock fingerprint values.

## Frontend UI evidence (Phase 9)

| Check | Result |
|---|---|
| Preview HTTP | 200 |
| Served `<title>` | `InsuranceAIPlatform · Auto Claim AI Workbench` |
| JS bundle file | `assets/index-DqfPgff0.js` |
| Advisory-only UI strings | **15 / 15 present** (`Advisory-only AI`, `Останній прогон AI-аналізу`, `Guardrails (порадницький режим)`, `Зведення`, `Рекомендована дія (порадницька)`, `Поліс / покриття`, `Запустити AI-аналіз`, `canApprovePayout`, `canRejectClaim`, `canAccuseFraudFinal`, `canSendCustomerMessage`, `canChangeClaimStatus`, `advisoryOnly`, `requiresHumanReview`, `loadLatestAiAnalysis`) |
| Browser automation | NOT available |
| Visual DOM rendering | deferred to operator manual click-through gate |

## Audit / outbox evidence (Phase 10/11)

| Metric | Value |
|---|---|
| `ai_analysis.AiAnalysisRuns` total post-gate | 16 (+2 this gate) |
| Runs by mode | Mock=9, DeepSeek=6, Disabled=1 |
| Owner-sim audit events | 7 (5 commands + 1 Mock AI + 1 DeepSeek AI; +1 idempotency-replay audit) |
| Owner-sim outbox messages | 7 |
| Forbidden audit categories | Payout=0 · CustomerMessage=0 · FraudFinal=0 · AiClaimReject=0 |
| Correlation match between response/audit/outbox | **PASS** for all 5 commands |

## Adversarial safety checks (Phase 10)

| Probe | Result |
|---|---|
| `POST /api/claims/CLM-1006/payout` | **404** (endpoint does not exist — architecturally impossible for AI to approve) |
| `POST /api/claims/CLM-1006/customer-messages` | **404** (endpoint does not exist) |
| `POST /api/claims/CLM-1006/fraud-confirm` | **404** (endpoint does not exist) |
| `POST /human-decision` with `decision='ApproveForPayout'` | **rejected** with sanitized message `"Decision 'ApproveForPayout' is not allowed. Allowed values: ..."` (allowlist-enforced enum) |
| `sk-`-shape in any HTTP response body | **none** |
| Unknown claim ai-analysis | safe envelope `{code:'no_ai_analysis_run', message:..., traceId:...}` — no DB exception leak |
| Duplicate idempotency replay | safe — second response = `success=true` with explicit `duplicate-idempotency-key` warning; outbox not re-emitted |
| Real provider failure silently faked as Mock | NO — orchestrator skips persistence on provider exception |
| Azure dependency required for local run | NO — only LocalDB + env-var; no Key Vault, no App Configuration, no managed identity |

## Portfolio / interview readiness (Phase 11)

### What the operator can demonstrate
- Backend: .NET 9 + EF Core 9 · 6 service-owned schemas with their own DbContexts + migrations · BFF/API gateway · sanitized error envelopes · idempotency · audit + outbox-as-record-of-side-effects.
- AI: `IAiProvider` abstraction with three opt-in-gated providers · advisory-only guardrails enforced at type level (`GuardrailFlags` private ctor + immutable `Advisory` singleton) · forbidden-token scan via `AdvisoryOnlyGuardrailEvaluator` · real DeepSeek HTTP adapter with bounded retry + sanitized error path.
- Frontend: React + TypeScript + Vite + Redux Toolkit + Redux Saga + shadcn/ui · BFF-only API surface · env-driven mock/backend facade · Ukrainian + English content paths.
- Testing: 119 backend tests including 6 retry-semantics tests · G2 evidence triple preserved across 6 historical real DeepSeek runs.
- Security: secret stays out of repo / appsettings / csproj / logs / exceptions · opt-in resolved at request time via `IConfiguration` · rotation precedent documented.

### What the project proves technically
- Multi-AI workflow (implementer + reviewer + auditor) can ship audit-grade work without privileged access leaks.
- Provider-agnostic AI integration without SDK lock-in.
- Domain authority separation: AI is advisory by **compile-time** type-level guarantee, not just a runtime check.

### Still intentionally local-only (do NOT claim production-ready)
- LocalDB SQL Server (no Azure SQL yet).
- `DEEPSEEK_API_KEY` in user-scope env (no Key Vault yet).
- No Application Insights / App Configuration / managed identity.
- No HTTPS termination / rate-limiting / CDN.

### Must be visually checked by operator manual step
- AI advisory card renders with provider chip, model badge, summary, recommended-action read-only block, policy explanation, all 7 guardrail pills with safety colour-coding.
- Page does NOT show any decision-as-action button (approve-payout / reject / fraud-confirm / send-message).
- Switching Mock ↔ DeepSeek (via env var + restart) produces visibly different content + metadata in the UI.

## Final safety scan (Phase 12)

| Check | Result |
|---|---|
| Git modified tracked | **0** |
| Git staged | **0** |
| HEAD unchanged | ✅ |
| origin/main unchanged | ✅ |
| Commit / push occurred | NO |
| Source edits | NO |
| Migrations / schema changes | NO |
| Secret in logs / handoff | NO |
| Azure resources / config | NONE |
| Provider SDK added | NONE |
| Real PII | NONE |
| Payout / customer message / binary upload | NONE |
| Ports 5284 / 5285 / 4173 after smoke | all free |

## Screenshots / limitation

No browser-DOM automation in this environment — no screenshots captured. Visual rendering verification used: (1) frontend preview HTTP 200 + correct served title; (2) JS bundle string presence for all 15 advisory-only UI labels; (3) backend Mock + real DeepSeek HTTP smoke through the BFF — proving the upstream contract the UI consumes is sound; (4) bundle does NOT contain any decision-as-action keywords beyond the 5 read-only Can-* labels in their negation context.

## Blockers

**None.**

## Limitations

- **Non-blocking:** No browser-DOM automation available; visual rendering of the AI advisory card + guardrail pills + provider/risk chips deferred to operator manual click-through gate.
- `AuditEvent.Actor` formatted as `"Synthetic Adjuster (human)"` by existing AuditCost service Name+Type formatting (carry-forward).
- Legacy seed run `run_8f3d2a7e` retains `ProviderMode='Disabled'` + NULL on new columns (carry-forward; expected).
- Legacy `'Azure OpenAI'` literal in `src/api/mockInsuranceApi.ts:94` inside unrelated `getAuditTrace` mock — out of scope.
- Idempotency design records every attempt in audit (2 rows for `owner-sim-draft-001`) while deduplicating outbox (1 row) — intentional "attempts logged, side-effect published once" pattern; operator should know this is by design.
- 9 untracked architecture planning docs + 8 untracked prior per-run report dirs remain per triage classification.

## Recommended next safe gate

**`MANUAL_OPERATOR_CLICK_THROUGH_SMOKE`** — operator (Slava) can now confidently do a hands-on browser walk; upstream contract proven sound, every backend safety check passed in this simulation. Suggested operator checklist:

1. Start backend (`dotnet run --project server/InsuranceAIPlatform.Api`) + frontend (`npm run dev` or `npm run preview`).
2. Open `http://localhost:4173/` (or dev server URL), navigate to Claims list, click `CLM-1006`.
3. Open the AI Evidence page.
4. Confirm advisory card renders with provider chip + 7 guardrail pills.
5. Click `Запустити AI-аналіз` in Mock mode → confirm UI updates with new run data.
6. Stop backend → restart with `--AiProvider:Mode=DeepSeek --AiProvider:RealCallsEnabled=true` → re-run analysis → confirm provider chip changes to `DeepSeek` and model badge to `deepseek-v4-flash`.

**Time estimate: 5-10 minutes.**

After operator manual checkpoint, candidates: `AZURE_READINESS_PRE_FLIGHT_V0.1` (planning doc only) or `DOCS_ARCHITECTURE_DURABLE_COMMIT_V0.1` (commit the 4 durable architecture docs).

## What was NOT touched

- `main` branch — verified unchanged.
- DevDept folder — untouched.
- Local secrets files — never read.
- Tracked production code — zero changes.
- DB schema — no migration created (only runtime smoke rows).
- No Azure / no provider SDK / no real PII / no payout / no customer messaging / no binary upload.
- No force push / no merge / no rebase / no amend / no tag push / no commit / no push.
- No untracked report/docs deleted or archived.
