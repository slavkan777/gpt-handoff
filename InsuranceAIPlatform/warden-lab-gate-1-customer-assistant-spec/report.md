# PROJECT: InsuranceAIPlatform — WARDEN-Lab Gate 1 — Customer Insurance AI Assistant SPEC

REQUEST_ID: REQ-2026-08-11-IAP-WARDEN-LAB-G1-SPEC
GATE: WARDEN_LAB_GATE_1_CUSTOMER_INSURANCE_AI_ASSISTANT_SPEC
DATE: 2026-08-11
EXECUTOR: Claude — read/plan executor. NOT external independent review.

## FINAL VERDICT (stated first)

**`WARDEN_LAB_GATE1_BLOCKED`**

Reason: **`WARDEN_CAPABILITY_GAP_SOURCE_BOUNDARY_NOT_CONFIGURABLE`**

WARDEN v1 cannot express the ProductSourceFingerprint boundary that Macro §7 mandates for this project, through any canonical CLI mechanism. The consequence is not cosmetic: **the evidence this Gate mandates cannot be produced without invalidating the fingerprint that evidence must be bound to.** Reproduced mechanically on a disposable copy, end to end.

Per §4, §7, §13 and §17, the correct outcome is BLOCKED — not a hand-forged `.warden/SOURCE_STATE.json` that would look identical in a report and be governance forgery.

---

## A. MACRO CRITIC PREFLIGHT

**CRITIC_VERDICT: `PASS`**

The Macro is sound and self-aware. It is **not** the defective layer here. §7 explicitly anticipated this exact outcome — *"If WARDEN's native capture cannot exclude unrelated untracked artifacts without an unsafe workaround, STOP and report the exact limitation"* — and §4 pre-classified it as `WARDEN_DEFECT` / `WARDEN_CAPABILITY_GAP`. Executing the Macro faithfully **led to** the stop. That is the Macro working, not failing.

Checks 1–15, grounded against reality rather than reasoned about:

| # | Check | Result |
|---|---|---|
| 1 | Owner goal complete / non-contradictory | **PASS** — Owner package and Macro agree on scope, out-of-scope, auth deferral, parked sidecar |
| 2 | Gate 0 facts sufficient | **PASS** — baseline reverified mechanically |
| 3 | Bootstrap without inventing CLI | **PASS with defect found** — `adopt`/`init`/`risk`/`source capture` are real and sufficient for §6; see D2 below |
| 4 | Write scope explicit enough | **PASS** — §12/§14 protect `src/**`, `server/**`, `ai-sidecars/**`, `e2e/**`, configs |
| 5 | Fingerprint boundary vs writable scope conflated? | **PASS** — §7 separates them explicitly and correctly |
| 6 | Proof-bearing E2E could escape integrity binding? | **PASS** — §10 mandates path+kind+SHA-256 binding (EXT-019 lesson carried over correctly) |
| 7 | `.warden` writes could change ProductSourceFingerprint? | **PASS** — `.warden/` is in `DefaultExclusions`; verified |
| 8 | Pre-existing `.docx` create an ambiguous boundary? | **DEFECT CONFIRMED — this is the blocker.** See §S |
| 9 | Asks for a WARDEN state v1 may not support? | **DEFECT CONFIRMED** — §7's boundary is unsupported |
| 10 | Report publication vs product no-push separated? | **PASS** — §16 separates them explicitly (Gate 0's D1 was fixed by GPT; good) |
| 11 | HIGH risk / external review unambiguous? | **PASS** — and mechanically verified satisfiable |
| 12 | Could permit false PASS / hand-edited governance? | **PASS** — §17 forbids it and is the reason this Gate is BLOCKED rather than faked |
| 13 | Completes as ONE autonomous Gate? | **PASS** — completed in one run, zero owner-facing micro-prompts |
| 14 | Owner decision still materially missing? | **PASS** — Gate 0's seven open decisions were all answered by the Owner package |
| 15 | Internal Critic independence represented honestly? | **PASS** — §3/§0 state it is not external evidence; restated here |

**Macro defects (non-blocking, GPT_MACRO_DEFECT class, low severity):**
- **D1** — §1 lists five canonical sources; two (`DECISION_2026-08-11_warden_lab_gate1_owner_package.md`, `WARDEN_LAB_GATE_1_..._SPEC.md`) were absent from a shallow AIKB clone and required a fresh clone to obtain. Not a Macro flaw so much as a fragility note: source availability is assumed.
- **D2** — §4 says "discover from help". WARDEN v1 has **no per-subcommand help**; `warden adopt --help` does not print help (see §D). The Macro's discovery instruction is satisfiable only by reading source. Worth stating explicitly in future Macros.

Because the verdict is PASS, execution continued in the same run — and stopped where §7 instructed.

## B. EXECUTION IDENTITY

One bounded Critic-first Macro. **Owner-facing prompts required: 1** (the Gate prompt itself). **Clarifications requested: 0.** No micro-prompt choreography. No Owner identity claimed. No self-acceptance. The internal Critic is not Codex and not external review.

## C. PRODUCT BASELINE RE-VERIFICATION

| Fact | Expected | Observed | Match |
|---|---|---|---|
| Repo root | `C:\Projects\InsuranceAIPlatform` | same | ✔ |
| Remote | `slavkan777/InsuranceAIPlatform` | `https://slavkan777@github.com/slavkan777/InsuranceAIPlatform.git` | ✔ |
| Branch | `rag/local-foundation-mega-v0.1` | same | ✔ |
| HEAD | `f9e34c65d98b251fa6dd8931d17256bb00a70992` | same | ✔ |
| Upstream ahead/behind | — | `0 / 0` | ✔ |
| Worktree | 2 pre-existing untracked `.docx` | exactly those 2 | ✔ |
| New product mutation since Gate 0 | none | 0 modified tracked, 0 staged | ✔ |

No `BASELINE_DRIFT_REQUIRES_OWNER_DECISION`. No checkout/switch/pull/reset/clean/stash/rebase.

## D. WARDEN CLI PROVENANCE / CAPABILITY MAP

- Executable: `C:\Projects\Warden\src\Warden.Cli\bin\Release\net8.0\warden.exe`, built 2026-08-11 09:01.
- Identity: `WARDEN 1.0.0 (schema v1)`.
- WARDEN source repo: HEAD `7df1564f140524c0646631ceffe654ced0b18b11` (the accepted delivered baseline), branch `master`. **Not modified by this Gate.** The 5 modified files under `.warden/` are the previously disclosed PROJECT-13152 delivery-authorization governance records, unchanged by Gate 1.

**Capability map for what this Gate needs:**

| Need (Macro §) | Canonical mechanism | Verdict |
|---|---|---|
| Adopt existing project (§6) | `warden adopt [--risk R] [--risk-by] [--risk-reason]` | **AVAILABLE** — verified |
| Gate creation (§6) | auto by `adopt` → generated Gate ID | **AVAILABLE** |
| HIGH risk, attributed (§6) | `--risk HIGH --risk-by --risk-reason`; also `risk set/history` | **AVAILABLE** — verified, and WARDEN *refuses* unattributed risk (`RISK_DECISION_DENIED_UNATTRIBUTED`) |
| External review required for HIGH (§6, §11) | `policy.risk.requiresExternalPlatformReview` | **AVAILABLE** — verified `True` |
| Contract/SPEC/PLAN/TASKS (§8) | `task init`, `spec acceptance`, `plan step`, `task add`, `freeze` | **AVAILABLE** (not exercised — blocked earlier) |
| Requirements/acceptance (§9) | `requirement add`, `claim add`, `ground` | **AVAILABLE** (not exercised) |
| Proof-asset SHA-256 binding (§10) | `support-contract migrate / bind-proof-assets / freeze` | **AVAILABLE** (not exercised) |
| Source capture (§7) | `source capture \| diff` | **AVAILABLE but scope not configurable — THE BLOCKER** |
| **Configure fingerprint boundary (§7)** | — | **ABSENT** |

**`WARDEN_DEFECT` (minor, discovered by accident):** `warden adopt --help` does **not** print help. It treats `--help` as noise and **executes adoption against the current working directory**. Run from `C:\Users\DEVELOPER`, it created `C:\Users\DEVELOPER\.warden\` and began fingerprinting the entire home directory until a 2-minute timeout killed it. Only an empty `.warden/tmp` scaffold was written — nothing was captured, nothing sensitive ingested — and I removed it. Two real issues: (a) a mutating command silently runs when the user asked for help; (b) the control directory is created *before* any validation that the target is a sensible repository root. A home-directory adoption that ran to completion would fingerprint unrelated user data. `DefaultExclusions` covers `.env`/`.env.*` but not a `secrets/` directory.

## E. WARDEN ADOPTION / BOOTSTRAP ACTIONS

**On the real product repo: NONE.** `.warden` does **not** exist in `C:\Projects\InsuranceAIPlatform`. Per §3, a BLOCKED Critic/execution path must not mutate product governance, and it did not.

All adoption was exercised on a **disposable copy** (`scratchpad/IAP_SCOPE_PROBE`) purely to establish capability truth. That copy adopted successfully as `GATE-20260811T153207Z` with risk HIGH, proving §6 is achievable — which is why the blocker is specifically §7 and not adoption generally.

## F. GATE / RISK / CONTRACT IDENTITIES

Not materialized on the product repo (Gate BLOCKED before governance mutation). Established as *achievable* on the probe copy:

- Logical Gate label: `IAP-WARDEN-LAB-CUSTOMER-ASSISTANT-V1`; WARDEN generates its own ID (`GATE-<UTC timestamp>Z`) — both must be recorded when the Gate is eventually created.
- Risk HIGH recorded canonically with attribution: `RISK-001 (initial) -> HIGH`, by Owner, method `INTERACTIVE_CLI_ASSERTION`, with reason. WARDEN honestly labels this an assertion, not proof of identity.
- HIGH profile yields `requiresExternalPlatformReview=True`, `requiredEvidenceTypes=[BUILD, LOCAL_TEST, INDEPENDENT_REVIEW]`, `conservativeOnAmbiguousScope=True`. §11's external-review contract is representable.

## G. PRODUCTSOURCEFINGERPRINT BOUNDARY — THE BLOCKER

### The mechanism
`FingerprintScope` carries `GovernedPaths` and `Exclusions`. It is populated exactly two ways:
- `Commands.SourceCapture`: `var scope = previous?.Scope ?? new FingerprintScope();`
- `Adoption.Adopt`: `Fingerprinter.Capture(repoRoot, null, clock)` — scope hardcoded `null` → defaults.

**No CLI surface exposes it.** Full command surface: `init --goal-file [--title] [--risk]`, `adopt [--risk R]`, `source capture | diff`. Every `--paths` in the CLI belongs to requirements, evidence, side-effects, audit scope or break-glass — never the fingerprint. On first adoption there is no previous scope, so `DefaultExclusions` applies unconditionally.

### What `DefaultExclusions` omits
Present: `.git/ .warden/ bin/ obj/ dist/ build/ out/ target/ artifacts/ nupkg/ .tools/ TestResults/ coverage/ node_modules/ packages/ __pycache__/ .pytest_cache/ .mypy_cache/ .gradle/ .tox/ venv/ .venv/ .vs/ .vscode/ .idea/ *.user *.suo *.userosscache *.pfx *.snk .env .env.*`

**Absent:** `playwright-report/`, `test-results/`, `*.tsbuildinfo`, `*.docx`.

### Reproduced on a disposable copy — not asserted
Adopted the copy with the canonical CLI. Fingerprint `7d2c2d557612…`, **657 governed files**, including:

| Pattern | Governed files |
|---|---|
| `*.docx` | **2** — the exact files §7 says are "not authorized as feature inputs" |
| `test-results/**` | **70** |
| `playwright-report/**` | **5** |
| `*.tsbuildinfo` | 2 |
| generated `vite.config.js` / `.d.ts` | 2 |

**81 of 657 governed files are regenerating build/test artifacts.**

Then simulated what the Gate's own mandated full-stack E2E does — rewrote `test-results/.last-run.json` and `playwright-report/results.json`:

```
before:  recorded 7d2c2d557612   current 7d2c2d557612   changed 0
after :  recorded 7d2c2d557612   current 9a8625cde455   changed 2
warden check -> live fingerprint 9a8625cde455  <-- SOURCE HAS CHANGED SINCE CAPTURE
  "evidence bound to 7d2c2d557612 is STALE for live source 9a8625cde455 (LAW-02, LAW-03)"
  exit 2
```

### Why this blocks the Gate
Macro §9 and §10 make **FULLSTACK E2E blocking evidence**, and the Owner package explicitly authorizes LocalDB + API + frontend E2E as required validation. But under WARDEN v1's only available boundary:

1. Producing the mandated E2E evidence rewrites governed source.
2. The ProductSourceFingerprint moves.
3. All evidence bound to the prior fingerprint becomes STALE (LAW-02/LAW-03).
4. `warden check` refuses, and re-capturing simply moves the target again on the next E2E run.

**The required evidence destroys the fingerprint it must be current for.** The implementation Gate could never legitimately reach `MATERIAL_CLEAN`. The same circularity applies to `npm run build` via `*.tsbuildinfo`.

Separately, §7's instruction that the two `.docx` "are not authorized as feature inputs" cannot be honoured — they are governed source, and §7 also forbids deleting them.

### This is a known lesson WARDEN learned once and did not generalize
`DefaultExclusions` contains `.tools/` with this comment:

> *"It contains a COPY of the built tool, so leaving it governed would make installing WARDEN change WARDEN's own ProductSourceFingerprint — the tool's own output would invalidate the evidence bound to the source that produced it (EXT-13152-009)."*

That is **exactly** the defect class hitting us now. EXT-13152-009's repair fixed the **instance** (`.tools/` for WARDEN's own repo) and not the **class** (verification output must never be governed source, in any adopted project). WARDEN v1 was only ever dogfooded on itself, a .NET repo with no Playwright — so the class defect never surfaced. It surfaces on the first real adopted carrier. That is precisely what this Lab exists to find.

### Why I did not work around it
- **Hand-writing `.warden/SOURCE_STATE.json` with a custom scope** — forbidden by §4 ("without manually forging `.warden/*.json`"), §14 ("no parallel shadow governance format"), §17 ("BLOCKED even if Claude could create equivalent Markdown manually"). It would also produce a report indistinguishable from a legitimate one, which is the exact failure mode this Lab tests for.
- **Deleting the artifacts before capture** — §7 forbids deleting the `.docx`; the E2E directories regenerate by design, so this is a treadmill, not a boundary.
- **Patching WARDEN** — forbidden by §2 and §13; WARDEN is at `MATERIAL_CLEAN` + `engineeringLocked` + accepted + delivered, and reopening it requires a legitimate Owner boundary.

## H–N. SPEC / PLAN / TASKS / REQUIREMENTS / EVIDENCE / EXTERNAL REVIEW / WRITABLE SCOPE

**Deliberately not materialized.** §3 forbids governance mutation when execution must stop, and §17 makes a bypass-built contract an automatic BLOCK. Writing a SPEC/PLAN/TASKS set into a Gate whose evidence contract is provably unsatisfiable would be a well-formatted lie — a frozen contract that cannot be discharged.

The Owner package's product content is accepted and unambiguous, and Gate 0 already supplies the architecture facts; **no product-design work is missing.** Nothing here is blocked on insurance-domain thinking. The instant the boundary is expressible, this Gate can be completed in one run: the SPEC content, the 20 acceptance groups from §9, the proof-asset SHA-256 contract from §10 and the writable scope from §12 are all fully determined by the Owner package plus Gate 0's verified topology.

The one substantive design fact worth recording now, because it constrains everything: Gate 0 proved the backend has **no authentication at all** and that every route/endpoint is operator/claim-scoped. The Owner package correctly responds by scoping v1 to general/policy/FAQ guidance with no claim-specific access, deferring auth + ownership to a post-close Child Gate. That decision is sound and needs no revision.

## O. WARDEN PRE-IMPLEMENTATION VALIDATION

Not applicable — no Gate was created on the product repo. Validation performed instead on the probe copy, and the results are reported above as capability truth. `ownerAccepted`, `deliveryAuthorized` and `engineeringLocked` were never set anywhere for this feature.

## P. WARDEN-LAB PROCESS SCORECARD

| Metric | Result |
|---|---|
| Owner-facing prompts required in Gate 1 | **1** |
| Clarifications requested | **0** |
| Whole logical Gate under ONE Macro | **Yes** — including the stop decision |
| Critic defects found in GPT Macro | 2, both low (D1 source fragility, D2 "discover from help" not satisfiable) |
| Executor guesswork required | **None on the blocker** — reproduced mechanically rather than argued |
| WARDEN CLI friction/ambiguity | **High**: no per-subcommand help; `--help` on a mutating command executes it |
| Canonical operation WARDEN could not express | **Yes — the fingerprint boundary (§7). The blocker.** |
| False positive / false negative | None observed in the Kernel. Staleness detection worked *correctly* — it is the boundary that is wrong, not the check |
| Evidence-gaming opportunity discovered | **Yes** — hand-writing `SOURCE_STATE.json` scope would silently produce a "clean" Gate. Available, undetectable in a report, and refused |
| Product/governance source-boundary confusion | **Yes — the core finding.** WARDEN conflates "files in the repo" with "product source"; verification *output* is not product source |
| Manual workaround attempted | **None.** Three were identified and all refused (§G) |
| Place where report could have overridden evidence | **Yes** — I could have written a complete, professional Gate 1 SPEC report and no reader could have told it was ungovernable. Refused; this is the `REPORT != EVIDENCE` test and the honest answer is BLOCKED |
| Time/work inflation from orchestration | Low. Gate 0 → Gate 1 handoff worked; §16 fixed Gate 0's publication contradiction |

**Positive findings — WARDEN behaved correctly:**
- Refused an unattributed HIGH risk (`RISK_DECISION_DENIED_UNATTRIBUTED`) — the EXT-13152-006 repair holds on a foreign project.
- HIGH risk automatically mandates external platform review and INDEPENDENT_REVIEW evidence.
- Staleness detection (LAW-02/03) fired correctly and immediately.
- `adopt` left the existing `README.md` and instruction files untouched rather than overwriting them.
- `.warden/` is correctly self-excluded — governance writes do not move the product fingerprint.

## Q. FINDINGS BY SOURCE LAYER

| # | Finding | Class | Severity |
|---|---|---|---|
| 1 | Fingerprint scope not configurable via any canonical CLI mechanism; `DefaultExclusions` omits `playwright-report/`, `test-results/`, `*.tsbuildinfo`. Mandated E2E evidence invalidates its own fingerprint. **CURRENT + REPRODUCIBLE + MATERIAL** | `WARDEN_DEFECT` | **P1 — blocks the Gate** |
| 2 | EXT-13152-009's repair fixed the instance (`.tools/`) not the class (verification output as governed source) | `WARDEN_DEFECT` | P2 (root cause of #1) |
| 3 | `warden adopt --help` executes adoption instead of printing help; creates the control directory before validating the target | `WARDEN_DEFECT` | P2 |
| 4 | No per-subcommand help; options discoverable only by reading source | `WARDEN_DEFECT` | P3 |
| 5 | Unrelated untracked files (`.docx`) silently become governed source with no way to exclude them | `WARDEN_DEFECT` | P2 (same root as #1) |
| 6 | §4 instructs discovery "from help" that WARDEN cannot provide | `GPT_MACRO_DEFECT` | P3 |
| 7 | Two §1 canonical sources missing from a shallow AIKB clone | `ENVIRONMENT_ISSUE` | P3 |
| 8 | I ran `warden adopt --help` from the home directory without `--repo`, creating `C:\Users\DEVELOPER\.warden` (empty scaffold; removed) | `EXECUTOR_ISSUE` | P3 |
| 9 | Backend has no authentication (carried from Gate 0) — already correctly handled by the Owner package | `PRODUCT_ISSUE` | P1 (deferred by design) |

## R. SIDE EFFECTS

**`C:\Projects\InsuranceAIPlatform` (product):**

| Action | Result |
|---|---|
| `.warden` initialized | **NO** — does not exist |
| source/test/config edits | **NO** — 0 modified tracked, 0 staged |
| commit / push / branch / merge / PR / tag | **NO** — HEAD, branch, remotes unchanged; upstream 0/0 |
| checkout / pull / reset / clean / stash / rebase | **NO** |
| Azure / DB / provider / secret mutation | **NO** |
| the two `.docx` | untouched |

**`C:\Projects\Warden` (WARDEN source):** HEAD `7df1564f…` unchanged; the 5 modified `.warden/` files are the previously disclosed PROJECT-13152 delivery-authorization records, untouched by this Gate. **No WARDEN source modified.**

**Elsewhere, disclosed:**
- `scratchpad/IAP_SCOPE_PROBE` — disposable copy adopted under WARDEN purely to reproduce the defect. Not the product repo.
- `C:\Users\DEVELOPER\.warden\` — **accidentally created by me** via `warden adopt --help` without `--repo`; contained only an empty `tmp` scaffold, captured nothing, and was removed. Disclosed rather than quietly cleaned.
- This report is committed and pushed to `slavkan777/gpt-handoff` — required by §16, separate scope from the product no-push rule.

## S. MATERIAL BLOCKERS / MINIMUM CORRECTION

**Blocker:** WARDEN v1 has no canonical way to declare which paths constitute product source, so verification output is governed as source and the mandated evidence cannot be produced without invalidating itself.

**Minimum correction — recommended (a separate WARDEN repair boundary, exactly as §13 anticipates):**

1. Expose the fingerprint boundary through the canonical CLI — e.g. `warden source scope --governed <paths> --exclude <patterns>`, and/or `adopt`/`init --exclude`. It must be an attributed, recorded governance decision (same treatment as the risk profile), not a convenience flag, since it decides what "the product" *is*.
2. Generalize the EXT-13152-009 lesson into `DefaultExclusions`: `playwright-report/`, `test-results/`, `*.tsbuildinfo`, and any declared verification-output directory.
3. Fix `--help` on mutating commands; validate the target before creating the control directory.

Estimated as a small, well-bounded WARDEN change — but it **must not** happen inside this project Gate.

**Rejected alternatives:** dropping full-stack E2E from blocking evidence (contradicts the Owner package and guts the acceptance contract); hand-writing the scope (governance forgery).

**No Owner product decision is missing.** This is purely a tooling capability gap. Once corrected, Gate 1 completes in one run with no further architecture round.

## T. FINAL VERDICT

**`WARDEN_LAB_GATE1_BLOCKED`** — `WARDEN_CAPABILITY_GAP_SOURCE_BOUNDARY_NOT_CONFIGURABLE`

No feature implemented. No product source mutated. No governance forged. No self-acceptance. The internal Critic is not external review.

The Lab's primary purpose was to find real defects in WARDEN, the Macro chain and the executor before trusting them. It found a P1 in WARDEN on the first real carrier — the one place a governance system must not be wrong: what counts as the product.
