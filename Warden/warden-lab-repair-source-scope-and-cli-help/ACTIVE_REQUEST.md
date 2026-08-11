# PROJECT: WARDEN
# REPAIR: Warden-Lab discovered source-scope + CLI help safety defects
# REQUEST_ID: REQ-2026-08-11-WARDEN-LAB-REPAIR-SOURCE-SCOPE

STATE: OWNER_AUTHORIZED / READY_FOR_CLAUDE
TASK_TYPE: BOUNDED_WARDEN_CHILD_GATE_REPAIR
OWNER: Slava / Вячеслав

======================================================================
0. PRIMARY INTENT — ONE MACRO, NOT A PROMPT CHAIN
======================================================================

Execute this entire repair as ONE bounded autonomous Critic-first Macro.

Owner-facing micro-prompt choreography is forbidden.
Internal micro-planning, local checkpoints and test/rework loops are expected inside this one run.

Return only when the repair is either:

`WARDEN_REPAIR_READY_FOR_CODEX_AFFECTED_REVIEW`

or

`WARDEN_REPAIR_BLOCKED`

Do not ask Slava incremental design questions that can be resolved from the accepted evidence and current source.

This Macro is authorized because Warden-Lab produced new CURRENT + REPRODUCIBLE + MATERIAL evidence after the accepted v1 closure.

======================================================================
1. OWNER AUTHORITY / HISTORICAL BOUNDARY
======================================================================

Slava explicitly said: `давай WARDEN Repair Macro` after reviewing the reproduced Warden-Lab Gate 1 blocker.

This authorizes ONE bounded WARDEN repair boundary for the defects defined below.

The accepted WARDEN v1 parent remains historical truth:

- repo: `C:\Projects\Warden`
- branch: `master`
- accepted delivered baseline commit: `7df1564f140524c0646631ceffe654ced0b18b11`
- parent Gate: `GATE-20260810T192440Z`
- parent ContractSha: `12e65bc31f3a5e135c8fdf0cdf8d0e69edafa447c7f536c960222fd055ad3f62`
- accepted ProductSourceFingerprint: `6802809f1e58879543696f335e8060b12390e4e9dea99d2a1c07a914f490754d`
- parent result: `MATERIAL_CLEAN`
- parent engineeringLocked: true
- Owner Acceptance: true
- private delivery completed
- final parent external review: `EXT-AUDIT-13152-CODEX-20260811-DELTA-4` PASS

Do NOT rewrite, unlock, mutate or falsify the closed parent Gate as though it never closed.

Open/use the legitimate canonical WARDEN repair/Child-Gate mechanism available in the accepted source. If the current implementation has no legitimate mechanism that allows this Owner-authorized repair while preserving parent history, STOP:

`WARDEN_REPAIR_BLOCKED`
Reason: `WARDEN_CHILD_GATE_REPAIR_MECHANISM_BLOCKED`

Do not use BREAK_GLASS as a shortcut unless the accepted child-gate model itself explicitly requires it for this exact case. Do not hand-edit authoritative `.warden` JSON to manufacture a repair boundary.

No commit/push/merge/tag/release/deploy is authorized by this Macro.

======================================================================
2. CANONICAL SOURCES — READ FIRST
======================================================================

Read before any source mutation:

A. AIKB repair decision
`slavkan777/ai-kb/01_PROJECTS/Warden/DECISIONS/DEC-0003_WARDEN_LAB_SOURCE_SCOPE_REPAIR_GATE_OPENED.md`

B. WARDEN current state
`slavkan777/ai-kb/01_PROJECTS/Warden/CURRENT_STATE.md`

C. Warden-Lab governance objective
`slavkan777/ai-kb/01_PROJECTS/Warden/DECISIONS/DEC-0002_WARDEN_LAB_PRIMARY_GOAL_GOVERNANCE_VALIDATION.md`

D. Triggering InsuranceAIPlatform report
`slavkan777/gpt-handoff/InsuranceAIPlatform/warden-lab-gate-1-customer-assistant-spec/report.md`

E. Accepted WARDEN v1 baseline/lab record
`slavkan777/ai-kb/00_GLOBAL_AI_ENGINEERING_OS/WARDEN_V1_EXECUTABLE_BASELINE_AND_LAB_GATE.md`

F. Actual local WARDEN source and current `.warden` state.

Primary truth priority inside this repair:

1. current local WARDEN source / git / executable behavior;
2. reproducible tests/probes;
3. accepted historical WARDEN governance state;
4. trigger report;
5. AIKB prose.

REPORT != EVIDENCE.

======================================================================
3. CRITIC FIRST — BEFORE ANY MUTATING PROJECT COMMAND
======================================================================

Before opening the repair Gate or editing WARDEN source, perform an INTERNAL READ-ONLY Macro Critic preflight.

The Critic is NOT Codex and NOT external evidence.

Check at minimum:

1. Is the repair scope materially tied to the reproduced lab evidence?
2. Is the parent accepted baseline preserved rather than rewritten?
3. Can a legitimate Child Gate/repair boundary be opened using actual WARDEN mechanisms?
4. Does the proposed repair solve the defect class rather than only adding InsuranceAI-specific exceptions?
5. Could any proposed source-scope design allow scope-shrink attacks that keep stale evidence current?
6. Are include/exclude semantics deterministic and repo-relative?
7. Could the design silently ignore legitimate source files?
8. Are globally-added default exclusions truly generated artifacts across projects rather than arbitrary project content?
9. Is `*.docx` incorrectly being proposed as a universal exclusion? If yes, reject that design.
10. Does initial adoption apply scope BEFORE the first governed fingerprint is captured?
11. Will `source capture`, `source diff`, `check` and evidence freshness all use the same persisted scope identity?
12. Is legacy accepted WARDEN state still readable/checkable?
13. Does CLI help interception occur before any mutating command handler/control-directory creation?
14. Does the Macro accidentally authorize unrelated cleanup/refactor/deferred findings?
15. Can the whole repair + local verification be completed without Owner-facing micro-prompts?
16. Does the final status correctly stop BEFORE claiming external-clean/MATERIAL_CLEAN if real Codex review is still required?

Critic verdict:

- `PASS`
- `MATERIAL_REWORK_REQUIRED`

If `MATERIAL_REWORK_REQUIRED`, do NOT edit source. Publish one BLOCKED report with exact Macro/design defect and minimum correction.

Otherwise continue immediately in the SAME run.

======================================================================
4. PRE-REPAIR IDENTITY / IMMUTABILITY BASELINE
======================================================================

Mechanically record:

- repo root;
- branch;
- HEAD;
- remotes;
- upstream/ahead/behind;
- `git status --short` inventory;
- current product-source diff/fingerprint under the closed parent;
- existing `.warden` governance-only dirt disclosed from prior delivery authorization;
- current `warden check` result for the closed parent;
- build/test baseline if safe to inspect without source mutation.

Expected source baseline is commit `7df1564f140524c0646631ceffe654ced0b18b11` plus previously disclosed governance-only local writes after delivery. Do not assume; verify.

If PRODUCT source already drifted from the accepted baseline before this repair, STOP unless that drift is exactly the authorized child-gate state created by the canonical mechanism.

Do not reset/clean/stash/checkout away evidence or local governance state.

======================================================================
5. OPEN THE LEGITIMATE REPAIR / CHILD GATE
======================================================================

Use actual WARDEN source/CLI to discover the canonical Child Gate / post-close new-boundary mechanism.

Do not invent a command.
Do not mutate the parent Gate in place.
Do not hand-edit Gate IDs or parent links.

The repair Gate must be explicitly linked to the accepted parent and attributable to Owner authorization.

Repair risk profile: **HIGH** unless the actual WARDEN risk model mechanically requires an even stronger classification.

Reason:

`Warden-Lab reproduced a control-plane source-identity defect that can make required verification artifacts part of ProductSourceFingerprint and render evidence convergence impossible; repair also touches mutating CLI safety.`

HIGH must preserve the requirement for real external platform review before final material closure.

Record logical repair label in governance/report:

`WARDEN-LAB-REPAIR-SOURCE-SCOPE-AND-CLI-HELP-V1`

If WARDEN generates a separate canonical Gate ID, record both logical label and generated Gate ID.

======================================================================
6. REPRODUCED FINDINGS TO FIX — NO SCOPE EXPANSION
======================================================================

### FINDING A — MATERIAL: source boundary cannot be configured canonically on initial adoption

Trigger evidence showed:

- `FingerprintScope` exists internally;
- initial `Adoption.Adopt` captures with null/default scope;
- first capture therefore uses defaults;
- no canonical CLI surface allows an adopter to define governed paths/exclusions before first ProductSourceFingerprint;
- regenerating artifacts can become governed source;
- evidence production then moves ProductSourceFingerprint and immediately stales the evidence.

This is the primary material blocker.

### FINDING B — MATERIAL SAFETY: source scope must be anti-gaming identity, not a cosmetic filter

A repair that merely allows editing an exclusion list is insufficient.

WARDEN must prevent this attack:

1. capture source + produce evidence;
2. change a governed source file;
3. shrink scope/exclude the changed file;
4. keep prior evidence looking current.

Scope definition/change must therefore be mechanically bound into source identity/fingerprint freshness.

### FINDING C — GENERATED ARTIFACT DEFAULTS: EXT-009 lesson must be generalized safely

Current default exclusions already include many generated directories and `.tools/` because WARDEN previously learned that its own generated tool copy must not invalidate source evidence.

Add only clearly general generated verification/build artifacts that are safe defaults across projects, including at minimum where current source confirms they are not already covered:

- `playwright-report/`
- `test-results/`
- `*.tsbuildinfo`

Review existing defaults before adding duplicates.

Do NOT globally exclude `*.docx`. A `.docx` may be legitimate product content. The new explicit project scope must solve unrelated-document cases.

Do not globally exclude ambiguous generated-looking JavaScript/config files that may be legitimate source. Use explicit source scope for project-specific exclusions.

### FINDING D — CLI HELP SAFETY

Reproduced behavior:

`warden adopt --help`

executed adoption against the current directory instead of returning help, creating a `.warden` scaffold before timeout.

Repair requirement:

- `--help` / `-h` on mutating command paths must be intercepted before any mutation;
- help must never create `.warden`, capture source, write events, or begin adoption;
- exit should be successful/helpful according to the CLI's normal convention;
- cover the relevant mutating command families with tests, not only `adopt` if parsing is shared.

Do not redesign the entire CLI parser unless required by the smallest correct fix.

### OUT OF SCOPE

Do NOT repair:

- old non-material/P3 deferred findings;
- delivery-authorization record schema gaps;
- stale prose residuals;
- unrelated risk UX;
- unrelated external-audit mechanics;
- InsuranceAI product defects;
- LangChain sidecar;
- authentication;
- any speculative WARDEN improvement not needed by Findings A-D.

======================================================================
7. SOURCE-SCOPE DESIGN REQUIREMENTS
======================================================================

Implement the smallest architecture consistent with existing WARDEN code and laws.

Do not blindly implement the example command name below; discover current CLI conventions and choose the smallest coherent surface.

Conceptually WARDEN must support something equivalent to:

`define source scope -> adopt/capture -> persisted scope -> diff/check reuse exact scope`

Required properties:

### 7.1 Initial adoption

A user adopting an existing project must be able to define the intended governed source boundary BEFORE first ProductSourceFingerprint is finalized.

Acceptable shapes include, depending on current architecture:

- source-scope options on `adopt`;
- a canonical pre-adoption scope configuration command;
- another native mechanism that is attributable, persisted and applied before initial capture.

A manual edit of `SOURCE_STATE.json` is NOT acceptable.

### 7.2 Scope model

Support canonical repo-relative governed include paths/patterns and exclusions sufficient for real multi-stack projects.

Normalize deterministically:

- repo-relative representation;
- separators;
- ordering;
- duplicates;
- empty/default semantics.

Reject unsafe scope entries:

- traversal outside repo;
- absolute external paths unless current WARDEN architecture explicitly supports and safely normalizes them;
- ambiguous malformed patterns that cannot be reproduced.

### 7.3 Persistence

Persist the effective scope canonically in WARDEN governance state.

Subsequent:

- source capture;
- source diff;
- `warden check` live-source calculation;
- evidence freshness logic

must use the same effective scope identity.

No component may silently fall back to a broader/different default after an explicit scope exists.

### 7.4 Scope identity / anti-shrink law

Changing effective scope is a source-identity change.

Mechanically ensure that changing only the scope definition changes the identity evidence is bound to, even if the included file bytes happen to be identical.

Preferred invariant:

`ProductSourceFingerprint = H(canonical_scope_identity + governed_file_path/content_identity)`

or an architecturally equivalent mechanism providing the same anti-shrink guarantee.

If existing data contracts make a separate `SourceScopeSha` cleaner, evidence/check must bind to it strongly enough that old evidence becomes stale after scope change.

Prove with tests; do not rely on prose.

### 7.5 Attribution / eventing

A scope-definition/change operation must be attributable and auditable consistent with existing governance conventions.

At minimum record:

- who/asserted-by;
- method or existing equivalent provenance field where supported;
- timestamp;
- effective scope identity/digest;
- reason if the current WARDEN governance model supports reasons for comparable control changes.

Do not fake cryptographic human identity; preserve WARDEN's existing accountable-assertion boundary.

### 7.6 Scope changes after evidence / freeze

A scope change after evidence exists must not make old evidence valid again.

At minimum old evidence must be stale/current check must fail until recaptured/reproved under the new source identity.

Respect existing engineering-lock / Child-Gate laws. Do not add a backdoor that lets a locked Gate silently redefine product source.

### 7.7 Legacy compatibility

Existing accepted WARDEN v1 state must remain readable/checkable.

Do not rewrite the closed parent SOURCE_STATE merely to upgrade schema.

If a schema version/migration is necessary:

- make migration explicit and safe;
- preserve old-state semantics;
- prove backward compatibility on a disposable copy of the accepted WARDEN baseline.

======================================================================
8. REQUIRED TEST MATRIX — MECHANICAL, NOT PROSE
======================================================================

Add the smallest sufficient automated tests to prove the repair.

At minimum cover:

A. initial adopt with explicit source scope -> first fingerprint contains only intended governed set;
B. explicit scope persists and is reused by capture/diff/check;
C. generated `playwright-report/**`, `test-results/**`, `*.tsbuildinfo` do not move fingerprint when excluded by defaults/effective scope as intended;
D. unrelated `.docx` can be excluded through explicit project scope WITHOUT globally ignoring every `.docx`;
E. actual mutation inside governed source moves fingerprint and stales evidence;
F. scope-only change changes source identity / stales prior evidence;
G. scope-shrink attack cannot restore current evidence after a governed source mutation;
H. malformed/out-of-repo scope entry is rejected;
I. deterministic ordering/duplicate equivalent scopes produce the same canonical scope identity;
J. legacy accepted/no-explicit-scope state still calculates/checks with legacy/default semantics;
K. `warden adopt --help` is non-mutating;
L. `warden adopt -h` is non-mutating;
M. representative other mutating command help paths are non-mutating if they share parsing/dispatch;
N. help in an empty disposable directory creates no `.warden` and no governance event;
O. existing EXT-009 `.tools/` regression remains protected;
P. full existing WARDEN test suite remains green.

Bind proof-bearing test files by exact byte SHA in the repair Gate's support/evidence contract if the current WARDEN Gate mechanism supports it.

Do not weaken or replace old tests merely to keep counts green.

======================================================================
9. DISPOSABLE REAL-CARRIER PROBE — REQUIRED
======================================================================

After unit/integration tests pass, prove the fix against a disposable carrier representative of the original failure.

Preferred: a disposable copy of current `C:\Projects\InsuranceAIPlatform` outside the real product repo.

Never initialize/change `.warden` in the real InsuranceAIPlatform repo during this WARDEN repair.

On the disposable carrier:

1. configure/adopt using the NEW canonical source-scope mechanism;
2. record scope identity + ProductSourceFingerprint;
3. show unrelated `.docx` is outside governed source through explicit project scope, not a universal `.docx` ignore;
4. run or simulate real regenerating verification outputs:
   - at minimum real `npm run build` when safely available;
   - create/rewrite `playwright-report/**` and `test-results/**` as Playwright would;
   - regenerate `*.tsbuildinfo` when the actual build does so;
5. verify `warden source diff` / equivalent reports `changed: 0` for those generated outputs;
6. mutate ONE disposable governed `src/**` file and prove source identity changes;
7. prove evidence bound to pre-mutation identity becomes stale;
8. attempt to shrink scope/exclude the changed governed file and prove old evidence does NOT become current;
9. if changing scope is allowed only through a new child boundary, prove the correct denial/required boundary rather than bypassing it;
10. remove nothing from the real product repo.

Also run a help-safety disposable probe:

- empty temp directory;
- invoke mutating command `--help` / `-h`;
- assert no `.warden` directory, event file or source capture is created.

The disposable carrier may be deleted after evidence is captured only if deletion is outside governed WARDEN/product repos and no required evidence path depends on its continued existence. Prefer retaining test logs/report summaries rather than raw huge copies.

======================================================================
10. BUILD / TEST / SELF-GOVERNANCE EVIDENCE
======================================================================

Run the strongest current local verification for WARDEN itself.

At minimum:

- Release build;
- full WARDEN tests;
- warnings/errors counts;
- repair-specific tests;
- disposable carrier probe;
- source diff/fingerprint after implementation;
- support/proof-asset checks;
- event-chain consistency;
- risk/external-review requirement state;
- secrets scan if current Gate requires it.

Every PASS must be bound to the CURRENT repaired ProductSourceFingerprint/source identity.

If source mutates after PASS evidence, rerun affected evidence. Do not carry stale green results forward.

Do not declare external review satisfied yourself.

======================================================================
11. INTERNAL REWORK LOOP — INSIDE THIS ONE MACRO
======================================================================

Claude may internally iterate:

`implement -> build/test -> inspect failure -> bounded repair -> rerun affected verification`

without returning to Slava for micro-prompts.

Rules:

- only Findings A-D and directly required tests/contracts may be changed;
- no broad cleanup;
- no architectural expansion because "while here";
- any newly discovered unrelated material WARDEN defect must be reported separately and may block if it invalidates this repair;
- non-material observations go to disclosures/backlog and do not grow this Gate.

Finite stop rule:

When repair requirements pass locally and only real external Codex review remains, STOP engineering and return READY_FOR_CODEX.

Do not keep polishing.

======================================================================
12. EXTERNAL REVIEW BOUNDARY — DO NOT RUN/FAKE IT HERE
======================================================================

This Macro does NOT authorize Claude to impersonate external review.

After local repair readiness, the next stage is one real Codex affected/DELTA-style review covering:

- configurable initial source boundary;
- scope persistence and anti-shrink binding;
- generated-artifact behavior;
- legacy compatibility;
- mutating `--help` safety;
- any directly affected evidence freshness/source identity laws.

There must be NO second EXTERNAL_FULL.

If current WARDEN schema requires a specific lineage/disposition format, prepare the exact current identities/artifacts needed for the later Codex reviewer, but do not manufacture the reviewer result.

Because repair risk is HIGH, do not claim final `MATERIAL_CLEAN` / final closure before real external review is ingested and satisfied.

======================================================================
13. GIT / SIDE-EFFECT BOUNDARY
======================================================================

Allowed:

- local WARDEN source/test edits inside the legitimate repair Child Gate;
- canonical `.warden` governance writes required by that repair Gate;
- ignored build/test outputs;
- disposable temp/carrier copies;
- report publication to `gpt-handoff`.

Not authorized:

- commit;
- push;
- merge;
- PR;
- tag;
- release;
- deploy;
- force operation;
- reset/clean/history rewrite;
- public visibility change;
- InsuranceAI source mutation;
- AIKB edits by Claude;
- WARDEN parent-Gate history rewrite.

Report publication to `gpt-handoff` is an explicitly allowed external handoff side effect and must be disclosed separately from product/source repo side effects.

======================================================================
14. PROCESS SCORECARD — REQUIRED LAB OUTPUT
======================================================================

Report both PRODUCT/REPAIR result and PROCESS result.

Capture:

- Owner-facing prompts/decisions required for this repair;
- clarification questions asked;
- whether the Macro was executable end-to-end without clarification;
- Critic findings;
- any GPT_MACRO_DEFECT;
- any WARDEN_DEFECT beyond the authorized findings;
- any EXECUTOR_ISSUE;
- any ENVIRONMENT_ISSUE;
- any manual workaround attempted/refused;
- any command UX that caused unsafe ambiguity;
- whether WARDEN itself correctly enforced the repair boundary;
- whether the loop remained finite;
- whether the next Codex handoff is mechanically clear.

Do not convert preferences into material defects.

======================================================================
15. FINAL REPORT / PUBLISH
======================================================================

Publish one canonical report to:

`Warden/warden-lab-repair-source-scope-and-cli-help/report.md`

Mirror current report to:

`Warden/_BRIDGE/LATEST_REPORT.md`
`Warden/latest-report.md`

If practical, publish a compact machine-readable latest summary/status under the Warden project handoff path, but do not let reporting expand the Gate.

Report structure:

A. Final verdict
B. Critic preflight
C. Parent baseline / child repair Gate identity
D. Trigger findings reproduced
E. Source-scope architecture implemented
F. Scope identity / anti-shrink proof
G. Default exclusion changes
H. CLI help-safety repair
I. Automated test evidence
J. Disposable InsuranceAI-style carrier proof
K. Legacy compatibility proof
L. Current source fingerprint / evidence freshness
M. WARDEN check / risk / external-review readiness
N. New findings/disclosures
O. Process scorecard
P. Side effects
Q. Exact Codex affected-review handoff identities
R. Final verdict

Final verdict exactly one:

`WARDEN_REPAIR_READY_FOR_CODEX_AFFECTED_REVIEW`

or

`WARDEN_REPAIR_BLOCKED`

READY requires:

- legitimate repair Child Gate exists;
- Findings A-D repaired in current source;
- all repair-specific tests pass;
- full existing WARDEN suite passes;
- actual source scope is configurable before initial capture;
- scope identity change mechanically invalidates old evidence;
- generated verification outputs no longer cause the reproduced carrier loop;
- help is non-mutating;
- current evidence is bound to current repaired source;
- no material internal finding remains;
- real external review is still correctly marked pending.

Do NOT call the repair accepted.
Do NOT self-accept.
Do NOT claim Codex PASS.
Do NOT resume InsuranceAI implementation inside this Macro.

BEGIN WITH CRITIC PREFLIGHT, THEN EXECUTE THE BOUNDED REPAIR AUTONOMOUSLY.
