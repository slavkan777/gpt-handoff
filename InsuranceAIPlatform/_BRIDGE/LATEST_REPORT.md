REQUEST_ID: REQ-2026-06-06-insuranceai-azure-ui-manual-acceptance-by-claude-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-azure-ui-manual-like-acceptance-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_UI_MANUAL_ACCEPTANCE_BY_CLAUDE_MEGA_V0.1
COMPLETED_BY: claude

# Manual-like Azure UI acceptance sweep — PASS (pre-acceptance; Slava remains final acceptor)

One-line: ran a 66-point manual-like acceptance sweep against the **live Azure dev/test site** with headless Chromium + direct API probes. **41 automated scenarios PASS, 0 PARTIAL, 0 FAIL**, plus 6/6 API probes green. The public UI is genuinely backend-mode (not mock), RAG works on CLM-1006 with claim-scoped citations + advisory + sane confidence, insufficient-evidence (CLM-1012) and not-found (CLM-1061/CLM-9999) are handled safely, and there are **no cross-claim citation leaks**. The only console/network noise is **expected** negative-path artifacts (404s from deliberately visiting non-existent claims; `ERR_ABORTED` from rapid navigation / the navigate-away test) — zero JS exceptions, zero 500s, zero auth bursts. **No source/Azure/DB mutation. Not a substitute for your final manual acceptance.**

## Routing Lock Verification
- PROJECT InsuranceAIPlatform; SOURCE_REPO `C:/Projects/InsuranceAIPlatform`; branch `rag/local-foundation-mega-v0.1`. This gate is **test-only** — no source edit, no commit to the product repo, no Azure/DB mutation. Target = live SWA `https://kind-meadow-03cf73103.7.azurestaticapps.net/` + backend `iap-demo-api…azurecontainerapps.io` (revision `iap-demo-api--0000003`).

## Site / Backend Mode Verification
- SWA `200`, title `InsuranceAIPlatform · Auto Claim AI Workbench`. Live bundle `index-B8iViJhY.js` carries the backend FQDN, 0 `localhost`. During the sweep the UI made **dozens of calls to the Azure backend, all 200** across the claim workspace + RAG. **Backend mode proven, not mock-only.**

## Credential Handling Statement
- Used the existing **seeded demo account** (`demo@insurance.local`) whose password is shown under the login form **by design** (committed in `e2e/helpers/auth.ts`) — not a secret known only to Claude. Password value not reproduced here. No credential created, stored in repo/handoff/screenshots, or printed. No real PII (synthetic customers e.g. "Роберт Джонсон").

## Browser / Playwright Setup
- Headless Chromium (Playwright, repo install), viewport 1440×900 (+ 390×844 for narrow checks). Global capture of console errors, failed/aborted requests, and all backend responses (RAG bodies parsed for providerMode/confidence/citations). Standalone script in temp (not committed to product repo).

## Scenario Matrix Summary
**41 PASS / 0 PARTIAL / 0 FAIL** (automated) + **6/6 API probes**. Verdict **PASS**.

## Detailed Scenario Results A–L
**A. Availability / backend mode** — A1 PASS (title), A2 PASS (bundle→backend FQDN, no localhost), A3 PASS (`/health` 200), A4 PASS (`/api/claims` 200 + CORS allow-origin=SWA), A5 PASS (0 console errors at load), A6 PASS (0 failed requests at load).
**B. Auth / session** — B7 PASS (login→`/`), B8 PASS (wrong creds: stays on `/login`, no crash), B9 PASS (refresh preserves session), B10 PASS (direct `/claims/CLM-1006` while logged in), B11 PASS (logout→`/login`, protects claim page).
**C. Dashboard / navigation** — C12 PASS (dashboard renders), C13 PASS (dashboard↔claims↔detail↔evidence nav), C14 PASS (Back/Forward), C15 PASS (hard reload), C16 PASS (unknown route → `/` app shell, no blank screen).
**D. Claims list** — D17 PASS (8 rows from backend), D18 PASS (≥5 render), D19 PASS (search filters to 1 for "CLM-1006"), D20 PASS (empty state shown for no-match), D21 PASS (click CLM-1006 → its detail), D22 PASS (click CLM-1007 → CLM-1007's own data).
**E. Claim detail CLM-1006** — E23 PASS (customer/vehicle/claim info renders), E24 PASS (no stale fixture after CLM-1007→CLM-1006), E25 PASS (7/7 tabs render: documents/ai-evidence/risks/policy/customer-vehicle/approval/audit), E26 PASS (reload keeps CLM-1006 context).
**F. Evidence Intelligence happy path** — F27 PASS (panel mounts), F28 PASS (infra honest: `vectorRuntime=disabled/in-memory-hash`, `localLLM=disabled`), F29 PASS (coverage answer card), F30 PASS (advisory banner), F31 PASS (confidence renders, sane), F32 PASS (citations table renders), F33 PASS (all citations CLM-1006-scoped), F34 PASS (audit endpoint stable), F35 PASS (repeat coverage stable, no stale spinner).
**G. RAG business scenarios** — G36–42 PASS (5/5 fixed use-case actions — coverage/missing_docs/risk/similar/summary — produced output; all advisory, no final payout/fraud language; `similar` renders claim-level cards).
**H. Negative / edge** — H43 PASS (irrelevant "weather" custom question → answered without crash; Mock/low-relevance), H44 PASS (CLM-1012 → insufficient evidence, conf 0, 0 citations), H45 PASS (CLM-1061 non-existent → safe, no crash), H46 PASS (rapid 5× click → answer stable, +0 console errors), H47 PASS (navigate away while RAG pending → no crash, request aborted cleanly), H48 PASS (reload after answer → panel ok).
**I. Cross-claim leakage** — I49–52 PASS (3× CLM-1006 asks incl. one right after visiting CLM-1007 → citations always `CLM-1006-*`; **0 leaks**; CLM-1007 ask returns only `CLM-1007-*`).
**J. API probes** — see below, 6/6 green.
**K. Visual / UX** — K59 PASS (1440×900 screenshots: dashboard, claims list, CLM-1006 detail, evidence answer), K60 PASS (390×844 narrow remains usable), K61 PASS (no blank panels/overlaps), K62 PASS (loading/error states understandable), K63 PASS (advisory wording visible).
**L. Reliability** — L64 PASS (initial load 2.8s; RAG ask 0.27–0.67s), L65 PASS (happy path stable across fresh contexts), L66 PASS (0 401/403/CORS bursts).

## J. API Endpoint Probes (deployed backend)
| # | Probe | Result |
|---|---|---|
| 53 | `/health` | **200** in 0.27s |
| 54 | `/api/claims` (+Origin SWA) | **200**, `access-control-allow-origin` = SWA |
| 55 | `/api/claims/CLM-1006/rag/infrastructure` | **200** |
| 56 | `/api/claims/CLM-1006/rag/ask` (coverage) | **200** in 0.67s — `Mock`, conf 62, citations all `CLM-1006-*`, advisory |
| 57 | `/api/claims/CLM-1012/rag/ask` | **200** in 0.18s — `Mock`, conf 0, **0 citations**, "Недостатньо релевантних доказів… Рекомендується перегляд людиною" |
| 58 | `/api/claims/CLM-9999/rag/infrastructure` | **404** structured `{code:CLAIM_NOT_FOUND}` (not 500) |

## Screenshots / Artifacts
Under `gpt-handoff/InsuranceAIPlatform/azure-ui-manual-acceptance-by-claude-v0.1/`: `K59-dashboard.png`, `K59-claims-list.png`, `K59-claim-1006-detail.png`, `K59-evidence-answer.png`, `K60-narrow-evidence.png` (all synthetic data, no secrets/PII).

## Network Findings
- Core API requests all 200 in normal paths. **21 `net::ERR_ABORTED`** — all from rapid test navigation + the explicit navigate-away-while-pending test (incl. one intended `POST /rag/ask` abort). These are browser-canceled in-flight XHRs, **expected/benign**, not failures. **0 401/403**, no CORS errors (allow-origin present).

## Console Findings
- **25 console errors, all "Failed to load resource: …404"** — exclusively from deliberately visiting non-existent claims (CLM-1061, CLM-9999) whose sub-resources 404. **0 at normal load (A5).** No JS exceptions, no unhandled promise rejections, no React error boundaries triggered.

## RAG Evidence Proof
- providerMode `Mock` everywhere (LocalLlama disabled). Infra panel honest: `vectorRuntime` disabled / `in-memory-hash` / not reachable; `localReasoningRuntime` disabled. SQL counts via UI infra match DB (PolicyClauses 8 / EvidenceChunks 50 / EvalQuestions 21). Confidence varies sanely by question (UI coverage button 14; custom ДТП question 62) — derived from hash-retrieval scores, never fabricated. Advisory-only banner + closing disclaimer present on every answer.

## Citation / Leakage Proof
- CLM-1006 citations ∈ {`CLM-1006-application`,`-statement`,`-repair-detail`,`-police`,`-photo-front`,`-policy-terms`}. CLM-1007 citations ∈ {`CLM-1007-application`,`-invoice`,`-missing-docs`,`-police`}. CLM-1012 → 0 citations. **No citation ID ever crosses claim boundary across the full sweep (leaks=0).**

## Visual / UX Findings
- Enterprise-grade layout renders cleanly at 1440×900 (sidebar nav, claim header/tabs, AI Analysis & Evidence, infra status cards, answer card with confidence bar + citation table). Narrow 390×844 stays usable (main content visible, no broken overlap observed). No infinite spinners, no blank panels. Advisory wording legible.

## Performance Notes
- Initial SWA load ~2.8s. RAG ask 0.18–0.67s (Mock + in-memory-hash is fast). Backend cold-start not observed during the run (revision warm). No obviously excessive request.

## Defects Found
- **None blocking.** One **cosmetic/minor observation** (not a defect for this gate): visiting a non-existent claim route (e.g. CLM-1061) triggers several `404` console lines as the page attempts each sub-resource; it is handled gracefully (no crash, no blank screen), but a future polish could short-circuit sub-resource fetches once the claim itself 404s. Non-blocking, owner's discretion.

## Regression Risks
- Low. Fallback mode means no external vector/LLM dependency at runtime. Confidence is intentionally modest in fallback (hash retrieval) — set expectations for the demo narrative. Serverless SQL auto-pause can add a one-time cold-connect delay after long idle (handled by retry; not user-facing in normal use).

## Boundaries Honored
NO source change · NO product repo commit/push · NO main · NO merge · NO Azure deploy/update/create/delete · NO DB schema/data change · NO new resources · NO Qdrant/Ollama Azure · NO paid provider · NO secrets printed/stored · NO real PII · NO fake pass · NO claiming final manual acceptance for Slava (this is pre-acceptance testing only).

## Files / Artifacts Created
- Temp (local, not committed to product repo): Playwright sweep script, result JSON, 5 screenshots. Screenshots + this report published to the gate folder in gpt-handoff only.

## Commands Run (high level)
- Headless Playwright sweep (login, nav, claims, CLM-1006/1007/1012/1061 flows, RAG asks, negative/edge, leakage, viewport); `curl` API probes (`/health`, `/api/claims`, `/rag/infrastructure`, `/rag/ask` CLM-1006/1012/1007, CLM-9999); bundle/CORS checks. All read-only.

## Slava Manual Checklist (for your final acceptance)
1. Open https://kind-meadow-03cf73103.7.azurestaticapps.net/ , log in with the demo account on the form.
2. Claims list → open CLM-1006 → AI Evidence → click "Coverage" → confirm answer + advisory + citation cards feel right to you.
3. Try a custom question + an irrelevant question; open CLM-1012 (insufficient) to see the cautious refusal.
4. Eyeball mobile width + overall polish. (Optional) decide whether to keep the dev/test SQL Entra admin or revoke it.

## Recommended Next Step
- Architect GPT audit via «отчёт». If accepted, this dev/test environment is demo-ready in honest fallback mode; an optional separately-authorized gate could wire a managed vector store + managed LLM behind the existing seam for richer confidence.

STOP — holding after report. No defect fixes, no deploy, no Azure mutation in this gate.
