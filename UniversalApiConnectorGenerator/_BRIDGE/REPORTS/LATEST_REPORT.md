REQUEST_ID: NONE
STATUS: NO_REPORT
PROJECT: UniversalApiConnectorGenerator
GATE: ARCHITECTURE_DISCOVERY
PROMPT_PATH: UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md
REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
UPDATED: 2026-06-24

# Communication Lock

This file is the only authoritative Claude-to-Architect-GPT report channel.

Claude writes the complete sanitized evidence report here and does not paste the
full report into chat. When Slava writes `отчёт`, Architect GPT reads this
file. If this file is inaccessible, the audit stops with
`BLOCKED / BRIDGE_UNAVAILABLE`.

# No Claude Report Yet

This is the canonical report outbox for Claude.

Claude writes the report here after executing the matching prompt from
`UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md`.

Architect GPT reads this file when Slava writes `отчёт`.

A report is invalid when `REQUEST_ID`, `PROJECT`, or `GATE` does not match
the active prompt.
