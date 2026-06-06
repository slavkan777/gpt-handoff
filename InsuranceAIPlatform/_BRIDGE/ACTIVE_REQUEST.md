REQUEST_ID: REQ-2026-06-06-insuranceai-azure-business-manager-workflow-acceptance-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-azure-business-manager-workflow-acceptance
PROJECT: InsuranceAIPlatform
GATE: AZURE_BUSINESS_MANAGER_WORKFLOW_ACCEPTANCE_MEGA_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/azure-business-manager-workflow-acceptance-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
SOURCE_REPO_BRANCH: rag/local-foundation-mega-v0.1
AIKB_PROJECT_PATH: ai-kb/01_PROJECTS/InsuranceAIPlatform/
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/azure-business-manager-workflow-acceptance-v0.1/report.md

OPERATOR NOTE:
The previous gate proved the Azure site works technically. This gate is different: act like a real insurance claim manager / adjuster using the live Azure dev/test site during a working session. Do not just click QA checks. Run realistic manager workflows from triage to evidence review, coverage support, anomaly review, escalation/human-review recommendation, audit trace, and moving between claims. The goal is to see whether a manager could use this demo in a believable work process before Slava performs final owner manual acceptance.

CURRENT ACCEPTED AZURE STATE:
- Public SWA URL: https://kind-meadow-03cf73103.7.azurestaticapps.net/
- Backend Container App: https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io
- Backend revision expected: iap-demo-api--0000003
- Current mode: Azure backend frontend + RAG fallback, not mock-only UI.
- Expected RAG runtime: vector fallback `in-memory-hash`, local LLM disabled, providerMode `Mock`, no fake Qdrant/Ollama.
- Known useful claims: CLM-1006 has rich RAG evidence; CLM-1007 has claim-specific evidence; CLM-1012 is valid insufficient-evidence case; CLM-1061/CLM-9999 may be non-existing safe 404.
- Previous UI sweep: 41/41 automated UI scenarios PASS, 6/6 API probes green, 0 blocking defects. This gate must not duplicate only that technical sweep; it must add manager-workflow realism.

GOAL:
Run a thorough business-manager workflow acceptance sweep against the live Azure dev/test site. Evaluate whether a real claim manager can use the app to triage claims, inspect claim details, use AI evidence/RAG to understand coverage and anomalies, handle insufficient evidence safely, avoid cross-claim confusion, and produce a next-action recommendation without the system pretending to make final payout/fraud/legal decisions.

DONE STATE:
1. Confirm routing lock and that this is read-only/test-only.
2. Log in to the live SWA with existing demo/dev credential path only; do not print secrets.
3. Run the manager workflow scenarios below as realistic work sessions, not isolated button tests.
4. For each workflow, record: manager goal, steps taken, what a manager learned, AI/RAG output quality, citations/evidence used, confidence/advisory safety, next action, blockers/defects.
5. Capture screenshots/artifacts for the key workflows under the gate-specific handoff folder.
6. Produce a final business verdict: DEMO_READY_FOR_MANAGER_WORKFLOW | PARTIAL_WITH_GAPS | NOT_READY.
7. Publish the report to all three project-specific report paths.

STRICT CREDENTIAL RULE:
- Use only the existing seeded demo/dev login path already available to the app/tests.
- Do not create a secret login known only to Claude.
- Do not print passwords or tokens.
- Do not store credentials in repo, gpt-handoff, screenshots, traces, or report.
- If login cannot be done safely, stop BLOCKED.

MANAGER WORKFLOW MATRIX:

A. Morning triage workflow
Manager intent: start the day and identify which claims need attention.
1. Log in and open dashboard.
2. Open claims list.
3. Identify available claims, statuses, customer/vehicle/amount/risk cues where visible.
4. Use search/filter/sort if present.
5. Pick at least three claims for review: CLM-1006, CLM-1007, and one additional available claim.
6. Record whether the list gives enough information for a manager to decide what to open next.
Acceptance: manager can start work without confusion; no stale fixture/mismatch; list data appears backend-driven.

B. First claim review — CLM-1006 coverage support
Manager intent: understand whether the claim has evidence supporting coverage.
1. Open CLM-1006 detail.
2. Inspect overview/customer/vehicle/policy/documents/evidence tabs if available.
3. Open Evidence Intelligence / AI Evidence.
4. Run coverage/policy coverage action.
5. Read answer, citations, confidence, advisory banner.
6. Manager note: what supports coverage? what remains uncertain? what should human review?
7. Verify AI does not make final payout/legal/fraud decision.
Acceptance: manager can form a support recommendation from citations, not blind trust.

C. Repair invoice / cost anomaly workflow — CLM-1006
Manager intent: check whether repair invoice/hours/cost evidence looks abnormal.
1. Ask/use available action for repair hours/cost anomaly or risk.
2. Look for evidence comparing repair detail, photos, claim application, policy terms, invoice/parts/time where available.
3. Record whether citations let manager verify the anomaly.
4. Record whether the app recommends human review rather than final fraud conclusion.
Acceptance: anomalies are explainable, cited, and safely framed.

D. Missing documents workflow
Manager intent: decide whether the claim is missing required documents before escalation.
1. Use missing-docs action if present on CLM-1006 and CLM-1007.
2. Compare outputs.
3. Confirm claim-scoped citations and no mixing.
4. Decide next manager action: request missing doc, continue, or human review.
Acceptance: manager can identify document gaps without cross-claim leakage.

E. Similar claims / pattern check workflow
Manager intent: see whether there are similar cases or patterns worth reviewing.
1. Use similar claims / pattern action if present.
2. Check whether the UI clearly separates similar-claim cards from citations/evidence for the current claim.
3. Confirm it does not imply fraud/payout decision.
4. Record whether a manager would understand what to do next.
Acceptance: similar case output is useful but not misleading.

F. Claim summary / handoff workflow
Manager intent: produce a quick internal summary for a human adjuster/supervisor.
1. Use summary action for CLM-1006.
2. Check if summary includes: claim context, key evidence, uncertainty, recommendation to review, citations.
3. Record whether summary is usable as a manager handoff.
Acceptance: summary is concise, grounded, and safe.

G. Second claim review — CLM-1007 or another evidence-backed claim
Manager intent: continue normal work on another claim and verify state isolation.
1. Navigate from CLM-1006 to CLM-1007 or another claim with evidence.
2. Confirm header/customer/vehicle/status changes.
3. Run available AI/RAG action.
4. Verify answer/citations belong to that claim only.
5. Navigate back to CLM-1006 and confirm no stale answer/header/citations.
Acceptance: manager can work multiple claims in sequence without state leakage.

H. Insufficient evidence workflow — CLM-1012
Manager intent: see how the app behaves when evidence is weak/missing.
1. Open CLM-1012 directly or via list/API if visible.
2. Run coverage/summary/custom question if available.
3. Expected: low/zero confidence, 0 citations or explicit limitation, human review wording.
4. Record whether manager is protected from hallucinated decisions.
Acceptance: app says it does not know when evidence is insufficient.

I. Non-existing / wrong claim workflow
Manager intent: typo or stale link handling.
1. Try CLM-1061 and/or CLM-9999 route/API.
2. Confirm safe not-found behavior, not blank screen/crash.
3. Record whether error is understandable enough for a manager.
Acceptance: typo/stale claim does not crash the work session.

J. Interrupted work workflow
Manager intent: real user switches pages, refreshes, and resumes.
1. Start a RAG/AI action, then navigate away during loading.
2. Return to the claim.
3. Refresh page after answer.
4. Use browser back/forward between claims/evidence pages.
5. Confirm no crash, stale spinner, auth loss, or wrong claim context.
Acceptance: app tolerates normal manager interruptions.

K. Manager custom questions workflow
Manager intent: ask practical questions not only fixed buttons.
If custom input exists, ask at least these questions on CLM-1006:
1. “What evidence supports coverage for this damage?”
2. “What evidence is missing before approval?”
3. “Are repair hours or parts inconsistent with the evidence?”
4. “What should a human adjuster review next?”
5. An irrelevant question: “What is the weather tomorrow?”
For each: verify answer is grounded, cited when relevant, cautious when irrelevant, and advisory-only.
If custom input does not exist, mark as product limitation/gap, not test failure.

L. Manager end-of-work recommendation workflow
Manager intent: after reviewing claim, know the next action.
For CLM-1006 and CLM-1012, produce a manager-facing outcome table:
- claim id;
- evidence strength;
- key supporting citations;
- missing/uncertain items;
- risk/anomaly notes;
- recommended next action;
- whether safe for automated final decision: should be NO.
Acceptance: the app supports manager decision support, not automated final decisions.

M. Audit / traceability workflow
Manager intent: understand whether AI actions are traceable.
1. Use audit/history UI/API if present after asking RAG questions.
2. Check whether manager can see recent AI questions/actions or evidence references.
3. Record whether audit is sufficient for a demo.
Acceptance: there is at least minimal traceability or the gap is documented.

N. Visual workflow realism
Manager intent: work without visual friction.
1. Desktop screenshot for triage, CLM-1006 Evidence Intelligence, CLM-1007/second claim, CLM-1012 insufficient evidence, and final recommendation state.
2. Narrow viewport quick check on Evidence Intelligence.
3. Note readability of citation cards, confidence, advisory banner, buttons, tab labels, empty/error states.
Acceptance: manager can read and use the workflow without obvious visual blocker.

O. Business narrative / demo readiness verdict
Manager intent: can this be demoed as an insurance AI workbench?
Produce final assessment:
1. What story Slava can show in demo.
2. What is real and working now.
3. What must be honestly described as fallback/mock/local-hash.
4. What manager value is visible.
5. What gaps should not be oversold.
6. Top 5 demo script steps for Slava.

ACCEPTANCE CRITERIA:
PASS / DEMO_READY_FOR_MANAGER_WORKFLOW if:
- manager can triage claims;
- manager can review CLM-1006 coverage/evidence;
- manager can identify anomaly/missing docs or risk cues if available;
- manager can switch claims without leakage;
- insufficient evidence is safe;
- citations/advisory/confidence are visible and useful;
- no blocking visual/auth/navigation/API issue;
- no final payout/fraud/legal decision is implied.

PARTIAL_WITH_GAPS if:
- core manager flow works but important manager actions are missing/unclear, e.g. no custom question input, no audit visibility, confusing error handling, weak business narrative, or minor UI blockers.

NOT_READY if:
- claim manager cannot complete CLM-1006 review;
- RAG/citations are broken or misleading;
- cross-claim data leaks;
- UI crashes in normal manager flow;
- app presents unsafe final decisions.

ALLOWED CHANGES:
- Browser automation scripts in temp/local only if needed.
- Sanitized screenshots, traces, and report artifacts under `gpt-handoff/InsuranceAIPlatform/azure-business-manager-workflow-acceptance-v0.1/`.
- No product source commit. No Azure changes. No DB changes. No user creation unless an already-existing safe dev/test path is required and explicitly noted; prefer no user changes.

STRICT BOUNDARIES:
NO source code changes. NO product repo commit/push. NO main. NO merge. NO Azure deploy/update/create/delete. NO DB schema changes. NO data mutation/deletion. NO new Azure resources. NO Qdrant/Ollama Azure. NO paid provider. NO secrets printed/stored. NO real PII. NO fake pass. NO claiming final acceptance for Slava. Claude performs business pre-acceptance only; Slava remains final owner/manual acceptor.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-06-insuranceai-azure-business-manager-workflow-acceptance-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL_WITH_GAPS | NOT_READY | BLOCKED | FAILED
TASK_TYPE: project-azure-business-manager-workflow-acceptance
PROJECT: InsuranceAIPlatform
GATE: AZURE_BUSINESS_MANAGER_WORKFLOW_ACCEPTANCE_MEGA_V0.1
COMPLETED_BY: claude

Sections:
- Routing Lock Verification
- Credential Handling Statement
- Environment / Backend Mode Verification
- Business Workflow Summary
- Detailed Workflow Results A-O
- Manager Outcome Tables
- RAG Evidence / Citation Proof
- Cross-Claim Leakage Proof
- Audit / Traceability Finding
- Visual / UX Findings
- Defects / Product Gaps
- Demo Narrative For Slava
- Screenshots / Artifacts
- Network / Console Findings
- Boundaries Honored
- Files / Artifacts Created
- Commands Run
- Final Business Verdict
- Slava Manual Manager-Workflow Checklist
- Recommended Next Step

STOP after report. Do not fix defects, deploy, mutate Azure, mutate DB, or continue to managed provider work in this gate.