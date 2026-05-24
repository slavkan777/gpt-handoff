---
project: Penpot
task: penpot-access-gate
date: 2026-05-24
status: blocked-plugin-not-connected
---

# Penpot Access Gate — 2026-05-24

## One-line goal
Verify that Penpot MCP tools can (a) read the currently open file/page, (b) create a small test frame, and (c) export a rendered shape.

## Verdict
**BLOCKED.** Penpot MCP server is reachable, but no design file is connected to the plugin instance. Steps 3–4 cannot be exercised until the user starts the Penpot MCP Plugin inside a target file.

## Step-by-step

| # | Step | Result |
|---|---|---|
| 1 | Enumerate Penpot tools | PASS — 4 tools resolved (see below) |
| 2 | Load high-level overview | PASS — overview returned full API map (Penpot, Board, Rectangle, FlexLayout, Token*, LibraryContext, etc.) |
| 3 | Read open file / page summary via `execute_code` | FAIL — deterministic plugin-not-connected error |
| 4 | Create small test frame | NOT ATTEMPTED — blocked by step 3 |
| 5 | Export test frame | NOT ATTEMPTED — blocked by step 3 |
| 6 | Report to gpt-handoff | PASS — this file |

## Tools enumerated (verified callable shape)

- `mcp__penpot__high_level_overview` — zero-arg; returns API tour text
- `mcp__penpot__penpot_api_info` — args `{ type, member? }`; returns type/member docs
- `mcp__penpot__execute_code` — args `{ code }`; runs JS in plugin context with `penpot`, `penpotUtils`, `storage` globals
- `mcp__penpot__export_shape` — args `{ shapeId, format?='png', mode?='shape' }`; supports special ids `selection`, `page`

## Evidence for the failure

- Probe 1 — full inspection script (`penpot.currentFile`, `penpot.currentPage`, `penpotUtils.getPages()`, `shapeStructure(root, 2)`):
  - Response (verbatim): `Error: No plugin instance connected for user token. Please ensure the plugin is running and connected with the correct token.`
- Probe 2 — minimal probe returning only `penpot.root.id.length` / `type` / `children.length`:
  - Response (verbatim, identical): `Error: No plugin instance connected for user token. Please ensure the plugin is running and connected with the correct token.`
- Two attempts, identical error → deterministic, not transient. Failure is at the plugin link, not transport: the server responded with a structured plugin-side error rather than a connection/timeout failure.

## What unblocks this

1. Open the target design file in Penpot.
2. Launch the Penpot MCP Plugin inside that file.
3. Confirm the plugin reports a connected token.
4. Re-trigger the access gate; on success it will continue past step 3.

## What is already known good

- MCP transport: reachable.
- API surface: full overview loaded; Penpot Plugin API types and patterns understood (Boards / Rectangles / FlexLayout / `penpotUtils.shapeStructure` / `export_shape` modes).
- Handoff channel: `slavkan777/gpt-handoff` repo writable from this session.

## Handoff links

- Report (this file): `https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/Penpot/latest-report.md`
- Summary JSON: `https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/Penpot/latest-summary.json`
