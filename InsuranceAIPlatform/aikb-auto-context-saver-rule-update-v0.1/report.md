# Gate Report — AIKB_AUTO_CONTEXT_SAVER_RULE_UPDATE_V0.1

**Project:** AI Engineering Laboratory / AIKB (durable rule update); InsuranceAIPlatform receives the first canonical `LATEST_CHAT_HANDOFF.md` snapshot.
**Date (UTC):** 2026-05-28
**Type:** durable AIKB/bootstrap/context-saver rule update
**InsuranceAIPlatform source `dev` HEAD:** `03725241c8dfdbed7ce17db61fb51d9d7f211116` — **unchanged**
**InsuranceAIPlatform source `origin/main`:** `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` — **unchanged**
**AIKB repo HEAD before:** `3843259c…` (from the prior maintenance gate)
**AIKB repo HEAD after:** *(set by commit, see below)*

---

## Verdict

**ACCEPT** — Auto Context Saver Rule persisted as a durable cross-project AIKB rule at `00_CONTROL_CENTER/AUTO_CONTEXT_SAVER_RULE.md`. Cross-refs added into the existing operating fabric (`AUTO_USE_RULE.md`, `MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md`, `OPERATING_PROTOCOL.md`) so the rule fires from the right surfaces. A reusable Claude prompt template was created at `02_TEMPLATES/TEMPLATE_CANVAS_TO_AIKB_CONTEXT_SAVER_GATE.md`. The first canonical project snapshot under the new convention was written at `01_PROJECTS/InsuranceAIPlatform/CONTEXT_PACK/LATEST_CHAT_HANDOFF.md` and reflects the current `MANUAL_V5_ACCEPTED` + open commit/push REWORK state. claude-vault was correctly identified as protected/sacred and SKIPPED. No InsuranceAIPlatform product source touched.

---

## Files read

### AIKB
- `00_CONTROL_CENTER/AUTO_USE_RULE.md`
- `00_CONTROL_CENTER/MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md`
- `00_CONTROL_CENTER/NEW_CHAT_BOOTSTRAP_PROMPT.md`
- `00_CONTROL_CENTER/OPERATING_PROTOCOL.md`
- `00_CONTROL_CENTER/CROSS_CHAT_CONTEXT_ROUTER_RULE.md`
- `00_CONTROL_CENTER/ACTIVE_PROJECTS.md` (directory listing for layout reconnaissance)
- `00_CONTROL_CENTER/` directory listing (9 rule files discovered)
- `01_PROJECTS/InsuranceAIPlatform/CONTEXT_PACK/` directory listing (existing `HANDOFF_2026-05-26_AFTER_SYSTEM_SKELETON.md` + `NEW_CHAT_CONTEXT.md`)
- `02_TEMPLATES/` directory listing

### claude-vault (ClaudeOS) recon
- Confirmed repo presence; HEAD `903c344e…` with uncommitted vault state already present. No safe editable rule-target file identified for this gate's scope — sacred-file constraint applies. Reported under SKIPPED below.

### InsuranceAIPlatform product source
- Read-only `git rev-parse HEAD` to confirm source state. No source files opened or modified.

---

## Files updated (AIKB)

| Path | Kind | Purpose |
|---|---|---|
| `00_CONTROL_CENTER/AUTO_CONTEXT_SAVER_RULE.md` | NEW | Canonical durable cross-project rule. 9 auto-trigger checkpoints. Snapshot content schema (12 mandatory sections). Target paths (LATEST_CHAT_HANDOFF.md + dated archives). Safety boundaries (no product code / no commit/push / no Azure / no sacred file edits / no secrets). New-chat restore behavior. Mandatory Claude prompt snippet. GPT auto-initiation script. Cross-rule relationships. Change log. |
| `00_CONTROL_CENTER/AUTO_USE_RULE.md` | UPDATED | Added "Auto Context Saver checkpoints (proactive writes)" subsection inside "When GPT should update AIKB automatically" — short list of triggers + cross-ref into the canonical rule + explicit "GPT must NOT wait for Slava to remember the save". |
| `00_CONTROL_CENTER/MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md` | UPDATED | Reading order extended: `AUTO_CONTEXT_SAVER_RULE.md` slotted at position 5 (between AUTO_USE_RULE and IDEA_TO_ACTION_PROTOCOL); project `CONTEXT_PACK/LATEST_CHAT_HANDOFF.md` slotted at position 10 (before CURRENT_STATE.md). New "LATEST_CHAT_HANDOFF.md priority" subsection explaining read order + reconciliation when context pack contradicts execution evidence. |
| `00_CONTROL_CENTER/OPERATING_PROTOCOL.md` | UPDATED | "After any serious task" checklist extended with two new bullets for `CONTEXT_PACK/LATEST_CHAT_HANDOFF.md` and dated `HANDOFF_<YYYY-MM-DD>_*.md`. New "Auto Context Saver" section pointing at the canonical rule and the template, with explicit safety re-cap. |
| `02_TEMPLATES/TEMPLATE_CANVAS_TO_AIKB_CONTEXT_SAVER_GATE.md` | NEW | Reusable Claude prompt template. GOAL / DONE STATE / INPUTS / TARGET AIKB PATHS / SNAPSHOT CONTENT SCHEMA / SAFETY BOUNDARIES / REPORT BACK FORMAT / STOP LINE sections. References the canonical rule. |
| `01_PROJECTS/InsuranceAIPlatform/CONTEXT_PACK/LATEST_CHAT_HANDOFF.md` | NEW | First canonical snapshot under the new convention. Captures: status code (`MANUAL_V5_ACCEPTED_CREATED_CLAIM_DETAIL_BINDING_FIXED_BLOCKED_ON_AI_DECISION_E2E_FLAKE`), source repo state (HEAD `03725241` unchanged), last accepted gates chain (6 entries), currently active gate + open blocker, boundaries/forbidden, verified facts (89/89 → 88/89 on AI decision flake, 137/137 xunit, FE build PASS), durable rules in force, latest gpt-handoff paths, next-safe-step (AI decision E2E flake investigation), stop lines, known limitations, new-chat restore instructions. |
| `01_PROJECTS/InsuranceAIPlatform/TASK_LEDGER.md` | UPDATED | Two new entries appended: (1) `COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1` REWORK_REQUIRED with the AI-decision-flow failure detail and unchanged HEAD; (2) this maintenance gate `AIKB_AUTO_CONTEXT_SAVER_RULE_UPDATE_V0.1`. |

Total: **3 new files, 4 updated files** in AIKB.

---

## Protected / skipped files

| Path | Reason |
|---|---|
| `C:/Users/DEVELOPER/CLAUDE.md` (home-dir project memory) | SACRED per `feedback_foundational_contract.md` / `how-to-work-with-slava.md` |
| `C:/Users/DEVELOPER/Claude/CLAUDE.md` (Vault-root project memory) | SACRED |
| `C:/Users/DEVELOPER/Claude/GitHub/claude-vault/` root operating files | SACRED + uncommitted vault state already present; AIKB is the canonical durable-rules repo |
| InsuranceAIPlatform product source under `C:/Projects/InsuranceAIPlatform/` | OUT OF SCOPE; not opened for write; HEAD unchanged at `03725241` |

Suggested follow-up if the operator wants the rule mirrored into ClaudeOS: open a separate small approval gate `CLAUDEOS_AUTO_CONTEXT_SAVER_RULE_REFERENCE_V0.1`.

---

## Canonical rule location

`slavkan777/ai-kb/00_CONTROL_CENTER/AUTO_CONTEXT_SAVER_RULE.md`

Companion references (cross-link, no duplication):
- `slavkan777/ai-kb/00_CONTROL_CENTER/AUTO_USE_RULE.md` (the proactive-write side)
- `slavkan777/ai-kb/00_CONTROL_CENTER/MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md` (the new-chat restore side)
- `slavkan777/ai-kb/00_CONTROL_CENTER/OPERATING_PROTOCOL.md` ("After any serious task" + "Auto Context Saver" section)

---

## Auto-triggers persisted (canonical list)

A context-save is auto-initiated when any of these fires, without waiting for Slava to ask:

1. Slava types or paraphrases: `создаю новый чат`, `новый чат`, `сохрани canvas`, `context saver`, `продолжим в новом чате`, `закрываю чат`, `закругляемся`, `на сегодня хватит`.
2. After Slava's **Manual Acceptance** of a real gate (e.g. `MANUAL_OPERATOR_BROWSER_CLICK_THROUGH_VN_ACCEPTED`).
3. After **ACCEPT** of a major gate that durably changed state.
4. After a durable **rule / decision / template** change has been persisted.
5. **Before** any commit/push gate is opened.
6. **Before** an Azure pre-flight / Azure resource gate is opened.
7. When the conversation becomes long, noisy, or unstable (context window crossing 60%, or after many multi-step turns).
8. After a significant false-positive or incident lesson is captured.
9. Before ending a long work session.

When multiple triggers fire together, a single combined snapshot is sufficient.

---

## New chat restore behavior (canonical)

When a new chat begins and Slava says `изучи источники`, `продолжаем <PROJECT>`, `новый чат по <PROJECT>`, or equivalent:

1. Read source bootstrap (`MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md` if accessible).
2. Read `00_CONTROL_CENTER/START_HERE.md`.
3. Read `00_CONTROL_CENTER/ACTIVE_PROJECTS.md` (route to right project).
4. Read `00_CONTROL_CENTER/OPERATING_PROTOCOL.md`.
5. Read `00_CONTROL_CENTER/AUTO_USE_RULE.md`.
6. Read `00_CONTROL_CENTER/AUTO_CONTEXT_SAVER_RULE.md`.
7. Read **target project** `CONTEXT_PACK/LATEST_CHAT_HANDOFF.md`.
8. Read **target project** `CURRENT_STATE.md`.
9. Read **target project** `TASK_LEDGER.md`.
10. Read `slavkan777/gpt-handoff/<project>/latest-report.md` if execution-adjacent.
11. Reconcile contradictions: real code > runtime evidence > git state > gpt-handoff > AIKB > chat.
12. **Do NOT open new gates or run Claude prompts until state is restored.**

First operational response leads with `CURRENT STATE / CURRENT GATE / BOUNDARIES / NEXT SAFE STEP / FACT / RISK / DECISION`.

---

## Context pack paths (target convention)

```
01_PROJECTS/<PROJECT>/CONTEXT_PACK/LATEST_CHAT_HANDOFF.md   ← overwritable
01_PROJECTS/<PROJECT>/CONTEXT_PACK/HANDOFF_<YYYY-MM-DD>_<SHORT_REASON>.md   ← append-only archive
```

`LATEST_CHAT_HANDOFF.md` is the durable carry-over read by every new chat. Dated archives are kept for traceability when a milestone is worth its own record (e.g. `HANDOFF_2026-05-28_AFTER_MANUAL_V5_ACCEPTED.md`).

---

## Confirmations

| | |
|---|---|
| Bootstrap updated | **yes** — `MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md` reading order extended (added `AUTO_CONTEXT_SAVER_RULE.md` at position 5 + project `CONTEXT_PACK/LATEST_CHAT_HANDOFF.md` at position 10; new "LATEST_CHAT_HANDOFF.md priority" subsection) |
| Context saver updated | **yes** — first canonical AIKB-side context-saver rule authored at `00_CONTROL_CENTER/AUTO_CONTEXT_SAVER_RULE.md` (the prior `CONTEXT-SAVER-MODE.txt` style files were not found in AIKB — likely ChatGPT Project Sources artifacts; AIKB is the canonical durable home going forward) |
| Operating protocol updated | **yes** — new "Auto Context Saver" section in `OPERATING_PROTOCOL.md` + two new bullets in "After any serious task" |
| Template created or updated | **yes** — NEW `02_TEMPLATES/TEMPLATE_CANVAS_TO_AIKB_CONTEXT_SAVER_GATE.md` |
| Current project handoff updated | **yes** — NEW `01_PROJECTS/InsuranceAIPlatform/CONTEXT_PACK/LATEST_CHAT_HANDOFF.md` (first canonical snapshot under the new convention) |
| Task ledger updated | **yes** — two new entries (`COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1` REWORK_REQUIRED + this maintenance gate) |
| Product source changed | **no** — `git rev-parse HEAD` = `03725241` unchanged |
| Source commit attempted | **no** |
| Source push attempted | **no** |
| Azure touched | **no** |
| Sacred CLAUDE.md edited | **no** |
| Secrets read / logged / pasted | **no** — DEEPSEEK_API_KEY value, sk-*, password connection strings: none touched |

---

## Blockers

**None for this maintenance gate.** The pre-existing blocker carried over from `COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1` (the AI decision recording E2E flake in `e2e/06-claim-actions.spec.ts:54`) remains open and is now recorded both in the TASK_LEDGER and in the new project handoff. Operator must open a separate investigation gate.

---

## Limitations

1. **ClaudeOS / claude-vault NOT mirrored** — sacred-file constraint; AIKB is the canonical durable-rules repo for this rule. If the operator wants the rule mirrored into claude-vault, open a separate small approval gate.
2. **The prior `CONTEXT-SAVER-MODE.txt` / `CONTEXT-SAVER-CANVAS-FIRST-MODE.txt` files were not located in AIKB** — they appear to be ChatGPT Project Sources artifacts. AIKB is now the canonical durable home for this rule; if those text files still exist in ChatGPT Project Sources, the operator can replace them with a single line "See `slavkan777/ai-kb/00_CONTROL_CENTER/AUTO_CONTEXT_SAVER_RULE.md`" to remove duplication.
3. **Rule is process-only** — GPT/Claude initiate the save; tooling auto-grep for trigger phrases is out of scope.
4. **The first project snapshot (`InsuranceAIPlatform/LATEST_CHAT_HANDOFF.md`) is the only project-level write in this gate** — other AIKB projects (AgentHub, BusinessLab, CareerProfile) will pick up the convention as they encounter their next lifecycle checkpoint.
5. **Pre-existing open blocker (AI decision recording E2E flake) is documented but not fixed** — this maintenance gate is documentation-only; investigation is a separate gate.

---

## Next safe step

`AI_DECISION_RECORDING_E2E_FLAKE_INVESTIGATION_V0.1` — bounded investigation gate to disambiguate the `e2e/06-claim-actions.spec.ts:54` failure: (1) reset LocalDB to a known seeded state and re-run the spec alone; (2) if still fails on fresh DB → diagnose `AiDecisionController` / `recordAiDecision` saga / EF Core insert path for slowness or hang; (3) if passes on fresh DB → categorize as test brittleness to accumulated DB state and harden test OR document as known limitation. No commit/push of fixes in the investigation gate. After fix gate is accepted and 89/89 PASS is restored on this exact working tree, re-open `COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1`.

This gate is now complete. STOP LINE applies — do not start commit/push, do not start Azure, do not modify product source.
