REQUEST_ID: REQ-2026-06-04-insuranceai-local-rag-infra-ui-v0-1
STATE: READY_FOR_CLAUDE
PROJECT: InsuranceAIPlatform
GATE: LOCAL_RAG_INFRASTRUCTURE_UI_DEMO_V0.1

TASK
Extend the existing local RAG demo with local infrastructure status and optional local runtime adapters.

GOAL
Show on the AI Evidence page how SQL, local evidence index, optional local reasoning runtime, audit history, and human review fit together as one insurance workflow.

DONE
- Add an Evidence Intelligence Stack panel.
- Show SQL Source of Truth status.
- Show Evidence Memory Index status.
- Show Local Reasoning Runtime status.
- Add safe local health/check/reindex actions where practical.
- Preserve existing RAG, similar claims, audit history, citations, and Playwright tests.
- Run build, tests, targeted E2E, and local smoke where practical.
- Publish final report to InsuranceAIPlatform project report paths.

BOUNDARIES
Keep work local. No commit, no push, no deploy. Do not touch other projects.

REPORT
Write report to InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md, InsuranceAIPlatform/latest-report.md, and InsuranceAIPlatform/local-rag-infrastructure-ui-v0.1/report.md.

STOP
Stop after implementation, verification, and report.