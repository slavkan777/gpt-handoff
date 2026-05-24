STATUS: PENPOT_ACCESS_TEST_BLOCKED

Task slug: penpot-access-gate-2026-05-24
Project gate: External Access Gate — Penpot
Handoff channel: GitHub (this repo)
Snapshot of: latest-report.md at fifth publication (Penpot gate blocked on credential exposure)

# Summary

Penpot Access Gate was offered for verification. The implementer refused to execute it because the Penpot MCP `userToken` was delivered through a chat surface that compromises it (prompt body → local session log). No `mcp__penpot__*` tool was invoked. No write test, no read of the open file, no screenshot, no export. The implementer's standing policy: any credential that reaches chat must be rotated before use.

This snapshot exists solely to document the gate state. No production design work was performed in Penpot. The Figma workstream remains independently blocked on manual UI action (see `runs/2026-05-24-figma-move-and-screenshot-blocked/`).

# Penpot MCP status

- Registration via the offered credential: NOT performed.
- Tool calls: NONE.
- Read state of Penpot file: unknown (not inspected).

# Test results

- Write test (`MCP Access Test — InsuranceAIPlatform` frame): NOT EXECUTED.
- Export / screenshot: NOT EXECUTED.

# Security

- userToken / MCP URL with token / cookies / OAuth / passwords / API keys / source code from private repos / internal abs paths / account email / handle / plan key in commit: NO across the board.
- Source-repo touched / commit / push: NO / NO / NO.

# Blocker

Credential (userToken) was pasted into the prompt body in plaintext. By the time the implementer received it, it had already been written to the local Claude Code session log on the operator's machine. The standing policy treats such a credential as compromised; the implementer refused to use it.

# Next safe step

Operator rotates the Penpot token, re-registers the MCP via CLI (not via chat paste), restarts Claude Code, signals `Penpot ready`. The implementer then runs the full Access Gate and republishes with PASSED / PARTIAL / BLOCKED.

# Visibility

gpt-handoff: public. No private repo modified.

# Security scan result

PASS — sanitization sweep clean; no token fragments, no MCP URL with credentials, no account-identifying metadata, no abs paths.

# Next gate

REVIEWER_AUDIT_PENPOT_ACCESS_BLOCKED_CREDENTIAL_EXPOSURE
