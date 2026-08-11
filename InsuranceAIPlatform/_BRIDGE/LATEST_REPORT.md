# PROJECT: InsuranceAIPlatform — WARDEN-Lab Gate 0 — Current Truth Adoption

REQUEST_ID: REQ-2026-08-11-IAP-WARDEN-LAB-G0-CURRENT-TRUTH
GATE: WARDEN_LAB_GATE_0_CURRENT_TRUTH_ADOPTION
DATE: 2026-08-11
EXECUTOR: Claude (read-only executor/discoverer). NOT an external independent reviewer.

---

## 0. MACRO CRITIC PREFLIGHT (internal, read-only)

Run before any project command, against the canonical `ACTIVE_REQUEST.md`.

**CRITIC_VERDICT: PASS**

Every assumption the Macro makes was checked mechanically rather than assumed:

| Assumption | Probe result |
|---|---|
| Local workspace exists | `C:\Projects\InsuranceAIPlatform` present |
| `slavkan777/InsuranceAIPlatform` reachable | yes, 3 branches |
| `slavkan777/ai-kb` reachable (needed by §8) | yes, 7 branches |
| .NET / Node / npm / Python / Azure CLI present | all present |
| Azure CLI authenticated (§7) | yes, `az account show` exit 0 |
| Build output gitignored (§2 safety) | yes — `server/.gitignore` covers `bin/`,`obj/`; root covers `node_modules`,`dist`,`playwright-report`,`test-results`; sidecar covers `.venv`,`__pycache__` |

Checks 1–15 findings — defects are real but **non-blocking and disclosable**, none forces a guess or authorizes an unsafe mutation:

- **D1 (moderate) — §12 vs §13 contradiction.** §12 requires proving "no commit/push side effect occurred"; §13 requires publishing the report to three paths in the `gpt-handoff` **git** repo. Both cannot be literally true. Resolved by scoping: §2/§12 govern the **InsuranceAIPlatform** product repo (proved zero side effects there); §13 publishing targets the **handoff** repo and is disclosed explicitly in §O.
- **D2 (low) — §8 has no fallback** if AIKB is unreachable. Moot: it is reachable.
- **D3 (moderate) — §12's READY bar ("enough primary truth") is judgment-based**, not mechanical like WARDEN's `MATERIAL_CLEAN`. Constrained by §8's materiality definition. Acceptable for a discovery gate; carried into the scorecard.
- **D4 (low)** — §2's npm-install carve-out is conditional; graceful degradation exists via `NOT_RUN_WITH_REASON`.
- **D5 (low)** — §2 assumes build output is ignored; verified true before building rather than assumed.

Check 14: the Macro never treats the internal Critic as external evidence. This Critic is **not** Codex, not external review, and is not evidence.

---

## A. EXECUTION IDENTITY

- Executor: Claude, read-only discoverer. No Owner authority claimed. Gate 0 not self-accepted.
- One autonomous run, no owner-facing micro-prompts.
- Git 2.37.3.windows.1 · .NET SDKs 8.0.423/9.0.315/10.0.301/10.0.302 · Node v21.3.0 · npm 10.2.4 · Python 3.11.2 · Azure CLI authenticated (`Azure subscription 1`, user).

## B. REPOSITORY IDENTITY

| Fact | Value |
|---|---|
| Repo root | `C:\Projects\InsuranceAIPlatform` (via `git rev-parse --show-toplevel`) |
| Remote | `https://slavkan777@github.com/slavkan777/InsuranceAIPlatform.git` — **matches expected** |
| Branch | `rag/local-foundation-mega-v0.1` |
| HEAD | `f9e34c65d98b251fa6dd8931d17256bb00a70992` |
| Upstream | `origin/rag/local-foundation-mega-v0.1`, ahead/behind **0/0** |
| Tracked files | 576 |
| Worktree at start | 2 untracked pre-existing `.docx` files, nothing else |

**Branch divergence — material and unresolved by the Owner:**

| Branch | Local | Remote | Same | Last commit |
|---|---|---|---|---|
| `main` | `69e67312` | `a8420d49` | **NO** | remote 2026-07-07 |
| `dev` | `70af7748` | `70af7748` | yes | 2026-05-30 |
| `rag/local-foundation-mega-v0.1` | `f9e34c65` | `f9e34c65` | yes | 2026-07-26 |

Resolved via a disposable bare clone in scratchpad (governed repo untouched):
- remote `main` ↔ working branch = **11 / 48** commits apart.
- The 11 main-only commits are **docs-only** (reviewer guide, assignment submission checklist, demo script, AI-assisted development notes).
- The 48 working-branch-only commits carry the actual RAG/product work.
- `git branch --contains f9e34c65` → **only** `rag/local-foundation-mega-v0.1`. **The product work is NOT merged into `main`, and `main` is the repository default branch.**

## C. WORKTREE IMMUTABILITY PROOF

After **all** builds, tests, E2E and probes:

```
HEAD    f9e34c65d98b251fa6dd8931d17256bb00a70992   (unchanged)
branch  rag/local-foundation-mega-v0.1             (unchanged)
remotes origin                                      (unchanged)
worktree entries   2   (identical to baseline: the same 2 pre-existing .docx)
modified tracked files  0
staged changes          0
```

No source/test/config/doc edit, no branch/commit/push, no lockfile change.

## D. CURRENT TOPOLOGY MAP

**Frontend** — React 18 + TypeScript + Vite 5 + Tailwind, Redux Toolkit + redux-saga.
- Entry `src/main.tsx`; router `src/app/router.tsx`; store `src/app/store.ts`; sagas `src/app/rootSaga.ts`.
- Routes: `/login`; `/` (Dashboard) behind `RequireAuth` + `AppShell`; `/claims`; `/claims/:claimId` (`ClaimShell`) with children `documents`, `ai-evidence`, `risks`, `approval`, `audit`, `policy`, `customer-vehicle`; `/customers`; `/demo`.
- API facade: `src/api/insuranceApi.ts` selecting `backendInsuranceApi.ts` or `mockInsuranceApi.ts` (`VITE_INSURANCE_API_MODE`).
- AI/RAG UI surfaces: `src/features/rag/{ragSlice,ragSaga,ragSelectors}.ts`, `src/features/aiReview/*`, `src/pages/AiEvidencePage.tsx`.
- **Every route is operator/adjuster-facing. There is no customer-facing surface.**

**.NET backend** — `server/InsuranceAIPlatform.sln`, 10 projects, all `net9.0`.
- Host `InsuranceAIPlatform.Api`; services `Claims`, `CustomersPolicies`, `Documents`, `AiAnalysis`, `Approval`, `AuditCost`; `BuildingBlocks`; `DbMigrator`; `Tests`.
- Endpoints (all operator-scoped, none customer-facing):
  - `ClaimsController` `api/claims` — `summary`, list, `{claimId}`, `/documents`, `/ai-evidence`, `/risks`, `/policy`, `/customer-vehicle`, `/approval`, `/audit`
  - `RagController` — `POST {claimId}/rag/ask`, `GET {claimId}/rag/evidence-search`, `/rag/evaluation-questions`, `/rag/audit`, `/rag/similar-claims`, `/rag/infrastructure`, `POST /rag/infrastructure/reindex`
  - `AdvancedAiReviewController` — `POST {claimId}/advanced-ai-review`
  - `AiAnalysisController`, `AiDecisionController`, `ClaimCommandsController` (approval-draft, human-decision, missing-document-requests, document-metadata, documents/upload, payout-simulation), `ClaimWriteController`, `CustomersController`, `DemoController`, `BffController`, `SystemController`, `HealthController`
- Persistence: EF Core, per-service DbContext, **18 migrations**; provider SQL Server (LocalDB locally, Azure SQL deployed).

**Python/LangChain sidecar** — `ai-sidecars/langchain-claim-analytics/`
- FastAPI + LangChain (`ChatPromptTemplate`, `PydanticOutputParser`, LCEL chain), `app.py`, `test_app.py`, `requirements.txt`, `Dockerfile`.
- Endpoints `GET /health`, `POST /advanced-claim-analytics`.
- Provider modes: **Deterministic by default**; real `ChatOllama` only when `OLLAMA_BASE_URL` is set **and** reachable.
- Called from .NET via `AdvancedClaimAnalyticsClient` gated by `AdvancedAiReviewOptions` (`Enabled=false` by default, `MaxEvidenceChunks=12`).

## E. BUILD / TEST TRUTH

| Verification | Command | Result |
|---|---|---|
| .NET build | `dotnet build server/InsuranceAIPlatform.sln -c Release` | **PASS** — exit 0, **0 warnings, 0 errors**, 18.3s |
| .NET tests | `dotnet test ... -c Release --no-build` | **PASS** — exit 0, **288 passed / 0 failed / 0 skipped**, 4s |
| Frontend build | `npm run build` (`tsc -b && vite build`) | **PASS** — exit 0, 156 modules, `dist/assets/index-*.js` 507.41 kB (gzip 142.18 kB), 6.45s |
| Frontend lint | `npm run lint` | **NOT_CONFIGURED** — script declared, but `eslint` is absent from `devDependencies` and no eslint config file exists. The declared lint gate cannot run. |
| Playwright E2E | `npx playwright test --config playwright.mock.config.ts` | **113 passed / 8 failed**, 8.6 min — see boundary below |
| Python sidecar tests | `.venv` + `pytest -q` | **PASS** — **16 passed**, 3.14s |

**E2E honest boundary.** `playwright.mock.config.ts` starts *only* the Vite dev server and forces `VITE_INSURANCE_API_MODE=mock`; its own header documents it for the RAG subset. I ran the **whole** suite under it. All 8 failures are backend-dependent specs (`03-customers`, `08-zero-to-end`, `11-customers-deep`, `18-persistence`, `21-created-claim-detail-binding`) failing on "expected backend-allocated `CUST-T####` id" and reload-persistence — i.e. they require the .NET API that this config deliberately does not start. Correct label for those 8: **NOT_RUN_WITH_REASON (invalid configuration)**, not "product defect". The full-stack config was not exercised. **The 113 passes are valid**, including `22-rag-evidence` and `23-rag-confidence-contract`.

Notable passing assertion: `22-rag-evidence … NEGATIVE_PASS: runtime row reads disabled/mock; does NOT claim a live model is running` — the UI is tested for *not* overstating AI liveness.

## F. RAG / AI TRUTH

| # | Capability | Status | Primary evidence |
|---|---|---|---|
| A | Document/evidence ingestion | IMPLEMENTED + TESTED | `Rag/Ingestion/EvidenceIngestionService.cs` — max 24 chunks × 800 chars, additive, per-key idempotent |
| B | Chunking / storage | IMPLEMENTED + TESTED + LIVE | `EvidenceChunk` rows; live SQL shows 57 chunks |
| C | Retrieval | IMPLEMENTED + TESTED + LIVE | `RagRetrievalService`, `VectorRetrievalRouter`; live index 13/13 embedded for CLM-1006 |
| D | Claim scoping / cross-claim isolation | IMPLEMENTED + TESTED + LIVE | `DbRagChunkSource` filters `Where(c => c.ClaimId == claimId)` **in SQL** |
| E | Grounded answer generation | IMPLEMENTED + TESTED + LIVE | `MockGroundedAnswerGenerator` (default); `LocalLlamaGroundedAnswerGenerator` (seam) |
| F | Citations / retrieved chunk ids | IMPLEMENTED + TESTED + LIVE | `BuildCitations` derives citations from **already-retrieved** chunks; shared with the LocalLlama generator so *a live model never authors citations* |
| G | Insufficient-evidence handling | IMPLEMENTED + TESTED | `retrieved.Count == 0` → "There is not enough relevant evidence in this claim to answer. Human review is recommended.", confidence **0** |
| H | Provider routing / fallback | IMPLEMENTED + TESTED + LIVE | `VectorRetrievalRouter`, `HttpRagRuntimeProbe`; backend reports `qdrant` **only** on a real serving round-trip, else honestly `in-memory-hash` |
| I | Confidence / cost / audit / trace | IMPLEMENTED + TESTED + LIVE | `RagAuditTrace` persists traceId, chunk ids, citations JSON, confidence, providerMode, tokens, `CostMicros=0`, retrievalMs, `AdvisoryOnly=true`. Live: **56 audit traces** |
| J | LangChain advanced review | IMPLEMENTED + TESTED, **LIVE BUT NOT SERVING** | see below |
| K | Human-in-the-loop / advisory-only | IMPLEMENTED + TESTED + LIVE | `AdvisoryOnly=true` hardcoded in the trace; advisory footer on every answer; `risk` use-case explicitly "advisory, no accusations"; sidecar `advisoryOnly=True` |

**Embedding truth:** `local-hash-embed-v0.1`, 256 dimensions, deterministic feature hashing. **Not** a learned/neural embedding model and not an external embedding service.

**Confidence is never invented:** derived from the top retrieval score (`ConfidenceFromScore`), shared by both generators.

**Live provider truth (from the deployed app's own `/rag/infrastructure`, claim CLM-1006):**
```
sqlSourceOfTruth : healthy — policyClauses 8, evidenceChunks 57, evaluationQuestions 21, auditTraces 56
evidenceMemoryIndex: healthy — 13/13 embedded, local-hash-embed-v0.1, dim 256
vectorRuntime    : disabled, enabled=false, backend=in-memory-hash, reachable=false   → Qdrant NOT live
localReasoningRuntime: disabled, enabled=false, model=llama3.1:8b, reachable=false    → Ollama NOT live
```
Deployed env confirms independently: `AiProvider__Mode=Mock`, `Rag__QdrantEnabled=false`, `Rag__LocalLlamaEnabled=false`. **No paid managed LLM is enabled anywhere.**

**J — the sidecar is enabled but not serving.** Deployed env sets `AdvancedAiReview__Enabled=true` and points at the internal FQDN. The sidecar container app revision `iap-langchain-sidecar--0000002` is **Healthy, 1 replica, RunningAtMaxScale**. Yet a live `POST /api/claims/CLM-1006/advanced-ai-review` returns:
```
providerMode "Unavailable", confidence 0, citations [], advisoryOnly true,
summary "The advanced analysis service is unavailable. Please use the core RAG analysis with citations."
```
So the .NET→sidecar call path is currently failing (internal ingress `external=false`, targetPort 8090, transport Auto, `minReplicas=0`, configured with an `https://` internal URL). **Positive safety finding:** the failure degrades honestly — advisory-only preserved, confidence 0, nothing fabricated.

## G. DATA / PERSISTENCE / SAFETY TRUTH

- **DB provider:** SQL Server. Local `(localdb)\MSSQLLocalDB;Database=InsuranceAIPlatform`. Deployed: Azure SQL `iap-sql-r2-6c7g465/InsuranceAIPlatform`, **Online**, `GP_S_Gen5_1` (serverless), 2 GB, germanywestcentral.
- **Migrations:** 18 across 6 service DbContexts. None created or applied by this Gate.
- **Synthetic boundary:** enforced and self-reported — live `/api/customers/count` → `{"count":208,"syntheticOnly":true}`. Chunk ids namespaced `{claimId}-uploaded-…`.
- **Real PII:** none in scope. Sidecar docstring: "No external service, no API key, no PII handling."
- **Document storage:** text uploaded via `POST {claimId}/documents/upload`, chunked into claim-scoped `EvidenceChunk` rows (migration `AddDocumentContentForLocalSandbox`). No blob/file path in the RAG flow.
- **Scoping keys:** `ClaimId` on `EvidenceChunk`, `RagAuditTrace`, `RagEvaluationQuestion`.
- **Audit structures:** `RagAuditTrace`, `AiAnalysisRun` structured fields, `AddOutboxAndCommandAudit`, `AuditCost` service.
- **Human approval controls:** `approval-draft`, `human-decision`, `missing-document-requests`, `payout-simulation` (explicitly a *simulation*).
- **Autonomous payout/fraud/messaging code:** none found. No customer messaging path. Fraud is explicitly refused in the generator lead text.
- **Secrets:** no secret values read or printed. Key names only — `APPLICATIONINSIGHTS_CONNECTION_STRING`, `ConnectionStrings__InsuranceAIPlatform` (both redacted), Key Vault `iapdemokv6c7g465vrcfi4` present. `RagOptions` and `AiProviderOptions` deliberately carry no key property.

> **SAFETY FINDING — P1, material.** **The backend has no authentication or authorization whatsoever.** No `[Authorize]`, no `AddAuthentication`, no `UseAuthorization`, no JWT anywhere in `server/**`. Proven empirically: from the public internet, with **no credentials**, I retrieved `/api/claims` (real claim list), `/api/claims/CLM-1006/ai-evidence` (findings + evidence text) and `/api/claims/CLM-1006/rag/infrastructure`. `RequireAuth` is a **client-side redirect only** and its own comment says "local/demo only — not a production auth guard". Claim isolation is by `claimId` **parameter**, so anyone who knows or guesses an id reads that claim's evidence. Data is synthetic, so this is not a live PII breach — but it is a hard architectural blocker for any customer-facing surface.

## H. AZURE / LIVE RUNTIME — READ ONLY

Subscription `Azure subscription 1` (user auth). Resource group `rg-iap-demo` (westeurope).

| Resource | Type | State |
|---|---|---|
| `iap-demo-swa` | Static Web App (Free) | `kind-meadow-03cf73103.7.azurestaticapps.net` — **HTTP 200** |
| `iap-demo-api` | Container App | external ingress, **Running**, rev `--0000008`, 1 replica, scale 0→2 — `/health` **200 Healthy**, environment `Production` |
| `iap-langchain-sidecar` | Container App | **internal** ingress (`external=false`), port 8090, rev `--0000002` **Healthy**, 1 replica, scale 0→1 |
| `iap-sql-r2-6c7g465` | Azure SQL server | db `InsuranceAIPlatform` **Online**, `GP_S_Gen5_1` |
| `iapdemokv6c7g465vrcfi4` | Key Vault | present (not read) |
| `iapdemost6c7g465vrcfi4` | Storage | present |
| `iap-demo-appi` / `iap-demo-law` | App Insights / Log Analytics | present |
| `iap-demo-cae` | Container Apps Env | present |
| `iap-demo-api-mi` | User-assigned identity | present |

The SWA hostname matches the CORS allow-list in `appsettings.json` — deployment and source agree.

No Azure write, restart, scale, deploy, config change, secret retrieval or DB mutation was performed.

> **HONESTY FINDING — P2, material for governance.** `GET /api/system/demo-status` on the **live** deployment returns
> `{"backend":"Skeleton","database":"NotConnected","aiProvider":"NotConnected","claimFlow":"Planned","message":"Backend skeleton is running. Claims API, database, and AI provider are planned future gates."}`
> `SystemController.cs` shows this is a **hardcoded constant**, not a computed status. The *same deployment* simultaneously serves 47 active claims, 208 customers and a healthy Azure SQL RAG store with 56 audit traces. **The system's own status endpoint contradicts the system.** `BffController`/`/api/bff/health` is similarly stale (`stage: skeleton-v0.1`, `upstream: in-memory-read-service`, services "Deferred"). Anyone — human, GPT, or a future WARDEN gate — reading these endpoints as truth would be misled. This is the "REPORT != EVIDENCE" pathology baked into the product.

## I. AIKB / CURRENT TRUTH DRIFT MATRIX

| AIKB / historical claim | Current primary evidence | Status | Materiality | Notes |
|---|---|---|---|---|
| Source repo `slavkan777/InsuranceAIPlatform` | remote matches exactly | **MATCH** | — | |
| Branch `rag/local-foundation-mega-v0.1` | checked out, = remote, 0/0 | **MATCH** | Medium | AIKB flagged it "not assumed current"; now confirmed current |
| "GitHub has later July 2026 history" | working branch 2026-07-26; main 2026-07-07 | **MATCH** | Medium | reconciled |
| Default branch is `main` | remote HEAD → `main` = `a8420d49` | **MATCH** | **HIGH** | but `main` lacks all 48 product commits |
| Local `main` current | local `69e67312` ≠ remote `a8420d49` | **STALE** | Medium | local-only staleness |
| Azure frontend + backend live | SWA 200, API 200 Healthy | **MATCH** | — | |
| Azure SQL | `InsuranceAIPlatform` Online, RAG counts healthy | **MATCH** | — | |
| .NET RAG providerMode = Mock | `AiProvider__Mode=Mock`, `RealCallsEnabled=false` | **MATCH** | — | |
| Vector retrieval in-memory-hash fallback | live `backend=in-memory-hash` | **MATCH** | — | |
| Qdrant not deployed | `Rag__QdrantEnabled=false`, reachable=false, no Azure resource | **MATCH** | — | |
| Ollama / LocalLlama not deployed | `Rag__LocalLlamaEnabled=false`, reachable=false | **MATCH** | — | |
| No paid/managed LLM | no LLM resource in RG; Mock mode; no key property in options | **MATCH** | — | |
| LangChain sidecar exists | container app Healthy, 16 tests pass | **MATCH** | — | |
| Sidecar deterministic-provider by default | `providerMode="Deterministic"` unless `OLLAMA_BASE_URL` | **MATCH** | — | |
| Sidecar usable in the product | enabled in Azure but live call → `providerMode:"Unavailable"` | **CONTRADICTED** | **HIGH** | capability listed as accepted is not currently serving |
| Accepted test counts | 288 .NET + 16 python + 113 E2E (mock subset) | **UNKNOWN→MEASURED** | Medium | no prior number in AIKB to compare |
| Claim-scoped citations | `BuildCitations` from retrieved chunks only | **MATCH** | — | |
| Cross-claim leakage guard | SQL `ClaimId ==` filter; `SimilarClaimsRanker` returns metadata only | **MATCH** | — | |
| Synthetic-data boundary | `syntheticOnly:true` live | **MATCH** | — | |
| "possible cosmetic global-ish RAG infrastructure count" | **CONFIRMED**: `policyClauses/evidenceChunks/evaluationQuestions/auditTraces` are **global** counts; only `EvidenceMemoryIndex` is claim-scoped | **MATCH (gap real)** | Medium | known gap still present |
| Insufficient-evidence handling | verified in code + tests | **MATCH** | — | |
| Advisory-only posture | `AdvisoryOnly=true`, footers, sidecar | **MATCH** | — | |
| — (not in AIKB) | **No backend authentication at all** | **NEW — CONTRADICTS "production-ish demo" framing** | **HIGH** | see §G |
| — (not in AIKB) | `/api/system/demo-status` hardcoded & false | **NEW** | **HIGH** | see §H |
| — (not in AIKB) | lint gate declared but uninstallable | **NEW** | Medium | see §E |
| Eval questions language | rows carry `"language":"uk"` while text is English (post English-only commit) | **STALE** | Low | metadata not migrated |

## J. VERIFIED REUSABLE CAPABILITIES

Proven present and working now, therefore safe to build on:
1. Claim-scoped evidence retrieval with SQL-level isolation.
2. Grounded answer generation where **citations and confidence are derived from retrieval, never from a model**.
3. Honest insufficient-evidence response with confidence 0.
4. Persisted audit trail (`RagAuditTrace`) with chunk ids, citations, provider mode, cost, latency.
5. Advisory-only enforcement at three independent layers (.NET generator, trace flag, sidecar).
6. Document text ingestion → bounded, idempotent, claim-scoped chunks.
7. Cross-claim similarity that exposes only claim-level metadata.
8. Provider seams (Qdrant / Ollama / sidecar) that are disabled-by-default and degrade honestly.
9. React shell, routing, Redux/saga orchestration, API facade with mock/backend switch.
10. 288 .NET tests + 16 sidecar tests + a 24-spec E2E suite.

## K. CUSTOMER ASSISTANT — CANDIDATE INTEGRATION BOUNDARY

**FACTS (verified):** no customer-facing route, page, endpoint, auth or session exists. Every route sits behind `RequireAuth` and every API path is `api/claims/{claimId}/…`. The backend has no authentication. Retrieval is claim-scoped by an id parameter. Policy knowledge exists as 8 `PolicyClause` rows. There is no pre-claim/guidance content, no customer identity model, and no customer messaging path.

**RECOMMENDATION (Gate 1 design candidate, not implemented):**
- **Surface:** a new public route (e.g. `/assist`) *outside* `AppShell`/`RequireAuth`. Do **not** reuse `ClaimShell` — it assumes an operator context.
- **Reuse:** `MockGroundedAnswerGenerator` grounding contract, `RagAuditTrace`, advisory footer, insufficient-evidence path, `IEmbeddingProvider`, the API facade pattern.
- **New:** a distinct customer-facing endpoint. **Do not expose `api/claims/{claimId}/rag/ask` to customers** — it has no authorization and its `claimId` is a bare parameter.
- **Scope for v1:** **policy/general guidance only**, backed by `PolicyClause` + curated FAQ content. Claim-scoped customer answers require authenticated claim ownership, which does not exist yet.
- **Auth:** this is the gating prerequisite. A customer surface needs real authentication plus an ownership check binding session → customer → claim. Nothing in the current codebase provides it.
- **Persistence:** new conversation/session entity; reuse the audit-trace pattern.
- **Human handoff:** reuse `missing-document-requests` / human-decision seam; add an explicit "talk to a human" action.
- **Guardrails:** inherit advisory-only; never state coverage/payout/fraud/legal conclusions; always cite; say "not enough information" rather than guess.
- **Explicitly OUT of v1:** claim-scoped customer answers, document upload by customers, status changes, payout figures, real PII, any autonomous decision.

## L. WARDEN GATE 1 ADOPTION RECOMMENDATION

- **Risk profile: HIGH.** Customer-facing + insurance domain + advisory AI + an authentication gap. HIGH forces external review and conservative failure.
- **Product source boundary:** `src/**`, `server/**`, `ai-sidecars/**`. Exclude `docs/**`, `e2e/**` from writable scope initially.
- **Candidate frozen Owner goal:** "A customer can ask about their insurance situation and receive grounded, cited, advisory-only guidance with an explicit path to a human — without any autonomous coverage, payout, fraud or legal decision."
- **Requirement groups:** BUILD · UNIT/INTEGRATION TESTS · E2E (real full-stack config) · GROUNDING (citations from retrieval only) · SAFETY (advisory-only, no final decisions) · AUTHZ (ownership enforced) · PRIVACY (synthetic only) · OBSERVABILITY (audit trace per answer).
- **Evidence classes:** BUILD, LOCAL_TEST, E2E, MANUAL, plus a runtime probe for provider honesty.
- **External review: required** (HIGH risk), consistent with WARDEN v1's `RequiresExternalPlatformReview`.
- **Proof assets:** bind the actual test source files by SHA-256 — WARDEN v1 EXT-019 proved a selector alone is not a proof method.
- **Security/privacy checks:** secret scan; assert no `[AllowAnonymous]` customer data path; cross-claim leakage test; "no final decision" assertion.
- **Delivery boundary:** local commit only; Owner Acceptance and Delivery Authorization separate, as in WARDEN v1.
- **Post-close Child Gate test:** verify lineage/budget continuity from Gate 1 into a follow-up gate.

**Unresolved Owner decisions — must be answered before freezing Gate 1:**
1. **Which branch is the canonical baseline** — `main` (default, docs-only, missing 48 commits) or `rag/local-foundation-mega-v0.1` (the real product)? Merge first, or retarget the default branch?
2. Does Gate 1 include **building real authentication**, or is v1 restricted to unauthenticated general guidance?
3. Is the **hardcoded false `demo-status`/`bff/health`** in Gate 1's scope to fix?
4. Should the **sidecar** be repaired (enabled but unreachable) or explicitly parked?
5. Is the **lint gate** to be installed and enforced as acceptance evidence?
6. Is Qdrant/Ollama enablement in scope, or does the cost-safe posture stand?
7. May the Gate 1 run start the **full-stack E2E** (LocalDB + API) — needed for valid E2E evidence?

## M. WARDEN-LAB LATER FALSIFICATION / META-TEST PLAN (plan only — nothing executed)

All on **disposable copies**, never the accepted feature:
1. **Stale evidence:** mutate a `src/**` file after capture → `warden check` must refuse `MATERIAL_CLEAN` on stale evidence.
2. **Proof replacement:** replace an E2E/unit test body while keeping the class/selector → must fail on proof-asset SHA-256 (the WARDEN v1 EXT-019 invariant).
3. **Material finding blocking:** inject a P1 → engineering must not lock.
4. **External lineage:** submit a DELTA naming a skipped predecessor → `EXTERNAL_AUDIT_REJECTED_DELTA_SKIPPED_PREDECESSOR`.
5. **Risk downgrade:** HIGH→LOW without boundary → denied; attributed+reasoned → allowed and recorded.
6. **BREAK_GLASS:** declare, verify it blocks clean until reconciled.
7. **Engineering lock:** after `MATERIAL_CLEAN`, `LOCAL_EDIT` → `DENIED_ENGINEERING_LOCKED`.
8. **Acceptance ≠ authorization:** after Owner Acceptance, push must still be `DENIED_OWNER_GATED` pending delivery authorization.
9. **Operation-specific grant:** a `CORPORATE_PUSH` grant must not open `MERGE`/`DEPLOY`.
10. **Child Gate:** lineage and macro-budget continuity after close.
11. **Lab-specific:** assert a WARDEN gate would *catch* the `demo-status` lie — a self-reported status contradicting measured runtime.

## N. MATERIAL UNKNOWNS / OWNER DECISIONS REQUIRED

**Material unknowns remaining:**
1. **U1 — full-stack E2E never validly exercised.** The 8 backend-dependent specs did not run against a backend. Minimum action: permission to run `playwright.config.ts` with LocalDB + API in a later gate.
2. **U2 — canonical branch undecided** (§B, §L-1). Owner decision, not discoverable.
3. **U3 — why the sidecar is unreachable** is diagnosed only to the level of "enabled, healthy, not serving". Root cause would need container log reads / config change, beyond Gate 0's read-only boundary.
4. **U4 — deployed image provenance.** The live API serves RAG endpoints that exist only on `rag/local-foundation-mega-v0.1`, so it was almost certainly built from that branch — but this is **inference**, not proof. No build metadata endpoint exists.

U1, U3 and U4 are **tooling/permission-bounded, not architecture-bounded**. U2 is an Owner decision. None of them changes the Customer Assistant's material architecture, risk, scope or acceptance contract: the surface must be new, the grounding contract is verified and reusable, and the authentication gap is established as the gating prerequisite. Per §12's materiality test, they do not block Gate 1 from being frozen — provided the Owner answers §L's decision list, which is exactly what Gate 0 is for.

## O. SIDE EFFECTS

**In `C:\Projects\InsuranceAIPlatform` (governed source):**

| Action | Result |
|---|---|
| source / test / config / doc edits | **NO** — 0 modified tracked files |
| commit / push / branch / merge / PR / tag | **NO** — HEAD, branch, remotes unchanged |
| `git pull` / fetch / reset / clean / checkout / stash | **NO** |
| lockfile or package changes | **NO** |
| EF migration create/apply · DB writes | **NO** |
| Azure mutation / deploy / restart / scale / config | **NO** — read-only `az ... list/show` and HTTP GET only |
| secret values read, printed or modified | **NO** — key names only, values redacted |
| paid-provider enablement | **NO** |
| production/customer data access | **NO** — synthetic only |

**Generated (ignored) outputs created by verification, disclosed not hidden:**
- `server/**/bin`, `server/**/obj` — .NET build (ignored by `server/.gitignore`)
- `dist/` — Vite build (ignored)
- `playwright-report/`, `test-results/` — E2E artifacts (ignored)
- `ai-sidecars/langchain-claim-analytics/.venv/` — **created by this Gate** to run sidecar tests; ignored by the sidecar `.gitignore`; removable
- Scratchpad only, outside the repo: `gpt-handoff` clone, `ai-kb` clone, `iap-main-probe` bare clone

Nothing was cleaned or reset to hide generated state.

**Outside the governed repo:** this report is committed and pushed to `slavkan777/gpt-handoff` (§13 publishing requirement). Disclosed explicitly because §12 asks for a no-side-effect proof — that proof is scoped to the InsuranceAIPlatform product repo, per Critic defect D1.

## P. FINAL VERDICT

**`WARDEN_LAB_GATE0_CURRENT_TRUTH_READY`**

Enough primary current truth exists to freeze Gate 1 without guessing about material architecture, scope, risk or acceptance facts — provided the Owner answers the seven decisions in §L. The remaining unknowns are tooling- and permission-bounded, not architectural.

Gate 0 is **not self-accepted**. Gate 1 is **not frozen**. No feature was implemented. The internal Macro Critic is **not** external evidence and is not a substitute for independent review.
