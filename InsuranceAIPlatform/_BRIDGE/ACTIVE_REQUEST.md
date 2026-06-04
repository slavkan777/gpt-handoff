REQUEST_ID: REQ-2026-06-04-insuranceai-rag-audit-history-ui-persistence-fix-v0-2
STATE: READY_FOR_CLAUDE
PROJECT: InsuranceAIPlatform
GATE: RAG_AUDIT_HISTORY_UI_PERSISTENCE_FIX_AND_PRE_MANUAL_ACCEPTANCE_V0.2

TASK
Add Audit History UI to the RAG Claim Evidence Intelligence page.

GOAL
The page should show persisted RAG audit history after reload by reading the existing audit endpoint for the current claim.

CONTEXT
Previous RAG completion report was accepted with one limitation: UI persistence after reload was skipped. Backend audit persistence already exists.

DONE
- Audit History panel exists.
- It loads current claim audit history.
- Reload shows previous RAG trace or answer metadata.
- Playwright persistence test passes instead of being skipped.
- Existing RAG buttons and similar-claims behavior continue working.
- Build and tests pass.
- Report is published to InsuranceAIPlatform project report paths.

REPORT
Write final report to:
- InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
- InsuranceAIPlatform/latest-report.md
- InsuranceAIPlatform/rag-audit-history-ui-persistence-fix-v0.2/report.md
