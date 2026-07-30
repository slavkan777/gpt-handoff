REQUEST_ID: REQ-2026-06-05-insuranceai-local-runtime-live-check-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-runtime-verification
PROJECT: InsuranceAIPlatform
GATE: LOCAL_RUNTIME_LIVE_CHECK_QDRANT_OLLAMA_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/local-runtime-live-check-qdrant-ollama-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/local-runtime-live-check-qdrant-ollama-v0.1/report.md

OPERATOR NOTE:
Сори, путаница вышла с проектами: раньше не было явно указано, какой проект, какая source-папка и куда писать отчёт. From now on, follow ROUTING LOCK strictly. Rule is fixed in AIKB: 00_CONTROL_CENTER/REQUEST_ROUTING_LOCK_RULE.md and START_HERE.md.

GOAL:
Verify the real local runtime path for InsuranceAIPlatform RAG: local Qdrant vector runtime and local Ollama/model runtime where they are already available or safely startable on this machine. This is local-only runtime verification, not commit, push, deploy, cloud, or paid provider work.

DONE STATE:
1. Confirm route lock, repo path, branch, HEAD, and working tree.
2. Check local Qdrant and local Ollama availability mechanically.
3. If a local runtime can be safely started/reused without external account or system install, use it for verification.
4. Run InsuranceAIPlatform local RAG infrastructure smoke for CLM-1006 with runtime flags enabled where appropriate.
5. Report honest labels: live_local only when actually reachable; otherwise blocked or skipped_not_available.
6. Preserve existing RAG fallback behaviour and tests.
7. Publish fresh report to the three project-specific paths.

BOUNDARIES:
No source commit/push. No deployment. No cloud resources. No paid provider. No unrelated project edits. No main branch. No schema migration. No fake live labels.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-05-insuranceai-local-runtime-live-check-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-runtime-verification
PROJECT: InsuranceAIPlatform
GATE: LOCAL_RUNTIME_LIVE_CHECK_QDRANT_OLLAMA_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Runtime Discovery, Qdrant Result, Ollama Result, Product Smoke Result, Files Changed, Commands Run, Verification Evidence, Boundaries Honored, Known Limitations, Not Touched, Next Safe Step.

STOP after report.
