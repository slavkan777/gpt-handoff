# PROJECT: InsuranceAIPlatform
# GATE: WARDEN_LAB_GATE_1_CUSTOMER_INSURANCE_AI_ASSISTANT_SPEC
# REQUEST_ID: REQ-2026-08-11-IAP-WARDEN-LAB-G1-SPEC

STATE: OWNER_PACKAGE_ACCEPTED / READY_FOR_CLAUDE
TASK_TYPE: WARDEN_EXISTING_PROJECT_ADOPTION_AND_FROZEN_SPEC
OWNER: Slava / Вячеслав

======================================================================
0. OWNER AUTHORITY / PURPOSE
======================================================================

Slava explicitly confirmed the complete Gate 1 Owner package on 2026-08-11.

This Gate is the first material WARDEN-Lab governance test on the existing InsuranceAIPlatform.

PRIMARY LAB PURPOSE:

1. test WARDEN v1 on a real existing project;
2. test GPT's one-Macro-per-logical-stage orchestration;
3. test Claude's ability to execute without owner-facing micro-prompt choreography;
4. produce a frozen feature contract precise enough for ONE later implementation Macro;
5. surface real WARDEN / Macro / executor / environment defects honestly.

The insurance feature is the carrier/load. A good feature SPEC alone is not sufficient if the governance/process is unusable.

ONE OWNER PROMPT.
ONE BOUNDED GATE MACRO.
INTERNAL MICRO-PLANNING ALLOWED.
NO OWNER-FACING MICRO-PROMPT CHOREOGRAPHY.

Do not self-accept the final feature.
Do not claim Owner identity.
Do not treat an internal Critic as external evidence.

======================================================================
1. CANONICAL SOURCES — READ FIRST
======================================================================

Read these exact durable sources before acting:

A. AIKB Gate 1 Owner decision
`slavkan777/ai-kb/01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-08-11_warden_lab_gate1_owner_package.md`

B. AIKB Gate 1 plan
`slavkan777/ai-kb/01_PROJECTS/InsuranceAIPlatform/FEATURE_PLANS/WARDEN_LAB_GATE_1_CUSTOMER_INSURANCE_AI_ASSISTANT_SPEC.md`

C. AIKB current state
`slavkan777/ai-kb/01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md`

D. Gate 0 current-truth report
`slavkan777/gpt-handoff/InsuranceAIPlatform/warden-lab-gate-0-current-truth-adoption/report.md`

E. WARDEN v1 accepted baseline / lab record
`slavkan777/ai-kb/00_GLOBAL_AI_ENGINEERING_OS/WARDEN_V1_EXECUTABLE_BASELINE_AND_LAB_GATE.md`

F. Warden-Lab primary-goal decision
`slavkan777/ai-kb/01_PROJECTS/Warden/DECISIONS/DEC-0002_WARDEN_LAB_PRIMARY_GOAL_GOVERNANCE_VALIDATION.md`

If any source is unavailable, use the local/canonical copy only if identity can be verified. Do not invent missing Owner decisions.

======================================================================
2. ROUTING / REPOSITORY LOCK
======================================================================

PRODUCT REPO EXPECTED:
`C:\Projects\InsuranceAIPlatform`

REMOTE EXPECTED:
`slavkan777/InsuranceAIPlatform`

CANONICAL PRODUCT BRANCH ACCEPTED FOR THIS LAB:
`rag/local-foundation-mega-v0.1`

GATE 0 OBSERVED HEAD:
`f9e34c65d98b251fa6dd8931d17256bb00a70992`

WARDEN v1 source/workspace expected:
`C:\Projects\Warden`

WARDEN accepted baseline reference:
private `slavkan777/Warden`, accepted baseline commit
`7df1564f140524c0646631ceffe654ced0b18b11`

Do NOT modify WARDEN source.
Do NOT reopen PROJECT 13152 merely because this lab is using WARDEN.

Before any product-repo governance mutation, mechanically verify:

- repo root;
- remote;
- current branch;
- HEAD;
- upstream/ahead/behind;
- worktree including the two pre-existing untracked `.docx` files from Gate 0;
- no new product source mutation since Gate 0.

If HEAD changed after Gate 0, do not automatically fail merely because time passed. Determine whether the change is an authorized current product change. If that cannot be established without guessing, STOP `WARDEN_LAB_GATE1_BLOCKED` with reason `BASELINE_DRIFT_REQUIRES_OWNER_DECISION`.

Do NOT checkout/switch/pull/reset/clean/stash/rebase.

======================================================================
3. CRITIC FIRST — BEFORE ANY WRITE
======================================================================

Before invoking any mutating WARDEN command or creating any product-repo governance artifact, run an INTERNAL READ-ONLY MACRO CRITIC over this entire request plus the durable Gate 1 plan.

The Critic challenges the Macro, not the product.

Check at minimum:

1. Owner goal completeness and contradictions.
2. Whether the accepted Gate 0 facts are enough to execute safely.
3. Whether the proposed WARDEN bootstrap/adoption can be performed without inventing CLI commands.
4. Whether write scope is explicit enough to protect `src/**`, `server/**`, `ai-sidecars/**`, tests and runtime config from implementation edits.
5. Whether source fingerprint boundary and writable scope are incorrectly conflated.
6. Whether proof-bearing E2E/tests could escape integrity binding.
7. Whether `.warden`/governance writes could accidentally change ProductSourceFingerprint.
8. Whether pre-existing untracked `.docx` files create an ambiguous source-boundary problem.
9. Whether any section asks for a WARDEN state that the actual v1 state machine may not support.
10. Whether report publication and product no-push rules are correctly separated.
11. Whether HIGH risk / external review requirement is unambiguous.
12. Whether the Macro could permit false PASS, hand-edited governance, or report-over-evidence substitution.
13. Whether it can complete as ONE autonomous logical Gate.
14. Whether any Owner decision is still missing materially.
15. Whether internal Critic independence is represented honestly.

Critic verdict exactly one:

`PASS`

or

`MATERIAL_REWORK_REQUIRED`

If `MATERIAL_REWORK_REQUIRED`:

- do NOT mutate InsuranceAIPlatform governance;
- do NOT initialize `.warden`;
- return one blocking report with exact Macro defects, severity, minimum correction and PROCESS SCORECARD;
- final verdict: `WARDEN_LAB_GATE1_BLOCKED`.

Do not ask Slava seven micro-questions.

If Critic PASS, continue immediately in the SAME run.

======================================================================
4. WARDEN CLI PROVENANCE / CAPABILITY DISCOVERY
======================================================================

This Gate tests WARDEN itself. Do not emulate WARDEN with ad-hoc JSON/Markdown if its CLI cannot perform a required canonical operation.

Mechanically establish the WARDEN CLI you will use:

- executable/path;
- relevant version/identity if exposed;
- `warden --help` and command-specific help;
- current WARDEN source repo identity read-only if needed to resolve command semantics;
- confirm no WARDEN source mutation before/after.

IMPORTANT:

Do NOT invent commands from this prompt.

Discover actual v1 commands from help/source and use only the canonical mechanisms they expose.

If existing-project adoption, Gate creation, risk recording, source capture, contract/spec/traceability materialization or required governance cannot be achieved canonically without manually forging `.warden/*.json`, STOP and classify it as a candidate `WARDEN_DEFECT` or `WARDEN_CAPABILITY_GAP`.

No silent workaround.

======================================================================
5. OWNER PACKAGE TO FREEZE — DO NOT REDESIGN
======================================================================

Materialize this accepted Owner direction, not a new product invented by Claude.

FEATURE:
`Customer Insurance AI Assistant v1`

FROZEN BUSINESS PAIN:
A customer after an accident/insurance event may not know the safe next steps, relevant general policy/FAQ guidance, when available evidence is insufficient, or when a human specialist is required.

FROZEN V1 GOAL:
A customer can use a customer-facing assistant to receive grounded, cited, advisory-only general insurance guidance and an explicit path to a human specialist, without any autonomous coverage, payout, fraud, legal or claim-approval decision and without access to customer claim-specific data.

IN SCOPE FOR THE LATER IMPLEMENTATION GATE:

- customer-facing/public assistant surface;
- general post-accident guidance;
- policy / curated FAQ retrieval;
- grounded answers;
- citations/evidence references;
- insufficient-evidence/cannot-answer behavior;
- advisory-only controls;
- human handoff;
- audit/trace behavior;
- synthetic/demo data only;
- truthful status/runtime surfaces required so evidence cannot be based on known false `/api/system/demo-status` / stale BFF status claims;
- valid full-stack local E2E with LocalDB + .NET API + frontend.

OUT OF SCOPE:

- claim-specific customer status/documents;
- real customer authentication in this first feature;
- customer→claim ownership in this first feature;
- autonomous coverage decisions;
- payout decisions;
- fraud decisions/accusations;
- legal advice;
- final claim approval/rejection;
- LangChain sidecar repair;
- Qdrant enablement;
- Ollama/LocalLlama enablement;
- paid/managed LLM enablement;
- production data/PII;
- Azure mutation/deployment;
- merge/main/default-branch retarget;
- unrelated refactor/cleanup;
- lint tooling repair merely to manufacture an acceptance gate.

POST-CLOSE CHILD GATE RESERVED:
real authentication + customer identity/session + ownership + claim-specific guidance.

Do not move this Child Gate work into v1.

======================================================================
6. ADOPT / BOOTSTRAP INSURANCEAIPLATFORM UNDER WARDEN
======================================================================

Using only actual canonical WARDEN mechanisms discovered in §4, create/adopt the project Gate for this feature.

The intended logical Gate name is:

`IAP-WARDEN-LAB-CUSTOMER-ASSISTANT-V1`

If WARDEN generates its own canonical Gate ID, record both the logical label and generated ID.

Required governance facts:

- Owner: Slava / Вячеслав as recorded authority, without claiming cryptographic proof;
- project/repo identity;
- branch/baseline identity;
- HIGH risk;
- external platform review REQUIRED;
- no Owner Acceptance yet;
- no Delivery Authorization yet;
- engineering not locked merely because SPEC is frozen;
- no BREAK_GLASS unless an actual justified emergency condition exists (none is expected).

If WARDEN defaults risk without attribution, explicitly attempt to record the Owner-approved HIGH decision through its canonical authority mechanism. If the CLI cannot record attribution/reason, disclose the limitation; do not hand-edit state.

======================================================================
7. PRODUCT SOURCE FINGERPRINT BOUNDARY
======================================================================

Use WARDEN's actual source-boundary/capture model.

The integrity goal is that later implementation or proof changes cannot leave evidence falsely current.

Relevant product/proof-bearing areas include at minimum:

- `src/**`
- `server/**`
- `ai-sidecars/**`
- `e2e/**`
- root runtime/build/test configs that affect behavior or evidence (`package*.json`, TypeScript/Vite/Playwright/Tailwind/PostCSS configs, root `index.html`, relevant env examples/config templates).

Do not treat `docs/**` as runtime source by default unless a specific file becomes an actual knowledge/runtime/proof input.

Do not include generated/cache/secrets:

- `.git/**`
- `.warden/**` governance plane;
- `node_modules/**`;
- `dist/**`;
- `bin/**` / `obj/**`;
- Playwright reports/results;
- `.venv/**` / Python caches;
- secrets;
- machine-local scratch.

The two pre-existing untracked `.docx` files discovered in Gate 0 are not authorized as feature inputs. Do not delete/edit them. If WARDEN's native capture cannot exclude unrelated untracked artifacts without an unsafe workaround, STOP and report the exact limitation.

IMPORTANT:

Distinguish:

`PRODUCT SOURCE FINGERPRINT BOUNDARY`

from

`LATER IMPLEMENTATION WRITABLE SCOPE`.

Proof-bearing test/E2E files must remain integrity-bound even if they are not initially writable.

Capture the exact current ProductSourceFingerprint only after the canonical boundary is established.

After governance writes verify that `.warden` changes do NOT change ProductSourceFingerprint. If they do, classify and investigate as a WARDEN behavior; do not normalize it away.

======================================================================
8. MATERIALIZE SPEC → PLAN → TASKS
======================================================================

Use WARDEN-native governance structures where available.

Create a molecular design internally but materialize one coherent frozen contract.

Required SPEC content:

- business pain;
- actors;
- customer journey;
- explicit v1 scope / out-of-scope;
- advisory/safety boundaries;
- current reusable architecture facts from Gate 0;
- smallest-correct candidate architecture;
- endpoint/surface boundaries;
- knowledge/grounding boundary;
- audit/trace boundary;
- human-handoff boundary;
- truthfulness-status repair requirement;
- synthetic-data boundary;
- no claim-specific access invariant;
- NFRs for correctness, security boundary, observability, deterministic fallback and usability;
- Child Gate boundary.

Required PLAN content:

- implementation order by vertical slice;
- affected layers;
- explicit writable scope for implementation;
- build/test/E2E order;
- evidence generation order;
- external FULL review point;
- same-Gate repair / affected DELTA loop;
- closure / MATERIAL_CLEAN / engineering lock path;
- Owner Acceptance path;
- separate Delivery Authorization path;
- Child Gate after closure.

Required TASKS:

Tasks must be atomic enough for traceability but grouped so the later implementation is still executable through ONE large bounded Macro.

Do NOT create 50 owner-facing microtasks as separate prompts.

======================================================================
9. REQUIREMENTS / ACCEPTANCE CONTRACT
======================================================================

Materialize concrete acceptance criteria for at least these logical groups, using WARDEN-native IDs/schema where supported:

- BUILD
- DOTNET TESTS
- FRONTEND BUILD
- FULLSTACK E2E
- CUSTOMER SURFACE
- GENERAL GUIDANCE
- POLICY/FAQ GROUNDING
- CITATIONS
- INSUFFICIENT EVIDENCE
- ADVISORY ONLY
- HUMAN HANDOFF
- NO CLAIM-SPECIFIC ACCESS
- NO FINAL INSURANCE DECISION
- CONTEXT ISOLATION
- AUDIT TRACE
- STATUS TRUTHFULNESS
- SYNTHETIC DATA ONLY
- PROVIDER HONESTY
- EXTERNAL REVIEW
- PROCESS SCORECARD

Every criterion must answer:

- what behavior is required;
- how it will be mechanically or runtime verified;
- what proof asset/evidence class supports it;
- what result is blocking;
- where Owner/manual judgment remains unavoidable.

Avoid vague criteria such as "works correctly".

======================================================================
10. EVIDENCE / PROOF-ASSET CONTRACT
======================================================================

Design the future implementation evidence package now.

At minimum require:

- source fingerprint current at evidence time;
- Release .NET build command/result;
- .NET tests with exact counts;
- frontend build;
- valid full-stack E2E using the correct config and LocalDB + API + frontend;
- browser/manual proof of customer assistant golden flow;
- grounded citation proof;
- insufficient-evidence negative flow;
- unsupported/final-decision refusal flow;
- explicit proof public assistant does NOT expose claim-specific data/routes;
- status endpoint runtime truthfulness proof;
- provider-mode honesty probe;
- audit trace per answer;
- secret scan;
- synthetic-only proof;
- no cross-context leakage tests.

WARDEN v1 EXT-019 lesson is mandatory:

Do not bind proof only to a command selector/test name.

Where a test/proof file is a material support asset, bind its exact path + kind + byte SHA-256 (and type/method identity where useful) through WARDEN's supported mechanism.

If WARDEN cannot represent the necessary proof-asset identity for this adopted project, report a candidate WARDEN material gap rather than silently downgrading the contract.

======================================================================
11. HIGH-RISK EXTERNAL REVIEW CONTRACT
======================================================================

Risk is Owner-approved HIGH.

Record external platform review as required for later closure.

External reviewer later: real Codex/platform review, not Claude Critic, not GPT self-review.

Planned lineage:

- one external FULL against the implementation fingerprint;
- if material findings cause source repair, Claude performs one bounded aggregated repair Macro;
- then external DELTA/AFFECTED review against the immediate predecessor;
- no repeated FULL unless real architectural invalidation objectively requires it.

Define the external report provenance fields WARDEN will require.

Do NOT run Codex in Gate 1: there is no implementation result to audit yet.

======================================================================
12. LATER IMPLEMENTATION WRITABLE SCOPE
======================================================================

Gate 1 does not implement anything, but it must freeze a bounded writable scope for the NEXT Macro.

Candidate allowed implementation areas may include only files proven necessary inside:

- customer-facing React route/components/state/API facade;
- bounded .NET endpoint/service/knowledge retrieval path for general guidance;
- bounded policy/FAQ knowledge data;
- audit/trace integration;
- status truthfulness repair;
- tests/E2E directly supporting this feature.

Explicitly forbid in next Macro unless separately reopened:

- authentication/claim ownership;
- current operator claim RAG contract broad rewrite;
- sidecar repair;
- provider infrastructure expansion;
- Azure deployment;
- main/merge;
- unrelated refactors.

The final exact path list should be grounded in current source topology, not guessed from this prompt.

======================================================================
13. WARDEN-LAB PROCESS SCORECARD
======================================================================

This is mandatory and as important as the product contract.

Report:

- number of Owner-facing prompts required in Gate 1;
- number of clarifications requested;
- whether this whole logical Gate completed under this ONE Macro;
- Critic defects found in GPT Macro;
- executor guesswork required;
- WARDEN CLI friction/ambiguity;
- any canonical operation WARDEN could not express;
- any false positive/false negative;
- any evidence-gaming opportunity discovered;
- any product/governance source-boundary confusion;
- any manual workaround attempted or avoided;
- any place where report could have overridden evidence;
- time/work inflation caused by orchestration;
- candidate improvements classified exactly as:
  - `WARDEN_DEFECT`
  - `GPT_MACRO_DEFECT`
  - `EXECUTOR_ISSUE`
  - `ENVIRONMENT_ISSUE`
  - `PRODUCT_ISSUE`

If WARDEN itself exhibits a CURRENT + REPRODUCIBLE + MATERIAL defect:

- capture evidence;
- do NOT patch WARDEN inside this project Gate;
- Gate 1 may be BLOCKED if that defect prevents trustworthy governance;
- a separate legitimate WARDEN repair boundary will be opened by Owner/GPT later.

======================================================================
14. NO PRODUCT IMPLEMENTATION LAW
======================================================================

During this Gate, do NOT modify implementation code/tests/runtime config under:

- `src/**`
- `server/**`
- `ai-sidecars/**`
- `e2e/**`
- package/lock/build/runtime configs

except normal ignored generated outputs from safe verification if needed.

Allowed product-repo writes are only:

- `.warden/**` or equivalent canonical WARDEN governance plane;
- WARDEN-required frozen governance/spec/plan/task artifacts if the canonical CLI places them elsewhere.

Do not manually create a parallel shadow governance format if WARDEN already has a canonical format.

No product commit/push/branch/merge/PR/tag/release.
No Azure/DB mutation.
No provider/secret changes.

======================================================================
15. VALIDATE THE FROZEN GOVERNANCE STATE
======================================================================

After materialization:

- rerun actual WARDEN checks/status commands relevant to a pre-implementation Gate;
- verify Gate/Contract/risk/requirements/traceability identities;
- verify ProductSourceFingerprint exactly current;
- verify source diff unchanged from baseline product source;
- verify governance writes did not mutate product fingerprint;
- verify ownerAccepted is NOT incorrectly set for final product;
- verify deliveryAuthorized is NOT set;
- verify engineeringLocked is NOT incorrectly set merely because planning is complete;
- verify external review requirement is represented for HIGH risk;
- verify no product implementation evidence is falsely marked PROVEN before implementation;
- verify event/integrity chain if WARDEN exposes it.

Do not force `MATERIAL_CLEAN` in Gate 1. Implementation requirements are expected to remain unsatisfied/open.

A planning Gate is READY when it is frozen and executable, not when the future feature is falsely declared clean.

======================================================================
16. REPORT PUBLICATION
======================================================================

Product-repo no-push and handoff publication are different scopes.

Publishing the Gate report to `slavkan777/gpt-handoff` is allowed and REQUIRED by this Macro. Disclose this side effect explicitly.

Write one report to:

`InsuranceAIPlatform/warden-lab-gate-1-customer-assistant-spec/report.md`

Mirror to:

`InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md`
`InsuranceAIPlatform/latest-report.md`

Do not modify another project's handoff path.

Report sections:

A. Macro Critic preflight
B. Execution identity
C. Product baseline re-verification
D. WARDEN CLI provenance/capability map
E. WARDEN adoption/bootstrap actions
F. Gate/risk/contract identities
G. ProductSourceFingerprint boundary and exact value
H. Frozen SPEC summary
I. PLAN summary
J. TASKS/traceability summary
K. Requirements/acceptance matrix
L. Evidence/proof-asset contract
M. External-review contract
N. Later implementation writable scope
O. WARDEN pre-implementation validation/status
P. PROCESS SCORECARD
Q. Findings classified by source layer
R. Side effects
S. Material blockers/unknowns
T. Final verdict

======================================================================
17. FINAL VERDICT / STOP
======================================================================

Final verdict exactly one:

`WARDEN_LAB_GATE1_SPEC_FROZEN_READY_FOR_IMPLEMENTATION_MACRO`

or

`WARDEN_LAB_GATE1_BLOCKED`

READY requires ALL of:

- Critic PASS;
- current baseline mechanically reverified;
- WARDEN canonical adoption/bootstrap successful;
- HIGH risk represented;
- current ProductSourceFingerprint captured;
- Owner goal/Contract frozen;
- SPEC → PLAN → TASKS materialized;
- requirements/acceptance/evidence contract complete;
- proof-asset integrity approach represented;
- real external review requirement represented;
- process scorecard completed;
- no feature implementation;
- no product source mutation;
- enough precision that GPT can create the NEXT implementation as ONE large bounded Macro without another architecture-question round.

If WARDEN cannot trustworthy materialize the required governance without bypass/forgery, verdict is BLOCKED even if Claude could create equivalent Markdown manually.

Do not self-accept.
Do not begin implementation after READY.
Do not ask Slava for micro-prompts unless a genuine material authority blocker makes safe continuation impossible.

BEGIN WITH INTERNAL MACRO CRITIC PREFLIGHT NOW.
