# AgentHub UI Visual Index — Rework

VISUAL_STATUS: AVAILABLE

Single contact-sheet image, two formats. Both renders carry identical pixel content (2400 × 6065 px, 2 columns × 5 rows, labels above each tile, route under label, screenshot below).

- **JPEG** (smaller, quality 85): https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-contact-sheet.jpg
- **PNG** (lossless, larger): https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-contact-sheet.png

## Tile table

| # | Tile label | Route | Source PNG | Source PNG size | Screenshot status |
|---|---|---|---|---|---|
| 1 | 01 Login | `/login` | `01-login.png` | 1440 × 900 | AVAILABLE (re-shot to hide DEV-default password hint) |
| 2 | 02 Dashboard | `/` | `02-dashboard.png` | 1440 × 1282 | AVAILABLE |
| 3 | 03 Agent Library | `/agents` | `03-agents-list.png` | 1440 × 1096 | AVAILABLE |
| 4 | 04 Create Agent Type | `/agents/new` | `04-agent-form-create.png` | 1440 × 1145 | AVAILABLE |
| 5 | 05 Agent Details | `/agents/2` | `05-agent-details.png` | 1440 × 900 | AVAILABLE |
| 6 | 06 Agent Edit | `/agents/2/edit` | `06-agent-edit.png` | 1440 × 901 | AVAILABLE |
| 7 | 07 Run Form | `/agents/2/runs/new` | `07-run-form.png` | 1440 × 1009 | AVAILABLE |
| 8 | 08 Run Details | `/agents/2/runs/5` | `08-run-details.png` | 1440 × 1396 | AVAILABLE |
| 9 | 09 Run Results | `/agents/2/runs/5/results` | `09-run-results.png` | 1440 × 900 | AVAILABLE |
| 10 | 10 Audit Log | `/audit-log` | `10-audit-log.png` | 1440 × 1826 | AVAILABLE (height-capped at 1300 px scaled to keep total sheet height manageable; aspect ratio preserved by also reducing width) |

`AVAILABLE` = tile is present in the contact sheet and visually clean.
`NOT_AVAILABLE` = source screenshot is missing; a placeholder marked `SCREENSHOT_NOT_AVAILABLE` is shown in the tile slot.
`UNSAFE_EXCLUDED` = the source screenshot contained a credential or PII and was intentionally NOT composited.

No tile in this delivery is `NOT_AVAILABLE` or `UNSAFE_EXCLUDED`.

## Layout decisions

- **Grid:** strict 2 columns × 5 rows. No tile spans more than one cell. No tile is full-width.
- **Per-row height:** each row's tile height equals the height of the tallest scaled screenshot in that row plus a fixed header (label + route) and bottom padding. Short pages (rows 1, 3, 5-left) get short rows; tall full-page captures (rows 2, 4, 5-right) get tall rows.
- **No aspect-ratio distortion:** every source PNG is scaled by a single factor (image-area-width ÷ 1440 = 0.7292); the only exception is tile 10 (Audit Log) whose 1826 px scaled height was capped at 1300 px — width was reduced proportionally so the aspect ratio is still preserved.
- **Tile card:** white background, light border, light gray strip behind the image. Header zone is taller than the original sheet to make 30 px bold labels easy to read at any normal browser zoom.
- **No overlap:** label and route are centred horizontally within the tile and live in the header zone above the image; the image is centred horizontally within the image-area strip. Label text never crosses the image boundary, even on the right column.

## Security check on the contact sheet

- **Tile 1 (Login):** the DEV-default password hint was hidden in the DOM before screenshot capture; verified visually post-capture and again here — the password literal does NOT appear anywhere in the sheet.
- **All other tiles:** re-inspected before composition; no passwords / tokens / JWT / cookies / API keys / auth headers / connection strings / private client data / source code / sensitive logs / production or staging URLs are visible.
- **Identifiable strings remaining (all already-public):** `admin@devdept.local` placeholder email (the same hardcoded default visible in `Login.tsx:9`); three open-source domains (github.com, stackoverflow.com, atlassian.com) from a test CSV; date and timestamp values from the local dev DB.

**Composition status:** PASS.

## How to consume

GPT should open either raw URL above. JPEG is smaller (~909 KB) and faster to fetch; PNG is lossless (~1.39 MB) when colour fidelity matters. The tile / route table above makes every tile addressable by `(row, col, route)` for review comments. For an HTML wrapper that embeds the JPG with relative path, see `latest-preview.html`.
