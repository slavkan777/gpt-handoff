REQUEST_ID=REQ-2026-06-24-uacg-generate-review-package-v0-1
STATE=DRAFT_FOR_REVIEW
PROJECT=UniversalApiConnectorGenerator
GATE=GENERATE_REVIEW_PACKAGE_MACRO_V0_1
WORKSPACE=C:\Projects\UniversalApiConnectorGenerator
TARGET=net9.0 temporary
FINAL_TARGET=net10.0 later migration

ROUTE USED: DIRECT_MACRO_CONTAINER with TITAN_ELITE_4 checks
ALLOWED: only local workspace edits and Bridge report during execution
FORBIDDEN: unrelated folders, remote repo operations, deployment, live provider calls, secrets, production claims, LLM calls

GOAL
Implement the first generate vertical slice. Input is the already supported local API spec source or ProviderSchemaDocument produced by inspect. Output is a deterministic reviewable connector package skeleton with package plan, generated source files, tests, manifest, diagnostics, and review checklist. This is still review package generation, not production connector delivery.

EXECUTION MODE
This target prompt must be reviewed before execution. Do not execute it until GPT publishes a separate READY_FOR_CLAUDE execution request.

PACKAGE POLICY
Do not add NSwag in this gate. Use owned deterministic C# emitter only. Add no new external packages unless the build is blocked and GPT explicitly approves a new package gate. Generated runtime must not reference the generator, LLM, SQLite, or provider credentials.

EXIT CODES
0 success or verified accepted state. 1 unexpected/internal error. 2 generate completed but review is required. 4 invalid or unsafe source. 5 output safety/no-overwrite failure.

OUTPUT SAFETY
Write only under output/<run-id>/generate/. Never overwrite an existing run folder. Generated package folder must be self-contained under the run folder. Do not write generated files into src/ except generator implementation code.

DETERMINISM
Repeated generate from the same fixture must produce stable content hashes for generated package files, plan, diagnostics, and review checklist, excluding run-id, timestamps, absolute paths, and output folder path.

AGENT WORKLOAD
Prometheus: gatekeeper and stop-condition enforcement.
Atlas: implementation builder.
Athena: architecture and domain critique.
Argus: safety/evidence/boundary audit.
Hermes: user-run simulation and Igor-style demo verification.

CONTAINER 0 PREFLIGHT
Read Bridge README, STATUS.json, this request, last accepted source scan report. Verify workspace, baseline build, baseline tests, and current inspect fixture command. STOP if baseline is not green.

CONTAINER 1 GENERATION DOMAIN AND APPLICATION CONTRACTS
Domain: ConnectorPackagePlan, GeneratedFileDescriptor, GenerationManifest, ReviewChecklistItem, MappingReviewMarker, PackageIdentity, GenerationDiagnostic. Keep pure POCO, no I/O.
Application: IConnectorPackagePlanner, IGeneratedPackageEmitter, IGenerationManifestWriter, GenerateCommandOptions, GenerateCommandResult, generation service/use case. Ports and DTOs only.
Infrastructure: deterministic file emitter, manifest writer, generated package builder/checker if needed.
Check dependency graph remains clean.

CONTAINER 2 PACKAGE PLANNER
Create package plan from ProviderSchemaDocument. For Rate proof case use package identity Ups.Rate.Connector, target net9.0, namespace Ups.Rate.Connector. Include selected operation, provider/profile, source hashes, schema names, endpoints, diagnostics. Missing selected operation returns exit 4.

CONTAINER 3 OWNED GENERATED PACKAGE EMITTER
Emit self-contained review package under output/<run-id>/generate/package/ with project file, Options, models, connector interface, connector class, mapper placeholder, DI extension, README, and tests folder when useful. Generated code must compile. Add review markers where mapping is unresolved. Do not call provider APIs.

CONTAINER 4 REVIEW ARTIFACTS
Write connector-package-plan.json, generation-manifest.json, diagnostics.json, REVIEW_CHECKLIST_UA.md, and file-hashes.json. Checklist in Ukrainian: what was generated, what requires human review, what is not production, how to build generated package.

CONTAINER 5 CLI GENERATE WIRING
Extend generate command: generate --source <path> --profile rate with optional --operation. Valid fixture creates review package and exits 2. Missing/unsafe source exits 4. Existing inspect/verify/no-args behavior remains.

CONTAINER 6 VERIFY COMMAND EXTENSION
Optionally extend verify to verify a generated run folder: verify --package <path>. It should check required files, manifest, compile generated package if possible, and return 0 for structurally valid review package. If too large, keep verify stub and document why.

CONTAINER 7 TESTS
Add tests for planner, emitter no-overwrite, generated package compile, generate exit code 2, missing source exit 4, deterministic generated content hashes, and dependency boundaries. Run restore, build, test.

CONTAINER 8 CLI SMOKE
Run inspect valid fixture, generate valid fixture, verify generated package if implemented, generate missing source, inspect no-source, generate/verify/no-args legacy checks. Capture exact outputs and exit codes.

CONTAINER 9 DOCS UPDATE
Update README and Ukrainian docs to explain generate review package flow, output structure, commands, review checklist, current limits, no live calls, no LLM, no production claim, net9 temporary and net10 later migration.

CONTAINER 10 FINAL REPORT
Write Bridge report with REQUEST_ID, STATUS READY_FOR_GPT_AUDIT or BLOCKED, containers completed, files changed, architecture, output package tree, commands, build/test, CLI smoke, deterministic checks, docs, boundary check, blockers, next safe step.

Claude chat reply only:
Bridge report ready. Tell GPT: отчёт.
