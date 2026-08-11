# WARDEN — Warden-Lab repair: source-scope + CLI help safety

REQUEST_ID: REQ-2026-08-11-WARDEN-LAB-REPAIR-SOURCE-SCOPE
LOGICAL LABEL: `WARDEN-LAB-REPAIR-SOURCE-SCOPE-AND-CLI-HELP-V1`
DATE: 2026-08-11
EXECUTOR: Claude — bounded repair executor. NOT external review.

## A. FINAL VERDICT

**`WARDEN_REPAIR_READY_FOR_CODEX_AFFECTED_REVIEW`**

Findings A–D are repaired in current source, proven by 31 new automated tests, the full suite (403/403), and a disposable real-carrier probe that reproduces the original failure and shows it gone. Real external review remains correctly **pending**. Nothing is self-accepted and no `MATERIAL_CLEAN` is claimed.

## B. CRITIC PREFLIGHT

**CRITIC_VERDICT: `PASS`** — one Owner prompt, zero clarifications.

All 16 checks were answered against real code and real command behaviour, not against the prompt. Highlights:

| # | Check | Finding |
|---|---|---|
| 3 | Child Gate openable canonically? | Yes — `gate child --boundary --reason`, `GateLineage.CanCreateChild`. Verified before use |
| 4 | Fixes the class, not the carrier? | Yes — configurable boundary is general; only genuinely universal generated artifacts added to defaults |
| 5 | Scope-shrink attack possible? | Addressed by binding scope identity into the fingerprint — and proven by test G and the carrier probe |
| 9 | `*.docx` as a universal exclusion? | **Rejected**, as the Macro required. A `.docx` can be real product content; test D asserts it is *not* in the defaults |
| 12 | Legacy state still checkable? | This is the one real design tension — see below |
| 16 | Stops before claiming external-clean? | Yes — verdict is READY_FOR_CODEX |

**Design tension worth stating plainly (Critic check 12 vs §7.4).** §7.4's preferred invariant is `Fingerprint = H(scope + files)`. Applied unconditionally that would change the accepted v1 baseline's recorded fingerprint and break §7.7 legacy compatibility. Resolution: **the scope term is bound in only when a boundary was explicitly declared.** A legacy/default-scope capture hashes byte-for-byte as WARDEN v1 did (test J recomputes the pre-repair formula literally and asserts equality); an explicit boundary gets full anti-shrink identity. Both requirements are met without a schema migration, and declaring a scope over legacy state is itself an identity change (test J2), which is the correct governance semantics.

**Macro defects found: none material.** The Macro was executable end-to-end.

## C. PARENT BASELINE / CHILD REPAIR GATE

Baseline verified mechanically before any edit — not assumed:

| Fact | Value |
|---|---|
| Repo / branch / HEAD | `C:\Projects\Warden` · `master` · `7df1564f140524c0646631ceffe654ced0b18b11` ✔ |
| Upstream ahead/behind | 0 / 0 |
| Product source modified | **0 files** outside `.warden/` |
| `source diff` | `6802809f1e58` → `changed: 0` ✔ |
| Parent Gate | `GATE-20260810T192440Z` · MATERIAL_CLEAN · locked · accepted · delivered ✔ |
| Pre-existing `.warden` dirt | exactly the 5 previously disclosed delivery-authorization files |

**Child Gate opened canonically:**

```
warden gate child --boundary MATERIAL_ARCHITECTURAL_INVALIDATION --reason "<lab reason>"
→ CHILD_GATE_ALLOWED
→ child Gate GATE-20260811T155146Z created
  parent       : GATE-20260810T192440Z (archived, immutable)
  root outcome : 51b839ddc42c… (INHERITED - budgets do not reset)
  boundary     : MATERIAL_ARCHITECTURAL_INVALIDATION
```

Risk raised with attribution: `RISK-002: MEDIUM → HIGH`, by Owner, reason recorded; WARDEN emitted `RISK-BASELINE` first, honestly labelling the prior MEDIUM as a migrated baseline with no decider claimed. HIGH carries `requiresExternalPlatformReview = True`.

The parent was archived to `.warden/lineage/`, and I additionally backed up the full pre-repair `.warden` to scratchpad before opening the child — parent history is recoverable independently of the mechanism.

## D. TRIGGER FINDINGS REPRODUCED

Confirmed against current source before repairing:
- `Adoption.Adopt` called `Fingerprinter.Capture(repoRoot, null, clock)` — scope hardcoded null.
- `SourceCapture` used `previous?.Scope ?? new FingerprintScope()`.
- No CLI surface for the boundary; every `--paths` belongs to requirements/evidence/audit/break-glass.
- `DefaultExclusions` omitted `playwright-report/`, `test-results/`, `*.tsbuildinfo`.
- `-h`/`--help` recognised **only** at `argv[0]`, so `adopt --help` fell through to the adopt handler.

## E. SOURCE-SCOPE ARCHITECTURE IMPLEMENTED

Smallest coherent surface consistent with existing conventions:

- **`FingerprintScope`** gains `IsExplicit`, `DeclaredBy`, `DeclaredReason`, `DeclaredAtUtc`, `ComputeScopeSha()`, plus `Canonicalize` / `CanonicalizeRoots`.
- **`SourceScopeAuthority`** (new kernel class) — validates and builds declarations. Codes: `SOURCE_SCOPE_ALLOWED`, `_DENIED_UNATTRIBUTED`, `_DENIED_NO_REASON`, `_DENIED_EMPTY_GOVERNED_SET`, `_DENIED_ESCAPES_REPOSITORY`, `_DENIED_ABSOLUTE_PATH`, `_DENIED_GOVERNS_CONTROL_PLANE`.
- **`SourceState.SourceScopeSha`** persisted on every capture.
- **CLI**: `warden source scope --governed a,b [--exclude x,y] --by who --reason why`; `adopt`/`init` accept `--governed/--exclude/--scope-by/--scope-reason` applied **before** the first capture.
- **`Adoption.Adopt`** takes an optional `scope` and captures with it.

Deliberate properties:
- Declaration is **attributed and reasoned**, exactly like a risk decision — choosing what the product *is* decides what every PASS binds to.
- Declared exclusions are **additive** to the built-in defaults, so declaring a project boundary cannot silently drop generated-artifact protection (test O2).
- Governing `.warden/**` is **refused** — product and control identity must stay separate (0A-02).
- Governed roots normalise `src`, `src/`, `./src/` identically; exclusion **patterns** keep their trailing slash because `bin/` means the directory and rewriting it would change what is matched.

## F. SCOPE IDENTITY / ANTI-SHRINK PROOF

`ProductSourceFingerprint = H(domain, {scope: scopeSha, files: manifest})` when explicit; `H(domain, manifest)` when not.

Proven mechanically:
- **Test F** — two scopes with an *identical* governed file set but different declarations produce different `SourceScopeSha` **and** different fingerprints.
- **Test G** — capture → mutate governed file → shrink scope to exclude it: the fingerprint never returns to the proven value; restoring the original bytes under the shrunk scope still does not resurrect it.
- **Carrier probe §9.8** — live attack, below.

## G. DEFAULT EXCLUSION CHANGES

Added exactly three, all universally tool-generated verification output:
`playwright-report/`, `test-results/`, `*.tsbuildinfo`

Explicitly **not** added: `*.docx` (test D asserts its absence), and no ambiguous generated-looking JS/config. Project-specific artifacts are the job of the explicit scope.

The code comment records why this is the same defect class as `.tools/` (EXT-13152-009): that repair fixed the instance for WARDEN's own repo; this generalises it.

## H. CLI HELP-SAFETY REPAIR

`CommandRouter.Run` now intercepts `--help`/`-h` after the verb is known and **before** Dispatch, option side effects or any control-directory creation. Because parsing is shared, this covers the whole mutating surface, not just `adopt`. `Help.PrintFor` adds focused per-command help (WARDEN v1 had none).

One subtlety handled explicitly: scanning **stops at `--`**. In `warden evidence exec ... -- dotnet build --help` the trailing flag belongs to the child process being measured; swallowing it would silently turn a real evidence run into a no-op — a false PASS. Test N2 pins both directions.

## I. AUTOMATED TEST EVIDENCE

`dotnet build -c Release` → **0 warnings, 0 errors**.
`dotnet test -c Release` → **403 passed / 0 failed / 0 skipped** (was 372; **+31**).

Matrix coverage: A initial-adopt scope · B persistence/reuse · C+C2 generated artifacts (explicit and legacy) · D `.docx` via project scope, not globally · E governed mutation stales · F scope-only change stales · G shrink attack defeated · H/H2/H3 unsafe + unattributed declarations refused · I ordering/duplicate determinism · J+J2 legacy semantics preserved · K/L `adopt --help`/`-h` non-mutating · M six mutating families non-mutating · N empty-dir help creates nothing · N2 pass-through separator · O EXT-009 defaults intact · O2 defaults not droppable · plus two end-to-end `source scope` command tests.

No existing test was weakened or deleted.

**Proof-asset identity (§8 closing requirement).** All 7 proof assets bound by the inherited support contract still match their frozen bytes exactly — this repair added a test file rather than editing any bound proof:

| Bound proof asset | frozen | live | match |
|---|---|---|---|
| `tests/Warden.Tests/ContractGroundingTests.cs` | `a11fe73d779e` | `a11fe73d779e` | ✔ |
| `tests/Warden.Tests/ControlAndSecurityTests.cs` | `aca246234e3b` | `aca246234e3b` | ✔ |
| `tests/Warden.Tests/EvidenceTests.cs` | `df68f2050b4b` | `df68f2050b4b` | ✔ |
| `tests/Warden.Tests/FindingLifecycleTests.cs` | `d8e9fa8ff46c` | `d8e9fa8ff46c` | ✔ |
| `tests/Warden.Tests/IdentityTests.cs` | `c2ffb685acb8` | `c2ffb685acb8` | ✔ |
| `tests/Warden.Tests/StateMachineAndLineageTests.cs` | `03d18f758408` | `03d18f758408` | ✔ |
| `tests/Warden.Tests/TerminationTests.cs` | `5191b87adc6d` | `5191b87adc6d` | ✔ |

**Candidate WARDEN gap, reported rather than silently downgraded (§8/§10).** The repair's own proof-bearing file **cannot** be bound through the canonical support-contract mechanism at this Gate. `gate child` reset the child's `supportContractSha256` (`frozen support sha : (NOT FROZEN)`), and the on-disk contract is the parent's 105 criteria — none of which reference the new tests. Binding would first require materialising repair-specific acceptance criteria on the child Gate, i.e. a SPEC/contract activity this repair Macro does not authorise and §6 warns against as scope expansion.

So the EXT-019 protection does **not** currently extend to a post-close repair Gate's own new proof. Exact bytes are published below for the reviewer instead:

| Repair proof-bearing file | SHA-256 |
|---|---|
| `tests/Warden.Tests/SourceScopeAndHelpSafetyTests.cs` | `c1b5dd6fc624f6816f3cd189b4e4e68e64fb707ffeda511f2a49c63f4a52e9ff` |
| `src/Warden.Core/Kernel/SourceScopeAuthority.cs` | `90595e210c2d41815290e08da9cf3dad0a20ecace88fedab7e5325765ae73cdf` |

## J. DISPOSABLE CARRIER PROOF (InsuranceAIPlatform copy)

Real product repo untouched throughout — no `.warden` created there.

1. **Adopt with explicit scope** → `SOURCE_SCOPE_ALLOWED: 9 governed path(s), 2 exclusion(s)`
   `SourceScopeSha 824f3001321b…` · `ProductSourceFingerprint 865bad81052a…` · **409 governed files** (was **657** under default-only scope).
2. **Boundary works**: `.docx` 0 · `playwright-report/` 0 · `test-results/` 0 · `tsbuildinfo` 0 · `docs/` 0 · `infra/` 0. Governed = `server 260, src 112, e2e 25, ai-sidecars 7` + 5 root configs.
3. **The original failure loop, re-run**: real `npm run build` (exit 0, 156 modules, tsbuildinfo regenerated ×2) **plus** fresh `playwright-report/results.json`, `test-results/.last-run.json`, `test-results/fresh-run-chromium/error-context.md` →

   ```
   recorded: 865bad81052a   current: 865bad81052a   changed: 0     (exit 0)
   ```

   Before the repair the identical sequence gave `7d2c2d557612 → 9a8625cde455, changed: 2, check exit 2`. **The loop is gone.**
4. **Governed mutation still detected**: touching `src/main.tsx` → `865bad81052a → f924a8c6a2f7`, `changed: 1`, evidence `EV-001` STALE, `check exit 2`.
5. **Scope-shrink attack** — declare a scope excluding the mutated file:
   ```
   SourceScopeSha           : 6aab032f6568…  (was 824f3001321b)
   ProductSourceFingerprint : b9dce2ad0814…  (was 865bad81052a)
   → REQ-CARRIER  STALE   MISSING: LOCAL_TEST     check exit 2
   ```
   The attacker hid the file and gained nothing: the fingerprint moved to a third value and the evidence stayed stale. WARDEN also printed the anti-shrink explanation rather than silently succeeding.
6. **Help safety, empty directory**: `adopt --help`, `adopt -h`, `init --help`, `source scope --help`, `transition --help`, `owner accept --help` → all exit 0, `.warden` never created, **0 directory entries after**.

## K. LEGACY COMPATIBILITY PROOF

Accepted v1 baseline (`6802809f1e58…`, 120 files) materialised from commit `7df1564f` with its original `.warden`:

- `scope.isExplicit` = **absent/false** → legacy hash path.
- **0 files** in the accepted manifest match any newly added exclusion (`playwright-report/`, `test-results/`, `*.tsbuildinfo`) → the new defaults are a **no-op** for that baseline.
- Governed path set under the repaired binary: **120 → 120**.
- The accepted Gate remains readable: `status` still shows `GATE-20260810T192440Z [MATERIAL_CLEAN] locked/accepted/delivered`.

**Honest disclosure:** my first materialisation used `git archive | tar`, which mangled the non-ASCII directory name `PROJECT 13152 — WARDEN v1` into a mojibake duplicate and altered two text files' line endings, producing a spurious `changed: 6`. That was my extraction method, not the repair. The path-set comparison above isolates it: the only two deltas are exactly the mojibake pair. Combined with test J (which recomputes the pre-repair formula literally), legacy identity is preserved. I am reporting the failed attempt rather than only the clean one.

## L. CURRENT SOURCE FINGERPRINT / FRESHNESS

Repaired WARDEN: **`aff922af6ac196abfce28909283424a9301aa24f929fd2fef87a2c3baa6c5f87`** · 122 files · `source diff → changed: 0`.

Evidence bound to that exact identity, via `evidence exec` (structural argv, no shell):
- `EV-EXEC-dd052d4c7d1d` — BUILD **PASS** exit 0 → `REQ-BUILD PROVEN`
- `EV-EXEC-fd14d3ebf1ee` — LOCAL_TEST **PASS** exit 0 → `REQ-TESTS PROVEN`

**A failed attempt is recorded and disclosed, not hidden.** The first BUILD capture (`EV-EXEC-b2098910dd86`) returned FAIL exit 1. Captured cause, verbatim: *"The process cannot access the file … Warden.Core.dll because it is being used by another process. The file is locked by: warden (35264)."* WARDEN cannot rebuild itself while running from its own output directory. Re-recorded from a detached copy of the CLI; the FAIL record stays in the ledger as history.

## M. WARDEN CHECK / RISK / EXTERNAL-REVIEW READINESS

```
GATE GATE-20260811T155146Z  [GOAL_DRAFT]  risk=HIGH
  engineering locked : False      owner accepted : False     delivery authorized: False
  fingerprint        : aff922af6ac1
KERNEL DECISION: NOT_CLEAN_MISSING_MANDATORY_EVIDENCE
  open PROVEN P0/P1/material-P2: 0/0/0
```

`requiresExternalPlatformReview = True`. Event chain: **350 events, consistent**. Secrets scan: 3 candidates, all the same pre-existing synthetic fixture in `ControlAndSecurityTests.cs:1822/1837` (deferred EXT-13152-011, untouched by this repair).

`NOT_CLEAN` is correct and deliberate: a repair child Gate must not claim clean before real external review, and the parent's full requirement set is not re-proven here. **No `MATERIAL_CLEAN` is claimed.**

## N. NEW FINDINGS / DISCLOSURES

1. **`WARDEN_DEFECT` (P3, disclosed, not repaired — out of scope):** `gate child` resets `FINDINGS.json`, `EVIDENCE.json` and `AUDITS.json` for the child, and the parent's copies are not archived alongside its `GateStatus` in `.warden/lineage/`. The parent's *status*, owner acceptance and external-audit log survive. I took an independent backup before opening the child. §6 forbids repairing unrelated mechanics here.
2. **`ENVIRONMENT_ISSUE` (P3):** WARDEN cannot capture BUILD evidence for itself while running from its own build output (self-lock). Workaround: run the CLI from a detached copy. Worth a future ergonomics note, not a defect in this repair.
3. **`EXECUTOR_ISSUE` (P3):** my `git archive | tar` legacy materialisation was not byte-faithful (§K). Corrected by path-set comparison; disclosed.
4. **`WARDEN_DEFECT` (P2, candidate — reported, not repaired):** a post-close repair Child Gate cannot bind its OWN new proof-bearing test through the canonical support-contract mechanism (§I). `gate child` clears the child's frozen support sha and the inherited contract has no criterion for the repair's tests, so the EXT-019 "same selector != same proof method" protection does not reach the evidence proving this very repair. Exact byte SHAs are published for the reviewer instead. Repairing this properly means letting a repair Gate declare repair-scoped criteria + proof assets without re-materialising a full SPEC — a separate Owner-authorised boundary, not this one.
5. No new material WARDEN defect was discovered that invalidates this repair.

## O. PROCESS SCORECARD

| Metric | Result |
|---|---|
| Owner-facing prompts | **1** |
| Clarification questions | **0** |
| Macro executable end-to-end without clarification | **Yes** |
| Critic findings | 1 real design tension (§7.4 vs §7.7), resolved explicitly; no material Macro defect |
| `GPT_MACRO_DEFECT` | none material |
| `WARDEN_DEFECT` beyond authorized findings | 1 × P3 (child-gate ledger reset) |
| `EXECUTOR_ISSUE` | 1 × P3 (extraction method) |
| `ENVIRONMENT_ISSUE` | 1 × P3 (self-lock on build) |
| Manual workaround attempted | **None.** No `.warden` JSON hand-edited; no parent history rewritten |
| Command UX causing unsafe ambiguity | **Yes — the repaired one.** `--help` executing a mutating command is exactly that |
| Did WARDEN enforce its own repair boundary? | **Yes** — refused unattributed risk; required a recognised child boundary; correctly reported NOT_CLEAN rather than letting a repair declare itself clean |
| Loop finite? | **Yes** — two internal rework cycles (one compile fix, one trailing-slash semantics fix), then stop |
| Codex handoff mechanically clear? | **Yes** — §Q |
| Scope creep | none: exactly 8 product files touched, all inside Findings A–D |

## P. SIDE EFFECTS

**`C:\Projects\Warden` (authorized repair Child Gate):**

| Action | Result |
|---|---|
| Product source edited | **YES** — 6 modified, 2 added, all inside Findings A–D |
| `.warden` governance writes | YES — child Gate, risk decision, source capture, evidence |
| commit / push / merge / PR / tag / release / deploy | **NO** |
| reset / clean / stash / checkout / history rewrite | **NO** — HEAD still `7df1564f…`, branch `master`, remotes unchanged |
| Parent Gate history rewritten | **NO** — archived immutably to `.warden/lineage/`, plus an independent backup |

Files changed: `src/Warden.Core/Model/SourceState.cs`, `src/Warden.Core/Kernel/Fingerprinter.cs`, `src/Warden.Core/Kernel/SourceScopeAuthority.cs` (new), `src/Warden.Core/Services/Adoption.cs`, `src/Warden.Cli/CommandRouter.cs`, `src/Warden.Cli/Commands.cs`, `src/Warden.Cli/Help.cs`, `tests/Warden.Tests/SourceScopeAndHelpSafetyTests.cs` (new).

**`C:\Projects\InsuranceAIPlatform`: untouched.** No `.warden`, no source change — all carrier work on a disposable copy.

**Outside product repos (disclosed separately per §13):** disposable carrier, legacy-baseline extraction, help probe, detached CLI runner and parent-state backup in scratchpad; this report committed and pushed to `slavkan777/gpt-handoff`.

## Q. CODEX AFFECTED-REVIEW HANDOFF IDENTITIES

| Field | Value |
|---|---|
| Repo / branch / HEAD | `C:\Projects\Warden` · `master` · `7df1564f140524c0646631ceffe654ced0b18b11` (working tree modified under the child Gate) |
| Repair Gate | `GATE-20260811T155146Z` (logical `WARDEN-LAB-REPAIR-SOURCE-SCOPE-AND-CLI-HELP-V1`) |
| Parent Gate | `GATE-20260810T192440Z` (MATERIAL_CLEAN, accepted, delivered) |
| Root outcome | `51b839ddc42c1a4ce93d8e726e5a58bd4e685d5ca99baa26a2d3e1c2a9d02ee8` (inherited) |
| ContractSha | `12e65bc31f3a5e135c8fdf0cdf8d0e69edafa447c7f536c960222fd055ad3f62` (unchanged) |
| **Source under review** | **`aff922af6ac196abfce28909283424a9301aa24f929fd2fef87a2c3baa6c5f87`** |
| Risk / external review | HIGH · required |
| Review type | **AFFECTED / DELTA — no second EXTERNAL_FULL** |
| Immediate predecessor | `EXT-AUDIT-13152-CODEX-20260811-DELTA-4` (parent's final PASS) |

Review surface: configurable initial source boundary · scope persistence and reuse by capture/diff/check · anti-shrink identity binding · generated-artifact defaults · legacy compatibility · mutating `--help` safety · directly affected evidence-freshness/source-identity laws.

**Proof-bearing files for the reviewer to pin (not bindable at this Gate — see §I/§N.4):**

| File | SHA-256 |
|---|---|
| `tests/Warden.Tests/SourceScopeAndHelpSafetyTests.cs` | `c1b5dd6fc624f6816f3cd189b4e4e68e64fb707ffeda511f2a49c63f4a52e9ff` |
| `src/Warden.Core/Kernel/SourceScopeAuthority.cs` | `90595e210c2d41815290e08da9cf3dad0a20ecace88fedab7e5325765ae73cdf` |

Changed files under review: `SourceState.cs`, `Fingerprinter.cs`, `SourceScopeAuthority.cs` (new), `Adoption.cs`, `CommandRouter.cs`, `Commands.cs`, `Help.cs`, `SourceScopeAndHelpSafetyTests.cs` (new).

## R. FINAL VERDICT

**`WARDEN_REPAIR_READY_FOR_CODEX_AFFECTED_REVIEW`**

Not accepted. Not self-accepted. No Codex PASS claimed. No `MATERIAL_CLEAN`. InsuranceAI implementation not resumed.
