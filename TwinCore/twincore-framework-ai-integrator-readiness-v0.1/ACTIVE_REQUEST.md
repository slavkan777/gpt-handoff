# ACTIVE REQUEST — Twincore-framework readiness/refactor analysis for AI Integrator v0.1

REQUEST_ID: REQ-2026-06-12-twincore-framework-ai-integrator-readiness-v0-1
PROJECT: TwinCore
GATE: planning / framework-readiness-analysis
TASK_TYPE: read-only analysis / report-only
STATUS: ACTIVE_REQUEST

## Task

Analyze the existing `Twincore-framework` from the TwinCore side and determine what must be finished, refactored, isolated, or added so the future AI Integrator / Integration Mapping Factory can be implemented safely.

This is NOT implementation.

The previous reports planned the AI Integrator product from the product/design side. This request covers the missing angle:

**How should the existing TwinCore framework be completed or refactored to host/support this workstream without creating architectural debt?**

## Read first — AIKB

- `00_CONTROL_CENTER/START_HERE.md`
- `00_CONTROL_CENTER/UNIVERSAL_MOLECULAR_BOOTSTRAP_RULE.md`
- `00_CONTROL_CENTER/ACTIVE_PROJECTS.md`
- `01_PROJECTS/TwinCore/PROJECT_PROFILE.md`
- `01_PROJECTS/TwinCore/CURRENT_STATE.md`
- `01_PROJECTS/TwinCore/TASK_LEDGER.md`
- `01_PROJECTS/TwinCore/CONTEXT_PACK/LATEST_CHAT_HANDOFF.md`
- `01_PROJECTS/TwinCore/FEATURE_PLANS/TWINCORE_LOGISTICS_CONNECTOR_FACTORY_MVP.md`
- `01_PROJECTS/TwinCore/FEATURE_PLANS/TWINCORE_AI_INTEGRATOR_IMPLEMENTATION_PLAN_V0_1.md`
- `01_PROJECTS/TwinCore/DECISIONS/ADR-2026-06-12-logistics-connector-factory-workstream.md`
- `01_PROJECTS/TwinCore/DECISIONS/ADR-2026-06-12-ai-integrator-technical-design-baseline.md` if present

## Read second — gpt-handoff

- `TwinCore/_BRIDGE/STATUS.json`
- `TwinCore/main-review-v0.2/report.md`
- `TwinCore/logistics-connector-factory-planning-v0.1/report.md`
- `TwinCore/ai-integrator-implementation-plan-review-v0.1/report.md`
- `TwinCore/ai-integrator-tech-design-v0.1/report.md`
- `TwinCore/latest-report.md`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`

## Source repo context

Existing source repo:

`https://dev.azure.com/twincore-net/_git/Twincore-framework`

Previously reviewed main commit:

`f6c0c84` (`add CLAUDE.md`)

Use the available source evidence/read-only repo access if available. If direct Azure repo access is unavailable, state that clearly and base the report on the prior `main-review-v0.2` evidence.

## Product context

Broad product:

`TwinCore AI Integrator / Integration Mapping Factory`

First MVP slice:

`Logistics Rate API Connector Factory`

Accepted technical direction:

- deterministic parser/generator first;
- human-reviewed `MappingProfile`;
- generated typed C# adapter;
- runtime zero LLM;
- generated adapters should have explicit DI/config/tests;
- future implementation grouped as G0-G4.

## Required analysis focus

Analyze from the existing TwinCore framework side:

1. Current framework architecture fit
   - where AI Integrator should live;
   - whether it should be inside existing framework layers or isolated module/application;
   - whether plain ASP.NET Core app/tool is safer than embedding into existing conventions.

2. Required framework completion before AI Integrator
   - automated tests baseline;
   - DI registration reliability;
   - configuration/options pattern;
   - logging/error handling;
   - module boundaries;
   - build/CI readiness;
   - documentation/code drift cleanup needed before implementation.

3. Refactor risks
   - what existing patterns are safe to reuse;
   - what patterns should be avoided;
   - what must not be copied into generated adapters;
   - what creates hidden coupling.

4. Integration seams
   - where `IRateProvider` contracts should live;
   - where generated adapter packages should plug in;
   - how explicit provider registration should work;
   - how quote orchestration should integrate without breaking existing architecture.

5. Data/persistence fit
   - where ProviderSchema / MappingProfile / IntegrationRun storage belongs;
   - whether existing EF/repository/unit-of-work patterns are suitable;
   - what persistence abstraction is needed from slice 1.

6. UI hosting fit
   - whether AI Integrator UI should be separate workbench app, inside existing admin portal, Blazor Server, Razor, SPA, or separate tool;
   - tradeoffs against current TwinCore structure.

7. Generated code compatibility
   - generated adapters should be independent, testable, explicit DI, no fragile assembly scanning;
   - how to package them for TwinCore consumption.

8. G0 setup requirements from framework side
   - branch/solution layout proposal;
   - project/folder boundaries;
   - test project layout;
   - fixture location;
   - CI/build gates;
   - what must be verified before G1.

9. What to finish/refactor first
   - prioritized list: Must before G0 / Must before G1 / Can defer / Do not touch.

10. Risks and stop conditions
   - no over-refactor;
   - no broad cleanup;
   - no source changes until explicit implementation gate;
   - no making AI Integrator depend on unstable framework conventions.

## Required report structure

# Twincore-framework readiness/refactor analysis for AI Integrator v0.1

1. Executive verdict
2. Source evidence and limitations
3. Current framework fit assessment
4. Recommended hosting model
5. Required framework completions before implementation
6. Required refactors or isolation boundaries
7. Integration seams and contracts
8. Persistence/storage fit
9. UI hosting recommendation
10. Generated adapter compatibility rules
11. G0 setup recommendation from framework side
12. Priority matrix: Must before G0 / Must before G1 / Can defer / Do not touch
13. Risks and stop conditions
14. What AIKB should update later if accepted
15. What must NOT be done yet
16. Final recommendation

## Write only here

- `TwinCore/twincore-framework-ai-integrator-readiness-v0.1/report.md`
- `TwinCore/latest-report.md`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`
- `TwinCore/_BRIDGE/STATUS.json`

Use:

REQUEST_ID: REQ-2026-06-12-twincore-framework-ai-integrator-readiness-v0-1
PROJECT: TwinCore
GATE: planning / framework-readiness-analysis
STATUS: READY_FOR_AUDIT

## Hard boundaries

- Do not modify `Twincore-framework`.
- Do not create branches.
- Do not write code.
- Do not create solution skeleton.
- Do not commit/push/PR in source repo.
- Do not write to Azure DevOps.
- Do not update AIKB.
- Do not change existing plan files.
- Do not create implementation files.
- Do not create `.claude/agents` files.
- Do not use secrets/API keys/provider credentials.
- Do not claim production readiness.
- Do not open implementation gate.
