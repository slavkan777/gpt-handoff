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
2. ai-kb/01_PROJECTS/Warden/DECISIONS/DEC-0002_WARDEN_LAB_PRIMARY_GOAL_GOVERNANCE_VALIDATION.md
3. ai-kb/01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md
4. ai-kb/01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-08-11_warden_lab_customer_assistant.md
5. ai-kb/01_PROJECTS/InsuranceAIPlatform/FEATURE_PLANS/WARDEN_LAB_GATE_0_CURRENT_TRUTH_ADOPTION.md

PRIMARY LAB OBJECTIVE:
The insurance feature is the carrier/test load. The main goal is to test and tune WARDEN v1 + GPT Macro orchestration + Owner operating flow on a real existing project. Gate success is not only product truth; it also requires an explicit process scorecard covering Macro completeness, Owner interruptions, ambiguity/guesswork, scope drift, WARDEN friction/defects, environment friction, and whether continuation remains finite and understandable.

OWNER DECISION:
Slava explicitly selected the existing InsuranceAIPlatform as the first real WARDEN-Lab carrier and authorized Gate 0. Gate 0 is read-only current-truth/existing-project adoption. No Customer Insurance AI Assistant implementation is authorized yet.

EXECUTION SHAPE:
One logical Gate = one bounded Macro. Do not turn this into a stream of owner-facing micro-prompts. If a genuinely material unresolved fact blocks trustworthy continuation, return BLOCKED with the minimum required Owner/tool action instead of asking many incremental questions.

WARDEN DEFECT RULE:
WARDEN v1 itself remains engineering-locked. If this lab proves a new CURRENT + REPRODUCIBLE + MATERIAL WARDEN defect, record exact evidence and classify it; do not silently repair WARDEN inside InsuranceAIPlatform. A separate WARDEN repair boundary will be opened by Owner/GPT if needed.

SUMMARY:
Establish exact current repo/runtime/Azure/RAG/LangChain truth from primary evidence, reconcile AIKB drift, recommend the smallest safe Gate 1 boundary for the future Customer Insurance AI Assistant, AND report how well WARDEN/GPT Macro orchestration operated. No source edits, commit/push, Azure/DB mutation, secret/provider changes, feature implementation, Gate 1 freeze, or WARDEN source repair.

FINAL REPORT PATHS:
- InsuranceAIPlatform/warden-lab-gate-0-current-truth-adoption/report.md
- InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
- InsuranceAIPlatform/latest-report.md

FINAL REPORT MUST INCLUDE:
- normal Current Truth sections from the canonical request;
- WARDEN/GPT Macro process scorecard;
- actual Owner-facing prompt/decision count for this Gate;
- whether the Macro was executable without clarification;
- any ambiguity, executor guesswork, scope overreach, workaround or tooling friction;
- each issue classified as WARDEN_DEFECT / GPT_MACRO_DEFECT / EXECUTOR_ISSUE / ENVIRONMENT_ISSUE / PRODUCT_ISSUE;
- no speculative WARDEN rework without reproduction/materiality.

FINAL VERDICT:
WARDEN_LAB_GATE0_CURRENT_TRUTH_READY
or
WARDEN_LAB_GATE0_BLOCKED

FINAL LINE:
GitHub handoff ready. Tell GPT: отчёт.
