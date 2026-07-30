REQUEST_ID=REQ-2026-06-24-uacg-manual-real-user-uat-v0-1
STATE=READY_FOR_CLAUDE
PROJECT=UniversalApiConnectorGenerator
GATE=MANUAL_REAL_USER_UAT_V0_1
WORKSPACE=C:\Projects\UniversalApiConnectorGenerator
MODE=MANUAL_UAT_READ_ONLY
TARGET=net9.0 temporary
FINAL_TARGET=net10.0 later migration

ROUTE USED: DIRECT_MACRO_CONTAINER_UAT with evidence checks
ALLOWED: read local workspace, run documented commands, create temporary UAT output under workspace output/, write only Bridge report
FORBIDDEN: source code edits, docs edits, AIKB edits, remote repo operations, unrelated folders, external provider calls, secrets, deployment, production claims

GOAL
Act as a real new user and validate the product manually from the documentation and CLI, after the Generate Review Package gate. Do not implement new features. Do not fix code. If a command from docs fails, report it as UAT failure with exact command/output.

READ
1. Bridge README
2. STATUS.json
3. Latest Generate Review Package report
4. README.md
5. docs/IGOR_DEMO_GUIDE_UA.md
6. docs/ARCHITECTURE_UA.md

GLOBAL STOP
Stop with BLOCKED if Bridge mismatch, workspace mismatch, solution missing, docs missing, baseline build/test fail, or commands would leave the workspace.

CONTAINER 0 PREFLIGHT
Verify workspace, target framework, solution exists, docs exist. Run dotnet --list-sdks, dotnet restore, dotnet build, dotnet test.

CONTAINER 1 DOCUMENTATION WALKTHROUGH
Follow README and Ukrainian demo guide as a user. Extract the documented commands and run them exactly where possible. Record any mismatch between docs and actual behavior.

CONTAINER 2 INSPECT USER FLOW
Run inspect on the fixture from a clean terminal context. Confirm exit 0, artifact folder printed, source-inventory.json, provider-schema-document.json, diagnostics.json, scan-manifest.json exist and contain expected key facts.

CONTAINER 3 GENERATE USER FLOW
Run generate on the fixture with default profile and with --profile rate if documented. Confirm exit 2 review-required, output package folder printed, package plan, manifest, diagnostics, file-hashes, REVIEW_CHECKLIST_UA.md, and package files exist.

CONTAINER 4 GENERATED PACKAGE BUILD
Build the emitted package csproj exactly as a user would. Confirm build exit 0 and no external/project references. Inspect README/checklist enough to confirm honest review-package limits.

CONTAINER 5 VERIFY USER FLOW
Run verify --package <generated-run-folder> if implemented. Confirm exit 0. Also run verify with no args and confirm legacy behavior remains documented or acceptable.

CONTAINER 6 NEGATIVE USER FLOWS
Run missing-source generate, generate without source, inspect without source, and unsafe outside-workspace source if safe to test. Confirm documented exit codes: missing/unsafe 4, no-source usage 0.

CONTAINER 7 REAL USER VERDICT
Assess: Can a new user clone/open locally, restore, build, test, inspect, generate, build emitted package, and understand limitations from docs? Since no GitHub repo exists yet, explicitly mark clone-from-GitHub as NOT TESTED / BLOCKED_BY_NO_REPO, not product failure.

CONTAINER 8 REPORT
Write UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md with REQUEST_ID, STATUS READY_FOR_GPT_AUDIT or BLOCKED, commands run, exact outputs/exit codes, docs mismatches, UAT verdict, blockers, and next safe step.

Claude chat reply only:
Bridge report ready. Tell GPT: отчёт.
