REQUEST_ID: NONE
STATE: IDLE
TASK_TYPE: none
PROJECT: UniversalApiConnectorGenerator
GATE: SOURCE_REPOSITORY_SETUP_PENDING
PROMPT_PATH: UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md
REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
UPDATED: 2026-06-24
UPDATED_BY: architect-gpt
ARCHITECTURE_BASELINE: MASTER_SPEC_V0_2_APPROVED
IMPLEMENTATION: NOT_OPEN
SOURCE_REPO: PENDING_CREATION_OR_VERIFICATION
SOURCE_REPO_FULL_NAME: slavkan777/universal-api-connector-generator
LOCAL_WORKSPACE: C:\Projects\UniversalApiConnectorGenerator
PREVIOUS_REQUEST_ID: REQ-2026-06-24-uacg-bridge-communication-bootstrap
PREVIOUS_GATE: BRIDGE_COMMUNICATION_BOOTSTRAP
PREVIOUS_VERDICT: ACCEPTED_FOR_BRIDGE_BOOTSTRAP

# No Active Claude Request

There is no active Claude task for `UniversalApiConnectorGenerator`.

Master Specification v0.2 is approved as the architecture baseline, but implementation is not open.

If Slava writes `промт`, Claude must read this file and stop without execution because:

```text
REQUEST_ID: NONE
STATE: IDLE
```

# Current Project Gate

```text
GATE: SOURCE_REPOSITORY_SETUP_PENDING
ARCHITECTURE_BASELINE: MASTER_SPEC_V0_2_APPROVED
IMPLEMENTATION: NOT_OPEN
SOURCE_REPO: PENDING_CREATION_OR_VERIFICATION
LOCAL_WORKSPACE: C:\Projects\UniversalApiConnectorGenerator
```

# Stop Conditions

Do not perform any task while this file remains IDLE.

Forbidden while IDLE:

- no source repository creation or source edits by Claude;
- no implementation or architecture changes;
- no AIKB writes by Claude;
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

Expected next project-level step before implementation is source repository creation/verification. Implementation still requires explicit owner wording:

```text
APPROVED FOR IMPLEMENTATION
```

Until then, Claude should answer only:

```text
BLOCKED / BRIDGE_IDLE
```
