REQUEST_ID: REQ-2026-08-11-IAP-WARDEN-LAB-G0-CURRENT-TRUTH
STATE: READY_FOR_CLAUDE
TASK_TYPE: READ_ONLY_CURRENT_TRUTH_ADOPTION
PROJECT: InsuranceAIPlatform
GATE: WARDEN_LAB_GATE_0_CURRENT_TRUTH_ADOPTION
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/warden-lab-gate-0-current-truth-adoption/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: GPT / WARDEN orchestration
CANONICAL_FULL_REQUEST: InsuranceAIPlatform/warden-lab-gate-0-current-truth-adoption/ACTIVE_REQUEST.md

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
REQUEST_ID: REQ-2026-08-11-IAP-WARDEN-LAB-G0-CURRENT-TRUTH
GATE: WARDEN_LAB_GATE_0_CURRENT_TRUTH_ADOPTION
SOURCE_REPO_REMOTE_EXPECTED: slavkan777/InsuranceAIPlatform
SOURCE_REPO_BRANCH: DISCOVER_CURRENT_TRUTH_DO_NOT_ASSUME_AI_KB
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/

READ FIRST:
1. InsuranceAIPlatform/warden-lab-gate-0-current-truth-adoption/ACTIVE_REQUEST.md
2. ai-kb/01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md
3. ai-kb/01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-08-11_warden_lab_customer_assistant.md
4. ai-kb/01_PROJECTS/InsuranceAIPlatform/FEATURE_PLANS/WARDEN_LAB_GATE_0_CURRENT_TRUTH_ADOPTION.md

OWNER DECISION:
Slava explicitly selected the existing InsuranceAIPlatform as the first real WARDEN-Lab carrier and authorized Gate 0. Gate 0 is read-only current-truth/existing-project adoption. No Customer Insurance AI Assistant implementation is authorized yet.

SUMMARY:
Establish exact current repo/runtime/Azure/RAG/LangChain truth from primary evidence, reconcile AIKB drift, and recommend the smallest safe Gate 1 boundary for the future Customer Insurance AI Assistant. No source edits, commit/push, Azure/DB mutation, secret/provider changes, feature implementation, or Gate 1 freeze.

FINAL REPORT PATHS:
- InsuranceAIPlatform/warden-lab-gate-0-current-truth-adoption/report.md
- InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
- InsuranceAIPlatform/latest-report.md

FINAL VERDICT:
WARDEN_LAB_GATE0_CURRENT_TRUTH_READY
or
WARDEN_LAB_GATE0_BLOCKED

FINAL LINE:
GitHub handoff ready. Tell GPT: отчёт.
