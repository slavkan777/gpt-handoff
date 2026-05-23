STATUS: FIGMA_DESIGN_BOOTSTRAP_BLOCKED_ON_MCP_INSTALL

Task slug: figma-design-bootstrap-2026-05-24
Project gate: Product concept / UX direction (Figma design only)
Handoff channel: GitHub (this repo)
Snapshot of: latest-report.md at first publication

# CURRENT STATE

Project skeleton initialized in the local vault. **No Figma file, page, or frame was created on canvas** — the design step is blocked on installing a personal Figma MCP server before any write-to-canvas can happen. A full pre-canvas specification (9 frame blocks, design tokens, Ukrainian copy, synthetic demo rows) is written into the local handoff document so the work resumes deterministically after install + Claude Code restart.

# FIGMA MCP STATUS

- Required: a personal Figma MCP exposing `use_figma` / `create_new_file` (write to canvas).
- Available in this session: only the Anthropic team-managed Figma MCP — out of scope for this task per local operating rule.
- Personal Figma MCP: NOT yet installed in Claude Code.
- Install command requested: `claude plugin install figma@claude-plugins-official` → Claude Code restart → `/plugin` → `figma` → Allow access.

# FIGMA FILE / LINK

Not yet created.

# PAGES CREATED

None.

# FRAME CREATED

None.

# DESIGN SUMMARY

Target product: **Auto Insurance Claim AI Workbench** — a focused operations product for processing motor-vehicle accident claims (ДТП). Central business object: Auto Insurance Claim (автостраховий випадок). Portfolio framing: AI Infrastructure / AI Engineering interview-defensible work. Visual register: Microsoft 365 / Fluent UI / Azure Portal / Dynamics — light enterprise.

# MAIN UI BLOCKS PLANNED

Sidebar; top command bar; 5-card KPI row; claim lifecycle strip (7 stages); work queue table (9 cols × 4 synthetic rows); right AI analysis panel; automation guardrails card; agent timeline preview (7 steps); audit & cost mini panel.

# CHECK AGAINST ACCEPTANCE

All canvas-resident checks: PENDING_CANVAS_BUILD. Synthetic/demo data only: YES.

# BLOCKERS

1. Personal Figma MCP not installed.
2. Team-managed Figma MCP out of scope per local operating rule.
3. Post-install Figma plan check needed (write-to-canvas requires Full seat or draft fallback per Figma May 2026 docs).

# NEXT SAFE STEP

Install personal Figma MCP → restart Claude Code → authenticate → resume with the trigger phrase to build the frame against the locked spec; publish updated `latest-report.md` with link + screenshot post-build.

# Security block

secrets / tokens / passwords / connection strings / private data / private source / absolute machine paths in commit: NO across the board.

# Source-repo block

source-project repo touched: NO. source-repo commits: NO. source-repo pushes: NO.

# Drive block

used for this handoff: NO.

# Visibility

gpt-handoff visibility: public.

# Security scan result

PASS — sanitization sweep clean; synthetic demo personas (Роберт Джонсон, Марія Коваль, Іван Петренко, Олена Шевченко) declared as fictional design data.

# Next gate

REVIEWER_AUDIT_BLOCKED_BOOTSTRAP_STATUS
