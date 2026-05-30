# Apply CLAUDE.md Evidence-Gate Patch (Patch B) — V0.1

CURRENT STATE: Attempted the single authorized sacred-file edit; the sacred-file guard blocked it; I did NOT bypass. CLAUDE.md is unchanged. Only this gpt-handoff report was written.
CURRENT GATE: APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.1
BOUNDARIES: CODE CHANGE limited to Patch B on `C:\Users\DEVELOPER\Claude\CLAUDE.md` only. No Patch A/C, no protocol/pending, no AIKB, no product, no Azure/provider, no vault-main commit, no hook bypass.
NEXT SAFE STEP: Operator decides how to lift protection (see §7) → a follow-up gate re-runs this exact patch.

**VERDICT: PROPOSED_ONLY_BLOCKED_BY_PROTECTION** — the patch is exact and ready; the `scope-guard.ps1` sacred-file hook blocked the write, and the gate forbids bypassing it, so nothing was changed. **This is a success of the protection layer, not a failure of the gate.**

---

## 1. What happened (FACT)

1. Read the live `C:\Users\DEVELOPER\Claude\CLAUDE.md`; confirmed the `## Session protocol` block and the `> **Report must include:** ...` anchor at **line 57**. Confirmed the three Patch-B lines are **not** already present (`grep -c` = 0).
2. Issued exactly the additive Patch-B `Edit` (3 rule lines after line 57, before the existing `Memory, hooks...` line). **No other content touched.**
3. A **PreToolUse hook blocked it**:
   ```
   PreToolUse:Edit hook error: [scope-guard.ps1]:
   [scope-guard] BLOCKED: C:\Users\DEVELOPER\Claude\CLAUDE.md is SACRED (matches C:\Users\DEVELOPER\Claude\CLAUDE.md)
   [scope-guard] Bypass: $env:CLAUDE_SACRED_BYPASS=1 (use sparingly)
   ```
4. The hook offered a bypass env var. **I did NOT use it** — the gate's FORBIDDEN list says "Do not use hook bypass," and the live CLAUDE.md hard gates say "no hook bypass… unless explicitly authorized by the current prompt." This prompt authorized the *patch content*, but **explicitly forbade hook bypass**. So the correct action is STOP + report, per SUBPHASE 2 and DONE-STATE #13.

## 2. Target + anchor

| Field | Value |
|---|---|
| Target file | `C:\Users\DEVELOPER\Claude\CLAUDE.md` (sacred; explicitly authorized for Patch B only) |
| Anchor found | YES |
| Anchor line | 57 — `> **Report must include:** \`AGENT TRACE\` / ... / \`NOT TOUCHED\`.` |
| Patch already present | NO (grep count 0) |
| Insertion point | immediately after line 57 (before the existing `> Memory, hooks...` line 59) |

## 3. Exact patch (READY, not applied)

```diff
  > **Report must include:** `AGENT TRACE` / ... / `NOT TOUCHED`.
  >
+ > **No `DONE`/`PASSED`/`CLEARED`** without a per-item evidence tuple (`path:line` / `command + exit` / `N/M`); else status = `IN PROGRESS`.
+ > **UI create/open/update:** report `MECHANICAL_PASS` / `SEMANTIC_PASS` / `PERSISTENCE_PASS (reload)` / `NEGATIVE_PASS`.
+ > **Provider/DB/cloud calls:** label `MOCK` / `FALLBACK` / `LOCAL_REAL` / `LIVE_REAL` / `NOT_TESTED`.
  >
  > Memory, hooks, settings, slash commands, and additional enforcement layers remain deferred. Do not add them without separate GPT + Operator approval.
```
The `old_string`/`new_string` were prepared exactly so the `Edit` would have been a pure 3-line addition (the tool does exact string replacement — no reformatting, no other section). Only the canonical no-trailing-space `>` separator from the SOURCE-OF-PATCH diff was used (no wording change).

## 4. Evidence the file is UNCHANGED

| Check | Result |
|---|---|
| Patch lines in CLAUDE.md | `grep -c` = **0** (no write occurred) |
| CLAUDE.md git status | ` M CLAUDE.md` — **pre-existing** (present at session start, NOT mine) |
| CLAUDE.md diffstat vs HEAD | `108 insertions(+), 28 deletions(-)` — **identical before and after** my blocked attempt → I added nothing |
| Vault HEAD | `a79f119` (unchanged; no commit) |

> Note: CLAUDE.md carried substantial pre-existing uncommitted edits (108/28) before this gate began. Those are the operator's vault state, not this gate's work, and were **not committed** (gate forbids vault-main commit).

## 5. Files changed / not touched

**FILES_CHANGED:** none in the vault. Only this gpt-handoff report (`apply-claude-md-evidence-gate-patch-v0.1/{report.md,summary.json}` + `latest-report.md` + `latest-summary.json`).
**FILES_NOT_TOUCHED:** `CLAUDE.md` (blocked — unchanged) · `claude-session-protocol.md` · `pending.md` · AIKB · `C:/Projects/InsuranceAIPlatform` + Twincore (product) · Azure / providers · vault-main (no commit) · the untracked prior code-quality-audit folder (out of scope).

## 6. Validation / scans

| Check | Result |
|---|---|
| CLAUDE.md contains the 3 rules | NO (blocked) — so not ACTIVATED; honestly reported as blocked |
| No other vault file changed | ✅ |
| No product / AIKB / protocol / pending changes | ✅ |
| Secrets scan (this report + attempted edit) | CLEAN (no keys/tokens/PII; `CLAUDE_SACRED_BYPASS` is a config flag name, not a secret) |
| Hook bypass used | **NO** |
| Sacred protection respected | ✅ (guard fired, honored) |

## 7. Sacred-file handling + how to unblock (operator decision)

The `scope-guard.ps1` PreToolUse hook treats `C:\Users\DEVELOPER\Claude\CLAUDE.md` as SACRED and blocks all `Edit`/`Write`. Two safe ways forward, **operator's choice** (a follow-up gate, not auto-started):

- **Option A (recommended):** the Operator edits CLAUDE.md by hand, pasting the 3 lines from §3 after the `Report must include` line. Zero bypass; full operator control of the sacred file.
- **Option B:** a follow-up gate (`APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.2`) that **explicitly authorizes** the one-shot `$env:CLAUDE_SACRED_BYPASS=1` for this single patch. Even then I would apply only these 3 lines, re-verify, and not commit the vault.

The patch in §3 is exact and ready for either path.

## 8. Limitations
- The 3 rules are **NOT active** in CLAUDE.md — they remain PROPOSED, blocked by the sacred guard. Do not treat them as in force.
- Most adjacent governance (protocol pointer, hard gates, G1–G7) is already active per the prior gate `CLAUDE_FUTURE_PROJECTS_OS_HARDENING_V0.2` — these 3 lines are the only outstanding additive items.

## 9. Next gates (not auto-started)
1. `APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.2` (Option B bypass-authorized) **or** operator hand-edit (Option A).
2. `APPLY_PROTOCOL_FRONTMATTER_HONESTY_FIX_V0.1` (Patch A).
3. `PENDING_QUARANTINE_RULES_ENTRY_V0.1` (Patch C).
4. `SEMANTIC_E2E_STANDARD_HARDENING_V0.1`.

---

### Final report block
```
VERDICT: PROPOSED_ONLY_BLOCKED_BY_PROTECTION
GATE: APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.1
TARGET_FILE: C:\Users\DEVELOPER\Claude\CLAUDE.md
ANCHOR_FOUND: YES
ANCHOR_LINE: 57 (`> **Report must include:** ... / `NOT TOUCHED`.`)
PATCH_STATUS: BLOCKED by scope-guard.ps1 PreToolUse hook (sacred); NOT applied; NOT bypassed
FILES_CHANGED: none in vault; only this gpt-handoff report (4 files)
FILES_NOT_TOUCHED: CLAUDE.md (unchanged) · protocol · pending · AIKB · product repos · Azure/providers · vault-main · prior code-quality-audit folder
VALIDATION: grep patch-lines=0; CLAUDE.md diffstat identical before/after (108/28, pre-existing not mine); vault HEAD a79f119 unchanged
SECRETS_SCAN: CLEAN
SACRED_FILE_HANDLING: guard fired -> honored -> no bypass (gate FORBIDDEN + CLAUDE.md hard gate)
GIT_STATUS_BEFORE: CLAUDE.md ` M` (pre-existing)
GIT_STATUS_AFTER: CLAUDE.md ` M` (identical — no change by this gate)
COMMIT: gpt-handoff report only (see publish section)
PUSH_RESULT: gpt-handoff only
GPT_HANDOFF_PATHS: latest-report.md · latest-summary.json · apply-claude-md-evidence-gate-patch-v0.1/{report.md,summary.json}
LIMITATIONS: 3 rules NOT active (blocked); remain PROPOSED
NEXT_SAFE_STEP: operator hand-edit (Option A) or bypass-authorized V0.2 gate (Option B); then GPT audit via "отчёт"
STOP_LINE: stopped after blocked attempt + report; no bypass; no Patch A/C; no AIKB; no product; next gate NOT opened
```
