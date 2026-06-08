REQUEST_ID: REQ-2026-06-08-insuranceai-langchain-advanced-claim-analytics-sidecar-v0-1
STATUS: PARTIAL
TASK_TYPE: project-langchain-advanced-claim-analytics-sidecar
PROJECT: InsuranceAIPlatform
GATE: LANGCHAIN_ADVANCED_CLAIM_ANALYTICS_SIDECAR_MEGA_V0.1
COMPLETED_BY: claude

# LangChain advanced-claim-analytics sidecar — VERDICT: PARTIAL (local complete; Azure deploy BLOCKED on owner approval)

One-line: the full feature is **implemented, tested, and locally proven** — a Python FastAPI **LangChain** sidecar produces a structured advisory manager review over the SAME claim-scoped evidence; a **feature-flagged** (OFF by default) .NET endpoint + typed client proxies to it; a React panel renders it. 188/188 .NET tests pass, the sidecar smoke passes, frontend builds, and the feature branch is pushed. The **only** unmet item is **Azure deployment**: the sidecar is a separate Python service, so putting it on Azure requires either a new Container App or adding a second container to the existing demo app's template — a topology/blast-radius change that needs **owner approval**, so it is reported **BLOCKED with exact next action** (the gate forbids owner-approval-required changes). The existing .NET RAG is untouched; flag OFF preserves current behavior. Slava remains final acceptor.

## Routing Lock Verification
- PROJECT InsuranceAIPlatform; remote `slavkan777/InsuranceAIPlatform`; branch `rag/local-foundation-mega-v0.1`; HEAD before `c457734`, after `2259946`. `origin/main` untouched (`69e6731`). No main/merge/force.

## Starting State
- Tree clean at `c457734` (prior gate: evidence ingestion, READY_FOR_AUDIT). Azure active revision `iap-demo-api--0000004` (unchanged this gate).

## Discovery: Existing Document/RAG Flow
- Core RAG stays authoritative: `IRagChunkSource.GetClaimChunksAsync` (strict `ClaimId==claimId`), `IClaimReadService.GetClaim`, RagController. The sidecar consumes the SAME claim-scoped chunks; it does not replace or re-query RAG.

## Implementation Summary
**1. Python LangChain sidecar** `ai-sidecars/langchain-claim-analytics/` (FastAPI):
- `GET /health`, `POST /advanced-claim-analytics`.
- LangChain used meaningfully: `ChatPromptTemplate` + `PydanticOutputParser` + LCEL chain. Model is **deterministic by default** (no key, no paid provider, no network → portable + reproducible); optional real local `ChatOllama` when `OLLAMA_BASE_URL` is set/reachable. `providerMode` reported honestly.
- Structured `AdvancedReview` (summary, coverageAssessment, evidenceStrength, anomalies[], missingItems[], recommendedNextAction, citations[], confidence, advisoryOnly, providerMode, framework). Advisory only; citations re-scoped to the input evidence ids (no cross-claim data fetched).

**2. .NET (feature-flagged, OFF by default):**
- `AdvancedAiReviewOptions` (`AdvancedAiReview:Enabled`=false, `SidecarBaseUrl`, `TimeoutMs`, `MaxEvidenceChunks`).
- `IAdvancedClaimAnalyticsClient` + `HttpAdvancedClaimAnalyticsClient` (named HttpClient `advanced-analytics`, camelCase JSON; returns null on any failure → never throws into the request path).
- `AdvancedAiReviewController` → `POST /api/claims/{claimId}/advanced-ai-review`: validates claim, **flag off → safe "Disabled" fallback (no sidecar call)**, else collects claim + claim-scoped chunks → calls sidecar → **re-scopes citations to the claim's own evidence** → returns; **sidecar null → "Unavailable" fallback**.
- Program.cs DI wiring. **No schema change, no migration.**

**3. React:** `AdvancedAiReviewPanel` (button `advanced-ai-review-btn` + structured panel with advisory banner, strength/confidence, anomalies, missing items, recommendation, claim-scoped citations) mounted on the AI Evidence page. Self-contained fetch → zero coupling to the existing api facade/mock.

## Files Changed
- NEW `ai-sidecars/langchain-claim-analytics/{app.py,requirements.txt,README.md,.gitignore}`
- NEW `server/.../Api/Rag/AdvancedAiReviewOptions.cs`, `Api/Rag/AdvancedClaimAnalyticsClient.cs`, `Api/Controllers/AdvancedAiReviewController.cs`
- MOD `server/.../Api/Program.cs`
- NEW `server/.../Tests/AdvancedAiReviewControllerTests.cs`
- NEW `src/components/claim/AdvancedAiReviewPanel.tsx`; MOD `src/pages/AiEvidencePage.tsx`
- Commit `2259946` on `rag/local-foundation-mega-v0.1` (pushed).

## Migration/Schema Statement
- **No schema change, no EF migration.** The sidecar reads claim-scoped evidence via the existing API; no new tables/columns.

## Tests Run
- `dotnet test` full suite → **188 passed / 0 failed** (185 prior + 3 new). New `AdvancedAiReviewControllerTests`: (1) flag OFF → "Disabled" fallback + sidecar NOT called; (2) flag ON → review returned BUT citations **re-scoped** to the claim (foreign `CLM-1007-*` dropped); (3) sidecar null → "Unavailable" fallback.
- Frontend `npm run build` → success (tsc + vite).

## Local Smoke Evidence
- Sidecar started (uvicorn :8090). `GET /health` → `{status:healthy, framework:langchain, providerMode:Deterministic, advisoryOnly:true}`. `POST /advanced-claim-analytics` with 2 evidence chunks → structured review: coverageAssessment "ймовірне покриття… виключень не виявлено", evidenceStrength `moderate`, anomaly "невідповідність вартості/годин… не вирок про шахрайство", missingItems [police, policy], citations = both input chunks (claim-scoped), confidence 49, advisoryOnly true. No-evidence request → strength `none`, confidence 0, 0 citations (safe insufficient).

## Commit/Push State
- Committed `2259946` (11 files, no `__pycache__`/venv). Pushed `c457734..2259946` to the feature branch only. **No main, no merge, no force.** Secret scan clean.

## Azure Deploy State — BLOCKED (owner approval required)
- **Not deployed this gate.** Reason: the sidecar is a separate **Python** service. Hosting it on Azure dev/test requires either (a) a **new** Container App (new resource), or (b) adding a **second container** to the existing `iap-demo-api` Container App template (changes the live demo app's topology). Both are blast-radius/ownership decisions the gate does not authorize ("Stop if … owner approval is required"). Deploying the .NET image flag-OFF *alone* would change nothing usable (no sidecar to reach), so no Azure revision was churned. Existing Azure (`iap-demo-api--0000004`, fallback RAG) is **unchanged**.

## New Claim / Advanced-Review Proof
- Sidecar smoke (above) is the runtime proof of the analytics. The .NET↔sidecar **contract** is proven: the sidecar accepts exactly the camelCase request the .NET client emits (`claimId, customerName, vehicle, eventType, description, question, evidence[{chunkId,kind,text}]`) and returns exactly the shape the client deserializes (camelCase, case-insensitive); the 3 unit tests cover the flag gate, citation re-scoping, and fallback. A **co-located runtime call** (.NET HttpClient → live sidecar) is what the BLOCKED Azure sidecar enablement (or a local API+seeded-DB run) would add end-to-end.

## Cross-Claim Leakage Proof
- Two independent guards: (1) the .NET endpoint only sends the claim's own `GetClaimChunksAsync` evidence and **re-scopes returned citations to those ids** (unit test 2 drops a foreign `CLM-1007-*` citation); (2) the sidecar itself re-scopes citations to the input evidence. No cross-claim data is fetched in the sidecar.

## Regression Proof
- 188/188 .NET tests green (185 prior intact). Feature flag OFF by default → existing claim/RAG flow byte-for-byte unchanged. Frontend builds; the panel is additive (mounted after the existing RAG panel).

## Screenshots / Artifacts
- Sidecar code + README under `ai-sidecars/langchain-claim-analytics/`. (No live-Azure UI screenshots — feature not deployed; the panel is built + renders against a local backend with the flag on.)

## Network / Console Findings
- Sidecar HTTP 200 on health + analytics. No secrets in logs/code. No external/paid calls.

## Rollback / Cleanup State
- Nothing deployed → nothing to roll back. Azure unchanged. Sidecar venv is outside the repo (temp) and not committed. No seeded data touched.

## Boundaries Honored
NO main · NO merge · NO production · NO paid provider · NO Azure Qdrant/Ollama · NO OpenAI/DeepSeek/Azure OpenAI key · NO secrets · NO real PII · did NOT replace existing .NET RAG · did NOT mutate seeded claims · NO final payout/fraud/legal decision (advisory only) · NO schema migration · NO force push · did NOT create an Azure resource / change app topology without approval (→ reported BLOCKED).

## Deviations
- React panel uses a self-contained fetch (not the api facade) to avoid churning the mock/facade contract — bounded + isolates the optional feature.
- Local smoke covered the sidecar (real HTTP) + the .NET endpoint via unit tests + contract verification, rather than a full local API+LocalDB run (LocalDB not seeded this session; the authoritative full-wire run is the BLOCKED Azure step).

## Defects / Product Gaps Remaining
- Co-located .NET→sidecar runtime call not executed end-to-end (contract+unit proven only) — closes when the sidecar is deployed/hosted.
- Deterministic analytics confidence is modest (no real LLM by default) — set the `OLLAMA_BASE_URL`/managed-LLM seam for richer output.

## Slava Manual Checklist
1. (Local) `cd ai-sidecars/langchain-claim-analytics`, create venv, `pip install -r requirements.txt`, `uvicorn app:app --port 8090`; `curl localhost:8090/health`.
2. (Local) Run the .NET API with `AdvancedAiReview__Enabled=true` + `AdvancedAiReview__SidecarBaseUrl=http://localhost:8090`; open a claim → AI Evidence → "Запустити розширений огляд" → see the structured review.
3. **Decide the Azure hosting** for the sidecar (see next step) — this is the owner approval that unblocks deployment.

## Recommended Next Step (exact action to unblock Azure)
- Owner-approve ONE of: (a) add the Python sidecar as a **second container** in the existing `iap-demo-api` Container App (true sidecar; .NET reaches it on `http://localhost:8090`), or (b) a **new** dev/test Container App `iap-langchain-sidecar` in `rg-iap-demo`. Then I: build+push the sidecar image to GHCR, deploy it, deploy the .NET image with `AdvancedAiReview__Enabled=true` + the sidecar URL, redeploy the SWA (backend-mode bundle already includes the panel), and run the Azure smoke (advanced review cited + claim-scoped + advisory; fallback when sidecar down). Then this gate flips to READY_FOR_AUDIT.

GitHub handoff ready. Tell GPT: отчёт.
