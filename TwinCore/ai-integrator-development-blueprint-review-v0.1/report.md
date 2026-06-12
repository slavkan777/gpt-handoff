# TwinCore AI Integrator Development Blueprint Review v0.1

REQUEST_ID: REQ-2026-06-12-twincore-ai-integrator-development-blueprint-review-v0-1
PROJECT: TwinCore
GATE: planning / development-blueprint-review
STATUS: READY_FOR_AUDIT
DATE: 2026-06-12
AUTHOR: Claude (reviewer); analysis-only, no code/AIKB/blueprint modified
REVIEWED ARTIFACT: `01_PROJECTS/TwinCore/ARCHITECTURE/TWINCORE_AI_INTEGRATOR_DEVELOPMENT_BLUEPRINT_V0_1.md` (ai-kb @ `162e5c9`, 1133 lines, status DRAFT_FOR_CLAUDE_REVIEW)

---

## 1. Executive verdict

**ACCEPT_WITH_REQUIRED_EDITS.**

The blueprint is structurally strong: boundaries, owner-proxy mode, gate roadmap, mapping semantics (IterationRoot / ArraySelect / closed TransformKind set / profile freeze), generator safety rules, and the manual testing path are all consistent with the accepted tech-design baseline and are the right document to eventually show Igor.

The single biggest problem is **timing drift**: the blueprint was written while G1 was still in flight (§9 marks G1 "IN PROGRESS / NEXT REPORT") and it specifies G1 names, routes, services, and a project split that **differ from what is now actually merged on the branch** (commit `dbea505…`, report `TwinCore/ai-integrator-g1-import-browse-v0.1/report.md`, 30/30 tests green). If GPT issues G2 prompts from the blueprint as-is, Claude will receive instructions that contradict the code it must extend — that is exactly the kind of churn macro-gates are meant to avoid. ~10 required edits below reconcile the document with shipped reality and fix a few real spec gaps (missing generated `.csproj`, missing `EnumValues` on ProviderField, an EF-timing contradiction with Implementation Plan v0.2, and a manual-testing command that would disturb the developer's main checkout).

With those edits applied, the document is good enough to drive G2-G5 and, after G5 evidence is appended, to send to Igor.

## 2. Current state restored

- **AIKB** (`ai-kb` @ `162e5c9`): blueprint registered (`74e7c6e`/`f328557`); new `00_CONTROL_CENTER/MACRO_GATE_TRIGGER_RULE.md` added and hardened (`5cce530`, `162e5c9`) — macro-gate execution is now an explicit control-center rule, consistent with blueprint §19. CURRENT_STATE/TASK_LEDGER updated for the blueprint registration. G0 recorded as accepted; G1 acceptance not yet recorded (audit pending).
- **Bridge** (`gpt-handoff` @ `b22326a`): G1 report published `G1_DONE_READY_FOR_AUDIT` (this reviewer's preceding gate); this queued request unblocked by its own queue note ("until G1 report is finished" — finished).
- **Source** (`Twincore-framework`): `feature/12695-ai-integrator-poc` @ `dbea505bf1dd3d8a29a04c368b46b2717fc831ff` (G1), base `main` @ `8f935f0…` untouched. Shipped G1 facts used throughout this review: 6 projects, 64 files changed in G1, build 0 warnings, tests 30/30, importer 32 request / 35 response fields from MockCarrierA, `$.output.rateReplyDetails[*]` browsable in the viewer.
- **Decision mode**: owner-proxy; everything below is a provisional owner-approved baseline by Slava, reversible when Igor returns.

## 3. Blueprint consistency review (question 1)

Internally consistent in its core invariants (zero runtime LLM stated in §1 and re-enforced in §6.5/§12/§13 tests; boundaries §3 = stop conditions §18; reporting paths §15 = actual bridge convention; IterationRoot rule §7.8 = G2 validation rules §11; TransformKind closed set §7.9 = tech-design ADR).

Found inconsistencies:

1. **§4/§9/§10 vs reality**: G1 marked "IN PROGRESS"; §10 prescribes G1 deliverables in future tense. G1 is done and audited material exists. The blueprint must be re-baselined on `dbea505` or it will mis-instruct G2.
2. **§7.5 vs §12**: "Required canonical fields" lists only option-level fields (`RateQuoteOption.Warnings/Errors`) but §12's quote-demo proof requires **result-level** warnings ("root alerts become warnings", golden `expected-canonical.json` has `resultWarnings` and no per-option errors). The canonical field list must include result-level `ResultWarnings` and an error slot, and reconcile per-option `Errors` with the golden file (shipped model: option-level `Warnings`, result-level `ResultWarnings` + `Error{Code,Message}`).
3. **EF timing contradiction with Implementation Plan v0.2**: plan §G2 says "EF persistence required before or at G2"; blueprint §6.4 says "in-memory storage acceptable through G2 if repository abstractions are clean". Both are AIKB sources of truth. Recommendation in §14.
4. **§10 routes** (`/provider-schema`, single `GetLatestAsync()` repository) conflict with the shipped multi-document model (`/schemas/{documentId:guid}`, list + per-integration queries). The shipped model is strictly more capable; blueprint should adopt it.

## 4. Architecture review (question 2)

The pipeline (§1) and per-project responsibility split (§6) correctly combine product plan, architecture, domain, services, UI, tests, gates, and manual testing in one document — это и есть его ценность: один source of truth вместо четырёх. Strong points worth keeping verbatim: untrusted-provider-strings rule set (§6.5), sandbox containment rules (§6.6), profile freeze + generation pinning (§7.6), factory-proof rule (§13), Igor-facing review points (§17).

Gaps at architecture level:

- **Generated package has no `.csproj`** (§12 "Generated package minimum" lists 6 `.cs` files + appsettings sample). Sandbox runs `dotnet build` on the package — impossible without a project file. Add `MockCarrierA.Generated.csproj` (net8.0, references Contracts only, explicit DI) to the minimum set.
- **Raw source persistence undefined**: `ProviderSourceDocument.ContentRef?` (§7.2) hints at storing raw source, but no decision is made. G2's mapping UI will want example values and re-import diff needs original bytes. Decide: store raw text on the schema document (in-memory now, column later) or drop `ContentRef` until G4.
- Roslyn `SyntaxFactory` preference from the tech-design baseline is not restated in §6.5 — restate it so the generator gate inherits it without archaeology.

## 5. Project / layering review (question 3)

Blueprint targets **8 projects** (adds `Application` + `Infrastructure` to the shipped 6). Assessment:

- The 4-layer split is interview-defensible Clean Architecture and the **interfaces named in §6.3/§6.4 are correctly placed** (ports in Application, fixture/in-memory adapters in Infrastructure).
- But shipped G1 put application services and in-memory/fixture adapters into **Domain** (`Services/`, `Persistence/InMemory/`, `Fixtures/`) — deliberately small for the slice. Both layouts are defensible; what is NOT acceptable is the blueprint silently assuming files live where the code does not have them.
- **Recommendation (pick one, write it into §5):**
  - **(a) preferred — split at G2 opening**: first commit of G2 is a mechanical, behavior-preserving move: `Domain/Services + Fixtures + Persistence/InMemory → Application/Infrastructure projects`, sln-only change inside `AiIntegrator/**`, tests must stay 30/30 green before any new G2 feature. G2 then adds mapping into the correct layers from day one. Cost: ~1 hour, low risk, prevents Domain from accreting orchestration.
  - (b) acceptable — keep 6 projects with namespace discipline and re-evaluate before G3 (Generator/Sandbox add real infrastructure weight).
- `Sha256HashService` (§6.4) is over-specified: hashing is a pure static function (shipped `HashCalculator`), not an injectable service. Drop it.

## 6. Domain model review (questions 4, 5)

Coverage for G1-G5 is **sufficient in scope**: schema side (§7.1-7.4), mapping side (§7.6-7.10), plus `IntegrationProject`, `IntegrationRun`, `GenerationArtifact` carry G2-G3; `SourceType` includes `Wsdl` for the G4 slot. No missing aggregate.

Field-level findings (blueprint vs shipped `dbea505` and vs G2 needs):

| Item | Blueprint | Shipped / needed | Action |
|---|---|---|---|
| Naming | `ProviderSchema`/`ProviderOperation`/`ProviderField`/`ProviderFieldDirection` | `ProviderSchemaDocument`/`SchemaOperation`/`SchemaField`/`SchemaDirection` (tested, in G1 report) | **Required edit**: adopt shipped names or explicitly order a rename in the G2 pre-commit; do not leave both |
| `ProviderField.EnumValues` | **absent** | shipped (`KG/LB`, `ACCOUNT/LIST` captured) and **required** to feed §7.9 `EnumMap` UI in G2 | **Required edit**: add |
| `ProviderField.Type: string` | string | shipped enum `SchemaFieldType` + `Format` (date/date-time) | adopt enum + format; `DateParse` needs format hints |
| `Name` + `WireName` separate | both | shipped single `Name` ≡ wire-verbatim (sanitization is G3, per tech design) | keep single wire name at import; sanitized names live only in generator output |
| `ParentPath`, `ExampleValue` | present | absent; ParentPath derivable from JsonPath; ExampleValue useful for mapping UX | ParentPath: drop. ExampleValue: optional add at G2 (source: OpenAPI `example` + golden response) |
| `ProviderSchema.Id: string` | string | shipped `Guid` | accept Guid (EF-friendly) |
| Contracts names | `RateQuoteOption`, `AddressDto/PackageDto/MoneyDto`, `ServiceCode`, `TransitDays` | shipped `QuoteOption`, `CanonicalAddress/CanonicalPackage/MoneyValue`, `ProviderServiceCode`, `EstimatedTransitDays` — names match golden `expected-canonical.json` 1:1 | **Required edit**: adopt shipped canonical names; the golden file is the contract anchor |
| `IRateProvider`, `ProviderExecutionContext`, `ProviderWarning/Error` types | in Contracts now | not shipped — runtime adapter contract is G3 material | keep in blueprint, mark "added at G3" |
| Option-level `Errors` | required | golden has none; shipped: result-level `Error` | reconcile per §3.2 |

MappingProfile/MappingRule/ArraySelect (§7.6-7.10): correct and complete for G2; statuses (`AutoMapped…Waived`) match the tech-design baseline; `ManualCode` blocking generation is right. One addition: `MappingRule` needs an `Order`/stable sort key for deterministic ProfileHash.

## 7. Application service review (question 6)

Placement is right (ports in Application). Signature-level findings:

- `IProviderSchemaRepository.GetLatestAsync()` (§10) encodes a single-schema world; shipped reality is multi-integration/multi-document (`GetAsync(id)`, `ListAsync`, `ListForIntegrationAsync`). **Required edit** — otherwise the G2 workbench can't address the schema it maps.
- `IProviderSourceService.LoadFixtureAsync(fixtureName)` vs shipped `IFixtureCatalog.ListSources()/ReadContent(relativePath)` + path-traversal guard (tested). Adopt shipped shape; the traversal guard must be stated as a rule (MockCarrierHostile ships traversal-shaped ids).
- `IInternalModelCatalog.GetRateQuoteModels()` vs shipped reflection-based `ICanonicalModelCatalog` (request+result trees, `[Description]`-driven, camelCase paths). Shipped version cannot drift from Contracts — keep it; blueprint's `StaticInternalModelCatalog` would reintroduce drift risk. **Required edit.**
- G2 service set (`IMappingValidationService.Validate(profile, schema, internalModels)` pure-function style, `IMappingProfileApprovalService.Approve`) — correct, deterministic, testable. Keep.
- Importer naming: `MockOpenApiProviderSchemaImporter` mislabels shipped behavior — the importer is a real (subset) OpenAPI 3.0 importer, not a mock (`OpenApiImporter`, dependency-free, deterministic, Swagger-2.0-degrading). Rename in blueprint; "Mock" belongs to fixtures, not the importer.

## 8. UI / workbench review

§6.7 page list is right for the MVP arc (G1 pages shipped; `MappingWorkbench`, `GenerationReview`, `QuoteDemo` pending G2/G3). Required reconciliations:

- Routes: adopt shipped `/`, `/integrations`, `/integrations/new`, `/schemas/{documentId}`, `/internal-model`; blueprint's `/provider-schema` (singular, stateless) is superseded. Add planned `/mapping-workbench`, `/generation-review`, `/quote-demo` as G2/G3 routes — fine as specified.
- §10 minimum UX is already exceeded by shipped G1 (4-step wizard with fixture list + paste tab vs "button: Load MockCarrierA fixture"; viewer shows hashes/enums/required/FieldKey). Blueprint should record the delivered UX as the new baseline so G5's manual script matches the real screens.
- "No domain logic in Razor components" — shipped and mechanically enforced (grep = 0 hits, in G1 report §6); keep the rule, add the grep as the acceptance mechanism.
- Blazor Server + prerender remains provisional (D14); SPA-replaceability is preserved by the service boundary. Consistent.

## 9. Generator / sandbox review

§6.5/§6.6/§12 are the strongest sections — they encode every safety decision from the tech-design baseline (untrusted strings, no interpolation, serializer-attribute wire names, sanitized identifiers as derivatives only, hash-pinned generation, sanitized logs, no network in sandbox). Required additions:

1. **Generated `.csproj` missing** from the package minimum (§4 above) — without it the sandbox's `dotnet build`/`dotnet test` of the package cannot run.
2. State the **emitter technology**: Roslyn `SyntaxFactory` preferred over string templates (tech-design decision) — this is the main anti-injection control and must not be rediscovered at G3.
3. Hostile-fixture round-trip belongs in G3 tests explicitly (§12 has "Hostile fixture names do not break sanitizer" — good; add "compiles and round-trips wire names via serializer attributes").
4. Sandbox: define artifact root under `AiIntegrator/.artifacts/` or system temp + cleanup rule, so generated output never lands in tracked paths accidentally.

## 10. Gate roadmap review (question 7)

Sizing assessment against the now-measured G1 baseline (one macro-gate, 64 files, 30 tests, single session):

- **G2** as specced ≈ 1.5-2× G1 (domain + workbench UI + validation + approval + freeze + tests). Feasible as ONE macro-gate **if** the §5 layer-split pre-commit is mechanical and the EF question is resolved beforehand (§14). If EF lands inside G2 too, it becomes the largest gate of the project — recommend EF-out (below).
- **G3** is the riskiest (generator + sandbox + demo). Keep as one bridge gate but pre-authorize an internal two-commit structure (G3a generator+tests, G3b sandbox+demo) — same report, two commits, no extra audit round.
- **G4, G5** sized correctly (G4 is the factory proof, G5 is documentation+packaging).
- Gate exit criteria (§9: one commit, evidence, scoped diff, report, audit) match the proven G0/G1 protocol. The new AIKB `MACRO_GATE_TRIGGER_RULE.md` is consistent with §19.

## 11. Testing strategy review

Per-gate test lists (§10-§13) are correct and deterministic-first. Gaps to add:

- **Component-level UI tests (bUnit)** proved their worth in G1 (wizard click-through with the real service stack — the only machine evidence of the manual path). The blueprint's test strategy is service-level only; add bUnit as the standing approach for wizard/workbench interactions, отдельно от ручного click-through Славы.
- **Determinism tests as a class**: re-import → same `SchemaHash`; approve → stable `ProfileHash`; generate → stable `ContentHash`. §12 has the generator case; add the schema and profile cases (schema one already shipped in G1).
- **Negative-path tests**: path-traversal rejection (shipped), Swagger-2.0 degradation (shipped), hostile import (shipped) — fold into the blueprint so G2+ keeps extending them.
- CPM (`Directory.Packages.props`) governs test dependencies since G1 — worth one line in §5 so future gates pin versions centrally.

## 12. Manual testing readiness review (question 9)

The G5 target is realistic — G1 already proves the heaviest prerequisite (app runs, imports, renders on a clean `dotnet run`). Two required fixes:

1. **§14 manual commands would disturb the developer's primary checkout**: `cd C:\Projects\Twincore-framework; git checkout feature/12695-ai-integrator-poc` switches the main working copy away from its current task branch. Replace with a `git worktree add <separate-folder> feature/12695-ai-integrator-poc` flow (this is exactly how G0/G1 were executed) or a fresh clone. Otherwise the first manual test session creates the dirty-tree stop condition for the next gate.
2. The 19-step browser flow should reference the **shipped** routes/screens after §8 edits (e.g. step 4-5 "see ProviderSchema" happens at `/schemas/{id}` reached from the wizard's "View schema" or `/integrations`).

Minor: add `DemoSeed:Enabled` note (Development seeds MockCarrierA automatically — manual testers should know why data is pre-populated, or the flag should be off for the manual script so the wizard path is exercised).

## 13. Igor-facing readiness review (question 10)

§17's ten review points are exactly the right story for Igor (isolation, untouched framework, zero runtime LLM, schema import, MappingProfile as the core value, freeze/hash, explicit-DI adapters, sandbox, factory proof, path to real providers). Verdict: **good enough to send to Igor after the required edits**, with two packaging additions:

- Attach per-gate evidence numbers (G1: build 0 warnings, 30/30 tests, 32/35 fields, `$.output.rateReplyDetails[*]` visible) — Igor responds to evidence, not promises.
- Add an explicit "what this is NOT yet" half-page (no real carriers, no production claims, in-memory/EF status, LLM design-time-only and not yet wired) — pre-empts the obvious challenge questions and keeps owner-proxy honesty.
- Keep the provisional-decision register reference (CURRENT_STATE) so Igor sees which decisions await his confirmation.

## 14. Required edits (question 11 — exact list for GPT)

1. **Re-baseline on shipped G1**: §4 add G1 commit `dbea505bf1dd3d8a29a04c368b46b2717fc831ff` + evidence; §9 G1 → DONE (audit pending); rewrite §10 from prescription to record (deliverables as shipped: wizard 4 steps + paste tab, multi-document schema viewer `/schemas/{id}`, reflection-based internal model browser, demo seed flag).
2. **Adopt shipped names** everywhere or order an explicit rename commit: `ProviderSchemaDocument/SchemaOperation/SchemaField/SchemaDirection/SchemaFieldType(+Format)`; Contracts `QuoteOption/CanonicalAddress/CanonicalPackage/MoneyValue/ProviderServiceCode/EstimatedTransitDays/EstimatedDeliveryDate/IsAvailable`; importer `OpenApiImporter` (not "Mock…"); catalog `ICanonicalModelCatalog` (reflection-based, not static).
3. **Fix §7.5 canonical field list**: add result-level `ResultWarnings` + `Error{Code,Message}`; reconcile option-level `Errors` with golden `expected-canonical.json`; note that `CarrierCode` lives at option level.
4. **Add `EnumValues` (and optional `ExampleValue`) to ProviderField**; type as enum + `Format`; drop `WireName` duplication and `ParentPath`.
5. **Resolve the EF contradiction** with Implementation Plan v0.2: recommended decision — in-memory through G2 (blueprint §6.4 wins), EF introduced as G3 pre-step when `IntegrationRun`/`GenerationArtifact` need durability; record as provisional owner decision and update the plan or the blueprint accordingly.
6. **§5 layering decision**: adopt option (a) — mechanical Application/Infrastructure split as the first, behavior-preserving commit of G2 (tests stay 30/30 before features); drop `Sha256HashService`.
7. **Repository signatures**: replace `GetLatestAsync()` with the shipped multi-document set; add the path-traversal guard rule to the fixture source service.
8. **Generated package**: add `MockCarrierA.Generated.csproj` to §12 minimum; state Roslyn SyntaxFactory preference; define sandbox artifact root + cleanup.
9. **§14 manual commands**: switch to `git worktree add` (or fresh clone) flow; align the 19-step script with shipped routes; add the `DemoSeed:Enabled` note.
10. **Routes**: §10 routes → shipped set; keep `/mapping-workbench`, `/generation-review`, `/quote-demo` for G2/G3.

## 15. Optional edits

- Add bUnit component testing + determinism-test class + negative-path tests to the testing strategy (§11 findings).
- Add `MappingRule.Order` (stable ProfileHash) and an explicit canonical-JSON definition for ProfileHash computation.
- One line on CPM (`Directory.Packages.props`) as the version-pinning mechanism.
- Decide `ProviderSourceDocument.ContentRef` (store raw source text or defer to G4).
- Add importer-warnings surface (`ImportOutcome.Warnings`) as the designed hook for the G4 LLM Gap Assistant narrative.
- §16 final-report checklist: add "stale `latest-summary.json` refreshed" housekeeping item.

## 16. What must not be changed

- §3 boundaries and §18 stop conditions (question 8: the 15-item stop list is sufficient — it is a superset of the G0/G1 ACTIVE_REQUEST stop conditions under which both gates executed cleanly, including the G0 hook incident that the STOP discipline caught correctly; no addition needed).
- Owner-proxy phrasing rules (§2) — including the forbidden-phrase discipline.
- Zero-LLM runtime invariant; design-time-only LLM with no provider configured until G4 placeholder.
- Closed TransformKind set; exactly-one-IterationRoot rule; no implicit `[0]`; ManualCode blocks generation; Draft→Approved→Superseded immutability with hash-pinned generation.
- Macro-gate execution model (§19 + new AIKB trigger rule); per-gate single-commit + evidence + audit discipline.
- Fixture-only scope until owner decision; the four-fixture set and golden files as acceptance anchors.

## 17. Final recommendation

**ACCEPT_WITH_REQUIRED_EDITS** — apply §14 items 1-10 (GPT can do this in one editing pass; nothing requires code changes), re-register the blueprint as v0.2, and only then open G2 with prompts derived from the corrected document. Suggested G2 opening order: (1) mechanical layer-split commit, (2) mapping domain + validation, (3) workbench UI, (4) approval/freeze — one macro-gate, one report. The document after edits is fit both as the G2-G5 execution contract and, with G5 evidence appended, as the Igor review package.

Provisional owner-approved baseline by Slava; reversible when Igor returns. No approval is attributed to Igor anywhere in this review.

---

*Report paths: `TwinCore/ai-integrator-development-blueprint-review-v0.1/report.md` (this file), mirrored to `TwinCore/latest-report.md` and `TwinCore/_BRIDGE/LATEST_REPORT.md`; `TwinCore/_BRIDGE/STATUS.json` updated. The G1 implementation report remains at `TwinCore/ai-integrator-g1-import-browse-v0.1/report.md` and still awaits GPT audit.*
