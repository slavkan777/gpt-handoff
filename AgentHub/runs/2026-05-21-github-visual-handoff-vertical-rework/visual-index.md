# AgentHub UI Visual Index — Vertical Rework

VISUAL_STATUS: AVAILABLE

Single contact-sheet image, two formats. Both renders carry identical pixel content (1600 × 13769 px, 1 column × 10 sections stacked vertically, label + route above each image, image below).

- **JPEG** (quality 85): https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-contact-sheet.jpg
- **PNG** (lossless): https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-contact-sheet.png

## Section table (top → bottom)

| # | Section title | Route | Source PNG | Source size | Display size | Status |
|---|---|---|---|---|---|---|
| 1 | 01 Login | `/login` | `01-login.png` | 1440 × 900 | 1440 × 900 | AVAILABLE (re-shot to hide DEV-default password hint) |
| 2 | 02 Dashboard | `/` | `02-dashboard.png` | 1440 × 1282 | 1440 × 1282 | AVAILABLE |
| 3 | 03 Agent Library | `/agents` | `03-agents-list.png` | 1440 × 1096 | 1440 × 1096 | AVAILABLE |
| 4 | 04 Create Agent Type | `/agents/new` | `04-agent-form-create.png` | 1440 × 1145 | 1440 × 1145 | AVAILABLE |
| 5 | 05 Agent Details | `/agents/2` | `05-agent-details.png` | 1440 × 900 | 1440 × 900 | AVAILABLE |
| 6 | 06 Agent Edit | `/agents/2/edit` | `06-agent-edit.png` | 1440 × 901 | 1440 × 901 | AVAILABLE |
| 7 | 07 Run Form | `/agents/2/runs/new` | `07-run-form.png` | 1440 × 1009 | 1440 × 1009 | AVAILABLE |
| 8 | 08 Run Details | `/agents/2/runs/5` | `08-run-details.png` | 1440 × 1396 | 1440 × 1396 | AVAILABLE |
| 9 | 09 Run Results | `/agents/2/runs/5/results` | `09-run-results.png` | 1440 × 900 | 1440 × 900 | AVAILABLE |
| 10 | 10 Audit Log | `/audit-log` | `10-audit-log.png` | 1440 × 1826 | 1183 × 1500 | AVAILABLE (proportional height cap; aspect ratio preserved) |

`AVAILABLE` = section is present in the contact sheet and visually clean.
`NOT_AVAILABLE` = source screenshot is missing; a visible `SCREENSHOT_NOT_AVAILABLE` placeholder section is shown in that slot.
`UNSAFE_EXCLUDED` = the source screenshot contained a credential or PII and was intentionally NOT composited.

No section in this delivery is `NOT_AVAILABLE` or `UNSAFE_EXCLUDED`.

## Layout decisions

- **One column, ten sections.** Strict vertical stack — the prior 2-column layout was abandoned because the right column showed ambiguity (apparent blanks, overlapping labels) when GPT visually parsed the JPG.
- **Native source resolution.** For 9 of 10 sections, the screenshot is composited at its native 1440-px source width. There is no fractional scaling that could introduce aliasing or blur.
- **One exception, aspect-preserving.** Audit Log's native size is 1440 × 1826 (a tall fullPage scroll capture). To keep the total canvas height manageable while preserving the aspect ratio, it was height-capped at 1500 px and the width was reduced proportionally to 1183 px. Centred horizontally within the 1600-wide section, this leaves white margin on both sides — no horizontal stretching, no vertical squashing.
- **Header zone.** Each section dedicates a fixed `30 + 60 + 16 + 38 + 30 = 174 px` band above the image to the label and route. The image starts only after that band, so no label or route text touches any screenshot.
- **Card style.** Each section is rendered as a white card spanning the full canvas width, with a 2 px light-gray border and a light-gray strip immediately behind the image. The 60 px vertical gap between cards makes each section visually distinct against the page background.

## Security check on the contact sheet

- **Section 1 (Login):** the DEV-default password hint was hidden in DOM before screenshot capture. Verified visually post-capture and again here — the password literal does NOT appear anywhere in the sheet.
- **All other sections:** re-inspected before composition. No passwords / API keys / JWT / cookies / auth headers / connection strings / private client data / source code / sensitive logs / production or staging URLs are visible.
- **Identifiable strings remaining (all already-public):** `admin@devdept.local` placeholder email (the hardcoded default visible in `Login.tsx:9`); three open-source domains (github.com, stackoverflow.com, atlassian.com) from a test CSV; date / timestamp values from the local dev DB.

**Composition status:** PASS.

## How to consume

GPT should open either raw URL above. JPEG (≈ 1.37 MB) is fast to fetch; PNG (≈ 0.90 MB lossless) is actually smaller for this layout because the large white card margins compress to near-zero in PNG. The section table makes every section addressable by `(row, route)` for review comments. For an HTML wrapper that embeds the JPG with relative path, see `latest-preview.html`.
