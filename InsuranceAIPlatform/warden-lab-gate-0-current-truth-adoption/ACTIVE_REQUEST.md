# PROJECT: InsuranceAIPlatform
# GATE: WARDEN_LAB_GATE_0_CURRENT_TRUTH_ADOPTION
# REQUEST_ID: REQ-2026-08-11-IAP-WARDEN-LAB-G0-CURRENT-TRUTH

STATE: OWNER_AUTHORIZED / READY_FOR_CLAUDE
TASK_TYPE: READ_ONLY_CURRENT_TRUTH_ADOPTION
OWNER: Slava / Вячеслав

## Owner authorization

Slava explicitly confirmed on 2026-08-11:

- use the existing InsuranceAIPlatform as the first real WARDEN-Lab carrier;
- future bounded feature direction is `Customer Insurance AI Assistant`;
- Gate 0 is authorized now;
- Gate 0 is read-only current-truth/existing-project adoption;
- no feature implementation is authorized yet.

## Role

Claude = read-only executor/discoverer for this Gate.

Do not claim Owner authority.
Do not freeze the future feature Contract.
Do not modify WARDEN v1 source.
Do not self-accept Gate 0.

This is ONE bounded Macro. Do not split it into owner-facing micro-prompts. Internal planning/checkpoints are allowed inside the run.

## Routing lock

PROJECT: InsuranceAIPlatform
SOURCE_REPO_REMOTE_EXPECTED: `slavkan777/InsuranceAIPlatform`
LOCAL_WORKSPACE: discover mechanically; do not assume stale AIKB path/branch
HANDOFF_REPO: `slavkan777/gpt-handoff`
PROJECT_BRIDGE: `InsuranceAIPlatform/_BRIDGE/`
GATE_REPORT: `InsuranceAIPlatform/warden-lab-gate-0-current-truth-adoption/report.md`
LATEST_REPORT: `InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md`
PROJECT_LATEST_REPORT: `InsuranceAIPlatform/latest-report.md`

AIKB CURRENT STATE:
`slavkan777/ai-kb/01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md`

AIKB GATE PLAN:
`slavkan777/ai-kb/01_PROJECTS/InsuranceAIPlatform/FEATURE_PLANS/WARDEN_LAB_GATE_0_CURRENT_TRUTH_ADOPTION.md`

AIKB OWNER DECISION:
`slavkan777/ai-kb/01_PROJECTS/InsuranceAIPlatform/DECISIONS/DECISION_2026-08-11_warden_lab_customer_assistant.md`

## Gate objective

Establish enough PRIMARY CURRENT TRUTH about the existing InsuranceAIPlatform to freeze the next WARDEN feature SPEC without guessing.

The project predates WARDEN. Historical AIKB, old Claude reports, old accepted gates, screenshots and prose are context only until checked against current source/runtime.

REPORT != EVIDENCE.

Do not rewrite historical evidence as if WARDEN governed it retroactively.

## Candidate next feature — context only, not implementation

Future feature candidate:

`Customer Insurance AI Assistant`

Intended product pain:

A customer who has had an accident/insurance event may not know:

- what to do next;
- which documents are required or missing;
- what current claim status means;
- what grounded policy/claim information is available;
- when a human specialist is required.

Hard future product boundary:

The assistant may guide, explain, retrieve grounded facts/citations and hand off to a human. It must not autonomously make final coverage, payout, fraud, legal or claim-approval decisions.

This candidate MUST NOT bias current-truth discovery into inventing capabilities that do not exist.

======================================================================
1. FIRST SAFE ACTION — IDENTITY / WORKSPACE DISCOVERY
======================================================================

Do not assume the old AIKB branch/path is current.

First resolve the real local repository safely.

Preferred sequence:

1. Check the obvious existing project workspace under `C:\Projects` if present.
2. Verify a candidate by `git rev-parse --show-toplevel` and its configured remote URL.
3. The canonical source candidate must match `slavkan777/InsuranceAIPlatform` or report the exact divergence.
4. Do not clone, checkout, pull, fetch-with-write, switch branch, reset, clean, stash, commit or modify remotes.

Record before any build/test:

- exact repo root;
- branch;
- HEAD SHA;
- remotes;
- upstream/ahead/behind if determinable without mutating source;
- tracked/untracked/modified/deleted worktree inventory;
- recent relevant commits;
- Git version.

If the local repo cannot be resolved unambiguously:

STOP with `WARDEN_LAB_GATE0_BLOCKED`.

======================================================================
2. READ-ONLY / NO-SOURCE-MUTATION LAW
======================================================================

No edits to governed product source, tests, config, migrations, docs or project files.

Allowed writes are only incidental non-governed/generated outputs required by normal read-only verification, such as ignored `bin/`, `obj/`, frontend build output, test result temp files or OS temp files.

Before and after every verification phase compare git worktree state.

No command may intentionally create a tracked/untracked project artifact unless it is clearly ignored generated output.

Forbidden:

- source/test/config/doc edits;
- package/version changes;
- `npm install` / lockfile changes unless dependency installation is proven necessary and can be done outside governed source without changing lockfiles; prefer existing environment;
- EF migration creation/application;
- DB writes;
- branch creation/switching;
- commit/push/merge/PR/tag/release;
- `git pull`, reset, clean, checkout restore, stash;
- Azure mutation/deploy/restart/scale/config changes;
- secrets modification;
- paid-provider enablement;
- production/customer data access.

If a required truth cannot be established without a forbidden mutation, report it as UNKNOWN/BLOCKED. Do not cross the boundary.

======================================================================
3. SOURCE / SOLUTION TOPOLOGY
======================================================================

Read the real repository sufficiently to produce a current topology map.

At minimum identify with exact paths:

Frontend:
- framework/toolchain;
- entrypoint;
- routing structure;
- homepage/dashboard route(s);
- claims routes;
- claim workspace components;
- API abstraction/facade;
- state-management/orchestration layer;
- existing AI/RAG UI surfaces.

.NET backend:
- solution/project files;
- API host;
- controllers/endpoints relevant to claims, documents/evidence, RAG, advanced AI review;
- application/domain/services boundaries;
- persistence projects and DB provider;
- authentication/demo auth shape if present;
- configuration/provider seams.

Python/LangChain:
- exact sidecar path;
- framework;
- endpoints;
- provider modes;
- how .NET calls it;
- feature flags/configuration.

Do not infer a layer from a historical report if current source does not support it.

======================================================================
4. CURRENT BUILD / TEST TRUTH
======================================================================

Discover actual repository commands from current files/scripts/readme/solution.

Run the strongest SAFE local verification possible without changing governed source.

Record exact command, exit code, pass/fail/skip counts, warnings/errors and duration where available.

At minimum attempt, when the current environment supports it:

- .NET clean/current Release build;
- .NET tests;
- frontend build;
- frontend lint if configured;
- frontend automated tests/E2E if configured and runnable safely;
- Python sidecar tests/smoke if configured and runnable safely.

Do not turn a missing dependency/tool/runtime into a fake PASS.

Use labels:

PASS / FAIL / NOT_CONFIGURED / NOT_RUN_WITH_REASON / BLOCKED.

After verification, prove worktree source state did not change.

======================================================================
5. CURRENT RAG / AI TRUTH
======================================================================

Trace CURRENT code end-to-end, with exact paths/classes/functions/endpoints, for:

A. document/evidence ingestion;
B. chunking/storage;
C. retrieval;
D. claim scoping / cross-claim isolation;
E. grounded answer generation;
F. citations/retrieved chunk IDs;
G. insufficient-evidence handling;
H. provider routing/fallback;
I. confidence/cost/audit/trace fields;
J. LangChain advanced review flow;
K. human-in-the-loop/advisory-only boundary.

For each capability distinguish:

- CURRENTLY IMPLEMENTED;
- CURRENTLY TESTED;
- CURRENTLY LIVE/CONFIGURED;
- SEAM ONLY / OPTIONAL;
- HISTORICAL CLAIM NOT YET REVERIFIED.

Do not say Qdrant/Ollama/paid managed LLM is live unless current primary evidence proves it.

======================================================================
6. DATA / PERSISTENCE / SAFETY TRUTH
======================================================================

Establish from current source/config/read-only runtime evidence:

- DB provider(s);
- schema/migration presence;
- synthetic demo-data boundary;
- whether any real PII path is in scope;
- document/file storage mechanism;
- claim/evidence scoping keys;
- audit trail structures;
- human approval/decision controls;
- any direct customer messaging/payout/fraud decision code.

Do not print secret values.

If config files contain secrets, report only key names/location/category and redact values.

======================================================================
7. AZURE / LIVE RUNTIME — READ ONLY
======================================================================

Historical AIKB says a live dev/test deployment exists. Reverify only through SAFE READ-ONLY means if authenticated tooling is already available.

Possible read-only checks:

- Azure account/context identity (redacted appropriately);
- resource existence/state;
- Static Web App URL reachability;
- Container App revisions/status;
- sidecar existence/ingress mode;
- backend health/read-only endpoints;
- current effective provider mode returned by health/infrastructure endpoints if safe;
- current public UI reachability.

NO Azure writes.
NO restarts.
NO deployments.
NO scaling.
NO config changes.
NO secret retrieval/printing.
NO DB mutation.

If auth/tooling is unavailable, record `UNKNOWN — AUTH/TOOLING NOT AVAILABLE` rather than relying on historical prose.

======================================================================
8. AIKB / CURRENT TRUTH DRIFT MATRIX
======================================================================

Build a table with at least these columns:

AIKB/HISTORICAL CLAIM | CURRENT PRIMARY EVIDENCE | STATUS | MATERIALITY | NOTES

STATUS:
- MATCH
- STALE
- UNKNOWN
- CONTRADICTED

Cover at minimum:

- source repo;
- current branch;
- current HEAD;
- default/main state;
- Azure frontend/backend;
- Azure SQL;
- .NET RAG provider mode;
- vector retrieval mode;
- Qdrant;
- Ollama/LocalLlama;
- paid/managed LLM;
- LangChain sidecar;
- sidecar provider mode;
- accepted test counts;
- claim-scoped citations;
- cross-claim leakage guard;
- synthetic-data boundary;
- known product gaps.

Materiality here means: would this stale/unknown fact change the future Customer Assistant architecture, risk, scope or acceptance contract?

======================================================================
9. CUSTOMER ASSISTANT INTEGRATION BOUNDARY — READ-ONLY RECOMMENDATION
======================================================================

Based ONLY on verified current source, propose the smallest safe integration boundary for the future feature.

Do NOT implement it.

Report:

- likely customer-facing route/surface;
- existing reusable frontend shell/components versus new components;
- existing reusable API/RAG services versus new endpoints/services;
- whether customer assistant should operate against claim-scoped context, policy knowledge, pre-claim guidance, or a bounded combination;
- authentication/demo-session implications;
- data/persistence needs;
- human-handoff seam;
- observability/audit needs;
- safety/advisory guardrails;
- what should explicitly stay out of v1.

Separate:

FACTS (verified current source)
from
RECOMMENDATION (design candidate for Gate 1).

======================================================================
10. WARDEN ADOPTION RECOMMENDATION
======================================================================

Do not initialize/mutate `.warden` in InsuranceAIPlatform during Gate 0 unless the Owner explicitly opens that mutation in a later gate.

Produce a recommendation for Gate 1 containing:

- candidate WARDEN risk profile and why;
- candidate product source boundary;
- candidate frozen Owner goal;
- candidate requirement groups;
- candidate acceptance/evidence classes;
- candidate external-review requirement;
- candidate build/test/runtime evidence package;
- candidate security/privacy/safety checks;
- candidate delivery boundary;
- candidate post-close Child Gate test.

Flag every unresolved Owner decision required before freezing Gate 1.

Do NOT invent Owner intent where it is missing.

======================================================================
11. WARDEN-LAB META TEST PLAN
======================================================================

Gate 0 must also state how this existing project will later test WARDEN itself without corrupting the real accepted feature.

Plan disposable-only checks for later gates, including:

- stale evidence after source mutation;
- proof/test replacement while selector remains similar;
- material finding blocking;
- external report lineage / immediate predecessor;
- risk downgrade without boundary;
- BREAK_GLASS lifecycle;
- engineering lock;
- Owner Acceptance distinct from Delivery Authorization;
- operation-specific side-effect grant;
- post-close Child Gate lineage/budget continuity.

These are PLAN ONLY in Gate 0. Do not execute intentional corruption now.

======================================================================
12. FINAL VALIDATION / STOP
======================================================================

Before reporting:

- rerun `git status` / source identity checks;
- prove no governed source mutation occurred;
- list any generated/ignored outputs created by builds/tests;
- verify no commit/push/branch/Azure/DB/secret/provider side effect occurred;
- do not clean/reset/delete to hide generated state.

Gate 0 READY standard:

Enough primary truth exists to freeze Gate 1 without guessing about material architecture/scope/risk/acceptance facts.

If material unknowns remain, verdict must be BLOCKED and name the MINIMUM Owner/tool action needed.

======================================================================
13. REPORT OUTPUT
======================================================================

Write one report to:

`InsuranceAIPlatform/warden-lab-gate-0-current-truth-adoption/report.md`

Mirror the same current report to:

`InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md`
`InsuranceAIPlatform/latest-report.md`

Do not overwrite another project path.

Report structure:

A. Execution identity
B. Repository identity
C. Worktree immutability proof
D. Current topology map
E. Build/test truth
F. RAG/AI truth
G. Data/persistence/safety truth
H. Azure/live read-only truth
I. AIKB drift matrix
J. Verified reusable capabilities
K. Customer Assistant candidate integration boundary
L. WARDEN Gate 1 adoption recommendation
M. WARDEN-Lab later falsification/meta-test plan
N. Material unknowns / Owner decisions required
O. Side effects
P. Final verdict

Final verdict exactly one:

`WARDEN_LAB_GATE0_CURRENT_TRUTH_READY`

or

`WARDEN_LAB_GATE0_BLOCKED`

Do not return feature implementation.
Do not freeze Gate 1.
Do not self-accept.

BEGIN READ-ONLY CURRENT TRUTH ADOPTION NOW.
