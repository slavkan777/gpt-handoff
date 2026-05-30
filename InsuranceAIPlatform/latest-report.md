# InsuranceAIPlatform — Latest gate report

**Gate:** CLAUDE_FUTURE_PROJECTS_OS_HARDENING_V0.2
**Type:** governance / workflow / protocol hardening (read-mostly; not product code)
**Date (UTC):** 2026-05-31
**Verdict:** **ACCEPT_WITH_LIMITATIONS** — core governance already ACTIVE; new items PROPOSED_ONLY (sacred-file protection)

**Full report:** [ai-engineering-os-claude-future-projects-hardening-v0.2/report.md](ai-engineering-os-claude-future-projects-hardening-v0.2/report.md)
**Machine summary:** [ai-engineering-os-claude-future-projects-hardening-v0.2/summary.json](ai-engineering-os-claude-future-projects-hardening-v0.2/summary.json)

---

## Headline finding (FACT — corrects the prior retrospective)

The live `CLAUDE.md` **already wires** the session protocol and operating principles — the prior retrospective's "protocol is unwired, biggest win = wire it" premise was based on the protocol file's **stale `not-wired-yet` frontmatter**, not live state. Reading the actual CLAUDE.md shows it already has:
- the protocol pointer (`CLAUDE.md:51`), the pre-action declaration, the hard gates, and the report fields (`:53–57`);
- **G1–G7** operating principles — external-call triple, stub-fallback, **no-self-acceptance / independent review**, risk-based phase gates (`:235–299`);
- complexity routing + subagent-brief requirements (`:206–224`).

So **~85% of the GPT/retrospective recommendations are already ACTIVE.** This is the live-state-over-stale-doc lesson (R-22) applied to the retrospective itself.

## What this gate did

- **Reconciled 16 themes** (GPT recs × Claude retrospective) into a decision matrix — *not* blindly accepting either side: GPT's "Opus for ALL / external-triple-everywhere / triangulate-everything" was **MODIFIED** per Claude's evidence-based objections; Claude's "more process" items (evidence-tuple, numeric verification) were **kept P0** because the incidents (A-020, A-021) prove the harm.
- **Preserved every Claude disagreement** explicitly; the two that *narrow Tier-A sacred rules* (Opus-scope, /qa-scope) are routed to **pending.md quarantine + 2nd Operator confirm — not activated here.**
- Separated **universal always-on** rules (mostly already active) from **risk-profile-conditional**, **project-type-conditional**, and **quarantine** tiers.

## What is NOT activated (honest)

CLAUDE.md is **sacred** → per the gate's own SUBPHASE-5 (`PROTECTED → proposed patch, do not bypass`), the 3 genuinely-new items are **PROPOSED_ONLY**: (1) no `DONE/PASSED/CLEARED` without a per-item evidence tuple; (2) UI `MECHANICAL/SEMANTIC/PERSISTENCE/NEGATIVE` pass labels; (3) provider `MOCK/FALLBACK/LOCAL_REAL/LIVE_REAL/NOT_TESTED` labels. Exact diffs are in the full report (Patches A/B/C). **Nothing sacred or vault-main was mutated; nothing was hidden-activated.**

## Next recommended gate

`APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.1` (apply Patch B after explicit sacred-file OK) and/or `AIKB_AND_TEMPLATE_SYNC_AFTER_OS_HARDENING_V0.1` (apply Patch A frontmatter honesty-fix + Patch C pending entries). Also: `SEMANTIC_E2E_STANDARD_HARDENING_V0.1`, `GPT_HANDOFF_AND_GPT_AUDIT_SCHEMA_HARDENING_V0.1`.

---

> **Boundaries:** no product code / Azure / provider / secrets / AIKB / sacred-file edit / vault-main commit / force-push. This publication committed only the four gpt-handoff report files. Independent Opus review (no-self-acceptance) applied before publish.
