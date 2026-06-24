REQUEST_ID: NONE
STATE: IDLE
PROJECT: UniversalApiConnectorGenerator
GATE: ARCHITECTURE_DISCOVERY
PROMPT_PATH: UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md
REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
UPDATED: 2026-06-24
UPDATED_BY: architect-gpt

# No Active Prompt

This is the canonical prompt inbox for Claude.

When Slava writes `промт` in Claude, Claude must:

1. Read this file.
2. Verify `REQUEST_ID`, `PROJECT`, `GATE`, repository, branch, allowed scope, forbidden scope, and report path.
3. Execute only when `STATE: READY_FOR_CLAUDE`.
4. Write the matching report to:
   `UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md`.
5. Echo the exact `REQUEST_ID`, `PROJECT`, and `GATE`.
6. Stop at `READY_FOR_GPT_AUDIT`; never self-accept.

Current instruction: do not execute. Wait for Architect GPT to publish an approved bounded request.
