REQUEST_ID: REQ-2026-06-14-twincore-rate-api-carrier-docs-research-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: research-spike
PROJECT: TwinCore
GATE: research / specification support
TARGET_REPORT_PATH: TwinCore/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: TwinCore/rate-api-carrier-docs-research-v0.1/report.md
COMPATIBILITY_REPORT_PATH: TwinCore/latest-report.md
AIKB_UPDATE_REQUIRED: after-gpt-audit-and-slava-approval
CREATED: 2026-06-14T00:00:00+00:00
CREATED_BY: GPT Architect

# ACTIVE REQUEST ROUTER

Read the canonical full prompt from:

```text
TwinCore/rate-api-carrier-docs-research-v0.1/ACTIVE_REQUEST.md
```

# TASK SUMMARY

Research public developer documentation for approximately 40 logistics carriers and shipping API aggregators.

Classify each provider by available material:

- OpenAPI / Swagger;
- REST documentation;
- SOAP / WSDL / XSD;
- SDKs, examples, or collections;
- developer portal/manual check required;
- rate/pricing/quote endpoint support.

Then recommend:

1. best first MVP candidates;
2. best OpenAPI path candidates;
3. best SOAP/WSDL path candidates;
4. best docs-only or LLM-assisted candidates;
5. what the first small TwinCore Rate API Connector Generator should include;
6. what it should not include;
7. how to minimize LLM usage.

# BOUNDARIES

Research and report only.
Do not change source code.
Do not change AIKB.
Do not create branches or PRs.
Do not perform live provider integrations.

# REPORT TARGETS

Write report to:

```text
TwinCore/rate-api-carrier-docs-research-v0.1/report.md
TwinCore/_BRIDGE/LATEST_REPORT.md
TwinCore/latest-report.md
```

After report: STOP. GPT will audit.
