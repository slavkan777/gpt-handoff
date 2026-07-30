# ACTIVE REQUEST — TwinCore AI Integrator technical design v0.1

REQUEST_ID: REQ-2026-06-12-twincore-ai-integrator-tech-design-v0-1
PROJECT: TwinCore
GATE: planning / technical-design
TASK_TYPE: technical-design-only / report-only
STATUS: ACTIVE_REQUEST

## Task

Prepare a technical design dossier for the next planning step of the TwinCore AI Integrator / Integration Mapping Factory.

This is NOT implementation.

Use the accepted plan and the Claude review report as inputs:

- `01_PROJECTS/TwinCore/FEATURE_PLANS/TWINCORE_AI_INTEGRATOR_IMPLEMENTATION_PLAN_V0_1.md`
- `TwinCore/ai-integrator-implementation-plan-review-v0.1/report.md`

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
- `01_PROJECTS/TwinCore/TASK_HISTORY/2026-06-12-logistics-connector-factory-planning.md`

## Read second — gpt-handoff

- `TwinCore/_BRIDGE/STATUS.json`
- `TwinCore/ai-integrator-implementation-plan-review-v0.1/report.md`
- `TwinCore/logistics-connector-factory-planning-v0.1/report.md`
- `TwinCore/latest-report.md`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`

Optional if framework risk context is needed:

- `TwinCore/main-review-v0.2/report.md`

## Product frame

Broad product:

`TwinCore AI Integrator / Integration Mapping Factory`

First MVP slice:

`Logistics Rate API Connector Factory`

MVP scope:

`Rate Quoting / Transit Time only`

Core principle:

The hard part is not Swagger → C# client. The hard part is external provider fields/docs/specs → internal entities/tables/business model through a reviewed `MappingProfile`.

## Mandatory technical design agenda

Design these areas clearly:

1. Mapping cardinality / array iteration semantics
   - iteration root;
   - one provider response → N normalized quote options;
   - nested arrays and charge selection;
   - array selection strategies.

2. TransformKind set
   - direct copy;
   - rename;
   - type convert;
   - unit convert;
   - enum/code translation;
   - date/format parse;
   - array select;
   - constant/default;
   - custom/manual placeholder.

3. MappingProfile lifecycle
   - draft profile;
   - approved frozen version;
   - edits create new draft version;
   - generation pins to approved profile version;
   - audit/reviewer metadata.

4. ProviderSchema identity and re-import readiness
   - stable field identity;
   - source hash;
   - path-based hashes;
   - future diff/review loop.

5. Storage fixes
   - ProviderEndpoints / ProviderExamples decision;
   - RawContentRef storage and retention;
   - repository abstraction for in-memory first slice.

6. Generator safety
   - identifier normalization;
   - reserved word handling;
   - literal escaping;
   - hostile-spec negative tests;
   - generated headers;
   - content hash / drift detection.

7. Generated-package build/test sandbox
   - temp project layout;
   - SDK invocation;
   - timeouts;
   - output capture;
   - isolation;
   - sanitized logs.

8. Fixture strategy
   - realistic FedEx-shaped mock OpenAPI;
   - nested arrays;
   - multi-option golden response;
   - messy/incomplete fixture for later LLM assistant.

9. UI/UX refinements
   - Provider Schema search/filter;
   - Workbench filters;
   - integration status stepper;
   - import error/partial failure behavior.

10. LLM governance
   - suggestion cache per ProviderSchema version;
   - prompt content minimization;
   - model/prompt template hash;
   - no runtime LLM;
   - human review required.

11. Gate grouping
   - G0 setup gate;
   - G1 import+browse;
   - G2 mapping+validation;
   - G3 generation+tests+quote demo;
   - G4 second provider+LLM+SOAP slot.

12. Acceptance evidence
   - command / artifact / log evidence per milestone;
   - deterministic hashes;
   - multi-option assertions;
   - repeatability proof with no factory-core changes.

## Required report structure

# TwinCore AI Integrator — Technical Design Dossier v0.1

1. Executive summary
2. Current state and boundaries
3. Design decisions
4. Mapping semantics design
5. TransformKind design
6. MappingProfile lifecycle and versioning
7. ProviderSchema identity / re-import readiness
8. Storage model refinements
9. Generator safety design
10. Build/test sandbox design
11. Fixture strategy
12. UI/UX technical implications
13. LLM governance design
14. Gate grouping G0-G4
15. Acceptance evidence model
16. Updated risks and mitigations
17. What AIKB should update later if Slava accepts
18. What must NOT be done yet
19. Final recommendation

## Write only here

- `TwinCore/ai-integrator-tech-design-v0.1/report.md`
- `TwinCore/latest-report.md`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`
- `TwinCore/_BRIDGE/STATUS.json`

Use:

REQUEST_ID: REQ-2026-06-12-twincore-ai-integrator-tech-design-v0-1
PROJECT: TwinCore
GATE: planning / technical-design
STATUS: READY_FOR_AUDIT

## Hard boundaries

- Do not modify `Twincore-framework`.
- Do not create branches.
- Do not write source code.
- Do not create solution skeleton.
- Do not commit/push/PR in source repo.
- Do not write to Azure DevOps.
- Do not update AIKB.
- Do not change the implementation plan file.
- Do not create implementation files.
- Do not create `.claude/agents` files.
- Do not use secrets/API keys/provider credentials.
- Do not claim production readiness.
- Do not open implementation gate.
