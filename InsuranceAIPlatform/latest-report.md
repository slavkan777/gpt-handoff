# InsuranceAIPlatform — Latest gate report

**Gate:** AIKB_AUTO_CONTEXT_SAVER_RULE_UPDATE_V0.1
**Type:** durable AIKB / bootstrap / context-saver rule update
**Date (UTC):** 2026-05-28
**Verdict:** **ACCEPT**

**InsuranceAIPlatform source `dev` HEAD before:** `03725241c8dfdbed7ce17db61fb51d9d7f211116`
**InsuranceAIPlatform source `dev` HEAD after:**  `03725241c8dfdbed7ce17db61fb51d9d7f211116` *(unchanged)*
**InsuranceAIPlatform source `origin/main`:**     `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` *(unchanged)*

**Full report:** [aikb-auto-context-saver-rule-update-v0.1/report.md](aikb-auto-context-saver-rule-update-v0.1/report.md)

---

## REPORT BACK FORMAT

```
VERDICT: ACCEPT
GATE: AIKB_AUTO_CONTEXT_SAVER_RULE_UPDATE_V0.1
FILES_READ:
  - ai-kb 00_CONTROL_CENTER/AUTO_USE_RULE.md
  - ai-kb 00_CONTROL_CENTER/MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md
  - ai-kb 00_CONTROL_CENTER/NEW_CHAT_BOOTSTRAP_PROMPT.md
  - ai-kb 00_CONTROL_CENTER/OPERATING_PROTOCOL.md
  - ai-kb 00_CONTROL_CENTER/CROSS_CHAT_CONTEXT_ROUTER_RULE.md
  - ai-kb 00_CONTROL_CENTER/ACTIVE_PROJECTS.md (directory listing)
  - ai-kb 00_CONTROL_CENTER/ directory listing (9 rule files)
  - ai-kb 01_PROJECTS/InsuranceAIPlatform/CONTEXT_PACK/ directory listing
  - ai-kb 02_TEMPLATES/ directory listing
  - claude-vault root directory listing (recon only; not opened for write)
FILES_UPDATED:
  - ai-kb 00_CONTROL_CENTER/AUTO_CONTEXT_SAVER_RULE.md (NEW canonical durable rule)
  - ai-kb 00_CONTROL_CENTER/AUTO_USE_RULE.md (added "Auto Context Saver checkpoints" subsection)
  - ai-kb 00_CONTROL_CENTER/MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md (reading order extended + LATEST_CHAT_HANDOFF.md priority subsection)
  - ai-kb 00_CONTROL_CENTER/OPERATING_PROTOCOL.md ("After any serious task" extended + new "Auto Context Saver" section)
  - ai-kb 02_TEMPLATES/TEMPLATE_CANVAS_TO_AIKB_CONTEXT_SAVER_GATE.md (NEW reusable Claude prompt template)
  - ai-kb 01_PROJECTS/InsuranceAIPlatform/CONTEXT_PACK/LATEST_CHAT_HANDOFF.md (NEW first canonical project snapshot)
  - ai-kb 01_PROJECTS/InsuranceAIPlatform/TASK_LEDGER.md (2 new entries: prior COMMIT_AND_PUSH REWORK + this maintenance gate)
  - AIKB commit SHA: set at publish time (FF-only)
CANONICAL_RULE_LOCATION: slavkan777/ai-kb/00_CONTROL_CENTER/AUTO_CONTEXT_SAVER_RULE.md (with companion cross-refs in AUTO_USE_RULE.md, MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md, OPERATING_PROTOCOL.md — no duplication)
BOOTSTRAP_UPDATED: yes
CONTEXT_SAVER_UPDATED: yes — first canonical AIKB-side rule authored; prior CONTEXT-SAVER-MODE.txt style files were not in AIKB (likely ChatGPT Project Sources artifacts); AIKB is the canonical durable home going forward
OPERATING_PROTOCOL_UPDATED: yes
TEMPLATE_CREATED_OR_UPDATED: yes — NEW 02_TEMPLATES/TEMPLATE_CANVAS_TO_AIKB_CONTEXT_SAVER_GATE.md
CURRENT_PROJECT_HANDOFF_UPDATED: yes — NEW 01_PROJECTS/InsuranceAIPlatform/CONTEXT_PACK/LATEST_CHAT_HANDOFF.md (first canonical snapshot)
TASK_LEDGER_UPDATED: yes — 2 new entries (COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1 REWORK + AIKB_AUTO_CONTEXT_SAVER_RULE_UPDATE_V0.1 ACCEPT)
PROTECTED_OR_SKIPPED_FILES:
  - C:/Users/DEVELOPER/CLAUDE.md — SACRED per feedback_foundational_contract.md / how-to-work-with-slava.md
  - C:/Users/DEVELOPER/Claude/CLAUDE.md — SACRED (Vault-root project memory)
  - C:/Users/DEVELOPER/Claude/GitHub/claude-vault/ root operating files — SACRED + uncommitted vault state already present; AIKB is the canonical durable-rules repo
  - C:/Projects/InsuranceAIPlatform/ product source — OUT OF SCOPE; not opened for write; HEAD unchanged at 03725241
AUTO_TRIGGERS_PERSISTED:
  1) Slava phrases: создаю новый чат / новый чат / сохрани canvas / context saver / продолжим в новом чате / закрываю чат / закругляемся / на сегодня хватит
  2) After Slava's Manual Acceptance of a gate
  3) After ACCEPT of a major gate
  4) After a durable rule/decision/template change
  5) Before commit/push gate
  6) Before Azure pre-flight / Azure resource gate
  7) When conversation is long, noisy, or unstable (context window crossing 60% or many multi-step turns)
  8) After a significant false-positive / incident lesson
  9) Before ending a long work session
NEW_CHAT_RESTORE_RULE_PERSISTED:
  - reading order in MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md extended to include AUTO_CONTEXT_SAVER_RULE.md (pos 5) and project CONTEXT_PACK/LATEST_CHAT_HANDOFF.md (pos 10)
  - reconciliation rule: real code > runtime evidence > git state > gpt-handoff > AIKB > chat
  - if LATEST_CHAT_HANDOFF.md contradicts execution evidence, execution evidence wins; flag contradiction and propose AIKB_REFRESH_* gate
  - first operational response must lead with CURRENT STATE / CURRENT GATE / BOUNDARIES / NEXT SAFE STEP / FACT / RISK / DECISION before opening any operational gate
PRODUCT_SOURCE_CHANGED: no
SOURCE_COMMIT_ATTEMPTED: no
SOURCE_PUSH_ATTEMPTED: no
AZURE_TOUCHED: no
GITHUB_HANDOFF_PATHS:
  - slavkan777/gpt-handoff: InsuranceAIPlatform/latest-report.md
  - slavkan777/gpt-handoff: InsuranceAIPlatform/latest-summary.json
  - slavkan777/gpt-handoff: InsuranceAIPlatform/aikb-auto-context-saver-rule-update-v0.1/report.md
  - slavkan777/ai-kb: 00_CONTROL_CENTER/AUTO_CONTEXT_SAVER_RULE.md (NEW canonical rule)
  - slavkan777/ai-kb: 00_CONTROL_CENTER/AUTO_USE_RULE.md (cross-ref)
  - slavkan777/ai-kb: 00_CONTROL_CENTER/MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md (reading order)
  - slavkan777/ai-kb: 00_CONTROL_CENTER/OPERATING_PROTOCOL.md (Auto Context Saver section)
  - slavkan777/ai-kb: 02_TEMPLATES/TEMPLATE_CANVAS_TO_AIKB_CONTEXT_SAVER_GATE.md (NEW template)
  - slavkan777/ai-kb: 01_PROJECTS/InsuranceAIPlatform/CONTEXT_PACK/LATEST_CHAT_HANDOFF.md (NEW first canonical snapshot)
  - slavkan777/ai-kb: 01_PROJECTS/InsuranceAIPlatform/TASK_LEDGER.md (2 new entries)
BLOCKERS: none for this maintenance gate. Pre-existing open blocker carried over from COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1 (e2e/06-claim-actions.spec.ts:54 AI decision recording reproducible failure) is documented in TASK_LEDGER + LATEST_CHAT_HANDOFF.md; operator must open AI_DECISION_RECORDING_E2E_FLAKE_INVESTIGATION_V0.1 to address it.
LIMITATIONS:
  1) ClaudeOS / claude-vault NOT mirrored — sacred-file constraint; AIKB is the canonical durable-rules repo for this rule; separate approval gate needed if a vault mirror is wanted.
  2) Prior CONTEXT-SAVER-MODE.txt / CONTEXT-SAVER-CANVAS-FIRST-MODE.txt files were not found in AIKB — likely ChatGPT Project Sources artifacts; AIKB is now the canonical durable home; operator may replace ChatGPT Sources copies with a one-line reference to remove duplication.
  3) Rule is process-only — GPT/Claude initiate the save; auto-tooling for trigger-phrase detection is out of scope.
  4) Only InsuranceAIPlatform got a LATEST_CHAT_HANDOFF.md in this gate; other AIKB projects (AgentHub, BusinessLab, CareerProfile) will adopt the convention at their next lifecycle checkpoint.
  5) Pre-existing AI-decision-recording E2E flake is documented but not fixed — separate investigation gate.
NEXT_SAFE_STEP: AI_DECISION_RECORDING_E2E_FLAKE_INVESTIGATION_V0.1 — bounded investigation gate. (1) reset LocalDB to seeded state and re-run e2e/06-claim-actions.spec.ts alone; (2) if still fails on fresh DB → diagnose AiDecisionController / recordAiDecision saga / EF Core insert path; (3) if passes on fresh DB → categorize as test brittleness to accumulated state; harden test or document as known limitation. No commit/push of fixes in the investigation gate. After 89/89 PASS is restored, re-open COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1.
```
