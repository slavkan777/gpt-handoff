# InsuranceAIPlatform — Latest gate report

**Gate:** APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.1
**Type:** sacred-file micro-activation (Patch B → CLAUDE.md)
**Date (UTC):** 2026-05-31
**Verdict:** **PROPOSED_ONLY_BLOCKED_BY_PROTECTION**

**Full report:** [apply-claude-md-evidence-gate-patch-v0.1/report.md](apply-claude-md-evidence-gate-patch-v0.1/report.md)
**Machine summary:** [apply-claude-md-evidence-gate-patch-v0.1/summary.json](apply-claude-md-evidence-gate-patch-v0.1/summary.json)

---

## Bottom line

The single authorized 3-line Patch B to `CLAUDE.md` was prepared exactly and **attempted** — and the **`scope-guard.ps1` PreToolUse hook blocked it** ("CLAUDE.md is SACRED"). The hook offered a bypass env var; **I did not use it**, because the gate's FORBIDDEN list and the live CLAUDE.md hard gates both prohibit hook bypass. **CLAUDE.md is unchanged.**

**This is a win for the protection layer, not a gate failure** — the sacred-file guard did exactly its job, and the executor respected it instead of routing around it. (That self-restraint is the whole point of the governance-hardening arc.)

## Evidence the file is unchanged
- `grep -c` for the patch lines in CLAUDE.md = **0** (no write occurred).
- CLAUDE.md diffstat vs HEAD is **identical before and after** the attempt (108/28 — and those are *pre-existing* operator edits, not this gate's).
- Vault HEAD `a79f119` unchanged; nothing committed.

## The patch is ready (not active)
Three lines to be inserted after the `Report must include` anchor (CLAUDE.md:57): a no-`DONE/PASSED/CLEARED`-without-evidence-tuple rule; UI `MECHANICAL/SEMANTIC/PERSISTENCE/NEGATIVE` pass labels; provider `MOCK/FALLBACK/LOCAL_REAL/LIVE_REAL/NOT_TESTED` labels. Exact diff in the full report §3.

## How to unblock (operator's choice — not auto-started)
- **Option A (recommended):** hand-edit CLAUDE.md, pasting the 3 lines — zero bypass, full operator control of the sacred file.
- **Option B:** a follow-up gate `APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.2` that explicitly authorizes a one-shot `CLAUDE_SACRED_BYPASS=1` for this single patch.

---

> **Boundaries:** no hook bypass · no Patch A/C · no protocol/pending/AIKB/product/Azure edits · no vault-main commit · no force-push. Only the four gpt-handoff report files were written + published.
