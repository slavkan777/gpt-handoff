# UniversalApiConnectorGenerator - Project Bridge Contract

Status: ACTIVE
Updated: 2026-06-24
Project: UniversalApiConnectorGenerator
Handoff repository: slavkan777/gpt-handoff

## Canonical bridge folder

Repository-relative path:

```text
UniversalApiConnectorGenerator/_BRIDGE/
```

Inside a local checkout of `slavkan777/gpt-handoff`:

```text
<LOCAL_GPT_HANDOFF>/UniversalApiConnectorGenerator/_BRIDGE/
```

This is the only current prompt/report channel for the project. Do not create a second bridge inside the source repository or TwinCore.

## Where Claude reads prompts

Current project request:

```text
UniversalApiConnectorGenerator/_BRIDGE/ACTIVE_REQUEST.md
```

Claude must read this file first and verify:

- `REQUEST_ID`;
- `PROJECT`;
- `GATE`;
- source repository and branch;
- allowed and forbidden paths;
- target report paths;
- stop conditions.

If `REQUEST_ID: NONE` or `STATE: IDLE`, Claude must not execute.

For an opened implementation or audit gate, Architect GPT may create:

```text
UniversalApiConnectorGenerator/<gate-slug>/ACTIVE_REQUEST.md
```

The gate-specific request is canonical for that gate. The project `_BRIDGE/ACTIVE_REQUEST.md` remains its current pointer/mirror.

## Where Claude writes reports

Current project report pointer:

```text
UniversalApiConnectorGenerator/_BRIDGE/LATEST_REPORT.md
```

For an opened gate, Claude must write the canonical evidence report to:

```text
UniversalApiConnectorGenerator/<gate-slug>/report.md
```

Claude then updates these project mirrors when the request permits it:

```text
UniversalApiConnectorGenerator/_BRIDGE/LATEST_REPORT.md
UniversalApiConnectorGenerator/latest-report.md
UniversalApiConnectorGenerator/latest-summary.json
```

Every report must echo the exact matching:

```text
REQUEST_ID
PROJECT
GATE
ACTIVE_REQUEST_PATH
TARGET_REPORT_PATH
```

A mismatch means `STALE_REPORT` and the report is not accepted.

## Status file

Indicator only:

```text
UniversalApiConnectorGenerator/_BRIDGE/STATUS.json
```

`STATUS.json` never authorizes execution by itself.

## Communication protocol

```text
Slava decision
-> Architect GPT writes ACTIVE_REQUEST.md
-> Claude reads and executes only that request
-> Claude writes report.md and LATEST_REPORT.md
-> Architect GPT audits the matching report
-> Slava accepts, rejects, or requests rework
```

Long prompts, implementation instructions, diffs, evidence, and reports travel only through this project bridge. Chat is limited to short control commands, decisions, status, and links.

## Current gate

```text
ARCHITECTURE_DISCOVERY
REQUEST_ID: NONE
STATE: IDLE
IMPLEMENTATION: NOT OPEN
```

No source implementation is authorized until the Master Specification is approved and Slava explicitly opens implementation with:

```text
APPROVED FOR IMPLEMENTATION
```

## Forbidden routing

- global `gpt-handoff/_BRIDGE/` as the authoritative project channel;
- TwinCore or `Twincore-framework` bridge/source paths;
- unrelated project folders;
- AIKB writes by Claude;
- source implementation while the bridge is IDLE;
- commit, push, PR, deployment, paid calls, credentials, or live UPS calls without an explicit matching gate;
- secrets in prompts, reports, status files, logs, screenshots, or Git.
