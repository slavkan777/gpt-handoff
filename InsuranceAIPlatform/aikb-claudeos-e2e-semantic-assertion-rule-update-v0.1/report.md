# Gate Report — AIKB_AND_CLAUDEOS_E2E_SEMANTIC_ASSERTION_RULE_UPDATE_V0.1

**Project:** InsuranceAIPlatform (durable knowledge maintenance, no product source touched)
**Date (UTC):** 2026-05-28
**Type:** durable knowledge / rules / state maintenance
**InsuranceAIPlatform source repo `dev` HEAD:** `03725241c8dfdbed7ce17db61fb51d9d7f211116` — **unchanged**
**InsuranceAIPlatform source repo `main`:** `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` — **unchanged**
**AIKB repo `main` HEAD before:** `19de40e8b73431bce40a326267c204e27af065af`
**AIKB repo `main` HEAD after:** *(set by commit, see below)*

---

## Verdict

**ACCEPT** — durable E2E Semantic Assertion Rule is persisted in AIKB at both the cross-project and project-specific layers; the prompt template is updated; CURRENT_STATE and TASK_LEDGER reflect Manual V5 acceptance and the recent gate sequence; ACTIVE_PROJECTS index is current. claude-vault (ClaudeOS) was correctly identified as protected/sacred and SKIPPED with explanation. No InsuranceAIPlatform product source touched; no source commit/push; no Azure work.

---

## Files read

### AIKB
- `00_CONTROL_CENTER/ACTIVE_PROJECTS.md` (read full + diff target)
- `00_CONTROL_CENTER/ACCEPTANCE_FIRST_DELIVERY_RULE.md` (read for rule-file convention)
- `00_CONTROL_CENTER/ADVERSARIAL_REVIEW_RULE.md` (read for rule-file convention)
- `01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md` (read full)
- `01_PROJECTS/InsuranceAIPlatform/TASK_LEDGER.md` (read full)
- `01_PROJECTS/InsuranceAIPlatform/DECISIONS/` (listed; format spotted from `DECISION_2026-05-27_*`)
- `01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-05-27_azure_ready_microservice_architecture_local_first.md` (read header for format)
- `02_TEMPLATES/TEMPLATE_CLAUDE_PROMPT.md` (read full)

### claude-vault (ClaudeOS) recon
- Verified repo present at `C:/Users/DEVELOPER/Claude/GitHub/claude-vault/` with HEAD `903c344e…` and uncommitted vault changes already in working tree. No safe editable rule-target file identified there for this gate's scope — sacred-file constraint applies. Reported under SKIPPED below.

### InsuranceAIPlatform source repo
- Read-only `git rev-parse HEAD` / `git rev-parse origin/main` to confirm source state. No source files opened or modified.

---

## Files updated (AIKB)

| Path | Kind | Purpose |
|---|---|---|
| `00_CONTROL_CENTER/E2E_SEMANTIC_ASSERTION_RULE.md` | NEW | Durable cross-project rule. Four required evidence layers (MECHANICAL / SEMANTIC / PERSISTENCE / NEGATIVE). Mandatory test-file shape. Required report fields. Prompt template snippet. GPT audit rule. Claude rule. Worked InsuranceAIPlatform example. Related-rules index. Change log. |
| `01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-05-28_e2e_semantic_assertion_rule.md` | NEW | Project-level adoption. Project-specific forbidden fixture markers (`CLM-1006`, `Роберт Джонсон`, `Toyota Camry`, seed claim ids `CLM-1006..CLM-1010`, golden customerId / vehicleVin / policyId / riskScore). Canonical reference test (`e2e/21-created-claim-detail-binding.spec.ts`). Required report fields. GPT audit rule and Claude rule for this project. |
| `01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md` | UPDATED | Status changed to `MANUAL_V5_ACCEPTED_CREATED_CLAIM_DETAIL_BINDING_FIXED_READY_FOR_COMMIT_GATE`. Added status snapshot (89/89 / 137/137 / FE build PASS / source HEAD unchanged / handoff `719a0c3`). Added recent gate sequence (last 6 gates). Added false-positive lesson section. Added verified facts (post-V5). Replaced Current gate / Next safe step with `COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1` sequence. Added Known limitations section. |
| `01_PROJECTS/InsuranceAIPlatform/TASK_LEDGER.md` | UPDATED | Appended 6 new entries: zero-to-end UI rework gate, total UI exploratory audit (with overturned-by-V4 caveat), Manual V4 rejection, PostManualV4 fix, Manual V5 acceptance, this maintenance gate. |
| `02_TEMPLATES/TEMPLATE_CLAUDE_PROMPT.md` | UPDATED | Added `Mandatory for UI / E2E create / open / update gates` subsection inside EVIDENCE / REPORT REQUIREMENTS. Four labelled layers, required report fields, prompt-template snippet to paste, and the explicit rejection rule ("100% pass without SEMANTIC_PASS + NEGATIVE_PASS is rejected"). |
| `00_CONTROL_CENTER/ACTIVE_PROJECTS.md` | UPDATED | InsuranceAIPlatform row updated to new status + current-gate / next-safe-step; the per-project subsection rewritten to reflect verified facts (89/89, 137/137, fix in place), active rule, next-safe-step (`COMMIT_AND_PUSH_DEV_*`), updated forbidden list. |

Total: **2 new files, 4 updated files** in AIKB.

---

## Protected / skipped files

| Path | Reason |
|---|---|
| `C:/Users/DEVELOPER/Claude/GitHub/claude-vault/CLAUDE.md` (and the vault's own root CLAUDE.md if any) | SACRED per memory rules `feedback_foundational_contract.md` / `how-to-work-with-slava.md`. Touching CLAUDE.md root or `working-relationship.md` / `how-to-work-with-slava.md` requires explicit operator approval. Not in this gate's scope. |
| `C:/Users/DEVELOPER/CLAUDE.md` (home-dir project memory) | Same sacred-file constraint. |
| `C:/Users/DEVELOPER/Claude/CLAUDE.md` (Vault-root project memory) | Same sacred-file constraint. |
| `claude-vault` ClaudeOS repo as a whole | Uncommitted vault changes already present; no clearly-safe editable rule-target file identified for this maintenance gate's scope. AIKB is the canonical durable-rules repo (per `ACTIVE_PROJECTS.md`: "ClaudeOS — Updates land in slavkan777/claude-vault, not here" runs in the opposite direction for *workflow* rules; this gate's E2E rule is a project-quality rule and belongs in AIKB by convention). If a future gate wants the same snippet mirrored into ClaudeOS, that's a separate approval. |
| InsuranceAIPlatform product source code (any file under `C:/Projects/InsuranceAIPlatform/`) | Out of this gate's scope. No edits, no commit, no push. Verified HEAD unchanged at `03725241`. |

Suggested follow-up if the operator wants the rule also surfaced in ClaudeOS: open a separate small gate `CLAUDEOS_E2E_SEMANTIC_ASSERTION_RULE_REFERENCE_V0.1` with an explicit writable file target. Until then, AIKB is the source of truth and prompts inherit the rule through `02_TEMPLATES/TEMPLATE_CLAUDE_PROMPT.md`.

---

## Exact rule persisted

**E2E Semantic Assertion Rule** (`00_CONTROL_CENTER/E2E_SEMANTIC_ASSERTION_RULE.md`):

For UI / E2E verification, URL / navigation / toast / click events are NOT sufficient as proof of business correctness. Every UI create / open / update flow must produce four labelled evidence layers — MECHANICAL_PASS (the chain fired), SEMANTIC_PASS (visible DOM carries the entity's actual business data via testids + asserted strings), PERSISTENCE_PASS (reload / search / re-open still shows the data), NEGATIVE_PASS (stale fixture / fallback / golden-demo data is asserted absent). A gate may only be ACCEPT when all four pass, or when a non-applicable layer is explicitly justified. Required report fields are MECHANICAL_PASS / SEMANTIC_PASS / PERSISTENCE_PASS / NEGATIVE_PASS / SEMANTIC_EVIDENCE / NEGATIVE_EVIDENCE / SPEC_FILE_PATH / TEST_CASE_NAME. Project-specific NEGATIVE markers for InsuranceAIPlatform: `CLM-1006`, `Роберт Джонсон`, `Toyota Camry`. GPT audit rule rejects reports that report only URL-level / toast-level success, omit the four labelled layers, or declare ACCEPT without an explicit NEGATIVE_PASS that names the absent fixture markers. Claude rule: do not self-report ACCEPT on a UI create / open / update gate without DOM-level positive AND negative assertions.

---

## AIKB CURRENT_STATE update summary

- Status changed from `BFF_SKELETON_COMMITTED_TO_DEV` (stale, 2026-05-27) to `MANUAL_V5_ACCEPTED_CREATED_CLAIM_DETAIL_BINDING_FIXED_READY_FOR_COMMIT_GATE` (2026-05-28).
- Added explicit status snapshot: source HEAD `03725241` unchanged, origin/main `69e67312` unchanged, gpt-handoff `719a0c3`, Playwright 89/89, xunit 137/137, frontend build PASS, fixture leak gone.
- Added "Recent gate sequence" section (last 6 gates, most recent first).
- Added "False-positive lesson" section linking the rule and decision documents.
- Added "Verified facts (post-V5)" enumerating the concrete observables.
- Current gate / Next safe step / Known limitations rewritten — points to `COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1` as the next operator-opened gate.
- "Original architectural baseline (still authoritative)" header inserted above the prior 7-services / decision-record / progress sections that remain unchanged (BFF Stage 1 narrative is preserved as historical baseline).

## AIKB TASK_LEDGER update summary

Six new entries appended in chronological order:

1. `POST_MANUAL_ZERO_TO_END_UI_FLOW_REWORK_WITH_E2E_TESTS_V0.2` — Playwright baseline + 5 bug fixes + 32/32.
2. `TOTAL_UI_EXPLORATORY_E2E_MATRIX_AND_ACTION_INVENTORY_V0.1` — 14-route / ~91-control / 12-scenario audit; marked `ACCEPTED_BUT_LATER_OVERTURNED_BY_MANUAL_V4`.
3. `MANUAL_OPERATOR_BROWSER_CLICK_THROUGH_V4` — Slava's manual repro of the false-positive; marked `REJECTED_FAILED_VERIFICATION`.
4. `POST_MANUAL_V4_CREATED_CLAIM_DETAIL_BINDING_FIX_V0.1` — root cause + smallest fix + new layered regression spec; 89/89 / 137/137 / FE build PASS.
5. `MANUAL_OPERATOR_BROWSER_CLICK_THROUGH_V5_ACCEPTED` — Slava's manual V5 retest (CLM-1041) accepted.
6. `AIKB_AND_CLAUDEOS_E2E_SEMANTIC_ASSERTION_RULE_UPDATE_V0.1` — this maintenance gate.

Each entry follows the existing TASK_LEDGER convention: Status / Result / Evidence (paths) / Next gate.

## ClaudeOS / template update summary

- AIKB `02_TEMPLATES/TEMPLATE_CLAUDE_PROMPT.md` updated to embed the four-layer requirement into every future Claude UI/E2E prompt. This propagates the rule to all projects via the template.
- ClaudeOS / claude-vault NOT modified — sacred CLAUDE.md root + uncommitted vault state. Documented under SKIPPED above.

---

## No product source change confirmation

InsuranceAIPlatform source repo:

```
HEAD before:  03725241c8dfdbed7ce17db61fb51d9d7f211116
HEAD after:   03725241c8dfdbed7ce17db61fb51d9d7f211116    (unchanged)
origin/main:  69e67312a10cc9bcf28c4e387a126b48c91fb9c5    (unchanged)
```

No file under `C:/Projects/InsuranceAIPlatform/` was opened for write during this gate. No `dotnet build` / `npm run build` / `npm run test:e2e` was run as part of this gate (covered already by PostManualV4 ACCEPT; this gate only persists knowledge).

## No commit / push / Azure confirmation

| Action | Status |
|---|---|
| InsuranceAIPlatform source `git commit` attempted | NO |
| InsuranceAIPlatform source `git push` attempted | NO |
| `origin/main` mutated (any repo) | NO (gpt-handoff `main` will receive a docs-only commit at publish time — that's the handoff repo, not InsuranceAIPlatform source) |
| Azure resources created / modified | NO |
| AI provider / DeepSeek key touched | NO (`DEEPSEEK_API_KEY` not read, not printed, not logged) |
| Sacred CLAUDE.md edited | NO |
| `feedback_foundational_contract.md` / `how-to-work-with-slava.md` / `working-relationship.md` touched | NO |

---

## Blockers

**None.**

---

## Limitations

1. **ClaudeOS / claude-vault not mirrored**. The rule and prompt snippet live in AIKB only. claude-vault was deliberately skipped under the sacred-file constraint. If the operator wants the rule also embedded in ClaudeOS prompts/playbook, open a separate small approval gate.
2. **Existing prompt template applies forward only**. Reports that have already been audited and accepted are not retroactively re-classified. The lesson is the new rule, not a re-audit of past reports.
3. **The rule is a process rule, not a runtime check**. It requires Claude to label evidence layers in reports and GPT to enforce in audit. Future tooling could auto-grep test files for the four layer markers and surface missing layers — out of scope here.
4. **Project-specific NEGATIVE markers are explicit only for InsuranceAIPlatform** in this gate. Other projects (AgentHub, BusinessLab, CareerProfile) inherit the cross-project four-layer requirement from the control-center rule, but their specific forbidden fixture markers should be added to their own DECISIONS when they introduce UI/E2E gates.
5. **No new test runs in this gate**. Playwright / xunit / frontend build numbers cited (89/89, 137/137, PASS) are inherited from the PostManualV4 fix gate's run captured in `da51cd3..719a0c3` on gpt-handoff. This is a knowledge-only gate.

---

## Next safe step

Operator opens `COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1` explicitly when ready — bounded commit-only gate for the accumulated `dev` working-tree changes from the last 4 gates (realistic DB sandbox, customers create + claims create, AI advisory layer, payout simulation, total UI E2E expansion, PostManualV4 fix, layered regression spec). Separate commit and push subphases. Explicit file list. No new edits. No Azure. No main-branch touch.

After that:
- `DOCS_ARCHITECTURE_DURABLE_COMMIT_V0.1` — commit architecture docs + inventories.
- `AZURE_READINESS_PRE_FLIGHT_V0.1` — planning doc only; no Azure resources.

The E2E Semantic Assertion Rule applies to every UI / E2E gate from now on, in every project, across every Claude prompt that uses `02_TEMPLATES/TEMPLATE_CLAUDE_PROMPT.md` as its baseline.
