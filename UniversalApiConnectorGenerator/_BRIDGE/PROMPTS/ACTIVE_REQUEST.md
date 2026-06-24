REQUEST_ID: REQ-2026-06-24-uacg-bridge-communication-bootstrap
STATE: READY_FOR_CLAUDE
TASK_TYPE: workflow-bootstrap
PROJECT: UniversalApiConnectorGenerator
GATE: BRIDGE_COMMUNICATION_BOOTSTRAP
PROMPT_PATH: UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md
REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
CREATED: 2026-06-24
CREATED_BY: architect-gpt

# ROUTING

ROUTE USED: DIRECT_MACRO
WHY: bounded read-only protocol bootstrap plus one sanitized Bridge report
ALLOWED: read the project Bridge; write and publish only the declared report
FORBIDDEN: product source, AIKB, TwinCore, claude-vault, implementation, secrets
FIRST SAFE ACTION: read the Bridge README and verify all paths

# READ FIRST

1. `UniversalApiConnectorGenerator/_BRIDGE/README.md`
2. This active request.
3. `UniversalApiConnectorGenerator/_BRIDGE/STATUS.json`

# GOAL

Adopt and verify the permanent Bridge-only communication protocol for the
UniversalApiConnectorGenerator project.

This request changes no product architecture and performs no implementation.

# STANDING COMMUNICATION CONTRACT

For this project, now and in every future Claude session:

1. When Slava writes `промт`, read:
   `UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md`.
2. Treat that Git file as the only authoritative task specification from
   Architect GPT.
3. Do not execute implementation instructions supplied only in Claude chat.
4. Execute only when the file has `STATE: READY_FOR_CLAUDE` and a non-empty
   matching `REQUEST_ID`.
5. After execution, write the full sanitized report only to:
   `UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md`.
6. The report must echo the exact `REQUEST_ID`, `PROJECT`, `GATE`,
   `PROMPT_PATH`, and `REPORT_PATH`.
7. In Claude chat, return only:
   `Bridge report ready. Tell GPT: отчёт.`
8. If Bridge read/write is unavailable, stop with:
   `BLOCKED / BRIDGE_UNAVAILABLE`.
9. Never fall back to copying a long prompt or full report through chat.
10. Never self-accept. Architect GPT audits; Slava decides.
11. At every new/reset session, re-read the Bridge README and active prompt;
    do not rely on old Claude chat memory.

# DONE STATE

- Bridge README and paths were read and verified.
- Claude explicitly acknowledges the standing communication contract.
- No product/source/AIKB/TwinCore/claude-vault files were changed.
- A sanitized acknowledgement report is written and published to the declared
  report path.
- Claude chat contains only the one-line ready notification.

# READ-ONLY SCOPE

- `gpt-handoff/UniversalApiConnectorGenerator/_BRIDGE/README.md`
- `gpt-handoff/UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md`
- `gpt-handoff/UniversalApiConnectorGenerator/_BRIDGE/STATUS.json`

# WRITABLE SCOPE

Only:

`gpt-handoff/UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md`

A commit/push to `slavkan777/gpt-handoff` is allowed only to publish this
single report file. Do not modify any other path.

# FORBIDDEN

- no source repository creation or source edits;
- no implementation or architecture changes;
- no AIKB writes;
- no TwinCore reads or writes;
- no claude-vault, CLAUDE.md, agents, hooks, settings, or plugin changes;
- no credentials, paid calls, UPS calls, deployment, PR, merge, or cleanup;
- no report pasted into chat.

# REPORT FORMAT

```text
REQUEST_ID: REQ-2026-06-24-uacg-bridge-communication-bootstrap
STATUS: READY_FOR_GPT_AUDIT | BLOCKED
PROJECT: UniversalApiConnectorGenerator
GATE: BRIDGE_COMMUNICATION_BOOTSTRAP
PROMPT_PATH: UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md
REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md

## Bridge Verification
## Standing Contract Acknowledgement
## Files Read
## File Written
## Git Publication Evidence
## Boundaries Honored
## Blockers
## Next Safe Step
```

Do not perform any other task.
