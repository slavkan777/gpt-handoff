# AI Engineering OS — Consolidated Safety & Template Hardening — V0.2

CURRENT STATE: Macro-gate executed phase-locked. Patch B re-verified ACTIVE. scope-guard shell-coverage = BLOCKED_BY_PROTECTION (exact patch below — not forced). Patch A + Patch C applied to vault working-tree (NOT committed). Report schema + semantic-E2E standard created in gpt-handoff. AIKB sync = PROPOSAL_ONLY. Only gpt-handoff files committed.
CURRENT GATE: AI_ENGINEERING_OS_CONSOLIDATED_SAFETY_AND_TEMPLATE_HARDENING_V0.2
BOUNDARIES: no product source/repo · no Azure · no providers/keys · no secrets/PII · no force-push · no vault-main commit · no AIKB write · no sacred-file bypass (none authorized in this gate) · no hidden activation.
NEXT SAFE STEP: Auditor review via "отчёт". Highest-value follow-up = HARDEN_SCOPE_GUARD_SHELL_COVERAGE_V0.1 (closes the gap below; needs a one-shot bypass authorization like Patch B V0.2).

**VERDICT: PARTIAL_ACCEPT_WITH_LIMITATIONS** — 4 of the actionable subphases applied (Patch A, Patch C, report schema, E2E standard); the headline security item (scope-guard hardening) is BLOCKED_BY_PROTECTION and AIKB sync is PROPOSAL_ONLY. All reported honestly with evidence.

---

## Phase-by-phase

### BOOTSTRAP_STATE (Subphase 0)
- Read: latest gpt-handoff report+summary (prior gate = ACTIVATED); live `CLAUDE.md` Session-protocol block (lines 48–63); `scope-guard.ps1` (full); `settings.json` (hook registration); `claude-session-protocol.md` (status); `pending.md` (quarantine queue).
- Repos: vault `main@a79f119` (many pre-existing uncommitted edits — Operator's, unrelated); gpt-handoff `main@2e373a2` (clean except one untracked out-of-scope folder); the `.claude` hooks dir is **not** a git repo (so the hook file isn't vault-tracked).
- No blocking dirty state for the targets I write (additive/non-overlapping). Product repos not touched.

### PATCH_B_STATUS (Subphase 1) — ACTIVE / EXACT ✅
- `CLAUDE.md:59` rule 1 (evidence tuple), `:60` rule 2 (UI passes), `:61` rule 3 (provider labels). `grep -c` = **1 / 1 / 1**.
- Located inside `## Session protocol` (line 48), between the `Report must include` anchor (`:57`) and the `Memory…` line (`:63`). No duplicate, no extra unauthorized rule text. No reapply needed.

### SCOPE_GUARD_STATUS (Subphase 2) — BLOCKED_BY_PROTECTION ⛔ (exact patch provided, not forced)
**Why blocked, precisely:**
1. The two files that must change are **themselves sacred**: `settings.json` (scope-guard.ps1:87) and `hooks\*.ps1` (scope-guard.ps1:88) → the Edit/Write tool is blocked on both.
2. This gate authorized **no** `CLAUDE_SACRED_BYPASS` (unlike APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.2, which carried an explicit one-shot grant).
3. The only non-Edit path is the **PowerShell tool — which is the very shell-write gap under audit.** Using it to write a sacred file without explicit authorization would be exploiting the vulnerability I was asked to close. Per the gate ("if sacred/protected → PROPOSED_ONLY, do not force") and CLAUDE.md ("no hook bypass unless explicitly authorized by the current prompt"), I declined.

**The gap (confirmed at source):**
- `scope-guard.ps1:44-48` extracts its target only from `tool_input.file_path` / `tool_input.path`. Shell tools carry their target in `tool_input.command`, never read → `$path` null → `exit 0`.
- `settings.json:121-148`: scope-guard is registered on `Edit|Write|MultiEdit|NotebookEdit` and the MCP fs/github matchers, **not** on `Bash`; the `Bash` matcher runs only `qa-commit-gate.ps1`; there is **no `PowerShell` matcher at all**. So a PowerShell write to a sacred path triggers no guard whatsoever.

**Exact proposed patch (apply under a one-shot authorization in a follow-up gate):**

*(a) `scope-guard.ps1` — insert after `$tool = $payload.tool_name` / path extraction, before the TwinCore guard:*
```powershell
# === GUARD 0: shell-tool writes to sacred paths (Bash/PowerShell) ===
# Historically scope-guard only saw Edit/Write (file_path). Shell tools carry the
# target in tool_input.command, which was never inspected -> sacred files were
# writable from a shell with NO bypass. Best-effort string scan closes the common cases.
if ($tool -in @('Bash','PowerShell')) {
    $cmd = [string]$payload.tool_input.command
    if ($cmd) {
        if ($env:CLAUDE_SACRED_BYPASS -eq '1') { Write-Audit 'BYPASS-ENV-VAR-SHELL' $tool $cmd; exit 0 }
        $sacredFragments = @('CLAUDE.md','\.claude\settings.json','\.claude\hooks\','sacred-manifest.md','memory\MEMORY.md')
        $writeVerbs = 'Set-Content|Add-Content|Out-File|WriteAllText|WriteAllLines|WriteAllBytes|New-Item|Remove-Item|Move-Item|Copy-Item|Rename-Item|Clear-Content|\brm\b|\bmv\b|\bcp\b|>>|>'
        foreach ($frag in $sacredFragments) {
            if (($cmd -match [regex]::Escape($frag)) -and ($cmd -match $writeVerbs)) {
                Write-Audit 'BLOCKED-SACRED-SHELL' $tool $cmd @{fragment = $frag}
                [Console]::Error.WriteLine("[scope-guard] BLOCKED: shell command appears to write sacred path '$frag'")
                [Console]::Error.WriteLine("[scope-guard] Bypass: `$env:CLAUDE_SACRED_BYPASS=1 (use sparingly)")
                exit 2
            }
        }
    }
}
```

*(b) `settings.json` — add one new PreToolUse entry so scope-guard also fires on shells:*
```json
{
  "matcher": "Bash|PowerShell",
  "hooks": [
    { "type": "command",
      "command": "powershell.exe -NoProfile -ExecutionPolicy Bypass -File \"C:\\Users\\DEVELOPER\\.claude\\hooks\\scope-guard.ps1\"" }
  ]
}
```
(qa-commit-gate's existing `Bash` entry stays untouched; this adds scope-guard alongside it and extends coverage to `PowerShell`.)

**Honest limitation (per the gate's "report limitations honestly" requirement):** a command-string scan is a **speed-bump, not a sandbox**. It is defeatable by var-built paths, `cmd /c`, base64/`-EncodedCommand`, here-strings that assemble the path, or piping. A robust fix would intercept at the filesystem layer, not the command string. The proposal raises the bar (no accidental/naive shell write to a sacred file) without claiming completeness.

### PATCH_A_STATUS (Subphase 3) — APPLIED ✅ (working-tree, not committed)
Precondition verified: `CLAUDE.md:51` points to this protocol. Edits to `00-Meta/governance/claude-session-protocol.md`:
- frontmatter `:4` `status: trial-ready → active`; `:5` `trial_status: not-wired-yet → wired-via-claude-md-pointer`; `:8` `updated → 2026-05-31`; tag `trial-ready → active`.
- body status banners `:13`, `:416`, `:442` → "ACTIVE / wired via the CLAUDE.md pointer".
- `grep -c 'trial_status: not-wired-yet'` = **0**; the remaining `trial-ready` mentions at `:15` (also contains a *rule* — deliberately untouched) and `:33` (explanatory, condition now satisfied) were **left by design**; no rule content changed.

### PATCH_C_STATUS (Subphase 4) — APPLIED ✅ (working-tree, not committed)
Appended 4 dated entries to `00-Meta/self-improvement/pending.md` (`grep -c '### [2026-05-31 03:21]'` = **4**):
1. **QUARANTINE** — narrowing of Tier-A "Opus mandatory for ALL critical work" → risk-profile-scoped (requires 2nd Operator confirmation).
2. **QUARANTINE** — narrowing of "/qa mandatory for ANY edit" → MED+/external-surface only (requires 2nd Operator confirmation).
3. **TRACKING** — Patch B active in local CLAUDE.md but not committed to vault-main.
4. **TRACKING** — scope-guard shell-coverage result = BLOCKED_BY_PROTECTION; needs dedicated gate.
No existing entries overwritten; no rule activated from pending.

### GPT_HANDOFF_SCHEMA_STATUS (Subphase 5) — APPLIED ✅
Created central, cross-project, sanitized templates (NOT under AIKB/ClaudeOS, so they don't trip the AIKB gate):
- `_schema/gate-report.schema.json` — JSON-Schema (draft 2020-12) for `summary.json`; verdict enum incl. `BLOCKED_BY_PROTECTION`; `evidence_labels` requires a tuple per item; conditional `semantic_pass_labels` / `provider_realness_labels`.
- `_schema/gate-report-template.md` — report structure + Auditor verdict rubric + rejection triggers (prose-only evidence, uncited numerics, UI missing SEMANTIC/PERSISTENCE, `git add -A` sweeps).

### SEMANTIC_E2E_STANDARD_STATUS (Subphase 6) — APPLIED ✅
- `_standards/semantic-e2e-standard.md` — the four-pass UI matrix (MECHANICAL/SEMANTIC/PERSISTENCE/NEGATIVE), the realness labels (MOCK/FALLBACK/LOCAL_REAL/LIVE_REAL/NOT_TESTED), the stub-fingerprint trap and external-call evidence triple. Explicitly separates click/toast from business correctness; references the Patch B labels.

### AIKB_SYNC_STATUS (Subphase 7) — PROPOSAL_ONLY 📋
The gpt-handoff `AIKB/` and `ClaudeOS/` folders are **report channels** (latest-report/summary only). The durable stores are separate **private** repos — `slavkan777/ai-kb` (`00_CONTROL_CENTER`, `02_TEMPLATES` incl. an existing `TEMPLATE_GPT_AUDIT`) and `slavkan777/claude-vault` (`claude-os/CURRENT_PROTOCOL.md`). Neither is in my local tree, neither is an authorized write surface for this gate, and the AIKB rule requires *all prior required phases passed* — Subphase 2 did not. So **no AIKB write**. Proposed sync (for a dedicated authorized gate):
- `slavkan777/ai-kb` → `02_TEMPLATES/`: merge the report schema into the existing `TEMPLATE_GPT_AUDIT`; add `TEMPLATE_SEMANTIC_E2E_STANDARD`. `00_CONTROL_CENTER`: record the Patch B evidence labels as ACTIVE governance.
- `slavkan777/claude-vault` → `claude-os/CURRENT_PROTOCOL.md`: note Patch B ACTIVE + the scope-guard shell-coverage gap as a known-issue until hardened.
- Recommended gate: `AIKB_TEMPLATE_SYNC_V0.1` (explicit authorization to clone + write + push those private repos; distinguish ACTIVE/PROPOSED/CONDITIONAL/QUARANTINE).

---

## FILES_READ
`CLAUDE.md` · `00-Meta/governance/claude-session-protocol.md` · `00-Meta/self-improvement/pending.md` · `.claude/hooks/scope-guard.ps1` · `.claude/settings.json` · gpt-handoff `InsuranceAIPlatform/latest-report.md` + `latest-summary.json` + prior gate report/summary · gpt-handoff `AIKB/latest-summary.json` · `ClaudeOS/latest-summary.json` · gpt-handoff `.gitignore`.

## FILES_CHANGED
- **Vault working-tree (NOT committed):** `00-Meta/governance/claude-session-protocol.md` (Patch A) · `00-Meta/self-improvement/pending.md` (Patch C).
- **gpt-handoff (committed):** `_schema/gate-report.schema.json` · `_schema/gate-report-template.md` · `_standards/semantic-e2e-standard.md` · this gate's `report.md` + `summary.json` · `latest-report.md` + `latest-summary.json`.

## FILES_NOT_TOUCHED
`.claude/hooks/scope-guard.ps1` (sacred; mtime May 12 — unchanged) · `.claude/settings.json` (sacred; mtime pre-gate — unchanged) · `CLAUDE.md` (no new change this gate; Patch B already live) · AIKB / `slavkan777/ai-kb` · `slavkan777/claude-vault` · product repos (`C:/Projects/...`) · Azure / providers · vault `main` (no commit) · the untracked prior code-quality-audit folder (out of scope — left unstaged).

## BYPASS_USED
**NO.** No `CLAUDE_SACRED_BYPASS` was set in this gate. The one sacred-touching subphase (scope-guard) was declined → BLOCKED_BY_PROTECTION.

## BYPASS_UNSET_CONFIRMED
N/A (never set). Verified `CLAUDE_SACRED_BYPASS` is empty in this process.

## VALIDATION
| Check | Evidence |
|---|---|
| Patch B 1/1/1 | `grep -c` on `CLAUDE.md` → 1,1,1 at lines 59/60/61 |
| Patch A applied | `claude-session-protocol.md:4,5,13,416,442`; `not-wired-yet` count = 0 |
| Patch C 4 entries | `grep -c '### [2026-05-31 03:21]'` = 4 |
| Schema/standard created | `ls _schema/ _standards/` → 3 files (4355 + 3616 + 4088 bytes) |
| JSON valid | `summary.json` + `gate-report.schema.json` parse-checked before commit |
| No vault commit | vault HEAD still `a79f119` |
| Sacred files untouched | scope-guard.ps1 / settings.json mtimes pre-date this gate |
| Out-of-scope folder unstaged | only explicit paths staged; never `git add -A` |

## SECRETS_SCAN
CLEAN — danger-scan over published files: no secrets/tokens/keys, no connection strings, no personal names (roles only — "Operator"/"Auditor"/"Executor"), no emails, no Azure GUIDs. Third-party-named folder left unstaged.

## BOUNDARIES_HONORED
product source ❌none · product repo ❌none · Azure ❌none · providers/keys ❌none · secrets/PII ❌none · force-push ❌none · vault-main commit ❌none · AIKB write ❌none · sacred bypass ❌none (declined) · hidden activation ❌none · phase-lock respected (BLOCKED_BY_PROTECTION ≠ failure, so lower-risk non-sacred phases proceeded).

## COMMITS / PUSH_RESULT / GPT_HANDOFF_PATHS
Commit + push = **gpt-handoff report + template files only** (exact sha + push range in the chat report-back). Staged by explicit path; `[qa-bypass: docs-only gpt-handoff report]` per docs-handoff precedent. No vault commit; no force-push.

Handoff paths:
- `InsuranceAIPlatform/latest-report.md` · `InsuranceAIPlatform/latest-summary.json`
- `InsuranceAIPlatform/ai-engineering-os-consolidated-safety-and-template-hardening-v0.2/report.md` + `summary.json`
- `_schema/gate-report.schema.json` · `_schema/gate-report-template.md` · `_standards/semantic-e2e-standard.md`

## LIMITATIONS
1. The **headline security fix is not applied** — scope-guard shell coverage remains open (BLOCKED_BY_PROTECTION). The sacred guard is still Edit/Write-only and shell-bypassable until `HARDEN_SCOPE_GUARD_SHELL_COVERAGE_V0.1`.
2. Patch A + Patch C are **working-tree only** — not committed to vault-main (gate forbids). A separate commit-to-vault gate is needed to persist them (and Patch B).
3. The proposed shell-scan is best-effort (string match), defeatable by obfuscation — honestly a speed-bump, not a sandbox.
4. AIKB durable sync deferred to a dedicated authorized gate (targets named above).
5. Published role labels are sanitized; the durable AIKB repos referenced are private.

## NEXT_SAFE_STEP
Auditor review via "отчёт". Recommended follow-ups (none auto-started): `HARDEN_SCOPE_GUARD_SHELL_COVERAGE_V0.1` (with one-shot bypass authorization) → `AIKB_TEMPLATE_SYNC_V0.1` → optional `COMMIT_GOVERNANCE_TO_VAULT_V0.1` (persist Patch A/B/C).

## STOP_LINE_CONFIRMATION
Stopped after the consolidated report. No product work, no Azure/provider work, no InsuranceAIPlatform quality gate, no next mutation gate auto-opened.

---

```text
VERDICT: PARTIAL_ACCEPT_WITH_LIMITATIONS
GATE: AI_ENGINEERING_OS_CONSOLIDATED_SAFETY_AND_TEMPLATE_HARDENING_V0.2
BOOTSTRAP_STATE: read latest handoff + live CLAUDE.md(48-63) + scope-guard.ps1 + settings.json + protocol + pending; vault main@a79f119; gpt-handoff main@2e373a2; .claude not a git repo
PATCH_B_STATUS: ACTIVE/EXACT — rules at CLAUDE.md:59/60/61, grep 1/1/1, inside Session protocol block
SCOPE_GUARD_STATUS: BLOCKED_BY_PROTECTION — both targets sacred (scope-guard.ps1:87-88), no bypass authorized; exact .ps1 GUARD-0 + settings Bash|PowerShell matcher patch provided; shell-scan is best-effort
PATCH_A_STATUS: APPLIED (working-tree, not committed) — protocol :4,:5,:8,:13,:416,:442; not-wired-yet=0; rule at :15 untouched
PATCH_C_STATUS: APPLIED (working-tree, not committed) — 4 entries (2 QUARANTINE narrowings + 2 TRACKING)
GPT_HANDOFF_SCHEMA_STATUS: APPLIED — _schema/gate-report.schema.json + gate-report-template.md
SEMANTIC_E2E_STANDARD_STATUS: APPLIED — _standards/semantic-e2e-standard.md (4-pass matrix + realness labels)
AIKB_SYNC_STATUS: PROPOSAL_ONLY — targets slavkan777/ai-kb(02_TEMPLATES,00_CONTROL_CENTER) + slavkan777/claude-vault(claude-os); needs AIKB_TEMPLATE_SYNC_V0.1
FILES_READ: CLAUDE.md, claude-session-protocol.md, pending.md, scope-guard.ps1, settings.json, gpt-handoff latest-* + prior gate + AIKB/ClaudeOS summaries + .gitignore
FILES_CHANGED: vault(working-tree,uncommitted): protocol, pending.md; gpt-handoff(committed): _schema x2, _standards x1, this report.md+summary.json, latest-report.md+latest-summary.json
FILES_NOT_TOUCHED: scope-guard.ps1, settings.json, CLAUDE.md, AIKB repos, product, Azure, vault-main, prior code-quality-audit folder
BYPASS_USED: NO
BYPASS_UNSET_CONFIRMED: N/A (never set; confirmed empty in process)
VALIDATION: grep 1/1/1 PatchB; PatchA not-wired-yet=0; PatchC=4; 3 template files created; JSON parsed; vault HEAD a79f119; sacred mtimes pre-gate
SECRETS_SCAN: CLEAN (roles not names; no keys/PII/GUIDs; prior code-quality-audit folder unstaged)
BOUNDARIES_HONORED: no product/Azure/providers/secrets/force-push/vault-commit/AIKB-write/sacred-bypass/hidden-activation
COMMITS: gpt-handoff report+templates only (sha in chat), [qa-bypass: docs-only gpt-handoff report]
PUSH_RESULT: gpt-handoff main only (range in chat); no force
GPT_HANDOFF_PATHS: latest-report.md, latest-summary.json, ai-engineering-os-consolidated-safety-and-template-hardening-v0.2/{report.md,summary.json}, _schema/{gate-report.schema.json,gate-report-template.md}, _standards/semantic-e2e-standard.md
LIMITATIONS: scope-guard not hardened (blocked); PatchA/C not committed; shell-scan best-effort; AIKB deferred
NEXT_SAFE_STEP: Auditor "отчёт"; then HARDEN_SCOPE_GUARD_SHELL_COVERAGE_V0.1, AIKB_TEMPLATE_SYNC_V0.1, optional commit-to-vault
STOP_LINE_CONFIRMATION: stopped after report; no product/Azure/quality-gate/next-mutation-gate
```
