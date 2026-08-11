REQUEST_ID: REQ-2026-08-11-IAP-WARDEN-LAB-G1-SPEC
STATE: READY_FOR_CLAUDE
TASK_TYPE: WARDEN_EXISTING_PROJECT_ADOPTION_AND_FROZEN_SPEC
PROJECT: InsuranceAIPlatform
GATE: WARDEN_LAB_GATE_1_CUSTOMER_INSURANCE_AI_ASSISTANT_SPEC
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/warden-lab-gate-1-customer-assistant-spec/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: GPT / WARDEN orchestration
CANONICAL_FULL_REQUEST: InsuranceAIPlatform/warden-lab-gate-1-customer-assistant-spec/ACTIVE_REQUEST.md

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
REQUEST_ID: REQ-2026-08-11-IAP-WARDEN-LAB-G1-SPEC
GATE: WARDEN_LAB_GATE_1_CUSTOMER_INSURANCE_AI_ASSISTANT_SPEC
SOURCE_REPO_REMOTE_EXPECTED: slavkan777/InsuranceAIPlatform
SOURCE_REPO_BRANCH_EXPECTED: rag/local-foundation-mega-v0.1
GATE0_OBSERVED_HEAD: f9e34c65d98b251fa6dd8931d17256bb00a70992
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/

READ FIRST:
1. InsuranceAIPlatform/warden-lab-gate-1-customer-assistant-spec/ACTIVE_REQUEST.md
2. ai-kb/01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-08-11_warden_lab_gate1_owner_package.md
3. ai-kb/01_PROJECTS/InsuranceAIPlatform/FEATURE_PLANS/WARDEN_LAB_GATE_1_CUSTOMER_INSURANCE_AI_ASSISTANT_SPEC.md
4. ai-kb/01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md
5. InsuranceAIPlatform/warden-lab-gate-0-current-truth-adoption/report.md

OWNER DECISION:
Slava explicitly confirmed the complete Gate 1 package. Use the existing InsuranceAIPlatform product branch as the lab baseline, freeze Customer Insurance AI Assistant v1 as general/public grounded guidance only, keep claim-specific access/authentication for a post-close Child Gate, include status-truthfulness repair in the future implementation scope, park the unavailable LangChain sidecar, keep Qdrant/Ollama/paid LLM out of scope, and require valid local full-stack E2E later on synthetic data.

PRIMARY LAB PURPOSE:
Test and tune WARDEN v1 + GPT one-Macro-per-logical-stage orchestration + Claude execution + later Codex independent review + low-interruption Owner flow. Product success alone is insufficient.

EXECUTION SHAPE:
ONE logical Gate = ONE bounded Critic-first Macro. Internal micro-planning is allowed. Owner-facing micro-prompt choreography is a process failure unless a genuine material authority blocker makes continuation unsafe.

WARDEN DEFECT RULE:
Do not modify WARDEN v1 source inside InsuranceAIPlatform. If Gate 1 proves a CURRENT + REPRODUCIBLE + MATERIAL WARDEN defect/capability gap, capture evidence and return BLOCKED when it prevents trustworthy governance. A separate WARDEN repair boundary will be opened later if justified.

SUMMARY:
Reverify current product baseline, discover/use the actual WARDEN v1 CLI without invented commands, canonically adopt/bootstrap InsuranceAIPlatform under WARDEN, record HIGH risk, capture ProductSourceFingerprint, freeze Owner Contract, materialize SPEC -> PLAN -> TASKS, requirements/acceptance/evidence/proof-asset contract, HIGH-risk external-review requirement, process scorecard, and closure/delivery/Child-Gate boundaries. NO Customer Assistant implementation in this Gate.

FINAL REPORT PATHS:
- InsuranceAIPlatform/warden-lab-gate-1-customer-assistant-spec/report.md
- InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
- InsuranceAIPlatform/latest-report.md

FINAL VERDICT:
WARDEN_LAB_GATE1_SPEC_FROZEN_READY_FOR_IMPLEMENTATION_MACRO
or
WARDEN_LAB_GATE1_BLOCKED

FINAL LINE:
GitHub handoff ready. Tell GPT: отчёт.
