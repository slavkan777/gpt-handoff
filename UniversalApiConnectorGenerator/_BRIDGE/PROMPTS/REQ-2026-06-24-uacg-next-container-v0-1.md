REQUEST_ID=REQ-2026-06-24-uacg-source-scan-final-v0-2
STATE=READY_FOR_CLAUDE
PROJECT=UniversalApiConnectorGenerator
GATE=SOURCE_SCAN_CONTAINER_V0_1
WORKSPACE=C:\Projects\UniversalApiConnectorGenerator
TARGET=net9.0 temporary
FINAL_TARGET=net10.0 later migration

ROUTE USED: DIRECT_MACRO_CONTAINER with TITAN_ELITE_4 checks
ALLOWED: only local workspace edits and Bridge report
FORBIDDEN: unrelated folders, remote repo operations, deployment, external service calls, secrets, production claims

GOAL
Add local API specification source scan, deterministic JSON artifacts, tests, CLI wiring, and Ukrainian docs for Igor. This is not connector generation.

GLOBAL STOP
Stop if Bridge mismatch, workspace mismatch, foundation missing, baseline build/test not green, package restore fails, source path is unsafe, or scope would leave the local workspace.

PACKAGE DECISION
Use Microsoft.OpenApi.Readers pinned to version 1.6.24 only inside Infrastructure. If this exact version cannot restore, stop and report BLOCKED_PACKAGE_RESTORE. Do not leak Microsoft.OpenApi types into Domain or Application public contracts.

EXIT CODES
0 success. 1 unexpected/internal error. 2 generate review-required stub. 4 source missing/invalid/unsafe. Existing verify returns 0. Inspect without --source prints usage and returns 0.

SOURCE SAFETY
Allowed extensions: .yaml, .yml, .json. Resolve full paths and reject any source outside the workspace root. Reject traversal, absolute escape, symlink escape, unsupported extension, empty file, and files larger than 5 MB. Enumerate files in deterministic sorted order.

DETERMINISM
Repeated scan of the same fixture must produce the same stable content hashes excluding run-id, timestamps, absolute paths, and output folder path. Manifest must record tool version, TFM, parser package and version, command arguments, selected operation, source hashes, diagnostics summary.

CONTAINER 0 PREFLIGHT
Read Bridge README, STATUS.json, this request. Verify workspace. Capture initial tree excluding bin/obj/output. Run dotnet --list-sdks, dotnet build, dotnet test. Check baseline is green and current tests are still 12/12 or more.

CONTAINER 1 LAYERED CONTRACTS
Domain: ProviderSchemaDocument, ProviderOperation, SourceFileEntry, SourceInventory, Diagnostic, reusing existing DiagnosticSeverity. Domain stays pure POCO and no project refs.
Application: ISourceInventoryService, ISpecParser, ISchemaExtractor if needed, IInspectionArtifactWriter, InspectCommandOptions, InspectCommandResult. Ports and DTOs only.
Infrastructure: inventory scanner, SHA-256 hasher, parser implementation, artifact writer.
Check dependency graph remains: Application -> Domain; Infrastructure -> Application + Domain; Domain -> none.

CONTAINER 2 INVENTORY
Implement file/folder inventory with source safety rules, SHA-256, size, relative path, extension, sorted enumeration, diagnostics. Tests: valid file, valid folder, missing path, unsupported extension, outside-workspace escape.

CONTAINER 3 PARSER
Parse YAML/JSON local API spec through Infrastructure parser. Produce ProviderSchemaDocument with title, version, selected operation, parameters, request/response refs, schema names, diagnostics. Selected default operation: POST /rating/{version}/{requestoption}. Add representative fixture tests/fixtures/api/rate-minimal.yaml. Do not claim fixture is official. Tests: fixture success, invalid file diagnostics, missing operation diagnostics.

CONTAINER 4 ARTIFACTS
Write output/<run-id>/scan/: source-inventory.json, provider-schema-document.json, diagnostics.json, scan-manifest.json. Indented JSON. No overwrite. Determinism test must compare stable hashes from two scans of the same fixture.

CONTAINER 5 CLI
Extend inspect command: inspect --source <path> with optional --operation and --profile. Valid fixture exits 0 and prints artifact folder. Missing/unsafe source exits 4. Inspect without --source prints usage and exits 0. Existing generate/verify/no-args behavior remains.

CONTAINER 6 VERIFY
Run restore, build, test, inspect valid fixture, inspect missing source, inspect outside-workspace escape, generate, verify, no-args with redirected stdin. Capture exact outputs and exit codes.

CONTAINER 7 UKRAINIAN DOCS FOR IGOR
Create README.md, docs/IGOR_DEMO_GUIDE_UA.md, docs/ARCHITECTURE_UA.md. Explain purpose, universal generator concept, Rate as first proof-case, how to open/build/test/run inspect, artifact locations, solution/project architecture, key classes, what works, what is not implemented, fixture honesty, temporary net9.0 and future net10.0 migration.

CONTAINER 8 REPORT
Write UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md with REQUEST_ID, STATUS READY_FOR_GPT_AUDIT or BLOCKED, files changed, architecture, commands, build/test, CLI smoke, docs, artifacts, boundary check, blockers, next step.

Claude chat reply only:
Bridge report ready. Tell GPT: отчёт.
