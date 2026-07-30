REQUEST_ID: REQ-2026-06-12-twincore-logistics-connector-factory-planning-v0-1
STATUS: BACKFILLED_AFTER_REPORT
TASK_TYPE: project-planning
PROJECT: TwinCore
GATE: planning / product architecture
ACTIVE_REQUEST_PATH: TwinCore/logistics-connector-factory-planning-v0.1/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: TwinCore/logistics-connector-factory-planning-v0.1/report.md
LATEST_REPORT_PATH: TwinCore/latest-report.md
BRIDGE_LATEST_REPORT_PATH: TwinCore/_BRIDGE/LATEST_REPORT.md
AIKB_UPDATE_REQUIRED: yes, completed by GPT after Slava approval

# TwinCore Logistics Connector Factory — Planning Request v0.1

## Purpose

Backfilled canonical ACTIVE_REQUEST for the planning report already produced by Claude.

This request identity must be used for the Logistics Connector Factory / AI Integrator planning gate.

## Current state

TwinCore is an existing AIKB project and Azure DevOps source context. The old audit workstream remains valid but is superseded for current planning by Work Item 12695 / AI Integrator / Logistics Connector Factory.

## Updated owner-provided Work Item 12695 context

Problem:

Need to connect many logistics APIs that return shipping rates/prices, for example:

- FedEx API;
- UPS API;
- USPS API;
- DHL API;
- Canada Post;
- Purolator;
- others.

Some carriers have Swagger/OpenAPI and can be generated deterministically. Not all have Swagger. Some have only docs. Some may expose SOAP/WSDL.

Generated client must be production-ready enough to include:

- retry/resilience;
- configuration from `appsettings.json`;
- auth configuration;
- no hardcoded secrets;
- request/response mapping;
- error handling;
- base tests.

## Target tool

A simple module/tool where the user provides:

- Swagger/OpenAPI;
- documentation;
- later SOAP/WSDL.

The tool generates:

- C# API client;
- provider/adapter class;
- request mapper;
- response mapper;
- basic tests;
- authorization/config skeleton;
- `appsettings.json` config section;
- retry/resilience skeleton.

## Runtime pattern

Internal Rate API receives:

- origin address;
- destination address;
- weight;
- package parameters.

Then it calls connected carrier adapters:

- `IRateProvider`;
- `FedExRateProvider`;
- `UpsRateProvider`;
- `UspsRateProvider`;
- `DhlRateProvider`;
- others.

Each adapter handles:

- authorization;
- mapping our request to carrier request;
- calling carrier API;
- error handling;
- retry/resilience;
- mapping carrier response to universal format.

## LLM minimization rule

Use LLM only for semantic gaps.

Deterministic:

- OpenAPI parsing;
- WSDL parsing where feasible;
- DTO/client generation;
- adapter template generation;
- test skeleton generation;
- config skeleton generation;
- mapping execution from approved profile;
- runtime execution.

LLM:

- documentation without Swagger/WSDL;
- endpoint identification;
- auth-flow understanding;
- semantic field mapping suggestions;
- enum/error explanation;
- review questions.

Runtime must have zero LLM calls.

## Required report

Produce `TwinCore Logistics Connector Factory — MVP Planning Dossier v0.1` with:

1. current state restored from AIKB;
2. new workstream interpretation;
3. product goal;
4. problem statement;
5. MVP scope;
6. explicit non-scope;
7. first user scenario;
8. canonical logistics model;
9. provider input formats;
10. Mapping Workbench / Mapping Review;
11. generated output;
12. runtime flow;
13. LLM vs deterministic responsibilities;
14. TwinCore fit;
15. architecture options and recommendation;
16. risks;
17. acceptance criteria;
18. questions for Igor;
19. recommended next gate;
20. not touched;
21. next safe step.

## Boundaries

Allowed:

- read AIKB and gpt-handoff;
- write sanitized report to project-specific `gpt-handoff/TwinCore/` paths.

Forbidden:

- source repo changes;
- Azure DevOps writes;
- code implementation;
- branch creation;
- commit/push/PR in source repo;
- cleanup/delete/archive;
- secrets/API keys;
- AIKB writes unless Slava explicitly approves later.

## Result

Claude already produced the report at:

- `TwinCore/logistics-connector-factory-planning-v0.1/report.md`
- `TwinCore/latest-report.md`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`

GPT audit verdict:

```text
ACCEPT_WITH_LIMITATIONS
```
