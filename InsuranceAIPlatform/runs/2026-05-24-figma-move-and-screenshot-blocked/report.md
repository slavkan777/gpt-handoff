STATUS: BLOCKED_NEEDS_MANUAL_ACTION

Task slug: figma-file-move-and-dashboard-screenshot-evidence-2026-05-24
Project gate: Figma artifact organization + screenshot evidence
Snapshot of: latest-report.md at fourth publication (blocked on manual action)

# Summary

Two-action task — (a) move dashboard file from Drafts to Team project, (b) publish dashboard screenshot — could not be executed via MCP.

Reason 1 (capability): Figma MCP does not expose any file-management tool. The full `mcp__figma__*` namespace is in-file authoring + reads + Code Connect + assets; no move/copy/duplicate.

Reason 2 (cap): Figma Starter monthly tool-call cap (Up to 6/month per docs) is exhausted for this billing period.

Result: file remains in personal Drafts; no screenshot was captured via MCP. Manual Figma UI action by Slava required for all three sub-steps (access check, move/duplicate, PNG export).

# Figma file (unchanged)

- File: https://www.figma.com/design/RE4vswlgy6RUjluHQ9PJ55
- Dashboard frame: https://www.figma.com/design/RE4vswlgy6RUjluHQ9PJ55?node-id=1-3
- Location: personal Drafts (owner-only by default)
- Pages: `00 — Product Concept` (empty), `01 — Screens` (1 frame)

# Manual actions Slava must perform

A. **Access check on Team project (Share dialog):** verify owner-only, "Anyone with the link" OFF.
B. **Move or Duplicate** the file from Drafts to `developer online's team → Team project` (right-click in Drafts → Move/Duplicate).
C. **PNG export of dashboard frame** via the right-sidebar Export panel; drag the PNG back to Claude.

# Notes

- Team project file-slot cap on Starter = 3; already has 2 → adding dashboard fills the third slot. Future portfolio screens may need a Drafts home or a different organization.
- No upgrade performed. No paid project created. No code/repo/Azure/source project touched. No secrets/private metadata published.

# Security block

secrets / tokens / passwords / connection strings / private data / private source / abs paths / account email / handle / planKey / callback URLs / OAuth data in commit: NO across the board.

# Visibility

gpt-handoff: public. No private repo modified.

# Security scan result

PASS — sanitization sweep clean.

# Next gate

REVIEWER_AUDIT_BLOCKED_NEEDS_MANUAL_ACTION
