REQUEST_ID: REQ-2026-06-24-uacg-local-foundation-skeleton-net9-v0-2
STATE: READY_FOR_CLAUDE
TASK_TYPE: implementation-local-foundation-rerun
PROJECT: UniversalApiConnectorGenerator
GATE: LOCAL_FOUNDATION_SKELETON_V0_1
PROMPT_PATH: UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md
REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
LOCAL_WORKSPACE: C:\Projects\UniversalApiConnectorGenerator
CREATED: 2026-06-24
CREATED_BY: architect-gpt
TEMP_TARGET_FRAMEWORK: net9.0
FINAL_ARCHITECTURE_TARGET: net10.0_later_migration

# ROUTING LOCK

ROUTE USED: TITAN_ELITE_4
WHY: bounded local implementation rerun after .NET 10 SDK blocker
ALLOWED: write only inside C:\Projects\UniversalApiConnectorGenerator and write the Bridge report
FORBIDDEN: TwinCore, AiIntegrator, claude-vault, AIKB writes, remote repo operations, provider/API calls, production/deployment
FIRST SAFE ACTION: read Bridge README, verify STATUS.json, then verify the local workspace path

# DECISION OVERRIDE

Slava approved temporary local target `net9.0` for this Foundation Skeleton gate because installed SDKs are 8.0.422 and 9.0.304, and .NET 10 SDK is absent.

Use `net9.0` for all projects in this gate.

Do not use `net10.0` in this rerun.

Preserve the architecture note: final target remains `net10.0` after a later SDK installation/migration gate.

# READ FIRST

1. UniversalApiConnectorGenerator/_BRIDGE/README.md
2. This active request
3. UniversalApiConnectorGenerator/_BRIDGE/STATUS.json

# GOAL

Create the local foundation skeleton for Universal API Connector Generator in:

C:\Projects\UniversalApiConnectorGenerator

This gate creates the solution, projects, references, CLI startup, command stubs, root path handling, build, and minimal tests only.

# STOP BEFORE START

Stop with BLOCKED if:

- the Bridge cannot be read or the report cannot be written;
- REQUEST_ID / PROJECT / GATE do not match STATUS.json;
- local workspace is not C:\Projects\UniversalApiConnectorGenerator;
- the workspace contains unexpected existing product files;
- dotnet SDK cannot create `net9.0` projects.

# WRITABLE LOCAL SCOPE

Only:

C:\Projects\UniversalApiConnectorGenerator

# REQUIRED STRUCTURE

Create:

UniversalApiConnectorGenerator.sln
src/ConnectorGenerator.Cli
src/ConnectorGenerator.Domain
src/ConnectorGenerator.Application
src/ConnectorGenerator.Infrastructure
src/ConnectorGenerator.Profiles.Rate
tests/ConnectorGenerator.Tests

Use target framework `net9.0` for this local gate.

Project references:

- ConnectorGenerator.Cli -> ConnectorGenerator.Application
- ConnectorGenerator.Cli -> ConnectorGenerator.Infrastructure
- ConnectorGenerator.Cli -> ConnectorGenerator.Profiles.Rate
- ConnectorGenerator.Application -> ConnectorGenerator.Domain
- ConnectorGenerator.Infrastructure -> ConnectorGenerator.Application
- ConnectorGenerator.Infrastructure -> ConnectorGenerator.Domain
- ConnectorGenerator.Profiles.Rate -> ConnectorGenerator.Application
- ConnectorGenerator.Profiles.Rate -> ConnectorGenerator.Domain
- ConnectorGenerator.Domain -> no project references

# REQUIRED BEHAVIOR

CLI no args:

- show product name;
- show available commands;
- in interactive console, allow basic selection or exit;
- in redirected/non-interactive input, print menu and exit 0.

Headless commands:

- inspect: safe stub, exit 0;
- generate: safe stub, no files generated, exit 2 review-required;
- verify: safe stub, exit 0.

Root folders:

- resolve workspace root, not bin/Debug;
- create or resolve data, logs, output folders under the workspace root;
- do not create external folders.

# MINIMAL CODE SHAPE

Keep it simple:

- Cli Program.cs
- Application command result / exit code types
- Application workspace path contract
- Infrastructure workspace path provider
- Domain diagnostic severity enum or marker
- Profiles.Rate profile marker
- Tests for workspace path provider and command stubs

Do not add OpenAPI parsing, NSwag, provider integrations, SQLite schema, UPS ingestion, generated package code, or paid/external calls.

# COMMANDS TO RUN

Run and capture evidence:

- dotnet --list-sdks
- dotnet restore
- dotnet build
- dotnet test
- dotnet run --project src/ConnectorGenerator.Cli -- inspect
- dotnet run --project src/ConnectorGenerator.Cli -- generate
- dotnet run --project src/ConnectorGenerator.Cli -- verify
- dotnet run --project src/ConnectorGenerator.Cli

For the no-args command, avoid hanging. If needed, pipe input or use non-interactive mode evidence.

# REPORT

Write the full report to:

UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md

The report must include:

REQUEST_ID: REQ-2026-06-24-uacg-local-foundation-skeleton-net9-v0-2
STATUS: READY_FOR_GPT_AUDIT or BLOCKED
PROJECT: UniversalApiConnectorGenerator
GATE: LOCAL_FOUNDATION_SKELETON_V0_1
TARGET_FRAMEWORK_USED: net9.0
FINAL_TARGET_NOTE: net10.0 later migration gate
PROMPT_PATH: UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md
REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md

Sections:

- Route Used
- Workspace Verification
- Files Created or Modified
- Solution Structure
- Project References
- Commands Run and Outputs
- Build/Test Result
- CLI Smoke Result
- Boundary Check
- Blockers
- Next Safe Step

In Claude chat reply only:

Bridge report ready. Tell GPT: отчёт.
