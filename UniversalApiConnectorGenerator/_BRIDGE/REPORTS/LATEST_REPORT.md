REQUEST_ID=REQ-2026-06-24-uacg-generate-review-package-prompt-review-v0-1
STATUS=READY_FOR_GPT_AUDIT
PROJECT=UniversalApiConnectorGenerator
GATE=GENERATE_REVIEW_PACKAGE_PROMPT_REVIEW_V0_1
MODE=READ_ONLY_REVIEW
TARGET_PROMPT=UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/REQ-2026-06-24-uacg-generate-review-package-v0-1.md
PROMPT_PATH=UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/REQ-2026-06-24-uacg-generate-review-package-prompt-review-v0-1.md
REPORT_PATH=UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
COMPLETED=2026-06-24
COMPLETED_BY=claude

# Prompt Review — GENERATE_REVIEW_PACKAGE_MACRO_V0_1 (target: generate-review-package-v0-1)

Read-only review of the draft generate macro prompt. Not executed (target is
DRAFT_FOR_REVIEW and self-gates execution). Reviewed against the accepted source-scan gate
already shipped in C:\Projects\UniversalApiConnectorGenerator (6 projects net9.0, build 0/0,
21/21 tests, inspect writes deterministic artifacts).

## Prompt Review Verdict

VERDICT: ACCEPT_WITH_REQUIRED_CHANGES.

The draft is strong and clearly absorbed prior review feedback: explicit exit codes,
output-safety/no-overwrite, determinism exclusions, NSwag explicitly excluded (owned emitter
only), clean runtime boundaries, layer placement, agent workload, and Ukrainian review
checklist. Six clarifications must be patched before an execution request is issued — mostly
to prevent silent architectural choices and a determinism trap.

## Critical Issues

1. INPUT SOURCE AMBIGUITY. GOAL says input is "the local API spec source OR
   ProviderSchemaDocument produced by inspect", and Container 5 wires `generate --source <path>`.
   It is unspecified whether generate re-parses the source in-memory or consumes a prior
   inspect run's `provider-schema-document.json`. Coupling to a prior run folder is fragile.
   PATCH: generate `--source` re-runs the existing inventory+parse pipeline (reuse
   `ISourceInventoryService` + `ISpecParser`) to build `ProviderSchemaDocument` in-memory,
   then plan+emit. No dependency on a previous run folder.

2. RATE-SPECIFIC IDENTITY HARDCODED IN GENERIC CORE. Container 2 hardcodes
   `Ups.Rate.Connector` identity/namespace in the planner "for Rate proof case." Putting
   provider/profile specifics into the generic planner re-creates the "Rate-only generator"
   anti-pattern. PATCH: the package identity, namespace, and default operation for Rate must
   come from `ConnectorGenerator.Profiles.Rate` (a profile preset/abstraction the planner
   consumes), keeping Domain/Application/planner generic.

3. COMPILE-CLEAN vs REVIEW-REQUIRED NOT DISAMBIGUATED. Container 3 says "Generated code must
   compile" while also adding review markers for unresolved mappings. PATCH: state the
   contract — the emitted skeleton ALWAYS compiles; unresolved business mappings become
   review markers + safe placeholders (e.g., a mapper that throws NotImplementedException with
   a marker), yielding `exit 2 review-required`. A genuine compile failure is a bug → `exit 1`
   / BLOCK. (CanGenerateSkeleton != BusinessMappingApproved.)

4. GENERATED MODELS ARE NAME-ONLY PLACEHOLDERS. FACT: the shipped `ProviderSchemaDocument`
   captures `SchemaNames` (names only) and `ProviderOperation` (method/path/params/responses);
   it does NOT capture schema PROPERTIES. So the emitter can only produce placeholder model
   shells. PATCH: state explicitly that generated models are placeholder classes (no
   properties) with review markers, deferring field-level schema to a later parse-enrichment
   gate — so the implementer neither assumes property data nor over-builds.

5. DETERMINISM TRAP IN GENERATED FILES. Determinism is required, but generated `.csproj`/
   source can easily carry volatile content. PATCH: explicitly forbid volatile content in
   emitted files — no timestamp headers, no random `<ProjectGuid>`/GUIDs; any auto-generated
   header must be static text. Otherwise file-hashes.json will not be stable.

6. STOPS / EXIT-CODE / CLI GAPS. PATCH: (a) add a consolidated GLOBAL STOP block (bridge
   mismatch, workspace mismatch, baseline not green, scope leaves workspace, output-safety
   violation, unexpected compile failure of the emitted package); (b) note the code change
   `ExitCodes.OutputUnsafe = 5` (current catalog is 0/1/2/4 only); (c) define `generate`
   without `--source` (usage + exit 0, mirroring inspect) and whether `--profile` is required
   or defaults to `rate`.

## Recommended Patches

- GOAL/Container 5: "generate re-parses --source via the existing inspect pipeline in-memory;
  it does not read a prior run folder."
- Container 2: "Rate package identity (Ups.Rate.Connector), root namespace, and default
  operation are provided by Profiles.Rate; the planner stays provider-agnostic."
- Container 3: add the compile-clean contract + placeholder-with-marker rule; define that
  unresolved mapping => exit 2, never a non-compiling emit.
- Container 3/4: "generated models are name-only placeholders (inspect captured schema names,
  not properties); mark each for human review."
- DETERMINISM: "emitted files contain no timestamps, no random GUIDs; headers are static."
- Add GLOBAL STOP block; add ExitCodes.OutputUnsafe=5; define generate-without-source and
  --profile default.

## Container-by-Container Notes

- 0 Preflight: good; add explicit STOP if baseline != 0/0 and 21/21.
- 1 Contracts: good layering. Domain POCO (ConnectorPackagePlan, GeneratedFileDescriptor,
  GenerationManifest, ReviewChecklistItem, MappingReviewMarker, PackageIdentity,
  GenerationDiagnostic). Reuse existing `DiagnosticSeverity`/`Diagnostic` rather than a new
  GenerationDiagnostic if shape matches (avoid duplication). Ports/DTOs in Application;
  emitter/writer in Infrastructure. Confirm Domain keeps zero project refs.
- 2 Planner: see Critical #2. Plan from in-memory ProviderSchemaDocument; missing selected
  operation => exit 4 (consistent).
- 3 Emitter: see Critical #3/#4/#5. Self-contained package under output/<run-id>/generate/package/;
  must reference neither the generator nor any provider SDK.
- 4 Review artifacts: good (UA checklist). Checklist should enumerate each review marker so a
  human sees exactly what is unresolved.
- 5 CLI generate: see Critical #1/#6. Valid => exit 2; missing/unsafe => exit 4; output-safety
  => exit 5; preserve inspect/verify/no-args.
- 6 Verify extension: acceptable to keep verify as a documented stub if `verify --package`
  is too large; if implemented, structural check + optional compile, return 0 when valid.
- 7 Tests: add an explicit boundary test — generated package references no
  ConnectorGenerator.* assembly and no provider SDK; plus emitter no-overwrite, planner,
  generated-package-compiles, determinism hashes, generate exit 2, missing-source exit 4.
- 8 Smoke: good coverage; use redirected stdin for no-args (proven pattern).
- 9 Docs: update README + UA docs honestly (review package, not production; no live calls;
  no LLM; net9 temporary/net10 later).
- 10 Report: standard.

## Risks

| Risk | Severity | Mitigation |
|---|---|---|
| Rate specifics leak into generic planner/core | High | Profiles.Rate preset (Patch #2) |
| Non-deterministic generated files (timestamps/GUIDs) | High | Forbid volatile content (Patch #5) |
| Emit that does not compile blocks the gate | Med-High | Compile-clean + placeholder/marker contract (Patch #3) |
| Over-building models from absent property data | Med | Name-only placeholder rule (Patch #4) |
| Generated package accidentally references generator | Med | Boundary test (Container 7) |
| Scope breadth (11 containers) overruns one gate | Med | Acceptable as macro-gate; verify (C6) may stay a documented stub to bound it |

## Suggested Final Prompt Changes

1. Define generate input = re-parse `--source` in-memory via inspect pipeline.
2. Move Rate identity/namespace/default-operation into Profiles.Rate; planner stays generic.
3. Add compile-clean contract + unresolved-mapping => review marker + exit 2 (never broken emit).
4. State generated models are name-only placeholders with review markers.
5. Forbid volatile content in emitted files (determinism).
6. Add GLOBAL STOP block; add ExitCodes.OutputUnsafe=5; define generate-without-source and
   --profile default.

## Execute / Do Not Execute Recommendation

DO NOT EXECUTE YET. The target is correctly DRAFT_FOR_REVIEW. Apply the six patches, then
publish a separate READY_FOR_CLAUDE execution request with a fresh REQUEST_ID. The structure
and safety posture are sound; the patches are additive and prevent silent architecture choices
and a determinism trap. No self-acceptance — GPT audits, Slava decides.
