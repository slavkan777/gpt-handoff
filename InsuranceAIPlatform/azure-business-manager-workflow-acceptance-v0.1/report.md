REQUEST_ID: REQ-2026-06-06-insuranceai-azure-business-manager-workflow-acceptance-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-azure-business-manager-workflow-acceptance
PROJECT: InsuranceAIPlatform
GATE: AZURE_BUSINESS_MANAGER_WORKFLOW_ACCEPTANCE_MEGA_V0.1
COMPLETED_BY: claude

# Business-manager workflow acceptance — VERDICT: DEMO_READY_FOR_MANAGER_WORKFLOW

One-line: I used the live Azure dev/test site as a claim manager would during a working session — morning triage, deep CLM-1006 coverage/anomaly/missing-docs review, similar-claims, manager handoff summary, a second claim (CLM-1007), an insufficient-evidence claim (CLM-1012), audit/traceability, interruptions, custom questions, and an end-of-work recommendation. A real manager **can run a believable claim-review process**: the AI gives **cited, claim-scoped, advisory-only** support, surfaces a **real cost anomaly** (38% over benchmark; 8h catalog vs 14h billed), behaves safely on weak/missing evidence, and **never makes a final payout/fraud/legal decision**. Confidence is intentionally modest (fallback hash-retrieval + Mock generator) and must be described honestly. Read-only; no source/Azure/DB mutation. **Slava remains the final manual acceptor.**

## Routing Lock Verification
- PROJECT InsuranceAIPlatform, branch `rag/local-foundation-mega-v0.1`. Test-only — no source edit, no product commit, no Azure/DB mutation. Target = live SWA + backend revision `iap-demo-api--0000003` (RAG fallback).

## Credential Handling Statement
- Used the existing seeded **demo** account (`demo@insurance.local`; password shown on the login form by design, committed in `e2e/helpers/auth.ts`) — not a Claude-only secret. Password not reproduced. No credential stored in repo/handoff/screenshots/report. Data is synthetic (no real PII).

## Environment / Backend Mode Verification
- Backend mode confirmed: 13 RAG asks + claim/triage data all served by the Azure backend (`iap-demo-api…`), providerMode `Mock`, vector `in-memory-hash`, local LLM disabled. Not mock-only UI.

## Business Workflow Summary
All 15 manager workflows (A–O) exercised. Manager can triage → review → detect anomaly → check missing docs → see similar cases → produce a handoff summary → switch claims safely → handle insufficient evidence → trace AI actions → recover from interruptions. **No blocking defect.** Verdict **DEMO_READY_FOR_MANAGER_WORKFLOW**.

## Detailed Workflow Results A–O
**A. Morning triage — PASS.** Claims list = 8 claims, backend-driven, with manager-grade columns: `CLAIM NO. · CUSTOMER·VEHICLE · TYPE · STATUS · DOCS · AI STATUS · RISK · SLA · NEXT ACTION`. A manager can decide what to open next without confusion; search filters; empty state clean. Picked CLM-1006, CLM-1007, CLM-1012.
**B. CLM-1006 coverage support — PASS.** Coverage answer grounds in driver statement (sudden braking, wet road, within speed limit), claim application (DTP 18.05.2026 Boryspil, Toyota Camry 2021, front bumper/fender), and STO detail. 4 citations, all `CLM-1006-*`, advisory banner, no final decision. Manager can form a *supported* recommendation from citations.
**C. Repair/cost anomaly — PASS (strong).** Risk + "repair hours/parts" action returned a **real, cited anomaly**: "repair $2720 exceeds average benchmark $1970 by **38%**; catalog norm **8h** body-work vs **14h billed**; part #521190X910 — discrepancy needs human review." Anomaly is explainable, cited, and safely framed (human review, not fraud verdict).
**D. Missing documents — PASS.** `missing_docs` on CLM-1006 (cits: coverage-check, statement, photo-rear, policy-terms) and CLM-1007 (cits: statement, missing-docs, application, invoice) — claim-scoped, no mixing. Manager can identify gaps and decide request-doc vs human-review.
**E. Similar claims / patterns — PASS.** `similar` returns claim-level cards (CLM-1010 66%, CLM-1011 53%) with **reasons** ("shared evidence categories: application, coverage-check, invoice, photo-caption, police, statement; semantic proximity 66%") and matching-category chips — clearly separated from current-claim citations; no fraud/payout implication.
**F. Summary / handoff — PASS (strong).** `summary` produced a manager-grade handoff: "Evidence summary for human decision: STO invoice (bumper $980 / painting $740 / body $1000 = $2720); comparative rate $85–95/hr district vs $120 billed — rate excess needs human review; estimate exceeds average…". Concise, grounded, safe. Usable as adjuster/supervisor handoff.
**G. Second claim CLM-1007 + isolation — PASS.** Header/customer/vehicle change to CLM-1007; coverage + missing_docs citations all `CLM-1007-*`; returning to CLM-1006 → citations back to `CLM-1006-*`. No stale header/answer/citations.
**H. Insufficient evidence CLM-1012 — PASS.** Coverage → confidence **0**, **0 citations**, answer: "Недостатньо релевантних доказів у матеріалах справи для відповіді. Рекомендується перегляд людиною. AI-аналіз має лише рекомендаційний характер…". Manager protected from hallucinated decisions.
**I. Non-existing claim — PASS.** CLM-1061/CLM-9999 → safe structured 404 (`CLAIM_NOT_FOUND`), no blank/crash (verified prior gate + API here).
**J. Interrupted work — PASS.** Started a RAG action then navigated away → no crash, claims list rendered; back/forward + reload tolerate interruptions; no auth loss; no wrong-claim context.
**K. Custom manager questions — PASS (with one honest note).** Asked 5 Ukrainian questions on CLM-1006: (1) evidence supporting coverage — conf 22; (2) missing docs before approval — conf 24; (3) repair hours/parts consistency — conf 14 (returned the 8h-vs-14h + 38% anomaly); (4) what a human should review next — conf 15; all grounded, `CLM-1006`-cited, advisory. (5) irrelevant "What's the weather tomorrow?" — the app did **not** hallucinate a forecast; it returned the claim's accident-weather evidence (rain/wet road) with citations + advisory. **Safe** (no fabrication) but the fallback does not *explicitly* reject off-topic questions — it always answers from claim evidence (honest model-mode limitation, see Gaps).
**L. End-of-work recommendation — PASS.** Manager outcome tables below; "safe for automated final decision" = **NO** for every claim.
**M. Audit / traceability — PASS.** `/rag/audit` records each RAG action (coverage conf 14, summary conf 41, …); the claim's **Audit & Cost** page shows "AI Run Audit & Cost · full execution trace · governance evidence · RUN SUCCESSFUL · RUN ID …". Sufficient traceability for a demo.
**N. Visual realism — PASS.** Screenshots captured for triage, CLM-1006 evidence, similar-claims, CLM-1007, CLM-1012 insufficient, audit, and narrow viewport. Citation cards, confidence bar, advisory banner, tab labels readable; narrow 390×844 usable.
**O. Demo readiness — PASS.** Narrative below.

## Manager Outcome Tables
**CLM-1006**
| Field | Value |
|---|---|
| Evidence strength | Strong (13 chunks; coverage/anomaly/docs all cited) |
| Key supporting citations | driver statement, claim application, STO repair-detail, police report, invoice, policy-terms |
| Missing / uncertain | rate excess ($120 vs $85–95/hr), labor hours (14 vs 8 catalog) need human verification |
| Risk / anomaly | repair $2720 = **+38%** over $1970 benchmark; flagged for human review |
| Recommended next action | Route to human adjuster to verify labor hours + rate before approval |
| Safe for automated final decision | **NO** (advisory-only) |

**CLM-1012**
| Field | Value |
|---|---|
| Evidence strength | None (0 evidence chunks) |
| Key supporting citations | none |
| Missing / uncertain | all — no seeded evidence |
| Risk / anomaly | n/a |
| Recommended next action | Gather evidence / human review; AI explicitly declines to answer |
| Safe for automated final decision | **NO** (insufficient evidence) |

## RAG Evidence / Citation Proof
- 13 asks, providerMode `Mock` throughout. Confidence varies sanely by question (0 for CLM-1012; 11–41 for evidence-backed). Every evidence-backed answer carried 4 citations + advisory banner + closing "фінальне рішення приймає людина-адʼюстер" disclaimer. Anomaly figures ($2720, +38%, 8h vs 14h, $120 vs $85–95) are drawn from cited evidence, not fabricated.

## Cross-Claim Leakage Proof
- CLM-1006 asks → only `CLM-1006-*` citations (statement/application/repair-detail/police/policy-terms/approval-summary/invoice/photo-front/photo-rear/coverage-check). CLM-1007 asks → only `CLM-1007-*` (statement/application/invoice/missing-docs). CLM-1012 → 0. **Zero leakage across 13 asks**, including an immediate CLM-1007→CLM-1006 switch.

## Audit / Traceability Finding
- `/rag/audit?limit` returns recent asks (useCase + confidence). Claim "Audit & Cost" page shows a full AI-run execution trace + governance evidence + RUN ID. Adequate for a demo; deeper per-question RAG audit UI could be a future enhancement.

## Visual / UX Findings
- Enterprise look holds: triage table with status/risk/SLA/next-action chips, claim header + 8 tabs, AI Analysis & Evidence, infra status cards, answer card with confidence bar + citation table, advisory banner. Narrow viewport usable. No blank panels / overlaps / infinite spinners observed.

## Defects / Product Gaps
- **None blocking.** Honest gaps for the demo narrative: (1) **Confidence is modest** (fallback hash-retrieval + Mock generator) — present it as decision *support*, not authority. (2) **No explicit off-topic rejection** — irrelevant questions get a claim-grounded answer (safe, no fabrication) rather than "this is out of scope"; a real LLM behind the seam would improve this. (3) Visiting a thin/non-existent claim emits benign `404` console lines (handled gracefully). (4) RAG-specific audit is via API + the general AI-run audit page; a dedicated per-question RAG history UI would be a nice future add.

## Demo Narrative For Slava
1. **Story:** "An AI claim-evidence workbench: a manager triages claims, then for any claim the AI retrieves the claim's own evidence and gives a **cited, advisory** read on coverage, cost anomalies, and missing docs — keeping the human as the decision-maker."
2. **Real & working now:** triage table; claim workspace + tabs; Evidence Intelligence with coverage/risk/missing-docs/summary/similar + custom questions; **claim-scoped citations**; cost-anomaly detection (38% / 8h-vs-14h); insufficient-evidence safety; similar-claims; audit trace; cross-claim isolation.
3. **Describe honestly as fallback:** retrieval uses **in-memory hash embeddings** (not a live vector DB), generation is the **Mock grounded generator** (local LLM disabled) → confidence is modest and phrasing is templated. All data synthetic.
4. **Manager value visible:** evidence is cited and verifiable; anomalies are quantified; the system defers to humans and refuses when evidence is thin.
5. **Do not oversell:** it is not making fraud/payout decisions, not a live LLM, not real customer data.
6. **Top-5 demo steps:** (1) Claims list — point out RISK/SLA/NEXT-ACTION columns. (2) Open CLM-1006 → AI Evidence → **Coverage** (show citations + advisory). (3) Ask **repair hours/cost anomaly** → show the **38% / 14h-vs-8h** cited finding. (4) **Summary** → read the human-handoff. (5) Open **CLM-1012** → show the safe "insufficient evidence — human review" refusal.

## Screenshots / Artifacts
Under `gpt-handoff/InsuranceAIPlatform/azure-business-manager-workflow-acceptance-v0.1/`: `triage-claims-list.png`, `clm1006-evidence-answer.png`, `clm1006-similar-claims.png`, `clm1007-evidence-answer.png`, `clm1012-insufficient.png`, `clm1006-audit.png`, `narrow-evidence.png` (synthetic data, no secrets/PII).

## Network / Console Findings
- Core requests 200. Console noise = **18 benign `404`s** (thin-claim sub-resource fetches, handled gracefully) + **8 `net::ERR_ABORTED`** (navigation/interrupted-work test, incl. the intended pending-`/rag/ask` abort). **Zero JS exceptions, zero 500s, zero 401/403.**

## Boundaries Honored
NO source change · NO product commit/push · NO main · NO merge · NO Azure deploy/update/create/delete · NO DB change · NO data mutation · NO new resources · NO Qdrant/Ollama Azure · NO paid provider · NO secrets printed/stored · NO real PII · NO fake pass · NO claiming final acceptance for Slava (business pre-acceptance only).

## Files / Artifacts Created
- Temp/local (not committed to product repo): Playwright manager-session script, result JSON, 7 screenshots. Screenshots + this report published to the gate folder in gpt-handoff only.

## Final Verdict
**DEMO_READY_FOR_MANAGER_WORKFLOW** — a claim manager can run a believable end-to-end review process with cited, advisory, claim-scoped AI support and safe handling of weak/missing evidence; honest fallback caveats apply. Slava remains the final manual acceptor.

STOP — holding after report. No defect fixes, no deploy, no Azure mutation in this gate.
