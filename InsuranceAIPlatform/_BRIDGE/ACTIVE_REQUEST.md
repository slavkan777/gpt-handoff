REQUEST_ID: REQ-2026-06-05-insuranceai-local-live-runtime-setup-check-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-runtime-verification
PROJECT: InsuranceAIPlatform
GATE: LOCAL_LIVE_RUNTIME_SETUP_CHECK_QDRANT_OLLAMA_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/local-live-runtime-setup-check-qdrant-ollama-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/local-live-runtime-setup-check-qdrant-ollama-v0.1/report.md

OPERATOR NOTE:
Сори, путаница вышла с проектами: раньше не было явно указано, какой проект, какая source-папка и куда писать отчёт. Follow ROUTING LOCK strictly. Rule is fixed in AIKB: 00_CONTROL_CENTER/REQUEST_ROUTING_LOCK_RULE.md and START_HERE.md.

GOAL:
Prepare and verify real local live runtime for InsuranceAIPlatform RAG using local Qdrant and local Ollama where available. This is local-only; no cloud, no commit, no push.

DONE STATE:
1. Verify routing lock, repo path, branch, HEAD, and working tree.
2. Check whether Qdrant can be started locally and whether localhost:6333 becomes reachable.
3. Check whether Ollama can run locally and whether localhost:11434 plus a local model are usable.
4. If any runtime prerequisite is missing, do not fake success; report the exact prerequisite and stop that subpart.
5. Run CLM-1006 RAG infrastructure smoke with runtime flags enabled where applicable.
6. Report live_local only when actually reachable; otherwise report blocked/skipped.
7. Publish report to the three project-specific paths.

BOUNDARIES:
No source commit/push. No Azure/cloud. No paid providers. No secrets. No real PII. No main branch. No unrelated project edits. No schema migration. No fake live labels.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-05-insuranceai-local-live-runtime-setup-check-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-runtime-verification
PROJECT: InsuranceAIPlatform
GATE: LOCAL_LIVE_RUNTIME_SETUP_CHECK_QDRANT_OLLAMA_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Runtime Setup Check, Qdrant Result, Ollama Result, Product Smoke Result, Files Changed, Commands Run, Verification Evidence, Boundaries Honored, Known Limitations, Not Touched, Next Safe Step.

STOP after report.
