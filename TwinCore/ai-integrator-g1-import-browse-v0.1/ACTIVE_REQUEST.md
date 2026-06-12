# ACTIVE REQUEST — TwinCore AI Integrator G1 Import + Browse v0.1

REQUEST_ID: REQ-2026-06-12-twincore-ai-integrator-g1-import-browse-v0-1
PROJECT: TwinCore
GATE: implementation / G1 import-and-browse
TASK_TYPE: bounded macro implementation
STATUS: ACTIVE_REQUEST

## Objective

Implement the G1 macro vertical slice under `AiIntegrator/**` only.

G1 means:

```text
Import + Browse vertical slice
```

This is one macro gate. Do not ask for micro-prompts unless a stop condition is hit.

## Canonical source repo

Azure DevOps:

```text
https://dev.azure.com/twincore-net/_git/Twincore-framework
```

Known local path:

```text
C:\Projects\Twincore-framework
```

Active branch from G0:

```text
feature/12695-ai-integrator-poc
```

G0 commit:

```text
d10e34aec6e1676ac34ad23071f9259d322da64d
```

## Read first — AIKB

- `00_CONTROL_CENTER/START_HERE.md`
- `00_CONTROL_CENTER/UNIVERSAL_MOLECULAR_BOOTSTRAP_RULE.md`
- `00_CONTROL_CENTER/ACTIVE_PROJECTS.md`
- `01_PROJECTS/TwinCore/PROJECT_PROFILE.md`
- `01_PROJECTS/TwinCore/CURRENT_STATE.md`
- `01_PROJECTS/TwinCore/TASK_LEDGER.md`
- `01_PROJECTS/TwinCore/CONTEXT_PACK/LATEST_CHAT_HANDOFF.md`
- `01_PROJECTS/TwinCore/FEATURE_PLANS/TWINCORE_AI_INTEGRATOR_IMPLEMENTATION_PLAN_V0_2.md`
- `01_PROJECTS/TwinCore/FEATURE_PLANS/TWINCORE_LOGISTICS_CONNECTOR_FACTORY_MVP.md`
- `01_PROJECTS/TwinCore/DECISIONS/ADR-2026-06-12-logistics-connector-factory-workstream.md`
- `01_PROJECTS/TwinCore/DECISIONS/ADR-2026-06-12-ai-integrator-technical-design-baseline.md`

## Read second — gpt-handoff

- `TwinCore/_BRIDGE/STATUS.json`
- `TwinCore/ai-integrator-g0-setup-v0.1/report.md`
- `TwinCore/ai-integrator-plan-v0.2-review-before-g0/report.md`
- `TwinCore/twincore-framework-ai-integrator-readiness-v0.1/report.md`
- `TwinCore/ai-integrator-tech-design-v0.1/report.md`
- `TwinCore/latest-report.md`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`

## Owner-proxy decision mode

Igor is unavailable.

Slava/GPT is acting as provisional owner decision gate.

Allowed phrasing:

```text
Provisional owner-approved baseline by Slava; reversible when Igor returns.
```

Forbidden phrasing:

```text
Igor approved.
```

## Explicit G1 authorization

You are authorized to do exactly this:

1. Continue on branch `feature/12695-ai-integrator-poc`.
2. Write only files under:

```text
AiIntegrator/**
```

3. Implement G1 Import + Browse vertical slice.
4. Commit only `AiIntegrator/**` changes.
5. Push only branch `feature/12695-ai-integrator-poc` to Azure origin.
6. Write G1 evidence report to the four gpt-handoff paths listed below.

No other Azure write is authorized.

## Hard forbidden in G1

- Do not change `main`.
- Do not create PR.
- Do not edit Azure work items.
- Do not create or edit Azure pipelines/policies.
- Do not edit `TwinCore.sln`.
- Do not edit `Src/**`.
- Do not edit root files outside `AiIntegrator/**`.
- Do not edit `.claude/**`.
- Do not fix framework courtesy items.
- Do not implement Mapping Workbench.
- Do not implement mapping validation engine.
- Do not implement adapter generator.
- Do not implement sandbox process execution.
- Do not connect real carriers.
- Do not add LLM provider integration.
- Do not add secrets/API keys/provider credentials.
- Do not claim production readiness.
- Do not merge anything.
- Do not force-push.

If any of those appear required, STOP and report BLOCKED.

## Pre-flight checks — must happen before any write

1. Enter local repo folder.
2. Run `git status`.
3. If working tree is dirty, STOP and report.
4. Run `git fetch --all --prune`.
5. Confirm local/remote branch `feature/12695-ai-integrator-poc` exists.
6. Confirm remote branch HEAD is G0 commit or record actual head.
7. Confirm `AiIntegrator/` exists.
8. Confirm no unreviewed local changes.
9. Confirm `TwinCore.sln`, `Src/**`, `.claude/**`, and root files are untouched before starting.

If `origin/main` has moved since G0, do not rebase silently. Record drift. Continue on feature branch unless merge/rebase is required; if merge/rebase is required, STOP and report.

## G1 deliverables

Implement one cohesive vertical slice.

### 1. Workbench shell/navigation

In `TwinCore.AiIntegrator.App`:

- basic shell/navigation for AI Integrator;
- pages or components for:
  - New Integration Wizard;
  - Provider Schema Viewer;
  - Internal Model Browser;
- neutral internal-tool UI only;
- no product branding claims;
- no real carrier connectivity.

### 2. Provider source input

Support loading provider source from existing fixture data, especially:

```text
AiIntegrator/fixtures/MockCarrierA/openapi.json
```

G1 may use file-based fixture loading and simple UI/demo flow. No external URL fetch required.

### 3. ProviderSchema import

Implement minimal deterministic OpenAPI importer for `MockCarrierA` sufficient to produce a `ProviderSchema` structure.

Required minimum:

- provider name/id;
- operations/endpoints;
- request fields;
- response fields;
- array indicators where visible;
- field paths;
- example/metadata references if simple.

No LLM. No SOAP. No broad OpenAPI generality beyond fixture-driven parser.

### 4. ProviderSchema viewer

UI/view-model should show imported schema as browsable data:

- operations/endpoints;
- request fields;
- response fields;
- path/type/required/array indicators where available;
- enough to visually prove import worked.

### 5. Internal Model Browser

Add a basic canonical internal model browser for the rate quote MVP:

- `RateQuoteRequest` or equivalent;
- origin/destination address;
- package weight/dimensions;
- canonical quote option/result fields;
- carrier/service/price/currency/transit/warnings/error fields.

This is browse/display only. No mapping workbench yet.

### 6. Repository abstraction + in-memory implementation

Add repository/service abstraction sufficient for G1:

- import provider schema from fixture;
- store/read in memory;
- expose to UI/service layer.

No EF yet unless trivial and isolated; G1 should prefer in-memory.

### 7. Tests

Add tests for:

- MockCarrierA fixture loads;
- importer creates expected operation count / core fields;
- ProviderSchema contains response array path relevant for future `IterationRoot`;
- internal model browser definitions are present;
- existing smoke tests still pass.

### 8. Build/test evidence

Run:

```text
dotnet build AiIntegrator/AiIntegrator.sln
dotnet test AiIntegrator/AiIntegrator.sln
```

Record command, exit code, warnings/errors.

## Architecture constraints

- UI actions must go through app/service layer; do not put domain logic directly in Razor components.
- Keep API/service boundary clean enough for future SPA replacement.
- Keep app isolated from `TwinCore.Admin`.
- Do not use `ServiceResponse` in `AiIntegrator` contracts/domain/importer.
- Do not depend on root TwinCore framework projects.
- Do not rely on convention scanning.
- Runtime path has zero LLM.
- Generated adapter design remains future G3; do not start it now.

## Required verification before commit

Before commit:

1. Run secret scan / grep checks sufficient for local setup.
2. Run:

```text
git diff --stat <pre-g1-head>..HEAD -- . ':!AiIntegrator'
```

Expected result:

```text
empty
```

3. Confirm `TwinCore.sln` unchanged.
4. Confirm `Src/**` unchanged.
5. Confirm `.claude/**` unchanged.
6. Confirm no real credentials / no real carrier secrets.

## Commit and push

Commit message:

```text
Add AI Integrator G1 import and browse slice
```

Push only:

```text
feature/12695-ai-integrator-poc
```

Do not create PR.

Verify remote branch SHA matches local HEAD.

## Stop conditions

Stop immediately and report if:

1. STATUS.json request identity does not match this request.
2. Local working tree is dirty.
3. Target branch is missing or divergent in a way that requires merge/rebase.
4. Any required change would touch outside `AiIntegrator/**`.
5. `TwinCore.sln` would need modification.
6. `Src/**` would need modification.
7. `.claude/**` would need modification.
8. Secret material would enter a commit.
9. Build/test cannot pass without framework edits.
10. Scope starts moving into Mapping Workbench/G2 or Generator/G3.
11. Push is rejected.
12. Any ambiguity widens scope.

No stash/reset/clean of someone else's work. If dirty, report only.

## Required report structure

# TwinCore AI Integrator G1 Import + Browse Report

1. Executive result
2. Request identity and gate
3. Pre-flight checks
4. Branch/head before and after
5. Files changed under `AiIntegrator/**`
6. Workbench shell/navigation delivered
7. Provider source input delivered
8. ProviderSchema importer delivered
9. ProviderSchema viewer delivered
10. Internal Model Browser delivered
11. Repository/in-memory service delivered
12. Tests added/updated
13. Build/test evidence
14. Verification that no files outside `AiIntegrator/**` changed
15. Secret scan result
16. Push verification
17. Deviations / limitations
18. Stop conditions encountered, if any
19. Next safe step
20. What must NOT be done next

Use final status:

- `G1_DONE_READY_FOR_AUDIT`
- `G1_PARTIAL_READY_FOR_AUDIT`
- `G1_BLOCKED`

## Write report only here

- `TwinCore/ai-integrator-g1-import-browse-v0.1/report.md`
- `TwinCore/latest-report.md`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`
- `TwinCore/_BRIDGE/STATUS.json`

Use:

REQUEST_ID: REQ-2026-06-12-twincore-ai-integrator-g1-import-browse-v0-1
PROJECT: TwinCore
GATE: implementation / G1 import-and-browse
STATUS: READY_FOR_AUDIT

## Reminder

This is G1 only. Do not proceed into G2. Do not implement mapping workbench, validation gate, profile approval, generator, sandbox execution, generated adapter, real carrier API, LLM provider, PR, or merge.
