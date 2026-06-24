REQUEST_ID=REQ-2026-06-24-uacg-next-container-v0-1
STATE=READY_FOR_CLAUDE
PROJECT=UniversalApiConnectorGenerator
GATE=SOURCE_SCAN_CONTAINER_V0_1
WORKSPACE=C:\Projects\UniversalApiConnectorGenerator
TARGET=net9.0

ROUTE USED: DIRECT_MACRO_CONTAINER with implementation checks
ALLOWED: only local workspace edits and Bridge report
FORBIDDEN: unrelated folders, remote repo operations, deployment, external service calls

GOAL
Add local API specification source scan, JSON artifacts, tests, and Ukrainian docs for Igor. This is not connector generation.

CONTAINERS
0. Preflight: verify Bridge, workspace, build, tests.
1. Contracts: add SourceInventory, SourceFileEntry, ProviderSchemaDocument, ProviderOperation, Diagnostic, service interfaces, options and result types.
2. Inventory: support .yaml .yml .json local files/folders, SHA-256, size, relative path, diagnostics.
3. Parser: add approved API spec reader package if needed, parse YAML/JSON, create normalized ProviderSchemaDocument, add representative fixture tests/fixtures/api/rate-minimal.yaml.
4. Artifacts: write source-inventory.json, provider-schema-document.json, diagnostics.json, scan-manifest.json under output/<run-id>/scan/.
5. CLI: extend existing inspect command to accept --source <path>, optional --operation and --profile. Valid fixture exits 0. Missing source exits non-zero. Existing generate/verify/menu stay working.
6. Verify: run restore, build, test, valid source command, missing source command, generate, verify, no-args.
7. Docs: create README.md, docs/IGOR_DEMO_GUIDE_UA.md, docs/ARCHITECTURE_UA.md with run instructions, architecture, classes, current limits, net9 temporary note, net10 future migration.
8. Report: write latest Bridge report with status READY_FOR_GPT_AUDIT or BLOCKED.

STOP
Stop if baseline fails, package restore fails, workspace mismatch, Bridge mismatch, or scope would leave the workspace.

Claude chat reply only:
Bridge report ready. Tell GPT: отчёт.
