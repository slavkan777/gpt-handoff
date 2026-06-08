REQUEST_ID: REQ-2026-06-08-insuranceai-langchain-advanced-claim-analytics-sidecar-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-langchain-advanced-claim-analytics-sidecar
PROJECT: InsuranceAIPlatform
GATE: LANGCHAIN_ADVANCED_CLAIM_ANALYTICS_SIDECAR_MEGA_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/langchain-advanced-claim-analytics-sidecar-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md

SUMMARY:
Add an optional Advanced AI Review feature for InsuranceAIPlatform.
The existing .NET RAG remains the core claim-scoped evidence and citation pipeline.
Add a Python FastAPI LangChain sidecar as an optional advanced analytics layer.

INTENDED FLOW:
React UI button -> .NET API endpoint -> collect current claim data and current claim EvidenceChunks -> call Python LangChain sidecar -> return structured manager review -> show report in UI -> preserve fallback behavior.

DELIVERABLES:
1. Python sidecar under an isolated folder such as ai-sidecars/langchain-claim-analytics/.
2. Sidecar endpoints: GET /health and POST /advanced-claim-analytics.
3. LangChain used meaningfully in sidecar workflow.
4. .NET feature flag, typed client, and endpoint such as POST /api/claims/{claimId}/advanced-ai-review.
5. React UI button and structured report panel.
6. Tests and local smoke.
7. Commit and push only to rag/local-foundation-mega-v0.1 if local checks pass.
8. Azure dev/test deployment only if safe; otherwise report BLOCKED with exact next action.
9. Report to all project-specific gpt-handoff paths.

BOUNDARIES:
No main. No merge. No production. No paid provider. No secrets. No real PII. Do not replace existing .NET RAG. Do not mutate seeded claims. Do not make final payout/fraud/legal decisions. Stop if a schema migration or owner approval is required.

REPORT MUST START:
REQUEST_ID: REQ-2026-06-08-insuranceai-langchain-advanced-claim-analytics-sidecar-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | ROLLED_BACK | FAILED
TASK_TYPE: project-langchain-advanced-claim-analytics-sidecar
PROJECT: InsuranceAIPlatform
GATE: LANGCHAIN_ADVANCED_CLAIM_ANALYTICS_SIDECAR_MEGA_V0.1
COMPLETED_BY: claude

FINAL LINE:
GitHub handoff ready. Tell GPT: отчёт.
