REQUEST_ID=REQ-2026-06-24-uacg-generate-review-package-final-v0-2
STATE=READY_FOR_CLAUDE
PROJECT=UniversalApiConnectorGenerator
GATE=GENERATE_REVIEW_PACKAGE_MACRO_V0_2
WORKSPACE=C:\Projects\UniversalApiConnectorGenerator
TARGET=net9.0 temporary
FINAL_TARGET=net10.0 later migration

ROUTE USED: DIRECT_MACRO_CONTAINER with TITAN_ELITE_4 checks
ALLOWED: only local workspace edits and Bridge report during execution
FORBIDDEN: unrelated folders, remote repo operations, deployment, live provider calls, secrets, production claims, LLM calls

GOAL
Implement the first generate vertical slice. Input is local API spec source via generate --source <path>. Generate re-runs the existing inspect pipeline in memory via ISourceInventoryService and ISpecParser to build ProviderSchemaDocument, then plans and emits a deterministic reviewable connector package skeleton. It does not read a previous inspect run folder. This is review package generation, not production connector delivery.

GLOBAL STOP
Stop if Bridge mismatch, workspace mismatch, baseline build/test not green, current inspect fixture command fails, scope leaves workspace, output safety is violated, package restore requires unapproved new packages, or emitted package unexpectedly does not compile.

PACKAGE POLICY
Do not add NSwag in this gate. Use owned deterministic C# emitter only. Add no new external packages unless GPT explicitly approves a new package gate. Generated runtime must not reference the generator, LLM, SQLite, provider SDK, or provider credentials.

EXIT CODES
0 success or verified accepted state. 1 unexpected/internal error. 2 generate completed and review is required. 4 invalid or unsafe source. 5 output safety/no-overwrite failure. Generate without --source prints usage and exits 0. --profile defaults to rate.

OUTPUT SAFETY
Write only under output/<run-id>/generate/. Never overwrite an existing run folder. Generated package folder must be self-contained under the run folder. Do not write generated files into src/ except generator implementation code.

DETERMINISM
Repeated generate from the same fixture must produce stable content hashes for generated package files, plan, diagnostics, manifest, checklist, and file-hashes, excluding run-id, timestamps, absolute paths, and output folder path. Emitted files must contain no timestamps, no random GUIDs, and no volatile auto-generated content; headers must be static.

AGENT WORKLOAD
Prometheus: gatekeeper and stop-condition enforcement.
Atlas: implementation builder.
Athena: architecture and domain critique.
Argus: safety/evidence/boundary audit.
Hermes: user-run simulation and demo verification.

CONTAINER 0 PREFLIGHT
Read Bridge README, STATUS.json, this request, last accepted source scan report. Verify workspace, baseline build 0 warnings/errors, baseline tests 21/21 or more, and current inspect fixture command. STOP if baseline is not green.

CONTAINER 1 GENERATION DOMAIN AND APPLICATION CONTRACTS
Domain: ConnectorPackagePlan, GeneratedFileDescriptor, GenerationManifest, ReviewChecklistItem, MappingReviewMarker, PackageIdentity. Reuse existing Diagnostic and DiagnosticSeverity if suitable; avoid duplicate diagnostic types unless required. Domain stays pure POCO, no I/O, zero project refs.
Application: IConnectorPackagePlanner, IGeneratedPackageEmitter, IGenerationManifestWriter, GenerateCommandOptions, GenerateCommandResult, generation service/use case. Ports and DTOs only.
Infrastructure: deterministic file emitter, manifest writer, generated package builder/checker if needed.
Check dependency graph remains clean.

CONTAINER 2 PROFILE PRESET AND PACKAGE PLANNER
Rate package identity, root namespace, and default operation must come from ConnectorGenerator.Profiles.Rate. The generic planner consumes profile preset data and stays provider-agnostic. For Rate preset use package identity Ups.Rate.Connector, namespace Ups.Rate.Connector, default operation POST /rating/{version}/{requestoption}. Missing selected operation returns exit 4. Plan includes provider/profile, source hashes, schema names, endpoints, diagnostics, and review-required markers.

CONTAINER 3 OWNED GENERATED PACKAGE EMITTER
Emit self-contained review package under output/<run-id>/generate/package/ with project file, Options, name-only placeholder model classes, connector interface, connector class, mapper placeholder, DI extension, README, and tests folder when useful. The emitted skeleton must always compile. Unresolved business mappings become review markers plus safe placeholders, for example mapper methods throwing NotImplementedException with marker comments. Review-required output exits 2. A compile failure is a bug and must BLOCK or exit 1.

CONTAINER 4 REVIEW ARTIFACTS
Write connector-package-plan.json, generation-manifest.json, diagnostics.json, REVIEW_CHECKLIST_UA.md, and file-hashes.json. Checklist in Ukrainian must enumerate each review marker, what was generated, what requires human review, what is not production, and how to build generated package. Generated models are name-only placeholders because current ProviderSchemaDocument contains schema names but not schema properties.

CONTAINER 5 CLI GENERATE WIRING
Extend generate command: generate --source <path> with optional --profile and --operation. --profile defaults to rate. Valid fixture creates review package and exits 2. Missing/unsafe source exits 4. Output safety violation exits 5. Generate without --source prints usage and exits 0. Existing inspect/verify/no-args behavior remains.

CONTAINER 6 VERIFY COMMAND EXTENSION
Optionally extend verify to verify a generated run folder: verify --package <path>. It should check required files, manifest, compile generated package if possible, and return 0 for structurally valid review package. If too large, keep verify stub and document why.

CONTAINER 7 TESTS
Add tests for profile preset isolation, generic planner, emitter no-overwrite, generated package compile, generated package references no ConnectorGenerator.* assembly and no provider SDK, generate exit code 2, missing source exit 4, output safety exit 5 if reachable, deterministic generated content hashes, and dependency boundaries. Run restore, build, test.

CONTAINER 8 CLI SMOKE
Run inspect valid fixture, generate valid fixture, verify generated package if implemented, generate missing source, generate without source, inspect no-source, generate/verify/no-args legacy checks with redirected stdin. Capture exact outputs and exit codes.

CONTAINER 9 DOCS UPDATE
Update README and Ukrainian docs to explain generate review package flow, output structure, commands, review checklist, current limits, placeholder models, no live calls, no LLM, no production claim, net9 temporary and net10 later migration.

CONTAINER 10 FINAL REPORT
Write Bridge report with REQUEST_ID, STATUS READY_FOR_GPT_AUDIT or BLOCKED, containers completed, files changed, architecture, output package tree, commands, build/test, CLI smoke, deterministic checks, docs, boundary check, blockers, next safe step.

Claude chat reply only:
Bridge report ready. Tell GPT: отчёт.
