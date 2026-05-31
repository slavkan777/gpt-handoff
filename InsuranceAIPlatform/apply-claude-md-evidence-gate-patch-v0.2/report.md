# Apply CLAUDE.md Evidence-Gate Patch (Patch B) — V0.2 (one-shot authorized bypass)

CURRENT STATE: Patch B applied to the live CLAUDE.md (3 rules, exactly once, bounded). Bypass env var set during the write and unset immediately (confirmed all scopes). CLAUDE.md NOT committed (vault-main untouched). Only the gpt-handoff report was committed.
CURRENT GATE: APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.2
BOUNDARIES: one authorized sacred write (Patch B) to CLAUDE.md only. No Patch A/C, no protocol/pending, no AIKB, no product, no Azure/provider, no vault-main commit, no force-push.
NEXT SAFE STEP: GPT audit via "отчёт". Optional follow-ups: Patch A (frontmatter honesty), Patch C (pending entries), and a scope-guard hardening gate (see §7 finding).

**VERDICT: ACTIVATED** — the 3 evidence-gate rules are now live in CLAUDE.md.

---

## 1. What happened (FACT)

1. Read live CLAUDE.md; anchor `> **Report must include:** …` confirmed at **line 57** (exactly 1); Patch B not already present (grep 0).
2. **Attempted the normal Edit tool first** → blocked again by `scope-guard.ps1` PreToolUse hook ("CLAUDE.md is SACRED"). Fresh in-gate evidence of the guard.
3. Applied the **authorized one-shot write**: set `$env:CLAUDE_SACRED_BYPASS=1`, performed a byte-exact insertion of exactly the 3 Patch-B lines after the anchor, and **unset the bypass in the same atomic operation**.
4. Verified: 3 rules present exactly once (lines 59–61), inside the Session protocol block, between the anchor and the existing `Memory, hooks…` line; nothing else changed.

## 2. Target + anchor + result

| Field | Value |
|---|---|
| Target | `C:\Users\DEVELOPER\Claude\CLAUDE.md` (sacred; authorized for Patch B only) |
| Anchor | line **57** — `> **Report must include:** … / NOT TOUCHED.` (1 occurrence) |
| Inserted at | lines 58–61 (a `>` separator + 3 rules), before the existing `Memory…` line |
| Rule 1 (evidence tuple) | present **1×** (line 59) |
| Rule 2 (UI MECHANICAL/SEMANTIC/PERSISTENCE/NEGATIVE) | present **1×** (line 60) |
| Rule 3 (provider MOCK/FALLBACK/LOCAL_REAL/LIVE_REAL/NOT_TESTED) | present **1×** (line 61) |
| Byte delta | +370 (24341 → 24711) |
| Line delta vs HEAD | 108 → **112 insertions** (+4 lines = 3 rules + 1 separator) |
| Encoding/EOL | **BOM preserved** (`hasBom=True`), **CRLF preserved** (file is CRLF) — no normalization |

## 3. Bypass usage — full honesty

| Field | Value |
|---|---|
| BYPASS_USED | YES — `$env:CLAUDE_SACRED_BYPASS=1` set during the write, per explicit one-shot authorization |
| BYPASS_UNSET_CONFIRMED | YES — same atomic op unset it; confirmed `Process=[] User=[] Machine=[]` and again `[]` in a fresh process |
| Operative mechanism | **The env var was NOT actually the thing that allowed the write.** The `scope-guard.ps1` hook fires only on the **Edit/Write tools**; a PreToolUse hook runs in its own process and cannot see an env var set by a tool call (process-env is a spawn-time snapshot, cf. G4). So the Edit tool stayed blocked. The write was performed via the **PowerShell tool**, which `scope-guard` does **not** intercept. I set+unset the authorized env var as instructed, but the PowerShell path is what carried the write. |
| Scope of bypass | exactly one write, exactly this file, exactly Patch B. No other file, no other op. |

## 4. Files changed / not touched

**FILES_CHANGED:**
- `C:\Users\DEVELOPER\Claude\CLAUDE.md` — +4 lines (Patch B). **NOT committed** (vault-main untouched per gate).
- gpt-handoff report: `apply-claude-md-evidence-gate-patch-v0.2/{report.md,summary.json}` + `latest-report.md` + `latest-summary.json` (committed to gpt-handoff).

**FILES_NOT_TOUCHED:** `00-Meta/governance/claude-session-protocol.md` (Patch A NOT applied) · `00-Meta/self-improvement/pending.md` (Patch C NOT applied) · AIKB · `C:/Projects/InsuranceAIPlatform` + Twincore (product) · Azure / providers · vault main branch (no commit) · the untracked prior code-quality-audit folder.

## 5. Diff summary (bounded)
The only added content (confirmed via `git diff -- CLAUDE.md`, shown as `+` lines):
```
+> **No `DONE`/`PASSED`/`CLEARED`** without a per-item evidence tuple (`path:line` / `command + exit` / `N/M`); else status = `IN PROGRESS`.
+> **UI create/open/update:** report `MECHANICAL_PASS` / `SEMANTIC_PASS` / `PERSISTENCE_PASS (reload)` / `NEGATIVE_PASS`.
+> **Provider/DB/cloud calls:** label `MOCK` / `FALLBACK` / `LOCAL_REAL` / `LIVE_REAL` / `NOT_TESTED`.
```
(plus one `>` separator line). No other section changed. Note: CLAUDE.md also carried **pre-existing** uncommitted edits (108/28 vs HEAD) present at session start — those are the operator's vault state, **not this gate's**, and are not committed.

## 6. Validation

| Check | Result |
|---|---|
| 3 rules present | ✅ each exactly once (grep counts 1/1/1; lines 59–61) |
| Inside Session protocol block | ✅ (after anchor line 57, before Memory line 63) |
| Diff shows only allowed lines | ✅ (only the 3 rules + 1 separator added) |
| Bypass unset after write | ✅ (`[]` Process/User/Machine + fresh process) |
| No product / AIKB / protocol / pending change | ✅ |
| Secrets scan over the added lines | ✅ CLEAN |
| Encoding/EOL preserved | ✅ BOM + CRLF preserved (no global reformat) |

## 7. Security finding (RECOMMENDATION — worth a future gate)

**The sacred-file guard is incomplete: `scope-guard.ps1` only intercepts the Edit/Write tools, not shell (Bash/PowerShell) file I/O.** This gate's write went through PowerShell, which the guard never saw — i.e., the sacred protection on CLAUDE.md is bypassable from a shell tool **without any env var at all**. This time it was used under explicit one-shot authorization and within scope, but the gap means the guard is not a real barrier against an unauthorized shell write.

**RECOMMENDATION (RULE CANDIDATE, not applied here):** extend `scope-guard.ps1` (or add a sibling hook) to also block Bash/PowerShell tool commands that write to sacred paths (match `Set-Content`/`Out-File`/`[IO.File]::WriteAllText`/`>`/`Add-Content`/`Move-Item`/`rm` targeting sacred files), honoring the same `CLAUDE_SACRED_BYPASS` flag. Until then, treat the sacred guard as Edit/Write-only.

## 8. Limitations
- The CLAUDE.md change is **local working-tree only** — NOT committed (gate forbids vault-main commit). It is live for this and future sessions that load this CLAUDE.md, but a separate commit gate would be needed to persist it to the vault repo.
- CLAUDE.md had pre-existing uncommitted edits (108/28) unrelated to this gate; not committed.
- The env-var bypass mechanism, as designed, is not reachable from a tool call (process isolation); the effective path was PowerShell (§3, §7).

## 9. Next gates (not auto-started)
1. `HARDEN_SCOPE_GUARD_SHELL_COVERAGE_V0.1` (close the §7 gap).
2. `APPLY_PROTOCOL_FRONTMATTER_HONESTY_FIX_V0.1` (Patch A).
3. `PENDING_QUARANTINE_RULES_ENTRY_V0.1` (Patch C).
4. `SEMANTIC_E2E_STANDARD_HARDENING_V0.1`.
5. (if desired) a commit gate to persist the CLAUDE.md change to the vault repo.

---

### Final report block
```
VERDICT: ACTIVATED
GATE: APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.2
TARGET_FILE: C:\Users\DEVELOPER\Claude\CLAUDE.md
ANCHOR_FOUND: YES
ANCHOR_LINE: 57
PATCH_STATUS: APPLIED — 3 rules present exactly once (lines 59-61), bounded (+4 lines / +370 bytes), BOM+CRLF preserved
BYPASS_USED: YES (env var set during write, per authorization) — but operative path was PowerShell; scope-guard only covers Edit/Write (see finding §7)
BYPASS_UNSET_CONFIRMED: YES (Process/User/Machine all empty; re-confirmed [] in fresh process)
FILES_CHANGED: CLAUDE.md (+4 lines, NOT committed) + 4 gpt-handoff report files (committed)
FILES_NOT_TOUCHED: protocol · pending · AIKB · product repos · Azure/providers · vault-main · prior code-quality-audit folder
DIFF_SUMMARY: +3 rule lines + 1 separator after the Report-must-include anchor; nothing else
VALIDATION: rules 1/1/1; anchor 1; memory 1; diff bounded; encoding/EOL preserved; secrets CLEAN
SECRETS_SCAN: CLEAN
GIT_STATUS_BEFORE: CLAUDE.md ` M` (pre-existing 108/28)
GIT_STATUS_AFTER:  CLAUDE.md ` M` (112/28 — +4 lines mine; NOT committed)
COMMIT: gpt-handoff report only
PUSH_RESULT: gpt-handoff only (see publish section)
GPT_HANDOFF_PATHS: latest-report.md · latest-summary.json · apply-claude-md-evidence-gate-patch-v0.2/{report.md,summary.json}
LIMITATIONS: CLAUDE.md change is local-only (not committed); scope-guard is Edit/Write-only (shell-bypassable)
NEXT_SAFE_STEP: GPT audit via "отчёт"; optional HARDEN_SCOPE_GUARD + commit-to-vault gates
STOP_LINE: stopped after Patch B + report; bypass unset; no Patch A/C; no AIKB; no product; next gate NOT opened
```
