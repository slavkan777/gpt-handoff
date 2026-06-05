REQUEST_ID: REQ-2026-06-05-insuranceai-business-process-acceptance-sweep-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-business-acceptance-simulation
PROJECT: InsuranceAIPlatform
GATE: BUSINESS_PROCESS_ACCEPTANCE_SWEEP_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/business-process-acceptance-sweep-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/business-process-acceptance-sweep-v0.1/report.md

OPERATOR NOTE:
Follow ROUTING LOCK strictly. This is a business-process acceptance simulation for a manager/adjuster user, not implementation. Current feature branch already has local Qdrant RAG committed as 912e841. Do not push or change product code.

GOAL:
Use Playwright/manual-like browser automation plus API checks to validate the insurance business flows, not only technical widgets. Test whether a non-technical claim manager can use the current Claim Evidence Intelligence/RAG feature across realistic claim scenarios and degraded states.

DONE STATE:
1. Verify routing lock, repo path, branch, HEAD 912e841 or current expected feature HEAD, and clean/expected working tree.
2. Start/reuse Qdrant `iap-qdrant`, backend with `Rag__QdrantEnabled=true`, frontend in backend mode (`VITE_INSURANCE_API_MODE=backend`). Do not commit `.env.local`.
3. Discover available seeded/demo claims and evidence types from UI/API. Use real existing demo data only. Do not create product data unless a built-in seed/reset flow already exists and is safe; if a scenario lacks data, mark it NOT COVERED with reason.
4. Execute the business scenarios below using Playwright as a human-like manager: visible UI, clicks, page flow, answer readability, citations, warnings, degraded states, console/network observations.
5. For each scenario, report: business goal, role, data used, steps, expected business outcome, observed result, pass/fail, severity, and recommendation.
6. Do not fix product code. If a business-flow problem is found, report a fix prompt proposal only.
7. Publish report to the three project-specific paths.

BUSINESS SCENARIOS TO COVER:
A. Manager happy-path coverage review:
- manager opens dashboard/claims list, finds CLM-1006, opens Evidence Intelligence;
- checks policy coverage;
- verifies answer is understandable, advisory-only, has citations, and retrieved evidence supports the answer;
- manager should know what to do next without knowing Qdrant/AI internals.

B. Evidence-backed explanation quality:
- inspect answer/citations/evidence table;
- verify each citation visibly maps to claim evidence, policy/coverage/relevant document context if available;
- verify answer is not just a generic AI paragraph;
- verify no unsupported final approval/rejection is implied.

C. Degraded runtime but business continuity:
- stop Qdrant during the manager flow;
- manager repeats/checks coverage;
- UI should show degraded/fallback state honestly, but business workflow should still produce advisory answer/citations;
- no fake live/qdrant claim; manager should not see a hard crash or blank screen.

D. Cross-claim business separation:
- compare CLM-1006 with another available claim such as CLM-1009;
- verify each claim's answer uses its own primary evidence;
- no foreign claim evidence leaks into the primary coverage decision;
- if similar/cross-claim UI appears, it must be clearly marked as similar/context, not primary evidence.

E. Repeated manager actions and audit trust:
- click Check multiple times, refresh page, navigate away/back, switch between claims;
- ensure answers do not duplicate confusingly, contradict themselves, or lose citations;
- verify audit/history/provider/advisory indicators, if visible, remain understandable and do not look like final production approval.

F. Insufficient or unclear evidence case:
- discover a claim with fewer/no evidence chunks or less clear coverage data, if present;
- run the same manager flow;
- expected: cautious answer, low-confidence/needs-review/advisory language, no overconfident coverage decision;
- if no such claim exists, report data gap and recommend a seeded test case.

G. Partial/ambiguous coverage case:
- look for a claim/evidence combination where only part of damage or only part of policy may apply;
- expected: nuanced answer, not binary oversimplification;
- if no such claim exists, report data gap and recommend a seeded test case.

H. Duplicate/similar claim business case:
- inspect similar-claims or similar evidence features if available;
- expected: similar claims help investigation but do not contaminate primary claim citations;
- if similar-claims remains in-process/not Qdrant, report that truthfully as known limitation, not a bug.

I. Non-technical usability/readability:
- manager should understand status labels enough to proceed;
- technical labels such as qdrant/local-hash should not block business comprehension;
- check mobile/narrow viewport for the main business action and citations.

J. Demo/presentation flow:
- validate a short demo script: dashboard -> claim -> Evidence Intelligence -> Vector Runtime healthy -> Check policy coverage -> citations -> degraded fallback -> restore;
- report whether this is presentable to a business stakeholder and what caveats Slava should say aloud.

K. Negative/error-state business behavior:
- backend unavailable or UI mock-mode gotcha if safe to test;
- expected: clear error/degraded state, not misleading business output;
- no fake successful AI assessment when backend mode is wrong.

L. Decision-boundary/legal safety:
- verify UI text keeps AI advisory and human-review boundaries clear;
- no wording that implies automatic payout, rejection, fraud finding, or binding decision.

BOUNDARIES:
NO product source changes. NO source commit. NO push. NO main. NO Azure. NO deployment. NO paid provider. NO secrets. NO real PII. NO Ollama work. NO schema migration. NO unrelated projects. NO fake pass. NO making up missing business data. NO accepting a scenario as passed if it only verifies a URL/toast without business semantics.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-05-insuranceai-business-process-acceptance-sweep-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-business-acceptance-simulation
PROJECT: InsuranceAIPlatform
GATE: BUSINESS_PROCESS_ACCEPTANCE_SWEEP_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Environment Setup, Data Discovery, Business Scenario Matrix, Passed Scenarios, Failed/Weak Scenarios, Data Gaps, Screenshots/Artifacts, Console/Network Findings, Business Readability Findings, Legal/Decision-Boundary Findings, Product Bugs Found, Recommended Fix Gates, Files Changed, Commands Run, Boundaries Honored, Manual Acceptance Recommendation, Next Safe Step.

STOP after report. Do not commit, push, merge, deploy, touch Azure, or touch unrelated projects.