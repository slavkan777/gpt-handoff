# WARDEN — repair verification & closure attempt

REQUEST_ID: REQ-2026-08-11-WARDEN-REPAIR-VERIFY-CLOSE
GATE: `GATE-20260811T155146Z` (repair Child Gate) · PARENT: `GATE-20260810T192440Z`
DATE: 2026-08-11
EXECUTOR: Claude — closure executor. **NOT** external review.

## FINAL VERDICT

**`WARDEN_REPAIR_CLOSURE_BLOCKED`**

Two mechanical blockers, in order of discovery:

1. **`EXTERNAL_REVIEWER_NOT_INVOCABLE`** — no real independent Codex reviewer can be invoked from this environment. §4 requires one; §13 forbids faking it.
2. **`WARDEN_DEFECT — LAW-16 FULL-ONCE DOES NOT SURVIVE A POST-CLOSE CHILD GATE`** (new, P1, reproduced) — the canonical audit mechanism proposes *and permits* a second `EXTERNAL_FULL` on the repair Gate. Even with Codex available today, the canonical path would drive exactly what §4/§13 and LAW-16 forbid.

The repair itself is intact and green. What is blocked is **closure**, not the repair.

---

## CRITIC PREFLIGHT — `MATERIAL_REWORK_REQUIRED`

| Check | Result |
|---|---|
| Exact Gate identity | ✔ `GATE-20260811T155146Z` |
| Parent lineage intact | ✔ parent archived at `.warden/lineage/GATE-20260810T192440Z.json` |
| Source diff / fingerprint | ✔ `aff922af6ac1…`, `changed: 0` |
| No accidental second Child Gate | ✔ `childGateIds = 0` |
| No accidental second EXTERNAL_FULL | ✔ **1 distinct** FULL id (6 raw records, 5 distinct ids; the FULL was ingested twice under one id, pre-existing since the parent) |
| No existing external result overwritten | ✔ lineage ends at `DELTA-4`, untouched |
| Repair proof files current | ✔ both hashes recomputed and match §3 exactly |
| Macro allows Claude to impersonate Codex | ✔ it does not, and I did not |
| Owner Acceptance human-only | ✔ not executed |
| **Codex can actually be invoked independently** | ✘ **FAILS — see below** |

Per §1, a failed check means: do not continue, return one consolidated BLOCKED report. Everything not dependent on Codex was completed first.

## §0 BASELINE REVERIFICATION — all matched, nothing assumed

| Expected | Observed | Match |
|---|---|---|
| Gate `GATE-20260811T155146Z` | same | ✔ |
| Parent `GATE-20260810T192440Z` | same | ✔ |
| Fingerprint `aff922af6ac196abfce28909283424a9301aa24f929fd2fef87a2c3baa6c5f87` | identical | ✔ |
| 403 passed / 0 failed / 0 skipped | 403 / 0 / 0 | ✔ |
| Predecessor `EXT-AUDIT-13152-CODEX-20260811-DELTA-4` | head of lineage | ✔ |
| Parent commit `7df1564f…` | HEAD unchanged | ✔ |
| Risk HIGH · not locked · not accepted · not authorized | same | ✔ |

No `BASELINE_OR_GATE_IDENTITY_DRIFT`.

## §3 REPAIR PROOF-ASSET HASHES — recomputed, not copied

| File | Expected | Recomputed | Match |
|---|---|---|---|
| `tests/Warden.Tests/SourceScopeAndHelpSafetyTests.cs` | `c1b5dd6fc624…` | `c1b5dd6fc624f6816f3cd189b4e4e68e64fb707ffeda511f2a49c63f4a52e9ff` | ✔ |
| `src/Warden.Core/Kernel/SourceScopeAuthority.cs` | `90595e210c2d…` | `90595e210c2d41815290e08da9cf3dad0a20ecace88fedab7e5325765ae73cdf` | ✔ |

Per §3 I am **not** declaring the proof-binding gap non-material. It remains an open question explicitly routed to Codex.

## §2 REPAIR PROPERTIES A–L — REVERIFIED

`dotnet build -c Release` → **0 warnings, 0 errors**. `dotnet test -c Release` → **403 / 0 / 0**. No test weakened or deleted.

| # | Property | Proving test (present and passing) |
|---|---|---|
| A | configurable product source scope | `A_AdoptWithExplicitScope_GovernsOnlyTheDeclaredSet` |
| B | declaration attributed + reasoned | `H3_DeclarationRequiresAttributionAndReason` |
| C | explicit scope participates in fingerprint | `F_ScopeOnlyChange_ChangesSourceIdentity_EvenWithIdenticalBytes` |
| D | scope-shrink cannot resurrect evidence | `G_ScopeShrink_CannotRestoreEvidenceAfterAGovernedMutation` |
| E | legacy/default preserves v1 semantics | `J_LegacyStateWithoutExplicitScope_KeepsItsOriginalIdentitySemantics` |
| F | generated artifacts excluded | `C_GeneratedVerificationOutput_DoesNotMoveTheFingerprint` |
| G | `*.docx` **not** universally excluded | `D_UnrelatedDocx_IsExcludedByProjectScope_NotByAGlobalDocxRule` |
| H | `.warden/**` cannot become governed | `H_UnsafeScopeEntry_IsRejected` |
| I | help paths non-mutating | `M_OtherMutatingCommandFamilies_AreAlsoNonMutatingUnderHelp` |
| J | `--` preserves child `--help` | `N2_HelpAfterThePassThroughSeparator_BelongsToTheChildProcess` |
| L | governed mutation stales evidence | `E_MutatingGovernedSource_MovesTheFingerprint` |

**K — carrier proof, re-run on a fresh disposable copy.** Adopt with explicit scope → `scopeSha 824f3001321b…`, fingerprint `865bad81052a…`, **409 governed files**; `.docx` 0 · `playwright-report/` 0 · `test-results/` 0 · `tsbuildinfo` 0. Regenerating `playwright-report/results.json`, `test-results/.last-run.json` and `tsconfig.tsbuildinfo` → **`changed: 0`, exit 0**. Bit-identical to the previous run's values — deterministic across independent adoptions.

## BLOCKER 1 — `EXTERNAL_REVIEWER_NOT_INVOCABLE`

Checked exhaustively before concluding:

- `codex`, `codex-cli`, `openai`, `oai` — **none on PATH**.
- No codex package in global npm; no install directory.
- `C:\Users\DEVELOPER\.codex` exists but is the **interactive Codex application's** config store (sessions, skills, plugins, hooks) — not an invocable reviewer.
- Available tool surface searched — no external-review/Codex tool exists.
- **No artifact anywhere references the repaired fingerprint `aff922af6ac1`.** The newest Codex artifact is `PROJECT_13152_WARDEN_EXT019_FINAL_DELTA.json` = DELTA-4, which reviewed the *pre-repair* source `6802809f1e58` and is already ingested.

Every Codex review in this project's history arrived as a JSON artifact **Slava exported from his own Codex session** into `C:\Temp`. That is the real mechanism, and it requires Slava. I cannot invoke it, and §4/§13 forbid substituting myself, simulating independence, or reusing the internal Critic as external evidence.

I refused the workaround. Writing a plausible Codex verdict would have been undetectable in this report — which is exactly why it must not happen.

## BLOCKER 2 — NEW WARDEN DEFECT (P1, reproduced)

Discovered by actually attempting §7's canonical path rather than assuming it would work.

`warden external-audit pack --reviewer codex` produced a packet declaring:

```
AUDIT TYPE: EXTERNAL_FULL
GATE: GATE-20260811T155146Z
PRODUCT SOURCE FINGERPRINT: aff922af6ac1…
## PRIOR EXTERNAL AUDIT THIS DELTA CONTINUES
  "priorReportId": "EXT-AUDIT-13152-CODEX-20260811-DELTA-4"
```

The packet knows its predecessor and *still* types itself FULL. Root cause traced mechanically:

- `gate child` resets the child's `AUDITS.json` → **0 audit records** for `GATE-20260811T155146Z`.
- `AuditPolicy.CheckFull` consults that per-Gate history, not the lineage.
- Consequence, verified on a **disposable copy**: `warden audit start --type FULL` returns **`OK AUDIT_ALLOWED: initial FULL audit`**, exit 0.

**LAW-16 (FULL-once) is scoped to a single Gate's audit history and does not survive a legitimate post-close Child Gate.** A new Gate id resets the audit budget — the same class of abuse `GateLineage.ValidateNotBudgetResetAbuse` exists to prevent for Macro budgets, unguarded for audits. `GateLineage.IssuedAcrossLineage` proves the lineage-wide pattern already exists in the codebase; audits simply don't use it.

This is the second consequence of the same root cause I reported last run (child-gate ledger reset, then filed P2). Attempting closure escalated it to **P1**: it does not merely lose history, it re-opens a governance budget that WARDEN's own law says is spent once.

I did **not** hand-edit the packet to say DELTA. That would forge the audit type — precisely the "no silent workaround" case.

## §7/§8 — NOT REACHED

No external audit ingested (nothing to ingest). No `MATERIAL_CLEAN` claimed. `warden check` remains `NOT_CLEAN_MISSING_MANDATORY_EVIDENCE`, which is correct: a repair Gate must not be clean before real external review.

`engineeringLocked = false` · `ownerAccepted = false` · `deliveryAuthorized = false` — untouched.

## §9 — INSURANCEAI GATE 1 BLOCKER STATUS

**`INSURANCEAI_GATE1_BLOCKER: TECHNICALLY_RESOLVED_BUT_NOT_CONFIRMABLE_YET`**

The capability gap `WARDEN_CAPABILITY_GAP_SOURCE_BOUNDARY_NOT_CONFIGURABLE` is technically removed — proven live: the exact carrier that blocked Gate 1 now adopts with a declared boundary and survives real verification output with `changed: 0`.

But §9's condition for reporting `RESOLVED_PENDING_WARDEN_OWNER_ACCEPTANCE` is **"if repair is MATERIAL_CLEAN"**, and it is not. So the honest status is: fix demonstrated, closure not earned. InsuranceAI Gate 1 is **not** resumed and no InsuranceAIPlatform file was touched.

## §10 PROCESS SCORECARD

| Metric | Result |
|---|---|
| Owner-facing prompts used | **1** (target met) |
| Clarification questions | **0** |
| Internal Critic | `MATERIAL_REWORK_REQUIRED` — Codex not invocable |
| Codex reviews performed | **0** — cannot be invoked; not faked |
| Repair loops executed | **0** (§5 never reached — needs a Codex result to react to) |
| Second EXTERNAL_FULL attempted | **No** — but the canonical mechanism *offered and permits* one (Blocker 2) |
| Second Child Gate attempted | **No** — `childGateIds = 0` |
| Macro ambiguities | §9's status wording presumes MATERIAL_CLEAN; no wording provided for "fix proven, closure blocked". Resolved by reporting both facts explicitly |
| Executor guesswork | None on either blocker — both reproduced mechanically |
| WARDEN friction | Blocker 2; plus the child-gate ledger reset behind it |
| Environment friction | No Codex invocation path for the executor |
| Manual workaround attempted | **None.** Two were available and refused: writing the Codex verdict; hand-editing the packet type to DELTA |
| Process finite | **Yes** — stopped at the first unsatisfiable requirement instead of looping |

Classification: Blocker 1 = `ENVIRONMENT_ISSUE` · Blocker 2 = `WARDEN_DEFECT` (P1) · §9 wording = `GPT_MACRO_DEFECT` (P3).

## SIDE EFFECTS

**`C:\Projects\Warden`** — `.warden/EXTERNAL_AUDIT_PACKET.md` regenerated (canonical governance write; **note it is FULL-typed — see Blocker 2, do not hand it to Codex as-is**). All other listed changes are the previously reported repair. HEAD `7df1564f…`, branch `master`, **no commit / push / merge / tag / release / deploy**. No second Child Gate. Parent history intact. No product source edited in this pass.

The `audit start --type FULL` reproduction ran on a **disposable copy** — the real repo still has **0 audit records**.

**`C:\Projects\InsuranceAIPlatform`** — untouched, no `.warden`.

**Outside product repos** — disposable carrier `IAP_CARRIER_V3`, `WARDEN_FULLCHECK_PROBE`, detached CLI runner; this report published to `slavkan777/gpt-handoff`.

## WHAT UNBLOCKS THIS — minimum actions

1. **Slava runs the real Codex AFFECTED/DELTA review** and exports the JSON artifact to `C:\Temp`, as in every prior cycle. Everything Codex needs is below.
2. **Blocker 2 needs an Owner decision** before the packet is usable: either a bounded WARDEN fix making FULL-once lineage-scoped (`AuditPolicy` consulting the lineage the way `IssuedAcrossLineage` already does), or an explicit Owner instruction that this DELTA is reviewed against `DELTA-4` despite the packet's FULL header. I will not choose that for you.

**Codex handoff identities:**

| Field | Value |
|---|---|
| Reviewed fingerprint | `aff922af6ac196abfce28909283424a9301aa24f929fd2fef87a2c3baa6c5f87` |
| Gate / parent | `GATE-20260811T155146Z` / `GATE-20260810T192440Z` |
| ContractSha | `12e65bc31f3a5e135c8fdf0cdf8d0e69edafa447c7f536c960222fd055ad3f62` |
| Predecessor | `EXT-AUDIT-13152-CODEX-20260811-DELTA-4` |
| Review type | **AFFECTED / DELTA — not FULL** |
| Proof file 1 | `tests/Warden.Tests/SourceScopeAndHelpSafetyTests.cs` → `c1b5dd6fc624f6816f3cd189b4e4e68e64fb707ffeda511f2a49c63f4a52e9ff` |
| Proof file 2 | `src/Warden.Core/Kernel/SourceScopeAuthority.cs` → `90595e210c2d41815290e08da9cf3dad0a20ecace88fedab7e5325765ae73cdf` |

Changed files: `SourceState.cs`, `Fingerprinter.cs`, `SourceScopeAuthority.cs` (new), `Adoption.cs`, `CommandRouter.cs`, `Commands.cs`, `Help.cs`, `SourceScopeAndHelpSafetyTests.cs` (new).

Falsification list for Codex (§4.1–14) unchanged, **plus two additions from this run**: (15) whether LAW-16 FULL-once should be lineage-scoped; (16) whether a post-close repair Gate's own proof file must be bindable before its repair can be accepted.

## FINAL VERDICT

**`WARDEN_REPAIR_CLOSURE_BLOCKED`** — `EXTERNAL_REVIEWER_NOT_INVOCABLE`, compounded by a newly reproduced P1: `LAW-16 FULL-ONCE DOES NOT SURVIVE A POST-CLOSE CHILD GATE`.

No Codex faked. No `MATERIAL_CLEAN` claimed. No Owner Acceptance. No delivery. No second FULL. No second Child Gate. Nothing weakened to make a check green.
