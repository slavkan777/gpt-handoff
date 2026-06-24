REQUEST_ID: NONE
STATE: IDLE
TASK_TYPE: none
PROJECT: UniversalApiConnectorGenerator
GATE: ARCHITECTURE_DISCOVERY
PROMPT_PATH: UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md
REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
UPDATED: 2026-06-24
UPDATED_BY: architect-gpt
PREVIOUS_REQUEST_ID: REQ-2026-06-24-uacg-bridge-communication-bootstrap
PREVIOUS_GATE: BRIDGE_COMMUNICATION_BOOTSTRAP
PREVIOUS_VERDICT: ACCEPTED_FOR_BRIDGE_BOOTSTRAP

# No Active Claude Request

The Bridge communication bootstrap request is closed.

There is no active Claude task for `UniversalApiConnectorGenerator`.

If Slava writes `промт`, Claude must read this file and stop without execution because:

```text
REQUEST_ID: NONE
STATE: IDLE
```

# Current Project Gate

The project returns to:

```text
GATE: ARCHITECTURE_DISCOVERY
IMPLEMENTATION: NOT OPEN
SOURCE_REPO: PENDING_CREATION
```

# Stop Conditions

Do not perform any task while this file remains IDLE.

Forbidden while IDLE:

- no source repository creation or source edits;
- no implementation or architecture changes;
- no AIKB writes;
- no TwinCore reads or writes;
- no claude-vault, CLAUDE.md, agents, hooks, settings, or plugin changes;
- no credentials, paid calls, UPS calls, deployment, PR, merge, or cleanup;
- no report pasted into chat.

# Next Authorized Action

Wait until Architect GPT publishes a new bounded request with:

```text
REQUEST_ID: <non-empty new id>
STATE: READY_FOR_CLAUDE
PROJECT: UniversalApiConnectorGenerator
```

Until then, Claude should answer only:

```text
BLOCKED / BRIDGE_IDLE
```
