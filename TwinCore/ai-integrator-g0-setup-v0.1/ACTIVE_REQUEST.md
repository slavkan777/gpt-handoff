# ACTIVE REQUEST — TwinCore AI Integrator G0 setup v0.1

REQUEST_ID: REQ-2026-06-12-twincore-ai-integrator-g0-setup-v0-1
PROJECT: TwinCore
GATE: implementation-setup / G0
TASK_TYPE: bounded setup implementation
STATUS: ACTIVE_REQUEST

## Objective

Create the isolated `AiIntegrator/` setup skeleton in the canonical Azure DevOps `Twincore-framework` repo.

This is G0 only.

G0 means:

```text
branch + isolated folder + solution skeleton + test project + fixture data + tool-scoped CLAUDE.md + local build/test evidence
```

G0 does NOT mean product implementation.

## Canonical source repo

Azure DevOps:

```text
https://dev.azure.com/twincore-net/_git/Twincore-framework
```

Known local path from Slava:

```text
C:\Projects\Twincore-framework
```

If local path differs, report and stop unless the canonical Azure remote is clearly the same repo.

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

G0 is allowed because Claude returned `READY_FOR_G0_WITH_LIMITATIONS` and Slava explicitly said to continue.

## Explicit G0 authorization

You are authorized to do exactly this:

1. Fetch latest Azure `origin/main`.
2. Record base SHA.
3. Create local branch:

```text
feature/12695-ai-integrator-poc
```

4. Create/modify only files under:

```text
AiIntegrator/**
```

5. Commit only `AiIntegrator/**`.
6. Push exactly that feature branch to Azure origin.
7. Write G0 evidence report to the four gpt-handoff paths listed below.

No other Azure write is authorized.

## Hard forbidden in G0

- Do not change `main`.
- Do not create PR.
- Do not edit Azure work items.
- Do not create or edit Azure pipelines/policies.
- Do not edit `TwinCore.sln`.
- Do not edit `Src/**`.
- Do not edit root files outside `AiIntegrator/**`.
- Do not edit `.claude/**`.
- Do not fix framework courtesy items.
- Do not implement product/business logic.
- Do not implement generator logic.
- Do not connect real carriers.
- Do not add secrets/API keys/provider credentials.
- Do not claim production readiness.
- Do not merge anything.
- Do not force-push.

If any of those appear required, STOP and report BLOCKED.

## Pre-flight checks — must happen before any write

1. Enter local repo folder.
2. Run `git status`.
3. If working tree is dirty, STOP and report.
4. Run `git remote -v` and confirm Azure DevOps `Twincore-framework` remote.
5. Run `git fetch --all --prune`.
6. Confirm default/current source branch is `main` or report actual default.
7. Record `origin/main` SHA and latest commit message.
8. Confirm target branch does not already exist locally or remotely.
9. Confirm `AiIntegrator/` does not already exist on `origin/main`.

If any check fails, do not create files.

## Required layout

Create exactly this root-level folder structure:

```text
AiIntegrator/
  AiIntegrator.sln
  src/
    TwinCore.AiIntegrator.Contracts/
    TwinCore.AiIntegrator.Domain/
    TwinCore.AiIntegrator.Generator/
    TwinCore.AiIntegrator.Sandbox/
    TwinCore.AiIntegrator.App/
  tests/
    TwinCore.AiIntegrator.Tests/
  fixtures/
  CLAUDE.md
```

## Project intent

### TwinCore.AiIntegrator.Contracts

Purpose:

- `IRateProvider` contracts later;
- canonical rate quote models later;
- orchestration contracts later.

G0 scope:

- project skeleton only;
- minimal compile placeholder allowed;
- no business logic.

Dependency rule:

```text
BCL + Microsoft.Extensions.* abstractions only.
```

### TwinCore.AiIntegrator.Domain

Purpose:

- ProviderSchema;
- MappingProfile;
- MappingRule;
- TransformKind;
- validation domain later.

G0 scope:

- project skeleton only;
- minimal compile placeholder allowed;
- no mapping engine.

### TwinCore.AiIntegrator.Generator

Purpose:

- future deterministic code generator;
- identifier/literal safety;
- artifact hashing.

G0 scope:

- project skeleton only;
- no generator implementation.

### TwinCore.AiIntegrator.Sandbox

Purpose:

- future generated-package build/test sandbox;
- local temp folders;
- offline package allowlist later.

G0 scope:

- project skeleton only;
- no process execution implementation.

### TwinCore.AiIntegrator.App

Purpose:

- future Blazor Server workbench host.

G0 scope:

- minimal Blazor Server app skeleton only if safe;
- no product pages beyond a neutral placeholder;
- no connection to real carriers;
- no dependency on existing `TwinCore.Admin`.

### TwinCore.AiIntegrator.Tests

Purpose:

- future unit/architecture tests;
- fixture hash checks later.

G0 scope:

- test project skeleton;
- at least one trivial smoke test proving test runner works.

## Fixtures

Create fixture folders/data for:

- `MockCarrierA`;
- `MockCarrierB`;
- `MockCarrierMessy`;
- `MockCarrierHostile`;
- golden sets.

G0 fixture scope:

- pure JSON/text data only;
- no secrets;
- no real carrier credentials;
- no real customer data;
- realistic enough to support future G1/G2/G3 work;
- record hashes in the G0 report.

If creating all four fixtures becomes too large for G0, create `MockCarrierA` + folders/placeholders for the others and explicitly report the limitation. Do not block unless build/test depends on missing fixture data.

## Tool-scoped CLAUDE.md

Create:

```text
AiIntegrator/CLAUDE.md
```

It must state:

- this folder is isolated from root TwinCore framework conventions;
- root `.claude/` skills and `Src/CLAUDE.md` examples do not automatically apply inside `AiIntegrator/`;
- do not copy `ServiceResponse.Error(ErrorType.NotFound)` example from `Src/CLAUDE.md`;
- write only under `AiIntegrator/**` during AI Integrator gates unless a future request explicitly says otherwise;
- generated adapters must use explicit DI, no convention scanning;
- runtime path has zero LLM;
- no secrets or real carrier credentials.

## Build/test evidence

G0 must produce local command evidence:

```text
dotnet build AiIntegrator/AiIntegrator.sln
dotnet test AiIntegrator/AiIntegrator.sln
```

Record:

- command;
- exit code;
- short result;
- any warnings/errors.

If build/test cannot pass without touching files outside `AiIntegrator/**`, STOP and report PARTIAL/BLOCKED.

## Required verification before commit

Before commit:

1. Run secret scan / grep checks sufficient for local setup.
2. Run:

```text
git diff --stat <base-sha>..HEAD -- . ':!AiIntegrator'
```

Expected result:

```text
empty
```

3. Confirm `TwinCore.sln` unchanged.
4. Confirm `Src/**` unchanged.
5. Confirm `.claude/**` unchanged.

## Commit and push

Commit message:

```text
Add AI Integrator G0 setup skeleton
```

Push only:

```text
feature/12695-ai-integrator-poc
```

Do not create PR.

Verify:

```text
git ls-remote origin feature/12695-ai-integrator-poc
```

Remote branch SHA must match local HEAD.

## Stop conditions

Stop immediately and report if:

1. STATUS.json request identity does not match this request.
2. Local working tree is dirty.
3. `origin/main` fetch fails.
4. Base SHA cannot be recorded.
5. Target branch already exists locally or remotely.
6. `AiIntegrator/` already exists on `origin/main`.
7. Any required change would touch outside `AiIntegrator/**`.
8. `TwinCore.sln` would need modification.
9. `Src/**` would need modification.
10. `.claude/**` would need modification.
11. Secret material would enter a commit.
12. Build/test cannot pass without framework edits.
13. Push is rejected.
14. Any ambiguity widens scope.

No stash/reset/clean of someone else's work. If dirty, report only.

## Required report structure

# TwinCore AI Integrator G0 Setup Report

1. Executive result
2. Request identity and gate
3. Pre-flight checks
4. Base branch and SHA
5. Created branch and commit
6. Files created under `AiIntegrator/**`
7. Fixtures created and hashes
8. Build/test evidence
9. Verification that no files outside `AiIntegrator/**` changed
10. Secret scan result
11. Push verification
12. Deviations / limitations
13. Stop conditions encountered, if any
14. Next safe step
15. What must NOT be done next

Use final status:

- `G0_DONE_READY_FOR_AUDIT`
- `G0_PARTIAL_READY_FOR_AUDIT`
- `G0_BLOCKED`

## Write report only here

- `TwinCore/ai-integrator-g0-setup-v0.1/report.md`
- `TwinCore/latest-report.md`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`
- `TwinCore/_BRIDGE/STATUS.json`

Use:

REQUEST_ID: REQ-2026-06-12-twincore-ai-integrator-g0-setup-v0-1
PROJECT: TwinCore
GATE: implementation-setup / G0
STATUS: READY_FOR_AUDIT

## Reminder

This is a setup gate only. Do not proceed into G1. Do not implement importer, mapping UI, generator, sandbox process execution, real adapter, or carrier API logic.
