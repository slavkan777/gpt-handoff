REQUEST_ID: REQ-2026-06-12-twincore-ai-integrator-tech-design-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: technical-design-only / report-only
PROJECT: TwinCore
GATE: planning / technical-design
ACTIVE_REQUEST_PATH: TwinCore/ai-integrator-tech-design-v0.1/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: TwinCore/ai-integrator-tech-design-v0.1/report.md
LATEST_REPORT_PATH: TwinCore/latest-report.md
COMPLETED: 2026-06-12
COMPLETED_BY: claude

# TwinCore AI Integrator — Technical Design Dossier v0.1

> Design notation note: JSON/YAML sketches and signature-level fragments below are **design
> notation inside this report**, not source code. No implementation files, no solution, no branch,
> no repo changes were created. Inputs: Implementation Plan v0.1 (`plan:NNN` line refs) and the
> plan review report (`review §N.N`), both read at ai-kb tip `170a6cf` / gpt-handoff tip `3c17fee`.

## 1. Executive Summary

This dossier turns the four design-level gaps from the plan review into concrete, implementable
designs and resolves the supporting agenda items:

1. **Mapping cardinality** is solved with **iteration scopes**: a reviewable `IterationRoot` rule
   binds a provider array path to the canonical `QuoteOptions[]` collection; rules inside the scope
   use element-relative paths; arrays crossed *outside* an iteration root require an explicit
   `ArraySelect` strategy (first / by-predicate / by-index / aggregate).
2. **TransformKind** is a closed nine-kind set with typed parameters and per-kind validation;
   enum/code translation is a first-class table, not a string transform.
3. **MappingProfile lifecycle** is copy-on-write versioning: Draft → Approved (frozen, hashed) →
   Superseded; edits clone a new draft; generation pins to `(ProfileId, Version, ProfileHash)`.
4. **ProviderSchema identity** is hash-based: `SourceHash`, `SchemaHash`, and per-field stable
   `FieldKey = hash(operationKey + direction + path)` — re-import/diff becomes a delta-review,
   not a remap.
5. Generator safety, build sandbox, fixtures, UI implications, LLM governance, G0-G4 gate grouping,
   and an acceptance-evidence model are specified below.

One decision is deliberately left to the owner: the UI stack (a recommendation is given in §12).
Nothing here opens implementation; G0 remains a future, separately authorized gate.

## 2. Current State and Boundaries

- AIKB: status `LOGISTICS_CONNECTOR_FACTORY_PLANNING_REGISTERED`, gate `product architecture / MVP
  planning (Work Item 12695)`; workstream `AI Integrator / Integration Mapping Factory`; first MVP
  slice `Logistics Rate API Connector Factory`; scope `Rate Quoting / Transit Time only`.
- Prior evidence chain: planning dossier (ACCEPT_WITH_LIMITATIONS) → implementation plan v0.1 →
  plan review (ACCEPT_WITH_LIMITATIONS, four design gaps) → this tech design.
- Boundaries honored by this gate: no `Twincore-framework` access or changes, no branches, no
  source code or solution skeleton, no AIKB writes, no plan-file edits, no Azure DevOps actions,
  no `.claude/agents`, no secrets, no implementation gate opened. Writes restricted to the four
  authorized gpt-handoff paths.

## 3. Design Decisions

| # | Decision | Status |
|---|---|---|
| D1 | Path notation = deterministic JSONPath subset (grammar frozen in §4.5) | Decided here |
| D2 | Iteration root modeled as a `MappingRule` of `RuleKind=IterationRoot` (one review surface) | Decided here |
| D3 | `ArraySelect` strategies: First / ByPredicate / ByIndex / Aggregate(Sum,Min,Max) | Decided here |
| D4 | TransformKind = closed 9-kind enum with typed params; chains capped at 2 per rule in MVP | Decided here |
| D5 | Profile versioning = copy-on-write; approved versions immutable + content-hashed | Decided here |
| D6 | Field identity = `FieldKey` path-hash; mapping rules reference FieldKey, not only DB ids | Decided here |
| D7 | Generated DTOs keep wire names via serializer attributes; C# identifiers are sanitized cosmetics | Decided here |
| D8 | Code emission through a single escaping/identifier utility (Roslyn SyntaxFactory preferred) | Decided here |
| D9 | Sandbox restore = offline, pre-staged local package cache; pinned package allowlist | Decided here |
| D10 | `ProviderEndpoints` + `ProviderExamples` become real tables; `EnumMapTables/Rows` added | Decided here |
| D11 | Raw sources in content-addressed file store behind `ISourceContentStore` | Decided here |
| D12 | Repository abstraction from slice 1; in-memory impls in G1, EF required from G2 start | Decided here |
| D13 | Integration status stepper = derived state, computed from artifact existence | Decided here |
| D14 | UI stack: recommend Blazor Server behind a clean API layer | Recommended — owner decides |
| D15 | LLM suggestion cache required; prompt/model hashes audited; budget guard config | Decided here |
| D16 | Implementation grouped into gates G0-G4, each one molecular Claude gate | Decided here (process) |
| D17 | Multi-package requests, schema-diff UI, SOAP implementation, `ManualCode` transform codegen | Explicitly deferred |

## 4. Mapping Semantics Design

### 4.1 Scopes and the iteration root

A `MappingProfile` contains two rule sets: **RequestMapping** (canonical → provider) and
**ResponseMapping** (provider → canonical). Every rule belongs to a **scope**:

- **Root scope** — the message root. Paths are absolute (`$.`-prefixed).
- **Iteration scope** — declared by an `IterationRoot` rule:
  `sourceCollectionPath` (provider array) → `targetCollection` (canonical collection).
  The mapper emits **one target item per source element**; rules inside the scope use paths
  **relative to the current element**.

MVP constraints (validated): ResponseMapping MUST contain exactly one `IterationRoot` targeting
`QuoteOptions[]` (its absence = `Required missing`; more than one for the same target = `Conflict`).
Nested iteration scopes are supported by the model but restricted to depth 1 in MVP UI.
RequestMapping in MVP is scalar (single package per canonical model); multi-package request
iteration is deferred (D17).

### 4.2 One response → N quote options (worked example, FedEx-shaped)

```yaml
ResponseMapping:
  - kind: IterationRoot
    source: $.output.rateReplyDetails[*]
    target: QuoteOptions[]
  - source: serviceType                # relative to rateReplyDetails element
    target: QuoteOption.ProviderServiceCode
    transform: DirectCopy
  - source: serviceName
    target: QuoteOption.ServiceName
    transform: DirectCopy
  - source: ratedShipmentDetails[?(@.rateType=='ACCOUNT')].totalNetCharge.amount
    target: QuoteOption.Price.Amount
    transform: [ArraySelect(ByPredicate), TypeConvert(decimal)]
  - source: ratedShipmentDetails[?(@.rateType=='ACCOUNT')].totalNetCharge.currency
    target: QuoteOption.Price.Currency
    transform: [ArraySelect(ByPredicate), DirectCopy]
  - source: commit.dateDetail.dayFormat
    target: QuoteOption.EstimatedDeliveryDate
    transform: DateParse("yyyy-MM-dd'T'HH:mm:ss", DateOnly)
  - source: $.output.alerts[*].message  # absolute path = root scope, evaluated once
    target: QuoteResult.Warnings        # design rule: root-level alerts map to the result
    transform: ArrayMapToList           # envelope (once), not into each QuoteOption
```

Golden assertion: a fixture response with 2 `rateReplyDetails` elements, each containing both an
`ACCOUNT` and a `LIST` charge entry, must produce exactly 2 `QuoteOption`s whose `Price.Amount`
equals the ACCOUNT entry's amount (not LIST), with root alerts mapped once to result-level warnings.

### 4.3 Arrays crossed outside an iteration root: ArraySelect

When a rule's source path traverses an array that is not its scope's iteration root, the rule MUST
carry an `ArraySelect` strategy (validation error otherwise — never an implicit `[0]`):

| Strategy | Params | Semantics | Failure behavior |
|---|---|---|---|
| `First` | — | first element | empty array → treat as missing source |
| `ByPredicate` | discriminator path, comparison value | first element where `elem.path == value` | no match → missing source; >1 match → first + warning |
| `ByIndex` | n | element at index | out of range → missing source |
| `Aggregate` | Sum / Min / Max | numeric fold over elements | empty → missing source |

Predicate comparison values are constants stored in the rule (human-confirmed in the Workbench;
suggested by the deterministic engine from example payloads, or by the LLM assistant). "Missing
source" then follows the rule's requiredness policy: required target → validation blocker at
mapping time, runtime warning at execution time; optional target → null + optional `Default`.

### 4.4 Workbench review surface

The iteration root is a reviewable row like any rule (status, provenance, accept/edit). The
Workbench shows the active scope when editing element-relative rules and previews live results
against the example payload (count of produced options + per-field sample values) so the human
approves semantics, not syntax.

### 4.5 Path notation (frozen grammar v1)

JSONPath subset, deterministic, no scripting:
`$.name`, `.name` (relative), `name.sub`, `arr[*]` (iterate), `arr[0]` (index),
`arr[?(@.field=='constant')]` (string-equality predicate only). Anything else is rejected at
profile validation. Extensions require a design revision, not ad-hoc parsing.

## 5. TransformKind Design

Closed enum; each rule = source selector (+ optional ArraySelect) + transform chain (≤2 in MVP) +
target. Optional `defaultValue` parameter exists on every rule for null/missing source.

| Kind | Params | Validation | Codegen |
|---|---|---|---|
| `DirectCopy` | — | source/target types compatible | assignment |
| `Rename` | — | same as DirectCopy (names differ; reporting distinction only) | assignment |
| `TypeConvert` | targetType, failurePolicy (Fail/Null/Default) | conversion pair supported (string↔decimal/int/bool, number→string) | invariant-culture parse/convert helper |
| `UnitConvert` | fromUnit, toUnit | units in versioned factor table (mass: kg/lb/oz/g; length: cm/in/mm/m) | constant-factor multiply, factors baked into generated code |
| `EnumMap` | enumMapTableRef, unmappedPolicy (PassthroughWarn/Fail/Default) | table exists, rows reviewed | generated switch/dictionary from frozen table rows |
| `DateParse` | formatString, outputKind (DateOnly/DateTimeUtc) | format non-empty; sample value parses against example payload | `DateTime.ParseExact`, invariant culture |
| `ArraySelect` | strategy + params (§4.3) | only on array-crossing paths | LINQ select per strategy |
| `Constant` | value, type | type matches target; no source path | literal assignment (escaped) |
| `ManualCode` | placeholder text | **blocks generation in MVP** unless rule is waived | none (post-MVP escape hatch) |

Enum/code translation is first-class data: `EnumMapTable` (e.g. provider service codes → canonical
service taxonomy) with reviewable rows, scoped to the profile version and frozen with it.
Unit factors and conversion helpers are generator constants (versioned with the generator), never
runtime config — preserving the determinism rule (plan:285).

## 6. MappingProfile Lifecycle and Versioning

States: `Draft → Approved → Superseded` (+ terminal `Rejected` for abandoned drafts).

- **Draft**: editable; rules carry mapping statuses (Auto-mapped / Needs review / Manual / Ignored
  / Required missing / Conflict); validation runs on demand.
- **Approve** (allowed only when the coverage gate is green): profile content becomes **immutable**;
  monotonic `Version` (per integration project) assigned; `ApprovedBy/ApprovedAt` recorded;
  `ProfileHash` = SHA-256 over canonical-JSON serialization (sorted keys, stable field order) of
  rules + transforms + enum tables + iteration roots.
- **Edit after approval**: the system clones the approved version into a new Draft
  (`ParentVersionId` set); the approved version is untouched. Approved → `Superseded` only when a
  newer version is approved; superseded versions are kept for audit and remain regenerable.
- **Generation pinning**: `GenerationPlan`/`GenerationRun` reference
  `(MappingProfileId, Version, ProfileHash)`. Regenerating an approved version is reproducible
  byte-for-byte (with the same generator version); drafts can never generate (negative test).
- **Waivers**: ignoring/waiving a REQUIRED canonical field demands a recorded reason + reviewer
  identity, stored on the rule; waivers freeze with the version.
- Per-rule audit: provenance (Deterministic / LLM / Human), reviewedBy/At, decision history
  (append-only `MappingRuleHistory`, optional in storage but recommended from G2).

## 7. ProviderSchema Identity / Re-import Readiness

- `SourceHash` — SHA-256 of raw uploaded bytes (exact, no normalization); duplicate-upload detection.
- `SchemaHash` — SHA-256 of the canonically serialized ProviderSchema tree (sorted keys); the
  import-determinism acceptance assertion: importing the same source twice yields equal SchemaHash.
- `OperationKey` — `{HTTPMethod} {pathTemplate}` (e.g. `POST /rates/quotes`).
- `FieldKey` — SHA-256 over `(OperationKey | Direction | canonical JSONPath)`. Stable across
  re-imports and databases; **MappingRules reference FieldKey** (plus DB FK for joins), so a future
  re-import maps existing rules onto the new schema version without manual rebinding.
- Re-import flow (designed now, UI deferred — D17): import v2 → new ProviderSchema row (version+1)
  → set-diff by FieldKey: `added` (unmapped, suggest), `removed` (referencing rules flagged
  `Broken/Needs review`), `typeChanged` (flag for re-review), `unchanged` (statuses carried over)
  → delta re-review in Workbench → new profile draft. MVP ships the identity + hashes only; the
  diff feature lands post-MVP without storage migration.

## 8. Storage Model Refinements

Additions/changes to the plan's §6 table set (plan:398-472):

| Table | Status | Key fields |
|---|---|---|
| `ProviderEndpoints` | **added** (fixes plan:171 vs §6 inconsistency) | Id, ProviderSchemaId, OperationKey, Method, PathTemplate, Summary, IsRateCandidate |
| `ProviderExamples` | **added** | Id, ProviderSchemaId, EndpointId, Direction, Name, ContentRef, ContentHash, Origin (SpecExample/ManualUpload) |
| `EnumMapTables` / `EnumMapRows` | **added** (for `EnumMap`) | TableId, ProfileVersionId, Name; RowId, SourceValue, TargetValue, Status, ReviewedBy/At |
| `MappingProfiles` | extended | + State, ProfileHash, ParentVersionId, WaiverCount |
| `MappingRules` | extended | + RuleKind (Field/IterationRoot), SourceFieldKey, TargetFieldKey, ScopeRuleId, TransformKind, TransformParams (JSON), ArraySelectStrategy/Params, DefaultValue, WaiverReason |
| `ProviderFields` | extended | + FieldKey, ParentFieldId (tree), IsArray, ExampleRef |
| `ProviderSchemas` | extended | + SourceHash, SchemaHash, SchemaVersion |
| `LlmSuggestions` | extended (F14) | + ModelId, PromptTemplateVersion, PromptContentHash, ResponseHash |

- **RawContentRef**: content-addressed file store under the app data root (`sources/{sha256}`),
  behind `ISourceContentStore` (file impl in MVP, blob-storage impl later). Upload caps enforced
  (size limit + content-type check); retention = lifetime of the integration project (specs are
  small and non-PII); store path never derived from user-provided names.
- **Repository abstraction** (slice 1): per-aggregate repository interfaces
  (`IIntegrationProjectRepository`, `IProviderSchemaRepository`, `IMappingProfileRepository`, …);
  G1 ships in-memory implementations; **EF Core implementations are a G2 entry condition** (mapping
  must persist). Domain/services depend on interfaces only — the swap is additive, not a rewrite.

## 9. Generator Safety Design

Untrusted input = every provider-derived string (field names, descriptions, enum values, operation
ids). Two-track defense:

1. **Wire fidelity without identifier risk (D7):** generated DTO properties carry serializer
   attributes with the exact original name (`[JsonPropertyName("svc-cd")]`-style); C# identifiers
   are sanitized derivatives used only for code readability. Serialization never depends on
   identifier sanitization.
2. **Single emission utility (D8):** all code text passes through one identifier-sanitizer and one
   literal-escaper (Roslyn `SyntaxFactory`/`Literal()` preferred — escaping by construction).
   String concatenation/interpolation of raw provider strings into templates is banned by
   convention and by a generator unit test that scans emitted code paths.

Identifier pipeline: Unicode NFC → transliterate/strip to `[A-Za-z0-9_]` → ensure first char is a
letter → PascalCase → C# keyword check (`class` → `@class`) → deterministic collision dedupe
(ordinal numeric suffix) → length cap. Same input always yields the same identifier (determinism).

Additional measures: descriptions sanitized before XML-doc emission (strip `*/`, control chars);
artifact file names derived only from sanitized identifiers; output paths confined to the artifact
store root (no traversal); generated file headers
`// <auto-generated> AI Integrator | profile {id} v{version} {profileHash} | generator {version} — DO NOT EDIT`;
`ContentHash` stored per artifact and checked before any overwrite — mismatch = hand-edit drift →
refuse to overwrite without an explicit force flag.

**Hostile-spec fixture** (mandatory negative test): field names with C# keywords, quotes/backslashes,
emoji + combining unicode, 300-char names, names colliding after sanitization, `../../` in operation
ids, script-like description content. Acceptance: generation succeeds, package compiles, wire names
intact, no path escapes.

## 10. Build/Test Sandbox Design

Purpose: compile and test generated packages (F11/M8) without hidden complexity or network trust.

- **Layout**: `{workRoot}/{generationRunId}/src/…` — one temp folder per run; concurrent runs
  isolated by folder; cleanup policy keeps the last N runs' artifacts, always deletes `bin/obj`.
- **Dependencies**: generated csproj references only packages from a **pinned allowlist** staged in
  a local offline package source; `dotnet restore --source {localFeed}`; no network restore, no
  packages influenced by spec content. SDK version recorded per run.
- **Invocation**: `dotnet build` then `dotnet test --logger trx`, each via a process wrapper with:
  configurable timeouts (defaults: build 120s, test 120s), stdout/stderr capture, kill-on-timeout,
  non-zero exit → failed run with captured output.
- **Environment hygiene**: child process gets a minimal whitelisted environment (no inherited
  secret-bearing variables); working directory = run folder; no elevated privileges.
- **Sanitized logs**: absolute paths rewritten to run-relative, machine/user names stripped before
  persisting `IntegrationLogs`/`TestRunResults`; raw logs are not stored.
- **Residual risk, stated honestly**: the sandbox executes code generated from our own templates +
  sanitized inputs (§9); it is an internal dev tool boundary, not a multi-tenant security boundary.
  Hardening beyond process isolation (containers) is deliberately out of MVP scope and noted for a
  future gate if the tool ever runs server-side for untrusted users.

## 11. Fixture Strategy

Fixtures are designed artifacts, versioned and hashed, committed at G0:

1. **`MockCarrierA` — realistic, FedEx-shaped** (names neutralized): `POST /rates/quotes`;
   request with shipper/recipient address objects + packages array (MVP maps `[0]`); response with
   `output.rateReplyDetails[*]` (each: serviceType, serviceName, `commit.dateDetail`,
   `ratedShipmentDetails[*]` containing both ACCOUNT and LIST charge entries with
   `totalNetCharge {amount, currency}`), root-level `alerts[*]`.
2. **Golden set for A**: request fixture + response fixture (≥2 service options × ≥2 charge entries)
   + **expected canonical output JSON** (exactly N QuoteOptions, ACCOUNT amounts, parsed dates,
   alerts→warnings) — the diff-able assertion artifact; plus an unavailable-service variant and an
   error-envelope variant (→ warnings path).
3. **`MockCarrierB` — structurally different**: flat response list (no nesting), different units
   (lb/in vs kg/cm → exercises `UnitConvert`), different service-code vocabulary (exercises
   `EnumMap`), ISO-string dates, currency at root. Proves factory generality in G4 without touching
   factory core.
4. **`MockCarrierMessy` — for the LLM assistant (M11/G4)**: deliberately incomplete spec — loose
   inline types, cryptic names (`svcCd`, `amt1`, `dlvDt`), no examples, prose-only descriptions —
   designed so deterministic suggestion fails and the assistant has a real gap to fill.
5. **`MockCarrierHostile`** — §9 negative fixture.

## 12. UI/UX Technical Implications

- **Schema viewer at real-spec scale**: ProviderFields is already an indexed table — search by
  name/path substring, filters by direction/required/mapped-status; tree view for browsing + flat
  filtered list (path column) for search results; lazy-load children for large trees.
- **Workbench filters**: default working view = `Needs review + Required missing + Conflict`;
  "unmapped required only" toggle; status-count chips doubling as filter buttons (same counts feed
  the coverage gate panel).
- **Integration status stepper (D13)**: `Draft → SourceImported → SchemaReady → MappingInReview →
  MappingApproved → Generated → Tested → DemoReady` — **derived** from artifact existence
  (source row, schema row, draft profile, approved version, generation run, passing test run, demo
  run), never hand-set; shown in the integrations list and as a wizard breadcrumb.
- **Import error behavior**: hard parse failure → reject with error list (location/JSON-pointer);
  recoverable issues (unresolved `$ref`, missing examples, loose types) → **import-with-warnings**:
  what parses is imported, gaps flagged `Incomplete` on the affected fields, warnings panel lists
  them; an `Incomplete` field used by a required mapping becomes a validation blocker downstream.
- **UI stack (D14, recommendation pending owner)**: **Blazor Server** for the MVP workbench —
  single .NET toolchain, no duplicated client models, fast iteration for an internal tool, SignalR
  progress updates for free — with the conceptual API layer (plan:474-522) kept clean so a future
  SPA can replace the UI without backend rework. If the owner prefers React (per other projects),
  the API-first structure makes that a UI-substitution, not a redesign. G0 cannot start until this
  is final.

## 13. LLM Governance Design

- **Suggestion cache (required)**: key = `(SchemaHash, promptTemplateVersion, modelId,
  unresolvedFieldKeySetHash)` → cached suggestions; re-running the assistant on an unchanged schema
  is a cache hit, not a new spend; invalidated by schema/template/model change.
- **Prompt minimization**: prompt contains only (a) unresolved canonical fields (name, type,
  description, requiredness) and (b) the candidate provider subtree (paths, types, sanitized
  example values) — never the full spec. A scrubber removes example values matching secret
  patterns, anything under auth/security sections, and URLs with embedded credentials.
- **Auditability**: every `LlmSuggestion` stores ModelId, PromptTemplateVersion, PromptContentHash,
  ResponseHash, timestamp, confidence, rationale — "why did AI suggest this" is answerable at
  review time and reproducible later.
- **Budget guard**: configuration caps (`MaxLlmCallsPerSchemaVersion`, `MaxPromptBytes`); exceeding
  caps disables the assistant with an explicit message (deterministic flow unaffected).
- **Boundary invariants** (tested): every LLM suggestion enters as `Needs review` with provenance
  LLM; never auto-approved; generation blocked while unreviewed LLM suggestions exist; the
  **generated package has zero LLM references** — structural assertions: generated csproj package
  list contains no LLM SDK, runtime demo DI scope registers no LLM client, demo-run logs show no
  LLM traffic. Provider/model choice stays an owner decision behind an `ILlmClient` port.

## 14. Gate Grouping G0-G4

Each gate = one molecular Claude implementation gate (own ACTIVE_REQUEST, internal phase locks per
milestone, stop conditions, evidence report). Sequential; no gate opens without the previous gate's
acceptance plus explicit authorization.

| Gate | Content (plan milestones) | Entry conditions | Exit = owner demo checkpoint |
|---|---|---|---|
| **G0 — setup** | M0 (new): feature branch `feature/12695-ai-integrator-poc`, solution skeleton (src/tests/fixtures layout), CI build+test hook, fixture assets (§11) committed + hashed | tech design accepted; UI stack decided; branch creation explicitly authorized | CI green on empty skeleton; fixtures present with recorded hashes |
| **G1 — import + browse** | M1 shell, M2 schema import, M3 internal model browser | G0 accepted | Demo #1: paste MockCarrierA spec → browse field tree + canonical model; in-memory persistence acceptable |
| **G2 — mapping + validation** | M4 Workbench (rules, iteration root, EnumMap tables, ArraySelect), M5 coverage gate + profile approve/freeze (§6) | G1 accepted; EF persistence ready at entry | Demo #2: reviewed, approved, frozen MappingProfile with green coverage gate |
| **G3 — generation + tests + demo** | M6 generation review, M7 generator (§9), M8 sandbox + test runner (§10), M9 quote demo | G2 accepted | Demo #3: generated MockCarrierA adapter compiles, tests pass, returns N≥2 normalized quotes |
| **G4 — factory proof** | M10 second provider (MockCarrierB), M11 LLM assistant (MockCarrierMessy, §13), M12 SOAP/WSDL importer interface slot | G3 accepted | Demo #4: two providers in one quote response; factory-core diff empty; AI assist with human review |

## 15. Acceptance Evidence Model

Standard evidence tuple per milestone, mandatory in every implementation report:
`{command + exit code}` and/or `{artifact path + content hash}` and/or `{log excerpt (sanitized)}`.
Named cross-gate assertions:

| Assertion | Evidence |
|---|---|
| ImportDeterminism | import same source twice → equal `SchemaHash` (hashes printed) |
| MultiOption | golden response → expected canonical JSON, diff empty, N≥2 options |
| ChargeSelection | `Price.Amount` equals ACCOUNT entry value, not LIST (golden diff) |
| GenDeterminism | generate approved version twice → identical artifact `ContentHash` set |
| HostileSpec | hostile fixture → generation exit 0 + `dotnet build` exit 0 |
| ProfilePinning | new draft edits do not change approved-version regeneration hashes |
| WaiverTrail | waived required field carries reason + reviewer in frozen version |
| ZeroLLM | no LLM package in generated csproj; no LLM registration in runtime DI; demo logs clean |
| PartialFailure | provider A forced-fail → B options + A warning entry returned |
| Repeatability | provider B onboarding leaves factory-core paths diff-empty (`git diff` scoped to core dirs) |
| SandboxBounds | build/test respect timeouts; logs sanitized (no absolute paths/usernames) |

## 16. Updated Risks and Mitigations

| Risk | Change vs plan/review | Mitigation |
|---|---|---|
| Mapping cardinality gap | **Closed by design** (§4) | implement as specified; golden multi-option assertions |
| Codegen injection | **Closed by design** (§9) | single emitter + hostile fixture in G3 |
| Build sandbox complexity | **Bounded** (§10) | offline allowlist restore; timeouts; stated residual risk |
| Profile mutability | **Closed by design** (§6) | copy-on-write + pinning assertions |
| JSONPath subset creep | new | grammar v1 frozen (§4.5); extensions need design revision |
| EnumMap table sprawl | new | tables scoped to profile version; frozen on approve |
| UI stack indecision | carried | D14 recommendation; G0 blocked until owner decides |
| Offline package cache setup cost | new | one-time G0 task; pinned allowlist documented |
| Fixture maintenance | carried | fixtures are hashed artifacts; changes reviewed like code |
| Blazor choice misfit (if SPA preferred later) | new | API-first layering keeps UI replaceable |

## 17. What AIKB Should Update Later (if Slava accepts)

For Architect GPT after acceptance (not performed here):

1. Record this dossier as the accepted technical design baseline (ledger entry + CURRENT_STATE
   evidence pointer + status, e.g. `TECH_DESIGN_BASELINE_REGISTERED`).
2. Either amend the implementation plan to v0.2 absorbing §4-§15, or link this dossier as the
   plan's normative technical annex (recommended — avoids re-writing an accepted artifact).
3. New ADRs: mapping semantics + TransformKind set (§4-§5); profile lifecycle (§6); UI stack
   (after owner decision, D14).
4. Next-gate pointer: G0 setup gate as the first implementation gate (entry conditions in §14);
   Igor validation remains open in parallel as the product-level gate.
5. Optional bridge hygiene: refresh `TwinCore/latest-summary.json` (still describes the first
   planning dossier; outside this request's write scope).

## 18. What Must NOT Be Done Yet

- No feature branch, no solution skeleton, no CI setup (all G0, not yet authorized).
- No source code, no generator prototyping, no fixture files on disk (fixture *specs* are designed
  here; the files are G0 artifacts).
- No final UI stack commitment without the owner (D14 is a recommendation).
- No LLM provider/model selection or spend; no real carrier sandboxes or credentials.
- No AIKB writes from this gate; no edits to the implementation plan file.
- No Azure DevOps actions of any kind.

## 19. Final Recommendation

Adopt this dossier as the technical contract for implementation. With §4-§10 specified, the four
review gaps are closed on paper; the remaining pre-implementation blockers are exactly two:
**(1) owner decision on the UI stack (D14)** and **(2) Igor's product validation** (still the first
product gate — this design does not substitute it). After both: open **G0 — setup gate** as the
first, separately authorized implementation gate per §14.

Report written only to the four authorized paths (gate report, `latest-report.md`,
`_BRIDGE/LATEST_REPORT.md`, `_BRIDGE/STATUS.json`). Nothing else touched.
