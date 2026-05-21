# AgentHub UI Visual Index — Stress Test

VISUAL_STATUS: AVAILABLE

Single contact-sheet image, two formats:

- **JPEG** (smaller, quality 80): https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-contact-sheet.jpg
- **PNG** (lossless, larger): https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-contact-sheet.png

Both renders are byte-equivalent in pixel content (2000 × 3652 px, 2 columns × 5 rows, labels above each tile).

## Tile table

| # | Tile label | Route | Source PNG | Screenshot status |
|---|---|---|---|---|
| 1 | 01 Login | `/login` | `01-login.png` | AVAILABLE (re-shot to hide dev-default password hint) |
| 2 | 02 Dashboard | `/` | `02-dashboard.png` | AVAILABLE |
| 3 | 03 Agent Library | `/agents` | `03-agents-list.png` | AVAILABLE |
| 4 | 04 Create Agent Type | `/agents/new` | `04-agent-form-create.png` | AVAILABLE |
| 5 | 05 Agent Details | `/agents/2` | `05-agent-details.png` | AVAILABLE |
| 6 | 06 Agent Edit | `/agents/2/edit` | `06-agent-edit.png` | AVAILABLE |
| 7 | 07 Run Form | `/agents/2/runs/new` | `07-run-form.png` | AVAILABLE |
| 8 | 08 Run Details | `/agents/2/runs/5` | `08-run-details.png` | AVAILABLE |
| 9 | 09 Run Results | `/agents/2/runs/5/results` | `09-run-results.png` | AVAILABLE |
| 10 | 10 Audit Log | `/audit-log` | `10-audit-log.png` | AVAILABLE |

`AVAILABLE` = tile is present in the contact sheet and visually clean.
`NOT_AVAILABLE` = source screenshot is missing; a placeholder marked `SCREENSHOT_NOT_AVAILABLE` is shown in that tile slot.
`UNSAFE_EXCLUDED` = the source screenshot was found to contain a credential or PII and was intentionally NOT composited.

No tile in this delivery is `NOT_AVAILABLE` or `UNSAFE_EXCLUDED`.

## Known visual issues

- All source screenshots were captured with `fullPage: true`, so pages with native scroll content (Dashboard at row 1, Agent Library at row 2, Audit Log at row 5) have height much greater than 900 px in the source. They are scaled down to fit a 960 × 600 tile area, which compresses them vertically relative to natively-900-px-tall pages (Login at row 1, the form pages). The compression is uniform per tile so UI elements remain identifiable; it is not a regression.
- Run Form (tile 7) and Run Details (tile 8) are at the natural viewport height and look closer to their real rendered proportions.

## Security check on the contact sheet

- **Tile 1 (Login):** dev-default password hint was hidden in DOM before screenshot; verified visually post-capture — the password literal is not visible.
- **All other tiles:** re-inspected before composition; no passwords / tokens / JWT / cookies / API keys present anywhere.
- **Identifiable strings remaining (all already-public):** `admin@devdept.local` placeholder email (same hardcoded default visible in `Login.tsx:9`); three open-source domains (github.com, stackoverflow.com, atlassian.com) from a test CSV; date / timestamp values from the local dev DB.

**Composition status:** PASS.

## How to consume

GPT should open either of the raw URLs above (JPG is smaller and faster; PNG is lossless when colour fidelity matters). The tile / route mapping table makes every tile addressable by `(row, col, route)` for review comments. For an HTML wrapper that embeds the JPG with relative path, see `latest-preview.html`.
