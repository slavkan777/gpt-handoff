STATUS: BLOCKED_NEEDS_MANUAL_ACTION

Task slug: figma-file-move-and-dashboard-screenshot-evidence-2026-05-24
Project gate: Figma artifact organization + screenshot evidence
Handoff channel: GitHub (this repo)

# CURRENT STATE

Two-action task was requested: (a) move the existing dashboard file from personal Drafts into the user's Team project, and (b) publish dashboard screenshot evidence into `latest-screens/`.

Both actions are **blocked from autonomous execution** for two independent reasons:

1. **Figma MCP does not expose file-management tools.** The complete `mcp__figma__*` tool namespace covers file creation (`create_new_file`), in-file authoring (`use_figma`), reads (`get_design_context`, `get_screenshot`, `get_metadata`, `get_libraries`, etc.), Code Connect, and assets — but there is no tool to move, duplicate, or copy a file between Drafts and a team project. This is a tool-design constraint, not a plan-tier constraint — `use_figma`'s JavaScript runs inside the file's plugin context and cannot change the file's parent.
2. **Figma Starter monthly tool-call cap is exhausted.** Even if a move/copy tool existed, every meaningful call this month has been rejected with the cap error. The screenshot capture path (`get_screenshot`) is the same — confirmed rejected earlier today.

Result: the file remains in personal Drafts; no screenshot was captured via MCP. Manual action by Slava in the Figma UI is required for both. No upgrade was attempted. No paid project was created.

# SOURCE FIGMA FILE

- Name: `InsuranceAIPlatform — Auto Claim AI Workbench`
- URL: https://www.figma.com/design/RE4vswlgy6RUjluHQ9PJ55
- Dashboard frame: https://www.figma.com/design/RE4vswlgy6RUjluHQ9PJ55?node-id=1-3
- Current location: personal **Drafts** (owner-only by default)
- Pages: `00 — Product Concept` (empty), `01 — Screens` (1 frame: dashboard `01 — Огляд автострахових випадків`, fully designed per locked 9-block spec)

# TARGET PROJECT

- `developer online's team` → `Team project`
- Per Slava's All-Projects screenshot taken today: the Team project already contains 2 files. On Starter tier the Team project caps at 3 files total — moving the dashboard file in would fill the third slot.
- Free / Starter tier may share the Team project with other team members if any have been added. Access verification by Slava in the Figma UI is required before the move.

# ACCESS CHECK

- **Source file** (Drafts): owner-only by default; no link sharing was enabled programmatically by this MCP session. Cannot be verified autonomously (no MCP tool for permission inspection).
- **Target Team project**: cannot be verified via MCP. Slava must check manually in the Figma UI before moving:
  - Open Team project → top-right `Share` button → review the member list and "Anyone with the link" toggle.
  - If only `developer online` (Slava) is listed and link-sharing is OFF — safe to move.
  - If any unknown collaborator, Igor, AgentHub teammate, BusinessLab teammate, or "Anyone with the link" — **DO NOT move**. Use a different safe target (keep in Drafts, or create a private project) and re-instruct Claude.

Until Slava confirms access is owner-only, **the move must not proceed**.

# MOVE / COPY RESULT

- Autonomous MCP move/copy: NOT POSSIBLE (no such tool exposed; cap also blocks any retry).
- Status: AWAITING MANUAL ACTION.

Manual steps for Slava (Figma UI, 30 seconds):

1. Open `https://www.figma.com/files/recent` and click `Drafts` in the left sidebar.
2. Locate `InsuranceAIPlatform — Auto Claim AI Workbench` in the Drafts grid.
3. Right-click the file thumbnail → choose `Move to project…` (to move) or `Duplicate to project…` (to keep a Drafts copy).
4. Select target: `developer online's team` → `Team project`.
5. Confirm.

If Slava chooses to keep the file in Drafts for now (also safe), no action is needed; the file URL above remains valid.

# SCREENSHOT RESULT

- Autonomous MCP screenshot: NOT POSSIBLE this month (cap exhausted).
- Status: AWAITING MANUAL EXPORT.

Manual steps for Slava (Figma UI, 60 seconds):

1. Open the dashboard frame: https://www.figma.com/design/RE4vswlgy6RUjluHQ9PJ55?node-id=1-3
2. In the left Layers panel, click frame `01 — Огляд автострахових випадків` (or click it on canvas).
3. Right sidebar → scroll to **Export** section → click `+` → set scale `1x` (or `2x` for higher fidelity) → format PNG.
4. Click `Export "01 — Огляд автострахових випадків"`. PNG saves to Downloads.
5. Either drag the PNG into the Claude chat, or save it to the path `10-Projects/InsuranceAIPlatform/evidence/dashboard-overview-2026-05-24.png` and tell Claude "готово".

On receipt, Claude will publish it to `InsuranceAIPlatform/latest-screens/dashboard-overview-2026-05-24.png`, mirror it to the snapshot folder, and republish `latest-report.md` with status `FIGMA_FILE_MOVED_SCREENSHOT_PUBLISHED` (or `..._COPIED_...`, depending on Slava's move choice).

If Slava prefers to send an OS-level screenshot (Win+Shift+S) instead of Figma's Export — also acceptable, will be published as `latest-screens/dashboard-overview-2026-05-24-manual-screenshot.png`.

# GITHUB HANDOFF UPDATED

- `latest-report.md`: YES (this file).
- `latest-summary.json`: YES.
- `latest-screens/<screenshot>`: NO (awaiting manual export).

# BLOCKERS

1. Figma MCP exposes no file move/copy/duplicate tool — manual UI action required.
2. Figma Starter monthly tool-call cap (Up to 6/month per docs) is exhausted — read/write retries are rejected.
3. Access-safety verification of the Team project cannot be done via MCP — Slava must inspect the member list and link-sharing toggle in Figma UI before the move.
4. Free Starter Team project file slot cap (3 files; 2 already present) — moving the dashboard file fills the third slot. Acceptable but tight; future portfolio screens may need a different home.

# NO CODE / REPO / AZURE TOUCHED

YES — strictly handoff documentation only. No source repo, no Azure, no code, no DevDept / AgentHub / BusinessLab.

# NEXT SAFE STEP

Slava completes three Figma UI actions in any order:

A. **Access check (mandatory before move):** open Team project → `Share` → verify owner-only / no link-sharing. If unsafe, do not move — reply with the access details and Claude advises an alternative target.

B. **Move or Duplicate to Team project:** if access is safe — right-click file in Drafts → Move/Duplicate → Team project. Both options are acceptable; Move keeps a single canonical copy, Duplicate preserves the Drafts version too.

C. **Export PNG of dashboard frame:** select frame `01 — Огляд автострахових випадків` → right-sidebar Export → 1x/2x PNG → drag PNG into chat OR drop at `10-Projects/InsuranceAIPlatform/evidence/dashboard-overview-2026-05-24.png` and write "готово".

On receipt of (A) confirmation + (C) PNG, Claude publishes the screenshot to `latest-screens/`, mirrors to the timestamped snapshot folder, and republishes `latest-report.md` with the appropriate `FIGMA_FILE_*_SCREENSHOT_PUBLISHED` status.

# Security block

- secrets / tokens / passwords / connection strings / private customer data / private source / abs paths / account email / handle / planKey / callback URLs / OAuth data in commit: NO across the board.

# Source-repo block

- source-project repo touched: NO. source-repo commits: NO. source-repo pushes: NO.

# Drive block

- used for this handoff: NO.

# Visibility

- gpt-handoff: public. No private repo modified.
- Dashboard Figma file remains in personal Drafts (owner-only) until Slava manually moves it.

# Security scan result

PASS — regex sweep across the 4 changed files: no provider keys, no JWTs, no credentials, no PII (only Slava's first name, allowed), no callback URLs, no internal abs paths, no source code from private repos.

# Next gate

External reviewer (GPT) audits this blocked-action handoff: (a) confirms the blocker reasoning (no MCP move tool + cap exhausted) is honest and complete, (b) confirms manual steps are minimal and human-only, (c) advises whether Team-project file-slot tightness should change the move recommendation.
