STATUS: PENPOT_EXPORT_ORDER_CLEANED_MANUAL_PDF_REQUIRED

Task slug: penpot-export-cleanup-before-interactions
Project gate: Penpot Export Cleanup — boards 01–11 sorted, MCP Access Test isolated
Handoff channel: GitHub (this repo)

# BOARD ORDER

Both axes (layer-order and canvas-position) now agree on `01 → 11`.

## Layer order (page root `children[]`)

```
[ test (off-canvas), 01, 02, 03, 04, 05, 06, 07, 08, 09, 10, 11 ]
```

This was reached by calling `sendToBack()` on each portfolio board in order 01→11 (3 small batches), then `bringToFront()` on the test frame. The portfolio block now sits contiguously at the end of the children list, in natural reading order.

## Canvas position (frame `x`, `y`)

Three-column / four-row grid, top-to-bottom left-to-right matches portfolio order:

```
Row 1 (y=320):    01 (x=40)   02 (x=1520)   03 (x=3000)
Row 2 (y=1260):   04 (x=40)   05 (x=1520)   06 (x=3000)
Row 3 (y=2200):   07 (x=40)   08 (x=1520)   09 (x=3000)
Row 4 (y=3140):   10 (x=40)   11 (x=1520)
Off-canvas:       MCP Access Test at (-2000, -2000)
```

Whether Penpot's PDF export uses children-array order, canvas-position order, or top-down/left-right scan, all three converge on `01, 02, 03, 04, 05, 06, 07, 08, 09, 10, 11`. The test frame sorts either alongside its `(-2000, -2000)` position (well separated) or at the page-children boundary, and is trivially deselected at the export picker.

## Note on the previously observed reversed PDF order

The earlier PDF came out `11 → 01`. The cause was unaudited layer order: the boards 02–11 were created in batches AFTER 01 and stacked at the front, while Penpot's PDF appears to honor reverse-children order. This run normalized that.

If the new PDF is **still** reversed (i.e. `11, 10, ..., 01`), Penpot is exporting reverse-children unconditionally. Trigger phrase to apply the one-call fix: `Penpot reverse children order` — the implementer will swap to `[test, 11, 10, ..., 01]` so the reverse-export yields `01, 02, ..., 11`. Two minutes of work; no content rebuild.

# MCP TEST FRAME

- Name: `[DO NOT EXPORT] MCP Access Test — InsuranceAIPlatform`
- Position: `(-2000, -2000)` — well outside any portfolio board area (which sits at `x ∈ [40, 4440]`, `y ∈ [320, 4040]`)
- The red `DO NOT EXPORT · MCP ACCESS TEST · NOT FOR PORTFOLIO` banner from the previous gate is still inside the frame
- Not deleted (per operator instruction — keep unless trivial / safe)

# CONTENT PRESERVED (all 11 boards verified post-reorder)

```
01 → 379    07 → 136
02 → 274    08 → 196
03 → 209    09 → 161
04 → 132    10 → 156
05 → 154    11 → 153
06 → 137
```

No content was touched in this run — only the layer-order (`children[]` index) of each board on the page root, and the test frame's `bringToFront()` to push it to the end of the page-children list. All shape contents, sizes, positions inside their parent frames remain identical.

# GITHUB HANDOFF

- `InsuranceAIPlatform/latest-report.md` — YES (this file).
- `InsuranceAIPlatform/latest-summary.json` — YES.
- `latest-screens/` — UNCHANGED.

# SECURITY

- MCP URL exposed: NO.
- MCP key exposed: NO.
- userToken exposed: NO.
- secrets exposed: NO.
- source repo touched: NO (Twincore-framework / Azure / AgentHub / BusinessLab / DevDept all untouched).
- source commit: NO.
- source push: NO.
- No code, no React, no backend, no deployment.
- Penpot shape/file IDs omitted.

# NEXT SAFE STEP

1. In Penpot: File → Export → PDF.
2. At the export picker: **deselect** `[DO NOT EXPORT] MCP Access Test — InsuranceAIPlatform`.
3. Keep boards `01 — Огляд автострахових випадків` through `11 — Демо-сценарій` selected, in order.
4. Export and review.

Expected PDF order: `01 → 02 → 03 → 04 → 05 → 06 → 07 → 08 → 09 → 10 → 11`. No test frame in the output.

If the order is still reversed: send `Penpot reverse children order` — single-call layer-order swap, no content rebuild.

If the order is correct: authorize the next gate, `PENPOT_INTERACTIONS_GATE_AFTER_CONTENT_ACCEPTANCE`, for the clickable navigation pass.

# Source-repo block

- Any source-project repo touched: NO. Source-repo commits: NO. Source-repo pushes: NO.

# Drive block

- Used for this handoff: NO.

# Visibility

- gpt-handoff: public. No private repo modified.

# Security scan result

PASS — regex sweep across the 2 changed files: no JWT-shaped substrings, no MCP URLs carrying credentials, no email/handle, no callback URLs, no provider API keys, no source code from private repos.

# Next gate

`PENPOT_INTERACTIONS_GATE_AFTER_CONTENT_ACCEPTANCE` — once the new PDF audit confirms ordering and content, the clickable navigation can proceed.
