# InsuranceAIPlatform — Latest gate report

**Gate:** AIKB_AND_CLAUDEOS_E2E_SEMANTIC_ASSERTION_RULE_UPDATE_V0.1
**Type:** durable knowledge / rules / state maintenance
**Date (UTC):** 2026-05-28
**InsuranceAIPlatform source `dev` HEAD before:** `03725241c8dfdbed7ce17db61fb51d9d7f211116`
**InsuranceAIPlatform source `dev` HEAD after:**  `03725241c8dfdbed7ce17db61fb51d9d7f211116` *(unchanged — gate is no-source-commit/no-source-push by spec)*
**InsuranceAIPlatform source `origin/main`:**     `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` *(unchanged)*
**AIKB repo updates committed and pushed** (see GITHUB_HANDOFF_PATHS below).
**ClaudeOS / claude-vault:** SKIPPED_PROTECTED — sacred CLAUDE.md root constraint.

**Full report:** [aikb-claudeos-e2e-semantic-assertion-rule-update-v0.1/report.md](aikb-claudeos-e2e-semantic-assertion-rule-update-v0.1/report.md)

---

## REPORT BACK FORMAT

```
VERDICT: ACCEPT
GATE: AIKB_AND_CLAUDEOS_E2E_SEMANTIC_ASSERTION_RULE_UPDATE_V0.1
AIKB_FILES_READ:
  - 00_CONTROL_CENTER/ACTIVE_PROJECTS.md
  - 00_CONTROL_CENTER/ACCEPTANCE_FIRST_DELIVERY_RULE.md
  - 00_CONTROL_CENTER/ADVERSARIAL_REVIEW_RULE.md
  - 01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md
  - 01_PROJECTS/InsuranceAIPlatform/TASK_LEDGER.md
  - 01_PROJECTS/InsuranceAIPlatform/DECISIONS/ (list)
  - 01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-05-27_azure_ready_microservice_architecture_local_first.md
  - 02_TEMPLATES/TEMPLATE_CLAUDE_PROMPT.md
AIKB_FILES_UPDATED:
  - 00_CONTROL_CENTER/E2E_SEMANTIC_ASSERTION_RULE.md (NEW)
  - 01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-05-28_e2e_semantic_assertion_rule.md (NEW)
  - 01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md (status, snapshot, gate sequence, lesson, verified facts, current/next gate, limitations)
  - 01_PROJECTS/InsuranceAIPlatform/TASK_LEDGER.md (6 new entries: zero-to-end, total-UI, Manual V4 rejection, PostManualV4 fix, Manual V5 acceptance, this maintenance gate)
  - 02_TEMPLATES/TEMPLATE_CLAUDE_PROMPT.md (4-layer evidence section for UI/E2E gates)
  - 00_CONTROL_CENTER/ACTIVE_PROJECTS.md (InsuranceAIPlatform status + per-project subsection)
DECISION_FILE: 01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-05-28_e2e_semantic_assertion_rule.md
TASK_LEDGER_UPDATED: yes — 6 new entries appended chronologically (PostManualZeroToEnd V0.2, TotalUIExploratory V0.1 with overturned caveat, Manual V4 rejection, PostManualV4 fix, Manual V5 acceptance, this maintenance gate)
CURRENT_STATE_UPDATED: yes — status changed BFF_SKELETON_COMMITTED_TO_DEV → MANUAL_V5_ACCEPTED_CREATED_CLAIM_DETAIL_BINDING_FIXED_READY_FOR_COMMIT_GATE; recent-gate sequence + verified facts + false-positive lesson + next-gate sequence added; original architectural baseline preserved as historical section
CLAUDEOS_FILES_READ:
  - C:/Users/DEVELOPER/Claude/GitHub/claude-vault/ (directory listing; HEAD 903c344 with uncommitted vault changes already present)
CLAUDEOS_FILES_UPDATED: none — SKIPPED_PROTECTED
PROTECTED_OR_SKIPPED_FILES:
  - C:/Users/DEVELOPER/CLAUDE.md (home-dir project memory) — SACRED per feedback_foundational_contract.md
  - C:/Users/DEVELOPER/Claude/CLAUDE.md (Vault-root project memory) — SACRED
  - C:/Users/DEVELOPER/Claude/GitHub/claude-vault/CLAUDE.md and root operating rules — SACRED + uncommitted vault state already present
  - InsuranceAIPlatform product source under C:/Projects/InsuranceAIPlatform/ — out of scope; not opened for write
RULE_PERSISTED: E2E Semantic Assertion Rule — four required evidence layers for every UI create/open/update gate: MECHANICAL_PASS (chain fired), SEMANTIC_PASS (visible DOM carries entity business data via testids+strings), PERSISTENCE_PASS (reload/search/re-open still shows the data), NEGATIVE_PASS (stale fixture/fallback data is asserted absent — for InsuranceAIPlatform: CLM-1006, Роберт Джонсон, Toyota Camry markers). A 100% pass rate is rejected without explicit SEMANTIC_PASS + NEGATIVE_PASS assertions. Required report fields: MECHANICAL_PASS/SEMANTIC_PASS/PERSISTENCE_PASS/NEGATIVE_PASS/SEMANTIC_EVIDENCE/NEGATIVE_EVIDENCE/SPEC_FILE_PATH/TEST_CASE_NAME.
PROMPT_TEMPLATE_SNIPPET_ADDED: yes — embedded in 02_TEMPLATES/TEMPLATE_CLAUDE_PROMPT.md EVIDENCE / REPORT REQUIREMENTS section as "Mandatory for UI / E2E create / open / update gates" subsection with the four labelled layers, required report fields, and the copy-paste snippet "Do not count URL / navigation / toast as success. Assert visible business data via testids and asserted strings, and assert stale fixture / fallback data is absent. ..."
PRODUCT_SOURCE_CHANGED: no
SOURCE_COMMIT_ATTEMPTED: no
SOURCE_PUSH_ATTEMPTED: no
AZURE_TOUCHED: no
GITHUB_HANDOFF_PATHS:
  - InsuranceAIPlatform/latest-report.md (gpt-handoff)
  - InsuranceAIPlatform/latest-summary.json (gpt-handoff)
  - InsuranceAIPlatform/aikb-claudeos-e2e-semantic-assertion-rule-update-v0.1/report.md (gpt-handoff)
  - ai-kb 00_CONTROL_CENTER/E2E_SEMANTIC_ASSERTION_RULE.md (durable cross-project rule)
  - ai-kb 01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-05-28_e2e_semantic_assertion_rule.md (project-level decision)
  - ai-kb 01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md (state refresh)
  - ai-kb 01_PROJECTS/InsuranceAIPlatform/TASK_LEDGER.md (6 new entries)
  - ai-kb 02_TEMPLATES/TEMPLATE_CLAUDE_PROMPT.md (4-layer evidence requirement)
  - ai-kb 00_CONTROL_CENTER/ACTIVE_PROJECTS.md (index update)
BLOCKERS: none
LIMITATIONS:
  1) ClaudeOS / claude-vault not mirrored — sacred-file constraint; AIKB is the canonical durable-rules repo for this rule; separate approval gate needed if a vault mirror is wanted.
  2) Prompt-template change applies forward only — past accepted reports are not retroactively re-classified.
  3) The rule is a process rule, not a runtime check — Claude labels, GPT enforces; tooling auto-grep is out of scope.
  4) Project-specific NEGATIVE markers explicit for InsuranceAIPlatform only; other projects inherit the cross-project four-layer requirement and should add their own markers when they introduce UI/E2E gates.
  5) No new product test runs in this gate (89/89, 137/137, FE build PASS are inherited from PostManualV4 fix gate's run on `da51cd3..719a0c3`); this is a knowledge-only gate.
NEXT_SAFE_STEP: COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1 — operator opens explicitly; bounded commit-only gate for the accumulated dev working-tree (4 gates' worth of changes), separate commit and push subphases, explicit file list, no new edits, no Azure, no main-branch touch. Then DOCS_ARCHITECTURE_DURABLE_COMMIT_V0.1, then AZURE_READINESS_PRE_FLIGHT_V0.1 (planning doc only).
```
