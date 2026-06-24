# UniversalApiConnectorGenerator - Project Bridge Contract

Status: ACTIVE
Updated: 2026-06-24
Project: UniversalApiConnectorGenerator
Handoff repository: slavkan777/gpt-handoff

## Canonical two-folder bridge

```text
UniversalApiConnectorGenerator/_BRIDGE/
|- PROMPTS/
|  `- ACTIVE_REQUEST.md
|- REPORTS/
|  `- LATEST_REPORT.md
|- ACTIVE_REQUEST.md       # compatibility pointer
|- LATEST_REPORT.md        # compatibility pointer
|- STATUS.json
`- README.md
```

## Command protocol

### Slava writes `промт` to Claude

Claude must:

1. Read `UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md`.
2. Verify `REQUEST_ID`, `PROJECT`, `GATE`, source repository, branch, allowed and forbidden scope, stop conditions, and report path.
3. Execute only when `STATE: READY_FOR_CLAUDE`.
4. If `REQUEST_ID: NONE` or `STATE: IDLE`, stop without execution.
5. Never invent or expand the task beyond the active prompt.

Architect GPT is the only writer of the canonical active prompt unless Slava explicitly changes that rule.

### Slava writes `отчёт` to Architect GPT

Architect GPT must:

1. Read `UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md`.
2. Verify that `REQUEST_ID`, `PROJECT`, and `GATE` match the active prompt.
3. Reject mismatches as `STALE_REPORT`.
4. Audit evidence and return a verdict.
5. Never treat `READY_FOR_GPT_AUDIT` as final acceptance.

Claude is the writer of the canonical report. Architect GPT reads and audits it. Slava remains the final decision-maker.

## Compatibility pointers

Existing ClaudeOS rules may first open:

```text
UniversalApiConnectorGenerator/_BRIDGE/ACTIVE_REQUEST.md
UniversalApiConnectorGenerator/_BRIDGE/LATEST_REPORT.md
```

Those files point to the canonical files inside `PROMPTS/` and `REPORTS/`.
They are not a second independent prompt/report channel.

## Matching contract

Every prompt and report must contain the exact matching:

```text
REQUEST_ID
PROJECT
GATE
PROMPT_PATH
REPORT_PATH
```

A mismatch means `STALE_REPORT`.

## Current state

```text
PROJECT: UniversalApiConnectorGenerator
GATE: ARCHITECTURE_DISCOVERY
REQUEST_ID: NONE
STATE: IDLE
IMPLEMENTATION: NOT OPEN
```

No implementation is authorized until the Master Specification is approved,
the source repository is verified, and Architect GPT publishes a bounded prompt
with a new request ID.

## Forbidden

- TwinCore source or bridge routing;
- global `gpt-handoff/_BRIDGE/` as the authoritative channel;
- unrelated project folders;
- source implementation while the bridge is IDLE;
- self-acceptance;
- secrets in prompts or reports;
- commit, push, PR, deployment, paid calls, credentials, or live UPS calls
  without an explicit matching gate.
