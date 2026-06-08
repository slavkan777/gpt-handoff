REQUEST_ID: REQ-2026-06-08-insuranceai-azure-langchain-sidecar-deployment-and-smoke-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-azure-langchain-sidecar-deployment-and-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_LANGCHAIN_SIDECAR_DEPLOYMENT_AND_SMOKE_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/azure-langchain-sidecar-deployment-and-smoke-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt
CANONICAL_FULL_REQUEST: InsuranceAIPlatform/azure-langchain-sidecar-deployment-and-smoke-v0.1/ACTIVE_REQUEST.md

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
REQUEST_ID: REQ-2026-06-08-insuranceai-azure-langchain-sidecar-deployment-and-smoke-v0-1
GATE: AZURE_LANGCHAIN_SIDECAR_DEPLOYMENT_AND_SMOKE_V0.1
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
SOURCE_REPO_BRANCH: rag/local-foundation-mega-v0.1
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/

READ FIRST:
1. InsuranceAIPlatform/azure-langchain-sidecar-deployment-and-smoke-v0.1/ACTIVE_REQUEST.md
2. InsuranceAIPlatform/langchain-advanced-claim-analytics-sidecar-v0.1/report.md
3. InsuranceAIPlatform/latest-report.md
4. ai-kb/01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md

OWNER DECISION:
Slava approved finishing the LangChain Azure feature. Use the gate-specific request above as the full prompt.

SUMMARY:
Deploy the existing Python FastAPI LangChain Advanced AI Review sidecar to Azure dev/test as a separate Container App, enable the .NET AdvancedAiReview endpoint against it, redeploy frontend only if needed, and run live Azure smoke proving the separate button and structured advisory report work with current-claim citations only.

FINAL REPORT PATHS:
- InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
- InsuranceAIPlatform/latest-report.md
- InsuranceAIPlatform/latest-summary.json
- InsuranceAIPlatform/azure-langchain-sidecar-deployment-and-smoke-v0.1/report.md

FINAL LINE:
GitHub handoff ready. Tell GPT: отчёт.
