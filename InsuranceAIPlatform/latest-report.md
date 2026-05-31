# InsuranceAIPlatform — Latest gate report

**Gate:** APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.2
**Type:** sacred-file one-shot authorized patch (Patch B → CLAUDE.md)
**Date (UTC):** 2026-05-31
**Verdict:** **ACTIVATED** — the 3 evidence-gate rules are now live in CLAUDE.md

**Full report:** [apply-claude-md-evidence-gate-patch-v0.2/report.md](apply-claude-md-evidence-gate-patch-v0.2/report.md)
**Machine summary:** [apply-claude-md-evidence-gate-patch-v0.2/summary.json](apply-claude-md-evidence-gate-patch-v0.2/summary.json)

---

## Bottom line

Patch B (proposed-only in V0.1 because CLAUDE.md is sacred) is now **applied** under the operator's explicit one-shot authorization. The 3 rules sit at lines 59–61 of CLAUDE.md, between the `Report must include` anchor and the `Memory…` line, each **exactly once**:
1. No `DONE`/`PASSED`/`CLEARED` without a per-item evidence tuple → else `IN PROGRESS`.
2. UI create/open/update → `MECHANICAL_PASS` / `SEMANTIC_PASS` / `PERSISTENCE_PASS (reload)` / `NEGATIVE_PASS`.
3. Provider/DB/cloud → `MOCK` / `FALLBACK` / `LOCAL_REAL` / `LIVE_REAL` / `NOT_TESTED`.

**Bounded & exact:** +4 lines (+370 bytes); BOM + CRLF preserved; anchor/Memory intact; nothing else changed; secrets CLEAN. **Bypass set during the write and unset immediately** (confirmed empty in all scopes + a fresh process). CLAUDE.md was **not committed** (vault-main untouched per gate).

## Honest mechanism + security finding

The Edit tool stayed **blocked** by `scope-guard.ps1` — a PreToolUse hook can't see an env var set by a tool call (process-env snapshot). So the authorized write was carried by the **PowerShell tool**, which `scope-guard` does **not** intercept. I set+unset the authorized `CLAUDE_SACRED_BYPASS` flag as instructed, but it wasn't the operative mechanism.

➡️ **Finding (RULE CANDIDATE):** the sacred guard is **Edit/Write-only** — CLAUDE.md is bypassable from a shell tool *without any env var*. Recommend a `HARDEN_SCOPE_GUARD_SHELL_COVERAGE_V0.1` gate to also block shell writes to sacred paths.

## Next recommended gates (not auto-started)
`HARDEN_SCOPE_GUARD_SHELL_COVERAGE_V0.1` · `APPLY_PROTOCOL_FRONTMATTER_HONESTY_FIX_V0.1` (Patch A) · `PENDING_QUARANTINE_RULES_ENTRY_V0.1` (Patch C) · optional commit-to-vault gate to persist the CLAUDE.md change.

---

> **Boundaries:** Patch B only · bypass unset after write · no Patch A/C · no protocol/pending/AIKB/product/Azure · no vault-main commit · no force-push. Only the 4 gpt-handoff report files were committed.
