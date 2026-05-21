# AgentHub UI Visual Index

VISUAL_STATUS: AVAILABLE

The single image `latest-contact-sheet.jpg` in this folder contains 10 screenshots arranged 2 columns × 5 rows. Each tile is labelled with the route it represents.

## Tile / route mapping

| # | Tile label | Route |
|---|---|---|
| 1 | 01 Login | `/login` |
| 2 | 02 Dashboard | `/` |
| 3 | 03 Agent Library | `/agents` |
| 4 | 04 Create Agent Type | `/agents/new` |
| 5 | 05 Agent Details | `/agents/2` |
| 6 | 06 Agent Edit | `/agents/2/edit` |
| 7 | 07 Run Form | `/agents/2/runs/new` |
| 8 | 08 Run Details | `/agents/2/runs/5` |
| 9 | 09 Run Results | `/agents/2/runs/5/results` |
| 10 | 10 Audit Log | `/audit-log` |

## Raw image URL

https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-contact-sheet.jpg

## Capture context

- Date: 2026-05-21
- Viewport: 1440 × 900, Chromium headless
- Existing dev data backing the screenshots: 10 agent types, 25 historical runs
- Agent #2 (an MVP demo agent type) had Run #5 used as the Run Details / Run Results tiles
- Run #5 status: Completed, 3 rows processed, 0 matched (consistent with the stub-fallback signature when no real LLM provider key is configured)

## Security check on the contact sheet

- Login tile: dev-default password hint was hidden via DOM manipulation before screenshot; the captured PNG does not show the literal hint.
- All other tiles: re-inspected visually; no passwords / tokens / JWT / cookies / API keys present.
- The only PII-shaped strings in the image are an `admin@devdept.local` placeholder email (already public in the application's source default) and three public domain names from a test CSV (github.com, stackoverflow.com, atlassian.com).
- Composition status: **PASS**.

## How to consume

GPT should open the raw image URL above. The tile / route mapping table makes each tile addressable by `(row, col, route)` for review comments.
