# Claude Future-Projects OS Hardening — V0.2 (governance gate, read-mostly)

CURRENT STATE: Governance/protocol hardening analysis complete. No product code, Azure, provider, secrets, or AIKB changes. No sacred file edited. No vault-main commit. Only this gpt-handoff report was published.
CURRENT GATE: CLAUDE_FUTURE_PROJECTS_OS_HARDENING_V0.2
BOUNDARIES: DOCS-ONLY analysis + gpt-handoff publish. CLAUDE.md is sacred → not edited (proposed-only). Vault governance docs not mutated (proposed-only, vault is on main). Product repos untouched.
NEXT SAFE STEP: Operator/GPT review → if accepted, a tiny follow-up gate applies the 3 proposed patches (CLAUDE.md extension is the only sacred-surface change and needs explicit per-file OK).

**VERDICT: ACCEPT_WITH_LIMITATIONS** — the core governance the gate sought is **already ACTIVE** (verified in live CLAUDE.md); the genuinely-new items are **PROPOSED_ONLY** because their only correct target (CLAUDE.md) is sacred and the protocol/pending docs are on vault-main.

---

## 0. Headline finding (FACT — corrects the retrospective's central premise)

The Claude self-improvement retrospective (published prior gate `bb99e4f`) said the single highest-leverage action was to **wire `claude-session-protocol.md` into CLAUDE.md** because it was "TRIAL READY / NOT WIRED YET." **That premise is wrong against live state.**

Reading the *actual* `C:\Users\DEVELOPER\Claude\CLAUDE.md` (not the protocol file's frontmatter) shows it **already**:
- **points to the protocol** — line 51: `> ... follow: [[00-Meta/governance/claude-session-protocol]]` (FACT);
- **inlines the pre-action declaration** — lines 53: `RISK PROFILE USED / WORKFLOW SELECTED / STOP CONDITIONS / ALLOWED / FORBIDDEN` (FACT);
- **inlines the hard gates** — lines 55: no PUSH / COMMIT / DELETE-ARCHIVE-MOVE / destructive-git / **self-acceptance** / main-push / secrets / hook-bypass, unless authorized (FACT);
- **inlines the report fields** — line 57: AGENT TRACE / RISK PROFILE / WORKFLOW / FILES READ / FILES CHANGED / VERIFICATION EVIDENCE / NOT TOUCHED (FACT);
- **carries G1–G7 as active operating principles** — lines 235–299: collaboration-context (G1), **external-call evidence triple (G2)**, secret-handling (G3), process-env-snapshot (G4), **stub-fallback-detection (G5)**, **independent-review-for-critical-gates / no-self-acceptance (G6)**, **risk-based phase gates (G7)** (FACT);
- **carries complexity routing + subagent-brief requirements** — lines 206–224 (FACT).

The "NOT WIRED YET" string lives only in the protocol file's **stale 2026-05-14 frontmatter** (`trial_status: not-wired-yet`) and its body line 13. The retrospective (and its subagents) read that stale doc and concluded "unwired." **This is exactly the live-state-over-stale-doc failure the retrospective itself named (R-22) and the read-before-concluding rule (R-08).** It is caught here by reading the live file.

**Consequence:** ~85% of the GPT/retrospective recommendations are **already ACTIVE in CLAUDE.md.** The real remaining gap is small (3 narrow additions) + a doc-honesty fix.

---

## 1. SOURCE_INVENTORY

| Source | Exists | Read | Role | Confidence | Notes |
|---|---|---|---|---|---|
| `C:\Users\DEVELOPER\Claude\CLAUDE.md` (workspace) | YES | YES (full) | Active startup/governance surface | FACT | Already wires protocol + hard gates + G1–G7 + routing. **Sacred.** |
| `C:\Users\DEVELOPER\CLAUDE.md` (home) | YES | YES (full) | "Safety" file (project-location + secrets) | FACT | No governance; no change needed |
| `00-Meta/governance/claude-session-protocol.md` | YES | YES (frontmatter + earlier full) | The protocol CLAUDE.md points to | FACT | Frontmatter `not-wired-yet` is **STALE/false** vs live CLAUDE.md |
| `00-Meta/self-improvement/{pending,anti-patterns,patterns,lessons-learned}.md` | YES | YES (this session) | Quarantine + incident ledger | FACT | Designated quarantine surface for new rule candidates |
| gpt-handoff retrospective report (`bb99e4f`) | YES | YES (this session) | Prior gate output | FACT | Its "unwired" premise corrected here |
| Multi-chat GPT recommendation dossier | NO | — | Requested input | **NOT_AVAILABLE** | Not attached to this prompt; reconciled from the prompt's inline summary only; not invented |
| Master-OS docs (`AI-ENGINEERING-MASTER-OPERATING-SYSTEM_v1.1.txt`, `...SELF-CHECK-ADVERSARIAL-REVIEW.txt`) | referenced | NO | Authoritative engineering OS | **NOT_READ** | Referenced by protocol; not read this gate; not quoted |
| AIKB operating protocol | unknown | NO | Durable memory | NOT_READ (by design — read-only/approval) | No AIKB touch this gate |
| Vault `ClaudeOS/` folder (latest-report/summary) | YES | NO | Possible alternate reporting surface | INFER | Noted; gate specifies gpt-handoff path, so not touched |
| Report template | YES | YES | protocol §10 canonical template | FACT | Hardened schema proposed (not scattered) |

**Working area / git:** gpt-handoff `main` HEAD `bb99e4f` (in sync, 0/0). Vault `main` (messy working tree — operator's normal in-progress notes; **not committed** this gate). Product repos (`C:/Projects/InsuranceAIPlatform`, `Twincore-framework`) **not touched**.

---

## 2. RECONCILIATION_MATRIX (GPT recs × Claude retrospective)

Legend: **ACTIVE** = already wired in live CLAUDE.md (FACT). **PROPOSED** = rule candidate, not active. **CONDITIONAL** = fires only under stated profile. **QUARANTINE** = narrows a sacred rule → pending.md + 2nd OK.

| # | Theme | GPT recommendation | Claude position | Evidence | Decision | Priority | Applies to |
|---|---|---|---|---|---|---|---|
| 1 | Activate protocol | wire it into CLAUDE.md | **already wired** | CLAUDE.md:51 | **ACTIVE** (no action) | P0 | all |
| 2 | No DONE w/o evidence tuple | mandatory | accept; make it a report field (R-17) | not explicit in CLAUDE.md | ACCEPT_AS_P0 → **PROPOSED** (sacred) | P0 | all |
| 3 | No self-acceptance | mandatory | accept | CLAUDE.md G1, G6 | **ACTIVE** | P0 | serious gates |
| 4 | Subagent re-verify | mandatory | accept; build-verify + evidence tuple before merge | subagent-brief reqs CLAUDE.md:221-224; build-step PROPOSED | ACCEPT_AS_P0 → partial ACTIVE + **PROPOSED** | P0 | code |
| 5 | Semantic/persistence/negative E2E | mandatory | accept, **conditional (UI projects)** | labels not in CLAUDE.md | ACCEPT_CONDITIONAL → **PROPOSED** | P1 | UI/E2E |
| 6 | Numeric verification | mandatory | accept; reviewer-checked report field | PRE-TYPE in memory, not a CLAUDE field | ACCEPT_AS_P0 → **PROPOSED** | P0 | all |
| 7 | mock/fallback/live/real labels | mandatory | accept, conditional | partial via G2/G5; explicit 5-label taxonomy missing | ACCEPT_CONDITIONAL → **PROPOSED** | P1 | provider/DB/cloud |
| 8 | External-call evidence triple | mandatory everywhere | **MODIFY: external boundaries only, not every in-process call** | CLAUDE.md G2 (incl. "Skip when") | **ACTIVE** + Claude modify **PRESERVED** | P0 cond. | external calls |
| 9 | Risk profiles | adopt | accept | protocol §4 + CLAUDE.md routing 206-224 | **ACTIVE** | P0 | all |
| 10 | Opus / QA scope | Opus for ALL critical; /qa for ANY edit | **MODIFY: scope Opus by risk profile; /qa for MED+ or external-surface, not trivial** | narrows Tier-A sacred rules | **MODIFY → QUARANTINE** (NEEDS_OPERATOR_DECISION) | P1 | serious gates |
| 11 | Stakeholder triangulation | before autonomous work | **MODIFY: only externally-visible / irreversible / spend-generating** | A-022 was reversible | ACCEPT_CONDITIONAL, Claude modify **PRESERVED** | P1 | multi-stakeholder |
| 12 | AIKB vs gpt-handoff stale | keep AIKB fresh | live-repo > stale-doc (R-22) | **this gate is living proof** (stale protocol frontmatter) | ACCEPT_AS_P1 → propose frontmatter fix | P1 | all |
| 13 | Prompt templates | standardize | accept | retrospective §12 | ACCEPT_AS_P1 → **PROPOSED** (one central file) | P1 | all |
| 14 | Report schemas | standardize | accept; fuller §9/§10 schema | partial ACTIVE (CLAUDE.md report fields) | ACCEPT_AS_P1 → **PROPOSED** | P1 | all |
| 15 | Operator bug protocol | reproduce-first | accept; **reconcile with sacred parallel-questions rule** (answer side-Qs inline; only directional bug-claims defer) | R-11 | ACCEPT_AS_P1 → **PROPOSED** | P1 | code/UI |
| 16 | commit/push/Azure/provider gates | separate gates | accept | protocol §4 profiles + G7 + hard gates | **ACTIVE** | P0 | commit/push/cloud |

**Not blindly accepting GPT:** themes 8, 10, 11 are MODIFIED per Claude's evidence-based objections. **Not blindly accepting Claude:** themes 2, 6 are accepted as P0 despite being "more process," because the incidents (A-020, A-021) prove the harm.

---

## 3. CLAUDE_DISAGREEMENTS preserved (explicit)

| Disagreement | Status | Where it lands |
|---|---|---|
| Opus mandatory for ALL critical work → scope by risk profile | PRESERVED — MODIFY | QUARANTINE (narrows Tier-A `feedback_opus_supervisor_directive`) |
| /qa mandatory for ANY edit → MED+ or external-surface only | PRESERVED — MODIFY | QUARANTINE (narrows Tier-A `feedback_qa_department_required`) |
| External-call triple → external boundaries only | PRESERVED — already reflected in G2 "Skip when" | ACTIVE |
| Phase gates selected by risk profile, not in-context judgment | PRESERVED | ACTIVE (protocol §4) — emphasize profile is authoritative |
| Stakeholder triangulation → externally-visible/irreversible/spend only | PRESERVED — MODIFY | CONDITIONAL |
| Sacred-rule narrowing → quarantine, not direct activation | PRESERVED — enforced **in this very gate** (themes 10) | QUARANTINE |

No disagreement was overridden. The two that *narrow sacred Tier-A rules* are explicitly routed to pending.md + a 2nd Operator confirmation — **not** activated here.

---

## 4. RULE_ARCHITECTURE

**Tier A — UNIVERSAL_ALWAYS_ON** (every task, every project)
| Rule | Status |
|---|---|
| Context/gate declaration (RISK PROFILE/WORKFLOW/STOP/ALLOWED-FORBIDDEN) | **ACTIVE** (CLAUDE.md:53) |
| No secrets; secret observability = present/length/prefix only | **ACTIVE** (G3) |
| No product side-effects outside declared scope; STOP on forbidden scope | **ACTIVE** (hard gates) |
| No self-acceptance on serious gates (reviewer ≠ implementer) | **ACTIVE** (G1, G6) |
| No DONE/PASSED/CLEARED without a per-item evidence tuple | **PROPOSED** (R-17) |
| No hidden limitations (LIMITATIONS near top of report) | **PROPOSED** |

**Tier B — RISK_PROFILE_CONDITIONAL**
| Rule | Status |
|---|---|
| External-call evidence triple | **ACTIVE** (G2), external boundaries only |
| Risk-based phase gates; COMMIT-PROOF/PUSH-PROOF | **ACTIVE** (G7 + protocol §6/§7) |
| Independent review for externally-visible/irreversible gates | **ACTIVE** (G6) |
| Opus reviewer scope; /qa scope | **QUARANTINE** (sacred-narrowing) |
| Stakeholder triangulation | **CONDITIONAL/PROPOSED** |

**Tier C — PROJECT_TYPE_CONDITIONAL**
| Rule | Status |
|---|---|
| UI semantic E2E: MECHANICAL/SEMANTIC/PERSISTENCE/NEGATIVE pass labels | **PROPOSED** |
| DB persistence: fresh-context read-back | **PROPOSED** (partial via G-principles) |
| Provider MOCK/FALLBACK/LOCAL_REAL/LIVE_REAL/NOT_TESTED labels | **PROPOSED** (partial via G2/G5) |
| Cost telemetry; audit/cost docs | **CONDITIONAL** (per project) |

**Tier D — PENDING_QUARANTINE** (pending.md, 24h + 2nd OK before any activation)
- Narrowing of Opus-mandatory scope (theme 10).
- Narrowing of /qa-mandatory scope (theme 10).
- The 3 proposed CLAUDE.md additions (R-17 + UI labels + provider labels) — sacred surface.

---

## 5. ACTIVATION_STATUS

| Item | Status | Proof / reason |
|---|---|---|
| Protocol pointer in CLAUDE.md | **ACTIVE (pre-existing)** | CLAUDE.md:51 (FACT) — no action needed |
| Hard gates + declaration + report fields | **ACTIVE (pre-existing)** | CLAUDE.md:53-57 |
| G1–G7 operating principles | **ACTIVE (pre-existing)** | CLAUDE.md:235-299 |
| R-17 evidence-tuple-before-status-word | **PROPOSED_ONLY** | target = sacred CLAUDE.md → SUBPHASE-5 says proposed-only |
| UI semantic-pass labels | **PROPOSED_ONLY** | sacred surface / project-conditional |
| Provider 5-label taxonomy | **PROPOSED_ONLY** | sacred surface; partially covered by G2/G5 |
| Protocol frontmatter honesty-fix | **PROPOSED_ONLY** | vault-main governance doc; conservative gate posture |
| pending.md quarantine entries | **PROPOSED_ONLY** | vault-main; queued as proposed patch |
| Opus/QA scope narrowing | **NEEDS_OPERATOR_DECISION** | narrows Tier-A sacred rules |

**Nothing was activated by this gate** because the activation the gate sought was **already present** (FACT), and every remaining change targets a sacred file or vault-main. This is the honest, SUBPHASE-5-compliant outcome (`PROTECTED → proposed patch, do not bypass`).

---

## 6. PROPOSED PATCHES (exact, not applied)

### Patch A — `00-Meta/governance/claude-session-protocol.md` frontmatter honesty-fix
```diff
- status: trial-ready
- trial_status: not-wired-yet
+ status: active
+ trial_status: wired-via-claude-md-pointer
  updated: 2026-05-14
+ # updated when wiring confirmed: CLAUDE.md "## Session protocol" section points here
```
And body line 13: `> **Status: TRIAL READY / NOT WIRED YET.**` → `> **Status: ACTIVE — wired via the CLAUDE.md "Session protocol" pointer.**`
*Rationale:* the doc currently misstates reality (a log-honesty defect, cf. R-18/R-19). Non-sacred file. Reversible.

### Patch B — `C:\Users\DEVELOPER\Claude\CLAUDE.md` minimal extension (SACRED — needs explicit per-file OK)
Append three lines to the existing `## Session protocol` block (additive, not a rewrite):
```diff
  > **Report must include:** `AGENT TRACE` / ... / `NOT TOUCHED`.
+ >
+ > **No `DONE`/`PASSED`/`CLEARED`** without a per-item evidence tuple (`path:line` / `command + exit` / `N/M`); else status = `IN PROGRESS`.
+ > **UI create/open/update:** report `MECHANICAL_PASS` / `SEMANTIC_PASS` / `PERSISTENCE_PASS (reload)` / `NEGATIVE_PASS`.
+ > **Provider/DB/cloud calls:** label `MOCK` / `FALLBACK` / `LOCAL_REAL` / `LIVE_REAL` / `NOT_TESTED`.
```
*Rationale:* the only genuinely-new universal rules. Sacred surface → proposed-only.

### Patch C — `00-Meta/self-improvement/pending.md` quarantine entries (vault-main → proposed)
Add quarantine candidates (24h + 2nd OK): (1) Opus-scope narrowing, (2) /qa-scope narrowing, (3) the Patch-B additions. Each with `Quarantine until: <date+1>` and "narrows Tier-A sacred rule — needs 2nd Operator confirm."

---

## 7. VALIDATION

| Check | Result |
|---|---|
| Changed files only allowed governance/handoff files | ✅ only this gpt-handoff report (4 files) |
| Product repos untouched | ✅ (read 0 product files) |
| AIKB untouched | ✅ |
| Azure/provider untouched | ✅ |
| Secrets scan clean | ✅ (no secrets/keys/PII; person name → "Operator") |
| Active protocol referenced or proposed-only reason clear | ✅ pointer ACTIVE (proven CLAUDE.md:51); additions proposed-only (sacred) |
| No broad rewrite | ✅ (zero vault/sacred edits) |
| No sacred/hook bypass | ✅ |
| Every ACTIVATED rule actually wired | ✅ (each cites CLAUDE.md line) |
| Every non-wired rule labeled PROPOSED/CONDITIONAL/QUARANTINE | ✅ |

## 8. LIMITATIONS

- GPT multi-chat dossier **NOT_AVAILABLE** (not attached); reconciliation used the prompt's inline summary only.
- Master-OS docs **NOT_READ** this gate.
- The "already-active" conclusion rests on the live workspace CLAUDE.md (FACT, read in full); a session that loads a *different* CLAUDE.md would need re-verification.
- All proposed patches are **unapplied**; activation of the sacred-surface Patch B needs explicit per-file Operator OK; the two scope-narrowings need pending.md quarantine + 2nd confirm.
- Person names sanitized to roles for this public repo.

## 9. NEXT GATES (not auto-started)
1. `GPT_HANDOFF_AND_GPT_AUDIT_SCHEMA_HARDENING_V0.1`
2. `SEMANTIC_E2E_STANDARD_HARDENING_V0.1`
3. `AIKB_AND_TEMPLATE_SYNC_AFTER_OS_HARDENING_V0.1` (could also apply Patch A frontmatter fix + Patch C pending entries)
4. `INSURANCE_AI_PRODUCT_QUALITY_NEXT_GATE_SELECTION_V0.1`
5. (suggested) `APPLY_CLAUDE_MD_EVIDENCE_GATE_PATCH_V0.1` — apply Patch B after explicit sacred-file OK.

---

### Final report block

```
VERDICT: ACCEPT_WITH_LIMITATIONS (core already ACTIVE; new items PROPOSED_ONLY — sacred-file protection)
GATE: CLAUDE_FUTURE_PROJECTS_OS_HARDENING_V0.2
WORKING_AREA: gpt-handoff (publish) + read-only vault governance
BRANCH: gpt-handoff main
HEAD_BEFORE: bb99e4f
HEAD_AFTER: <set on commit>
FILES_READ: CLAUDE.md (workspace+home), claude-session-protocol.md, pending/anti-patterns/patterns/lessons, retrospective report, git states
FILES_CHANGED: only this gpt-handoff report (report.md, summary.json, latest-report.md, latest-summary.json)
FILES_NOT_TOUCHED: CLAUDE.md (sacred), claude-session-protocol.md, pending.md, AIKB, product repos, Azure, vault-main
ACTIVATION_STATUS: protocol pointer + hard gates + G1-G7 = ACTIVE (pre-existing, FACT); 3 additions + frontmatter fix + scope-narrowings = PROPOSED_ONLY / QUARANTINE
SECRETS_SCAN: CLEAN
FORBIDDEN_SCOPE_CHECK: no product / Azure / provider / secrets / sacred-overwrite / main-vault / hook-bypass / hidden-activation
NEXT_SAFE_STEP: Operator/GPT audit via "отчёт"; then optional patch-apply gates
STOP_LINE: stop after report; no rule activation; no AIKB; no next gate
```
