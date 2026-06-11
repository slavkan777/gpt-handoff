REQUEST_ID: REQ-2026-06-12-twincore-ai-integrator-implementation-plan-review-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: analysis-only / review-only / report-only
PROJECT: TwinCore
GATE: planning / implementation-plan-review
ACTIVE_REQUEST_PATH: TwinCore/ai-integrator-implementation-plan-review-v0.1/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: TwinCore/ai-integrator-implementation-plan-review-v0.1/report.md
LATEST_REPORT_PATH: TwinCore/latest-report.md
COMPLETED: 2026-06-12
COMPLETED_BY: claude

# AI Integrator Implementation Plan v0.1 — Review Report

Reviewed artifact: `01_PROJECTS/TwinCore/FEATURE_PLANS/TWINCORE_AI_INTEGRATOR_IMPLEMENTATION_PLAN_V0_1.md`
(ai-kb tip `170a6cf` at review time). Line references below (`plan:NNN`) point to that file.

## 1. Executive Verdict

**ACCEPT_WITH_LIMITATIONS.**

The plan is implementable, honestly bounded, and consistent with the accepted architecture
(deterministic core, LLM for semantic gaps only, human-reviewed mapping, persistent MappingProfile,
generated typed adapter, zero runtime LLM). The vertical-slice, UI-feature-first structure matches
the owner's "workbench tool" vision, and the second-provider repeatability milestone is exactly the
right factory proof.

It is not yet ready to hand to an implementation gate as-is. Four design-level gaps must be resolved
(in a tech design gate or a plan v0.2): **mapping cardinality/array-iteration semantics, approved-
profile immutability, the generated-package build/test sandbox, and codegen injection safety** —
plus several smaller consistency fixes listed below. None are blockers for continuing planning.

## 2. Current State Restored

- AIKB: TwinCore status `LOGISTICS_CONNECTOR_FACTORY_PLANNING_REGISTERED`, gate `product
  architecture / MVP planning for Work Item 12695`; workstream `AI Integrator / Integration Mapping
  Factory`, first MVP slice `Logistics Rate API Connector Factory`, scope `Rate Quoting / Transit
  Time only`. Implementation is NOT open; the reviewed plan itself states it does not open
  implementation (plan:24).
- Prior accepted evidence: planning dossier v0.1 (`REQ-2026-06-12-twincore-logistics-connector-
  factory-planning-v0-1`, GPT verdict ACCEPT_WITH_LIMITATIONS) and framework audit
  (`REQ-2026-06-04-twincore-main-review-v0-2`) with still-relevant risks: zero tests, doc/code
  drift, fragile name-based assembly-scoped DI.
- This review honors all hard boundaries: no source repo access, no code, no branches, no AIKB
  writes, no plan modification, no Azure DevOps actions, no secrets. Writes limited to the four
  authorized gpt-handoff paths.

## 3. Role-Based Findings

### 3.1 Product Architect Reviewer

- ✅ Feature map (plan:65-103) covers the full product loop: import → schema → mapping → validation
  → generation → test → demo → repeatability. The pipeline (plan:48-56) matches the accepted ADR.
- ⚠️ **Owner-visible value lands late.** The first "wow" demo (generated adapter returning quotes)
  is M9 of 12. Define explicit owner demo checkpoints (recommended: after M4 mapping workbench and
  after M9 quote demo) so Igor sees progress twice before the full loop exists.
- ⚠️ **Provider schema re-import / versioning loop is absent.** Storage carries `Version` fields
  (plan:418, 436), but no feature handles "provider uploaded spec v2 → diff → re-review only the
  delta". Acceptable to defer the feature, but field identity must be diff-stable from day one
  (path-based hashes, not only auto-increment ids) or the deferral becomes a rewrite.
- 🔧 Pick one product display name for the UI shell (AI Integrator vs Connector Factory) before M1;
  three working names in AIKB are fine, three names in a demo UI are not.

### 3.2 UX / Workflow Reviewer

- ✅ Table/selectors-first Workbench, drag-and-drop deferred (plan:221, 786-788) — correct
  prioritization. Wizard with explicitly disabled future options (plan:131) is honest UX.
- ⚠️ **No search/filter in Provider Schema Viewer or Workbench** (plan:160-177, 217-233). Mock
  specs are small; a real FedEx-class spec has hundreds of fields. Add field search + "show only
  unmapped required / needs-review / conflicts" filters to F4/F7 acceptance — cheap now, painful later.
- ⚠️ **No integration status stepper.** `IntegrationStatus` exists in the domain (plan:135) but no
  UI surfaces "draft → source imported → mapped → validated → generated → tested". A thin status
  indicator in the integrations list gives users orientation for free.
- 🔧 Specify empty/error states for failed imports (partial parse of a broken spec: reject whole vs
  import-with-warnings). F3 has a validation panel (plan:147); decide the partial-failure behavior.

### 3.3 Backend / .NET Architecture Reviewer

- ✅ Conceptual API plan (plan:474-522) is clean and REST-sensible; explicit generated-provider
  registration mechanism (plan:315) correctly avoids the framework's fragile convention-scan DI.
- ⚠️ **Service sprawl risk.** Mapping alone names four services (plan:225) plus suggestion services
  (plan:203). Consolidate around aggregates (MappingProfile as the aggregate, one application
  service + validators) at tech design; otherwise the tool reproduces the anemic-service pattern
  the framework audit criticized.
- ⚠️ **Undecided: does the factory tool itself use TwinCore framework or plain ASP.NET Core?** The
  ADR fixes only that *generated adapters* must be contract-compatible with TwinCore-based apps.
  Given audit findings (zero tests, DI landmines), recommend the tool be plain ASP.NET Core with
  its own conventions; record this as an explicit tech-design decision either way.
- ⚠️ **UI stack is unspecified** (Blazor / Razor / SPA). This is the biggest unstated effort
  variable in the plan. Decide at tech design; for a .NET-shop internal workbench, a server-rendered
  .NET UI minimizes toolchain cost for M1-M9.
- ⚠️ **`GeneratedPackageBuildService` (plan:297) hides real complexity**: compiling and running
  tests on generated artifacts means temp-project orchestration, SDK invocation, timeouts, output
  capture, and isolation. This deserves its own design section before M8, not a one-line service name.

### 3.4 Domain Modeling Reviewer

- ✅ Domain object list (plan:377-396) is coherent and matches the dossier's canonical model; status
  enum extension with `Approved/Rejected` (plan:388) is a sensible refinement of the six statuses.
- ❌ **The deepest gap in the plan: mapping cardinality / array iteration semantics.**
  `MappingTransform` is "optional transformation rule" (plan:390) and rules are flat
  source→target pairs (plan:442). But a rate response is a LIST: `rateReplyDetails[]` must become
  N `ShipmentQuoteOption`s, and within each, the correct entry of a charges array must be selected.
  Without an explicit **iteration root** concept ("one QuoteOption per element of path X") and an
  **array selection strategy** (first / filter-by-type / aggregate), the response mapper cannot be
  generated. This must be specified before any generator design; it also changes the Workbench UI
  (the user must see/confirm the iteration root).
- ⚠️ **Transform kinds are unenumerated.** Recommend fixing the MVP set explicitly: direct copy,
  rename, type convert, unit convert, enum/code translation table, date/format parse, array select,
  constant/default. Enum translation (provider service codes → canonical taxonomy) was first-class
  in the accepted dossier and must not collapse into a generic "transform" string.
- ⚠️ **Approved-profile immutability is unspecified.** Storage has `Version/ApprovedAt/ApprovedBy`
  (plan:436), but F7/F8 never say "an approved MappingProfile version is frozen; edits create a new
  draft version; generation pins to an approved version". Without this, the audit trail and the
  generator determinism rule (plan:285) are both soft.

### 3.5 Data / Storage Reviewer

- ✅ Twelve-table logical model (plan:398-472) is proportionate; compute-on-demand validation
  (plan:247) and "no DB if in-memory is faster" for slice 1 (plan:713) are good lean calls.
- ❌ **Internal inconsistency:** F4 storage names `ProviderEndpoints` and `ProviderExamples`
  (plan:171) and the domain has `ProviderExamplePayload` (plan:169), but §6 defines neither table
  (plan:398-472). Either add both tables or fold endpoints/examples into `ProviderSchemas`/
  `ProviderFields` explicitly.
- ⚠️ `RawContentRef` (plan:412) — blob storage location, size limits, and retention policy are
  undefined. Low risk (specs, not PII), but decide at tech design.
- 🔧 If slice 1 is in-memory, mandate a repository abstraction from day one so the in-memory →
  EF transition (M2+) does not rewrite domain services.

### 3.6 Generator Pipeline Reviewer

- ✅ Determinism rule "same schema + same profile + same generator version = same output"
  (plan:285) and generator-version stamping (plan:587) are exactly right; snapshot/diff tests
  (plan:583-588) support partial regeneration claims.
- ❌ **Codegen injection safety is absent.** Provider field names are untrusted input that flows
  into generated C# identifiers and string literals. A hostile or merely weird spec (C# keywords,
  unicode tricks, quotes in descriptions) must produce safe, compilable code: identifier
  escaping/normalization, literal escaping, reserved-word handling. Add to generator design + tests.
- ⚠️ **Package output format undefined**: zip of a compilable project folder? NuGet package? Where
  do artifacts live beyond "path/blob" (plan:454)? Recommend MVP = downloadable zip of a buildable
  project + stored content hashes (already present, plan:454) for drift detection.
- 🔧 Generated files need "GENERATED — DO NOT EDIT" headers and a regeneration-drift check via the
  existing `ContentHash` (detect hand-edits before overwrite).

### 3.7 Testing / QA Reviewer

- ✅ Test strategy (plan:557-618) is the strongest section: importer, suggestion, validation,
  snapshot, generated-adapter, runtime, negative, and repeatability tests all present — a direct
  answer to the framework audit's zero-test finding.
- ❌ **Multi-option fixtures are missing.** Tests say "provider response maps to canonical quote"
  (plan:593) and "quote returns at least one option" (plan:321) — singular. The defining rate-API
  shape is N options per response. Golden fixtures must contain ≥2 service options and ≥2 charge
  entries, asserting ≥2 normalized QuoteOptions with the correct charge selected.
- ⚠️ **The mock fixture itself is a key unplanned artifact.** "Mock OpenAPI" appears throughout but
  is never designed. Build it deliberately FedEx-shaped (nested arrays of rated details, charge
  lists, alerts) so M4-M10 hit realistic complexity, plus a deliberately messy second fixture for
  the LLM assistant demo (M11) — deterministic-friendly mocks will make F14 look useless.
- ⚠️ Acceptance matrix (plan:756-774) lacks an evidence column. Add per-row evidence tuples
  (command + exit code / artifact path / log) so milestone acceptance is verifiable, not narrative.
- 🔧 Add determinism tests at import level too: import same spec twice → identical ProviderSchema
  content hash (plan:175 implies it; make it a hash assertion).

### 3.8 Security / Secrets / Compliance Reviewer

- ✅ No real credentials in MVP (plan:131), sanitized logs as a named service and test
  (plan:297, 303), no secrets in fixtures (plan:753), oversized-input rejection (plan:155).
- ❌ **Codegen injection** (see 3.6) is also a security finding: generated code is compiled and
  executed (F11). With mock-only inputs the blast radius is local, but the test harness will run
  attacker-shaped input the moment a real spec is imported. Sanitize identifiers/literals + add a
  hostile-spec negative test.
- ⚠️ **LLM prompt scrubbing needs a definition.** F14 tests "sensitive data not included in prompt
  context" (plan:355) — define what is scrubbed: example values matching secret patterns (real
  specs embed sample tokens/keys in `example` fields), URLs with credentials, anything from
  `securitySchemes` beyond scheme type.
- ⚠️ **Future URL import = SSRF surface** (plan:147 placeholder). Fine as placeholder; when
  activated, require scheme/host validation and no internal-network fetches. Record now so it does
  not ship naively later.
- 🔧 Upload handling: content-type/extension validation and parse-only treatment of uploads are
  implied but unstated — one line in tech design closes it.

### 3.9 LLM Governance Reviewer

- ✅ Strong: no LLM in first suggestion pass (plan:207), every LLM suggestion `Needs review`, never
  auto-approved (plan:347), generation blocked while unreviewed suggestions exist (plan:579), zero
  runtime LLM with an explicit test (plan:321, 602), assistant deferred to M11 after the
  deterministic factory is proven.
- ⚠️ **Suggestion caching is "optional" (plan:211) — make it required** per ProviderSchema version:
  it is the main cost-control lever and was part of the accepted dossier's LLM-minimization answer.
- ⚠️ **LLM call auditability:** `LlmSuggestion`/`SuggestionProvenance` (plan:350-352) should also
  record model id, prompt template version, and prompt content hash — otherwise "why did AI suggest
  this?" is unanswerable during review, which undercuts the governance story.
- 🔧 Minimize prompt context structurally: send only unresolved fields + their subtree, never the
  whole spec. Add a budget guard (max calls per schema version) as configuration.

### 3.10 Delivery / Milestone Reviewer

- ✅ M1-M12 (plan:620-692) are coherent, each with a DONE line; first slice (plan:694-754) is
  genuinely small and demoable.
- ❌ **M0 is missing.** The plan starts at "M1 — UI shell" but nothing creates the workspace:
  feature branch, solution skeleton, test harness, CI hook, fixture assets. That setup is itself a
  gated action (branch creation is currently forbidden) and must be the first implementation gate.
- ⚠️ **12 sequential milestones = 12 micro-gates** if mapped 1:1 to Claude prompts — against the
  owner's molecular mega-gate preference. Group into 4 implementation gates with internal phase
  locks: G0=setup(M0), G1=M1-M3 (import+browse), G2=M4-M5 (mapping+gate), G3=M6-M9
  (generate+test+demo), G4=M10-M12 (repeatability+LLM+SOAP slot).
- 🔧 No effort sizing anywhere — correct for v0.1 (no invented numbers), but sizing must happen at
  tech design once the UI stack is chosen, or G1 cannot be scoped honestly.

## 4. Plan Strengths

1. Vertical-slice discipline: every feature names UI + backend + domain + storage + tests + DONE.
2. Deterministic-first ordering with LLM late (M11) — the architecture promise is enforced by the
   milestone order itself, not just by prose.
3. Mock-first carrier strategy kills the sandbox-blocker risk for the whole MVP.
4. Second-provider repeatability as a mandatory, named milestone — the factory-vs-hardcoded-demo
   proof is in the plan, not in good intentions.
5. Coverage gate with negative tests (invalid profile cannot generate) — quality is structural.
6. Explicit DI registration and own test harness for generated output — directly responds to the
   framework audit's two biggest risks.
7. Honest SOAP placeholder with "no false implementation claim" (plan:373).
8. Acceptance matrix exists at all (plan:756-774) — most plans this early have none.

## 5. Gaps in the Implementation Plan

Ranked by severity:

1. **Mapping cardinality / array iteration semantics undefined** (3.4) — blocks generator design.
2. **Codegen injection safety absent** (3.6/3.8) — security + correctness.
3. **Generated-package build/test sandbox undesigned** (3.3) — M8's hidden iceberg.
4. **Approved-profile immutability/versioning unspecified** (3.4) — audit trail + determinism soft.
5. **M0 setup milestone missing** (3.10).
6. Storage inconsistency: `ProviderEndpoints`/`ProviderExamples` named but not defined (3.5).
7. Multi-option golden fixtures missing; mock fixture itself unplanned (3.7).
8. Transform kinds unenumerated; enum translation demoted to generic transform (3.4).
9. No search/filter/status-stepper UX for real-spec scale (3.2).
10. LLM suggestion audit fields and required caching (3.9).
11. Tool's own stack decisions open: TwinCore-framework-or-plain, UI technology (3.3).
12. ProviderSchema re-import/diff loop absent; field identity stability undecided (3.1).

## 6. Recommended Additions

1. **"Mapping semantics" section in plan v0.2 or tech design**: iteration root, array selection
   strategies, enumerated TransformKind set, profile draft/approved versioning with frozen approved
   versions.
2. **M0 — Workspace setup milestone/gate**: feature branch, solution skeleton, test harness, CI,
   fixture assets. First implementation gate; explicitly authorized branch creation.
3. **Designed mock fixtures as artifacts**: one FedEx-shaped realistic spec (arrays, nested
   charges, alerts), one messy/incomplete spec for the LLM assistant demo, plus golden multi-option
   response fixtures.
4. **Generator safety subsection**: identifier/literal sanitization, reserved words, hostile-spec
   negative test, "GENERATED — DO NOT EDIT" headers, drift detection via ContentHash.
5. **Build-sandbox design note** for F11/M8: temp project layout, SDK invocation, timeout, output
   capture, isolation.
6. **Evidence column in the acceptance matrix** (command+exit / artifact / log per row).
7. **UX minimums for scale**: field search/filter, "unmapped required only" view, integration
   status stepper.
8. **LLM governance hardening**: required suggestion cache per schema version, model/prompt-hash
   audit fields, prompt context minimization, call budget config.
9. **Storage fixes**: add or explicitly fold `ProviderEndpoints`/`ProviderExamples`; blob location
   + retention for `RawContentRef`; repository abstraction for the in-memory slice.

## 7. Recommended Removals or Deferrals

Nothing in the plan needs deletion. Confirm/sharpen these deferrals:

1. **ProviderSchema re-import/diff** — defer the feature, but adopt diff-stable field identity now.
2. **`GenerationPlans` persistence** (plan:444-448) — MVP can compute plans on demand; persist only
   `GenerationRuns`/artifacts. Optional simplification, not required.
3. **Feature-flag machinery in F1** (plan:115) — static config is enough for MVP; do not build a
   flag system.
4. **URL import** — stays a placeholder; activate only with SSRF guards (3.8).
5. **Drag-and-drop, real sandbox, SOAP implementation, docs-only LLM parsing** — already correctly
   deferred; keep them out of G1-G3 scope statements explicitly.

## 8. First Slice Review

Verdict on plan:694-754: **right size, right content, two adjustments.**

- ✅ Shell + wizard + paste/upload + schema viewer + in-memory schema is a genuine end-to-end thread
  with no generator/LLM/DB — small enough for one bounded implementation gate after M0.
- 🔧 **Add the designed FedEx-shaped fixture to the slice DONE list** — "system shows endpoint and
  field tree" should be proven against the realistic fixture, not a toy, or the schema viewer's
  tree/array handling will be revisited in M4.
- 🔧 **Mandate the repository abstraction inside the slice** ("no database required" is fine; "no
  persistence seam" is not — the M2+ transition must not rewrite services).
- Optional cheap win: the integration status stepper (even static) in the slice shell.

## 9. Proposed Refined Milestone Sequence

```text
G0 (setup gate)        M0: branch + solution skeleton + CI + test harness + fixtures
G1 (import gate)       M1 shell → M2 schema import → M3 internal model browser
                       [Owner demo checkpoint #1: import & browse]
G2 (mapping gate)      M4 workbench → M5 validation/coverage gate
                       [Owner demo checkpoint #2: reviewed, gated MappingProfile]
G3 (generation gate)   M6 generation review → M7 generator → M8 test runner → M9 quote demo
                       [Owner demo checkpoint #3: generated adapter returns normalized quotes]
G4 (factory gate)      M10 second provider → M11 LLM assistant (messy fixture) → M12 SOAP slot
                       [Owner demo checkpoint #4: factory proof + AI assist]
```

Mapping semantics (iteration root, transforms) must be settled in tech design before G2; build
sandbox design before G3. Each G-gate = one molecular Claude gate with internal phase locks, stop
conditions, and a per-milestone evidence report — 4 gates instead of 12 micro-prompts.

## 10. Acceptance Criteria Improvements

Strengthen the matrix (plan:756-774) with:

1. **Evidence tuple per row**: command + exit code / artifact path / log excerpt — no narrative-only
   acceptance.
2. **Multi-option assertion**: golden fixture with ≥2 service options and ≥2 charge entries →
   ≥2 QuoteOptions, correct charge entry selected per option.
3. **Determinism hashes**: same spec imported twice → identical ProviderSchema hash; same profile
   generated twice → identical artifact ContentHashes.
4. **Hostile-spec safety**: spec containing C# keywords / quotes / unicode in field names →
   generation succeeds, package compiles, no template injection.
5. **Approved-version pinning**: editing an approved profile creates a new draft version; generation
   from the approved version remains reproducible; negative test that drafts cannot generate.
6. **Required-field waiver trail**: ignoring/waiving a REQUIRED field demands a recorded reason +
   reviewer identity (plan:231 hints at waiver policy — make it a tested rule).
7. **Zero-LLM runtime, verified structurally**: no LLM service registered in the runtime DI scope +
   no outbound LLM traffic in demo run logs (not just "no call happened to occur").
8. **Repeatability without core change, verified by diff**: G4 acceptance includes showing the
   factory-core diff between provider A and provider B integrations is empty.

## 11. Risks and Mitigations

Plan's own risk list (plan:776-830) is good; confirmed + additions:

| Risk | Status | Mitigation |
|---|---|---|
| Scope creep to TMS | In plan | Keep; reinforce via G-gate scope statements |
| UI polish too early | In plan | Keep; table-first confirmed correct |
| Real sandbox blockers | In plan | Keep; mock-first confirmed |
| LLM overuse | In plan | Keep; add required cache + budget guard (3.9) |
| Hardcoded demo | In plan | Keep; strengthen with core-diff-empty acceptance (10.8) |
| Weak tests | In plan | Keep; add multi-option + hostile-spec + hash tests |
| TwinCore DI/docs drift | In plan | Keep; explicit DI already planned |
| SOAP derail | In plan | Keep; slot-only confirmed |
| Mapping ambiguity | In plan | **Partially mitigated** — statuses exist, but cardinality gap (5.1) must close or this risk lands in the generator |
| **NEW: codegen injection** | Missing | Identifier/literal sanitization + hostile-spec tests (6.4) |
| **NEW: build-sandbox complexity** | Missing | Design note before G3; timeouts/isolation (6.5) |
| **NEW: too-clean mock fixture → false confidence** | Missing | FedEx-shaped + messy fixtures as designed artifacts (6.3) |
| **NEW: milestone fragmentation overhead** | Missing | 4 molecular G-gates instead of 12 micro-gates (§9) |
| **NEW: stack indecision late-binding** | Missing | UI stack + tool-framework decision fixed at tech design, before G0 |

## 12. What Should Be Updated in AIKB Later (if Slava accepts this review)

For Architect GPT after acceptance (not performed by this review):

1. Plan v0.2 amendments or a linked tech-design mandate covering: mapping cardinality/iteration
   root + TransformKind set; approved-profile versioning; M0 setup milestone; G0-G4 gate grouping;
   storage table fixes (`ProviderEndpoints`/`ProviderExamples`); generator safety section; build
   sandbox note; fixture artifacts; acceptance matrix evidence column; LLM cache/audit hardening.
2. `TASK_LEDGER.md`: entry for this review gate with verdict and evidence paths.
3. `CURRENT_STATE.md`: point latest evidence at this review report; next safe step = Igor
   validation and/or tech design gate.
4. Optional: refresh `TwinCore/latest-summary.json` in gpt-handoff — it was outside this request's
   write scope and still describes the previous (dossier) report.

## 13. What Should NOT Be Done Yet

- No implementation, no feature branch (M0 is a *future* gate), no solution skeleton.
- No code generation experiments against the plan, no `.claude/agents` files.
- No final UI-stack or tool-framework decision without Igor (tech design gate input).
- No real carrier sandbox accounts, no provider credentials, no LLM provider selection/spend.
- No AIKB writes from this review (GPT applies §12 only after Slava's acceptance).
- No edits to the reviewed plan file itself.

## 14. Final Recommendation

**ACCEPT_WITH_LIMITATIONS** — adopt the plan as the working implementation blueprint, with the
§5/§6 items folded into either a plan v0.2 or (recommended) the upcoming **tech design gate**, which
should open with the four design-level gaps as its mandatory agenda: mapping cardinality semantics,
approved-profile versioning, build-sandbox design, and generator injection safety. Keep Igor
validation as the first next gate — this review sharpens the plan but does not substitute owner
validation of the product framing.

Report written only to the four authorized paths (gate report, `latest-report.md`,
`_BRIDGE/LATEST_REPORT.md`, `_BRIDGE/STATUS.json`). Nothing else touched.
