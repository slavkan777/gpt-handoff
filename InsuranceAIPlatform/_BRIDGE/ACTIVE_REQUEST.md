REQUEST_ID: REQ-2026-06-05-insuranceai-playwright-manual-acceptance-sweep-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-manual-acceptance-simulation
PROJECT: InsuranceAIPlatform
GATE: PLAYWRIGHT_MANUAL_ACCEPTANCE_SWEEP_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/playwright-manual-acceptance-sweep-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/playwright-manual-acceptance-sweep-v0.1/report.md

OPERATOR NOTE:
Follow ROUTING LOCK strictly. This is an acceptance-simulation / QA sweep, not product implementation. The current RAG/Qdrant feature is already committed locally on the feature branch as 912e841. Do not push.

GOAL:
Use Playwright as a human-like manual tester to exercise the InsuranceAIPlatform UI and API paths around the newly implemented local Qdrant RAG functionality. Find regressions, stale/fake labels, broken flows, console/network errors, UX blockers, and missing evidence before Slava does hands-on acceptance.

DONE STATE:
1. Verify routing lock, repo path, branch, HEAD, and clean/expected working tree after commit 912e841.
2. Start/reuse local Qdrant container `iap-qdrant`, start backend with `Rag__QdrantEnabled=true`, start frontend in real-backend mode (`VITE_INSURANCE_API_MODE=backend`). Do not commit `.env.local`.
3. Create/run a temporary or uncommitted Playwright exploratory/manual-acceptance spec if useful. Product code changes are forbidden.
4. Test the core UI flows and edge cases listed below. Capture screenshots/traces only where useful; keep artifacts in test-results or gpt-handoff path and do not commit them.
5. Report pass/fail per case, with exact URL/page, action, expected result, observed result, screenshot/artifact path if available, and severity.
6. If a bug is found, do NOT fix product code in this gate. Report it with reproduction steps and recommended next fix gate.
7. Publish fresh report to the three project-specific paths.

TEST CASES TO COVER:
A. Startup/routing:
- backend starts, frontend starts, app loads without fatal console errors;
- navigation to claims/dashboard/detail works;
- no global bridge/other project files touched.

B. CLM-1006 Evidence Intelligence happy path:
- open CLM-1006 detail / Claim Evidence Intelligence page;
- Evidence Intelligence Stack visible;
- Vector Runtime row shows live_local / qdrant / reachable=true while Qdrant is up;
- SQL/index/evidence layers are healthy;
- click Check policy coverage;
- answer card appears with advisory/non-final decision warning;
- citations render and all visible claim ids / retrieved chunk ids are CLM-1006 only;
- no stale fallback/demo foreign claim data appears.

C. Qdrant semantic proof through UI/API:
- verify infrastructure endpoint and UI agree on live_local/qdrant;
- verify Qdrant collection has points after UI/RAG action;
- verify repeated Check does not create broken UI state or duplicate confusing output;
- verify Reindex/Check stack control if present.

D. Fallback path:
- stop `iap-qdrant` while backend/frontend are running;
- refresh/check stack;
- Vector Runtime flips to skipped_not_available / in-memory-hash / reachable=false;
- Check policy coverage still returns answer/citations from fallback;
- no fake qdrant/backend/live label remains;
- restart Qdrant and verify live_local/qdrant returns.

E. Cross-claim / leakage guard:
- discover at least one non-CLM-1006 claim visible in the UI/API;
- open it or query its RAG path if available;
- verify its evidence/citations do not show CLM-1006 chunks unless intentionally cross-claim UI says similar claims;
- verify CLM-1006 flow never surfaces foreign-claim evidence as primary retrieval.

F. Negative/user behavior:
- click Check multiple times quickly or while loading;
- navigate away/back after answer renders;
- refresh page after answer/status loads;
- test empty/invalid question only if there is a visible question input;
- verify loaders/errors are understandable and no blank white screen occurs.

G. Responsive/accessibility smoke:
- run one desktop viewport and one narrow/mobile viewport for CLM-1006 evidence panel;
- ensure Vector Runtime and citations are visible/readable enough;
- check obvious keyboard focus/clickability for the main Check button.

H. Regression guard:
- run existing targeted Playwright `22-rag-evidence` in mock mode and, if practical, one real-backend Playwright run;
- run a fast backend smoke or tests only if needed for confidence; do not rerun expensive full suite unless a change or failure requires it.

BOUNDARIES:
NO product source changes. NO source commit. NO push. NO main. NO Azure. NO deployment. NO paid provider. NO secrets. NO real PII. NO Ollama work. NO schema migration. NO unrelated projects. NO fake pass. NO accepting tests that only check URL/toast without semantic data. If product code must change to fix a bug, stop and report a fix prompt proposal.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-05-insuranceai-playwright-manual-acceptance-sweep-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-manual-acceptance-simulation
PROJECT: InsuranceAIPlatform
GATE: PLAYWRIGHT_MANUAL_ACCEPTANCE_SWEEP_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Environment Setup, Playwright Strategy, Test Matrix, Passed Cases, Failed/Blocked Cases, Screenshots/Artifacts, Console/Network Findings, Qdrant UI/API Proof, Fallback Proof, Cross-Claim Leakage Findings, Product Bugs Found, Files Changed, Commands Run, Boundaries Honored, Manual Acceptance Recommendation, Next Safe Step.

STOP after report. Do not commit, push, merge, deploy, touch Azure, or touch unrelated projects.