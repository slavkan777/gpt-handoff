REQUEST_ID: REQ-2026-06-05-insuranceai-business-process-acceptance-sweep-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-business-acceptance-simulation
PROJECT: InsuranceAIPlatform
GATE: BUSINESS_PROCESS_ACCEPTANCE_SWEEP_V0.1
COMPLETED_BY: claude

# Business-Process Acceptance Sweep — Claim Manager Role

Simulated a non-technical claim manager using the Claim Evidence Intelligence/RAG feature across realistic
scenarios + degraded states (Playwright UI + API content analysis). **11/12 scenarios pass, 1 data-gap
(NOT COVERED), and 1 real product bug found** (manager-facing confidence renders as `1400%`). No product
code changed; no commit; no push. The bug is reported as a fix-gate proposal, not fixed here.

## Routing Lock Verification

PROJECT InsuranceAIPlatform · SOURCE_REPO C:/Projects/InsuranceAIPlatform · branch rag/local-foundation-mega-v0.1 ·
HEAD **912e841** (clean tree, restored after sweep) · report → gpt-handoff/InsuranceAIPlatform/. ✅

## Environment Setup

Qdrant `iap-qdrant` :6333; backend built DLL with `Rag__QdrantEnabled=true` :5284; frontend Vite :5173 in
`VITE_INSURANCE_API_MODE=backend` (temporary `.env.local` flip + webServer env; reverted to mock after). Uncommitted
sweep spec/config created and deleted after the run.

## Data Discovery (real seeded demo data only)

- 56 demo claims. **6 carry RAG evidence chunks:** CLM-1006(13), CLM-1010(10), CLM-1011(8), CLM-1009(7), CLM-1007(6), CLM-1008(6).
- **50 claims have ZERO chunks** (e.g. CLM-1061) → used as the genuine insufficient-evidence case.
- CLM-1006 evidence kinds: application, approval-summary, invoice, invoice-detail, photo-caption, police, statement (7 kinds).
- Eval questions: CLM-1006/1009/1010/1011 = 4 each; 1008 = 3; 1007 = 2.
- similar-claims(CLM-1006) → CLM-1010(0.66), 1011(0.53), 1009(0.53), 1008(0.51), 1007(0.49) — claim-level only.

## Business Scenario Matrix

| # | Scenario | Result | Note |
|---|---|---|---|
| A | Manager happy-path coverage review | ✅ PASS | answer readable + advisory + 4 citations; vector live_local/qdrant |
| B | Evidence-backed explanation quality | ✅ PASS | answer stitches the 4 retrieved chunks verbatim ([1]…[4]); citations map to real kinds; not a generic paragraph |
| C | Degraded runtime, business continuity | ✅ PASS | stop Qdrant → honest skipped/in-memory-hash; coverage still answers |
| D | Cross-claim business separation | ✅ PASS | CLM-1009 cites only CLM-1009; zero CLM-1006 leakage |
| E | Repeated actions + audit trust | ✅ PASS | repeated checks, reset, claim-switch stable; audit-history panel present |
| F | Insufficient/no evidence | ✅ PASS | CLM-1061: conf 0%, 0 citations, "Недостатньо релевантних доказів… перегляд людиною" |
| G | Partial/ambiguous coverage | ⚠️ NOT COVERED | no claim is modeled as partial-coverage AND the mock generator is deterministic/templated — cannot exhibit nuance. Data + generator gap (see Data Gaps) |
| H | Duplicate/similar claim | ✅ PASS | similar-claims = claim-level cards only, no evidence text / no citation table; **truthful limitation: ranking is in-process centroid, not Qdrant** |
| I | Non-technical usability/readability | ✅ PASS (with caveat) | mobile flow usable; BUT confidence label reads `1400%` (see Bug) and labels are technical (qdrant/local-hash) |
| J | Demo/presentation flow | ✅ PASS (with caveats) | full script healthy→coverage→citations→degrade→fallback→restore works; caveats below |
| K | Negative/error-state behavior | ✅ PASS | mock-mode gotcha is honest-degraded (mock shows disabled); no fake success. The confidence bug is the one real defect |
| L | Decision-boundary / legal safety | ✅ PASS | every answer advisory-only; no binding/payout/rejection/fraud wording |

Sweep run: **7 UI scenarios passed (50.8s)**; B/G/L resolved via API content analysis.

## Passed Scenarios (business highlights)

- **A/B:** CLM-1006 coverage answer opens "За умовами полісу та матеріалами справи:" then quotes the 4 retrieved chunks
  (driver statement, application, repair detail, police report). Summary use-case surfaces the financial discrepancy
  ("оцінка 2720 доларів перевищує бенчмарк … на 38 відсотків … потребує перевірки людиною"). A manager gets actionable, sourced context.
- **F:** zero-evidence CLM-1061 returns a cautious "insufficient evidence, human review recommended, AI is advisory only" with 0% confidence and no citations — no overconfident decision.
- **C/J:** during a live Qdrant outage the panel honestly flips to skipped_not_available / in-memory-hash and the coverage answer still renders from the fallback — business continuity holds, no crash/blank.
- **D/H:** claim isolation holds (CLM-1009 cites only itself); similar-claims stays claim-level (no foreign evidence as primary citation).

## Failed / Weak Scenarios

None failed. **I** is weakened by the confidence-display bug (below). **G** not covered (data/generator gap).

## Data Gaps

- **G partial/ambiguous coverage:** no seeded claim models "only part of the damage / part of the policy applies," and the
  current generator is the deterministic mock (templated; cannot produce graded nuance). Recommend a seeded partial-coverage
  test claim AND a real generator before claiming nuanced-coverage capability.
- Only 6/56 claims have RAG evidence; the other 50 are bare. Fine for a demo, but stakeholders should be steered to CLM-1006/1009/1010.

## Screenshots / Artifacts

`test-results/biz-*.png` captured per scenario (ephemeral — gitignored, not committed; cleared by later Playwright runs).
Authoritative evidence = the per-scenario pass/fail + the captured `BIZ_FINDING` log lines + API answer captures in this report.

## Console / Network Findings

No fatal console errors during the manager flows (scenario A). Network: UI ↔ :5284 ↔ Qdrant :6333 all healthy when up;
graceful fallback on outage.

## Business Readability Findings

- **Confidence reads `1400%`** for CLM-1006 coverage (and `1700%`, `4100%` for risk/summary) — see Product Bugs. This is the single biggest readability/credibility problem for a manager.
- Layer labels are engineer-oriented (`qdrant`, `local-hash-embed`, `in-memory-hash`, `live_local`). A manager can still proceed
  (the advisory banner + answer + citations carry the meaning), but a "plain-language status" would help a non-technical user.
- Answers are in Ukrainian (matches the corpus); fine for the target user.

## Legal / Decision-Boundary Findings

**Safe.** All answers carry `advisoryOnly=true` and the persistent "AI advisory only — human makes the final decision"
banner. The risk use-case is explicitly framed "рекомендаційно, без звинувачень" (advisory, without accusations). No answer
implies automatic payout, rejection, binding decision, or a fraud finding — even CLM-1009, whose police report notes
intoxication, presents it as cited evidence for human review, not an accusation. No legal-boundary violation observed.

## Product Bugs Found

1. **[MEDIUM] Confidence renders as `1400%` in real-backend mode.** The UI computes `confidence * 100`
   (`ClaimEvidenceIntelligencePanel.tsx:303`). The mock API returns confidence as a 0–1 fraction (0.82 → "82%"), but the
   real backend returns it as a 0–100 integer (`backendInsuranceApi.ts:374` passes `dto.confidence` through unchanged;
   API returned 14/17/41), so the UI shows 1400% / 1700% / 4100%. Real-vs-mock contract mismatch. Manager-facing
   credibility issue; not a crash, no data leak, no security/legal impact. **Not fixed (out of scope this gate).**
   Repro: backend `Rag__QdrantEnabled=true`, UI `VITE_INSURANCE_API_MODE=backend`, open CLM-1006 → Check policy coverage → Confidence field reads `1400%`.

## Recommended Fix Gates

1. **CONFIDENCE_CONTRACT_FIX (small):** align the confidence scale — either map `confidence/100` in `backendInsuranceApi.ts`,
   or have the backend emit a 0–1 fraction, or have the UI not multiply when the value is already 0–100. Make mock and real agree; add a test asserting the rendered % is ≤100.
2. **(Future) Real generator + seeded nuance cases:** a real local generator (owner-approved Ollama) + a seeded partial-coverage claim to cover scenario G.
3. **(Optional) Plain-language status:** a manager-friendly label alongside the technical `qdrant`/`live_local` values.

## Files Changed

None committed. Transient, since-deleted: `playwright.acceptance.config.ts`, `e2e/_business-sweep.spec.ts`.
`.env.local` (gitignored) flipped to backend for the run, reverted to mock. Product HEAD `912e841` unchanged.

## Commands Run

- API discovery + `/rag/ask` answer capture (CLM-1006 coverage/risk/summary, CLM-1061, CLM-1009) via curl/python.
- `npx playwright test --config playwright.acceptance.config.ts` → 7 passed.
- `docker stop/start iap-qdrant` (degraded scenario).

## Boundaries Honored

NO product source changes · NO commit · NO push · NO main · NO Azure · NO deploy · NO paid provider · NO secrets ·
NO real PII · NO Ollama · NO schema migration · NO unrelated projects · NO fake pass (every scenario asserts business
semantics) · NO invented data (zero-evidence + partial-coverage gaps reported honestly).

## Manual Acceptance Recommendation

**Business-ready for hands-on acceptance with ONE fix before a stakeholder demo:** correct the confidence display
(`1400%` → real %). Everything else a manager needs is present and sound: sourced advisory answers, honest degraded
state + continuity, claim isolation, cautious behavior on thin evidence, and clear legal/advisory boundaries.
Demo caveats Slava should say aloud: (a) answers come from a deterministic local mock generator, not a live LLM;
(b) similar-claims ranking is in-process, not yet Qdrant; (c) confidence figure is currently mis-scaled (fix pending).

## Next Safe Step

Tell GPT `отчёт` to audit this sweep. Recommended next gate: **CONFIDENCE_CONTRACT_FIX** (small, then re-verify),
after which manual acceptance / demo. Await an explicit prompt before any fix or push.

STOP after report — no product change, commit, push, deploy, or unrelated-project work performed.
