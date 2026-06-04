REQUEST_ID: REQ-2026-06-05-aikb-project-specific-bridge-rule-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: aikb-governance
PROJECT: AIKB
GATE: PROJECT_SPECIFIC_BRIDGE_RULE_V0.1
COMPLETED: 2026-06-05
COMPLETED_BY: claude

## Current State

The project-specific bridge rule is now **durably fixed and enforced** in AIKB (`slavkan777/ai-kb`).
A control rule already existed (`00_CONTROL_CENTER/PROJECT_BRIDGE_PROTOCOL.md`); this gate made it
**authoritative and unmissable**: a MANDATORY/ENFORCED banner + a 5-point TL;DR were added at the
top of the rule, and short cross-references were added to the control-center docs that drive every
bootstrap and every `отчёт`. No large text was duplicated. Changes are committed and pushed to
`ai-kb` (commit `b9024c5`). No source/product repo, Azure, or secret was touched.

## DONE criteria vs evidence

| Criterion | Status | Evidence |
|---|---|---|
| AIKB clearly says project-specific bridge is PRIMARY | ✅ | `PROJECT_BRIDGE_PROTOCOL.md` TL;DR #1 + banner; INDEX entry; START_HERE (pre-existing) |
| Global bridge is fallback only | ✅ | TL;DR #2 ("router / fallback / compatibility ONLY"); echoed in INDEX, OPERATING_PROTOCOL, AUTO_USE |
| `отчёт` checks project-specific report FIRST | ✅ | `OPERATING_PROTOCOL.md` step 1 (project-specific first, global fallback); `MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md` `отчёт` line; protocol "Audit rule" |
| REQUEST_ID / PROJECT / GATE mismatch ⇒ STALE_OR_WRONG_REPORT | ✅ | TL;DR #5; OPERATING_PROTOCOL step 2; startup line; INDEX entry — phrase present in all 4 + the protocol |
| Parallel projects cannot overwrite each other's channel | ✅ | TL;DR #3 ("each project owns `gpt-handoff/<Project>/_BRIDGE/` … cannot overwrite each other's ACTIVE_REQUEST or LATEST_REPORT") |
| Report what files changed | ✅ | see below |

## What Changed (ai-kb — 5 files, all 00_CONTROL_CENTER, +23/−6, additive)

| File | Change |
|---|---|
| `PROJECT_BRIDGE_PROTOCOL.md` | **Strengthened** (not duplicated): added MANDATORY/ENFORCED banner (v1.1, dated) + a 5-point TL;DR "railroad" making primary/fallback/parallel-isolation/`отчёт`-order/mismatch-contract explicit |
| `INDEX.md` | Added the protocol to "Active operating rules" catalog + a pointer in the Evidence-channel section |
| `OPERATING_PROTOCOL.md` | `отчёт` flow now reads project-specific bridge first, verifies `REQUEST_ID/PROJECT/GATE`, returns `STALE_OR_WRONG_REPORT` on mismatch; points to the protocol |
| `MANDATORY_NEW_CHAT_STARTUP_INSTRUCTION.md` | `отчёт` command line upgraded to project-specific-first + match-check + protocol pointer |
| `AUTO_USE_RULE.md` | Short pointer: per-project bridge primary, global fallback — see protocol |

`START_HERE.md` already linked the protocol (lines 11, 41-42, 76, 86) — left intact.

## Verification (personally checked)

| Check | Result |
|---|---|
| Diff scope | `git diff --stat` → **5 files, all `00_CONTROL_CENTER`**, +23/−6 |
| "Project-specific bridge is PRIMARY" | present in protocol |
| "fallback" | 6 occurrences in protocol |
| `STALE_OR_WRONG_REPORT` | present in all 4 referencing files + protocol |
| "cannot overwrite each other" | present (parallel isolation) |
| `отчёт` project-first | present in OPERATING_PROTOCOL |
| Commit + push | `ai-kb` `244dfab..b9024c5`, working tree back in sync |

## This report demonstrates the rule

This very report is published to **AIKB's own project-specific bridge** —
`gpt-handoff/AIKB/_BRIDGE/LATEST_REPORT.md` (newly created) + `gpt-handoff/AIKB/latest-report.md`
+ the versioned `gpt-handoff/AIKB/project-specific-bridge-rule-v0.1/report.md` — leaving the global
`gpt-handoff/_BRIDGE/` and every other project's bridge untouched.

## Boundaries Honored

- **No source / product repo change** (only the `ai-kb` knowledge base + the gpt-handoff report).
- **No Azure, no secrets, no PII.** No unrelated project edited (InsuranceAIPlatform / TwinCore bridges untouched).
- **No force-push** — `ai-kb` was clean + in sync; additive ff commit. Existing AIKB structure preserved; no large text duplicated.
- Global `gpt-handoff/_BRIDGE/` untouched.

## Next Safe Step

GPT audit on `отчёт` (read `gpt-handoff/AIKB/_BRIDGE/LATEST_REPORT.md`). From now on, every serious
project routes through its own `gpt-handoff/<Project>/_BRIDGE/`; global is fallback/router only.

## Not Touched

Product/source repos; Azure; secrets; other projects' bridges; global `gpt-handoff/_BRIDGE/`;
`ai-kb` files outside the 5 listed.
