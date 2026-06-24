REQUEST_ID: NONE
STATE: IDLE
TASK_TYPE: project-bootstrap
PROJECT: UniversalApiConnectorGenerator
GATE: ARCHITECTURE_DISCOVERY
PROMPT_PATH: UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md
REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
COMPATIBILITY_PATH: UniversalApiConnectorGenerator/_BRIDGE/ACTIVE_REQUEST.md
CREATED: 2026-06-23
UPDATED: 2026-06-24
CREATED_BY: architect-gpt

# Compatibility Prompt Pointer

The canonical Claude prompt is:

```text
UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md
```

When Slava writes `промт`, Claude must read that canonical file and execute
only when it contains `STATE: READY_FOR_CLAUDE` and a non-empty
`REQUEST_ID`.

Current state is IDLE. Do not execute.
