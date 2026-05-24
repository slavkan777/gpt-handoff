STATUS: PENPOT_ACCESS_TEST_PARTIAL

Task slug: penpot-access-gate-2026-05-24
Project gate: External Access Gate — Penpot
Handoff channel: GitHub (this repo)
Snapshot of: latest-report.md at sixth publication (partial — restart needed before full gate)

# Summary

Operator clarified that the Penpot MCP was registered via the Claude Code CLI, not via chat. The implementer accepted the operator's override of the earlier credential-exposure refusal and proceeded with the gate.

Local verification (no token reproduced anywhere in this artifact): `claude mcp list` shows `penpot` registered in user scope and the server health-check returned `Connected`. However, the `mcp__penpot__*` tool surface is not loaded into the active Claude Code session — same restart-required behaviour observed earlier today for the Figma MCP.

The Access Gate sub-steps that require Penpot tool calls (read open file, create test frame, attempt export) were therefore NOT executed in this session.

# State

- Penpot MCP registration: PRESENT (user scope).
- Penpot server reachability: CONNECTED.
- `mcp__penpot__*` in current session: NOT LOADED.
- Probe / read / write / export: NOT EXECUTED.

# Security

- MCP key exposed: NO.
- userToken exposed: NO.
- secrets exposed: NO.
- source repo touched: NO.
- source commit: NO.
- source push: NO.

The implementer did not echo `claude mcp list` raw output anywhere outside the local tool result. The URL containing the userToken was not reproduced, not quoted, and not used as input to any subsequent tool call.

# Blocker

Single blocker: Claude Code session needs a restart so the new Penpot MCP's tool schemas register into the implementer's tool surface.

# Next step

Operator restarts Claude Code, confirms `/mcp` shows `penpot — Connected`, sends `Penpot ready`. Implementer then enumerates Penpot tools, reads the open file's page summary, creates `MCP Access Test — InsuranceAIPlatform` frame, attempts export if supported, and republishes with PENPOT_ACCESS_TEST_PASSED / PARTIAL / BLOCKED.

# Visibility

gpt-handoff: public. No private repo modified.

# Security scan result

PASS — sanitization sweep clean; no JWT-shaped substrings, no MCP URLs with query-param credentials, no account metadata.

# Next gate

REVIEWER_AUDIT_PENPOT_PARTIAL_RESTART_NEEDED
