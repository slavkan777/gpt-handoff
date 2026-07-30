REQUEST_ID: REQ-2026-06-05-insuranceai-qdrant-to-manual-acceptance-mega-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-finalization-to-manual-acceptance
PROJECT: InsuranceAIPlatform
GATE: QDRANT_TO_MANUAL_ACCEPTANCE_MEGA_GATE_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/qdrant-to-manual-acceptance-mega-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/qdrant-to-manual-acceptance-mega-v0.1/report.md

OPERATOR NOTE:
Сори, путаница вышла с проектами: раньше не было явно указано, какой проект, какая source-папка и куда писать отчёт. Follow ROUTING LOCK strictly. Rule is fixed in AIKB: 00_CONTROL_CENTER/REQUEST_ROUTING_LOCK_RULE.md and START_HERE.md.

USER APPROVAL:
Slava explicitly approves one large molecular prompt up to the manual-check boundary, including commit-only if all gates pass. This does NOT authorize push, main merge, Azure, deployment, paid providers, secrets, real PII, or unrelated projects.

CONTEXT:
Qdrant runtime is live locally. QDRANT_RETRIEVAL_ADAPTER_V0.1 was accepted by GPT with limitations: Qdrant now stores and serves claim evidence vectors, tests pass, but there are still final pre-manual acceptance checks/polish boundaries.

GOAL:
Bring the current Qdrant RAG work to Slava manual acceptance readiness in one bounded mega gate: final audit, missing polish only if needed, verification, manual checklist, and commit-only if everything is clean.

DONE STATE:
1. Verify ROUTING LOCK, repo path, branch, HEAD, current uncommitted state, and latest accepted report.
2. Inspect current Qdrant adapter implementation and verify no semantic gap remains: backend=qdrant only when Qdrant actually serves retrieval.
3. Run final product smoke for CLM-1006:
   - /rag/infrastructure shows vectorRuntime live_local, reachable=true, backend=qdrant when Qdrant is serving;
   - /rag/ask returns citations/retrievedChunkIds only for CLM-1006;
   - fallback works when Qdrant is unavailable/disabled, with backend=in-memory-hash and no fake qdrant label.
4. Run backend build/tests and targeted RAG tests. Run frontend build/E2E if frontend or visible status/UI is touched.
5. Make only minimal fixes/polish needed to pass the checks. Do not expand scope.
6. Produce a Slava manual acceptance checklist with exact URLs/pages/actions/expected observations.
7. If and only if all verification passes, create ONE local source commit on the current feature branch. No push. Commit message should be clear, e.g. `feat(rag): add local qdrant retrieval adapter`.
8. Publish a fresh sanitized report to all three project-specific paths.

BOUNDARIES:
NO push. NO main. NO Azure. NO deployment. NO paid provider. NO secrets. NO real PII. NO unrelated projects. NO schema/EF migration unless already present and unavoidable; if migration is needed, stop BLOCKED. NO Ollama work. NO fake backend=qdrant. NO fake live_local. NO broad refactor. NO committing if tests/smoke fail.

STOP CONDITIONS:
Stop with BLOCKED or FAILED if routing lock mismatches, build/tests fail and cannot be fixed narrowly, Qdrant semantic proof is weak, fallback breaks, secret scan fails, schema migration is required, or commit would include unrelated files.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-05-insuranceai-qdrant-to-manual-acceptance-mega-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-finalization-to-manual-acceptance
PROJECT: InsuranceAIPlatform
GATE: QDRANT_TO_MANUAL_ACCEPTANCE_MEGA_GATE_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Starting State, Final Fixes/Polish, Files Changed, Verification Matrix, Qdrant Semantic Proof, Fallback Proof, Tests, Commit Result, Manual Acceptance Checklist, Boundaries Honored, Known Limitations, Not Touched, Next Safe Step.

STOP after report. Do not push, merge, deploy, touch Azure, or touch unrelated projects.
