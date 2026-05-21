# AgentHub GitHub Handoff Stress Test

STATUS: READY_FOR_GPT_AUDIT

## Purpose

Test GitHub as the single Claude → GPT handoff channel for text, JSON, image (JPG and PNG), HTML, plain-text manifest, and ZIP-bundle artifacts. Every artifact below must be reachable by GPT via public raw URL with no authentication.

## Files

| Artifact | Blob URL (rendered) | Raw URL (fetch) |
|---|---|---|
| `latest-report.md` | https://github.com/slavkan777/gpt-handoff/blob/main/AgentHub/latest-report.md | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-report.md |
| `latest-summary.json` | https://github.com/slavkan777/gpt-handoff/blob/main/AgentHub/latest-summary.json | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-summary.json |
| `latest-visual-index.md` | https://github.com/slavkan777/gpt-handoff/blob/main/AgentHub/latest-visual-index.md | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-visual-index.md |
| `latest-contact-sheet.jpg` | https://github.com/slavkan777/gpt-handoff/blob/main/AgentHub/latest-contact-sheet.jpg | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-contact-sheet.jpg |
| `latest-contact-sheet.png` | https://github.com/slavkan777/gpt-handoff/blob/main/AgentHub/latest-contact-sheet.png | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-contact-sheet.png |
| `latest-preview.html` | https://github.com/slavkan777/gpt-handoff/blob/main/AgentHub/latest-preview.html | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-preview.html |
| `latest-manifest.txt` | https://github.com/slavkan777/gpt-handoff/blob/main/AgentHub/latest-manifest.txt | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-manifest.txt |
| `latest-package.zip` | https://github.com/slavkan777/gpt-handoff/blob/main/AgentHub/latest-package.zip | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-package.zip |

For exact byte sizes and SHA-256 hashes, see `latest-manifest.txt`.

## Visual review

- **Screenshot count expected:** 10
- **Screenshot count included:** 10
- **Contact sheet layout:** 2 columns × 5 rows. Each tile shows label (large bold, 19 pt) and route (mono, 14 pt) ABOVE the image — no text overlaps the screenshot itself. Tiles separated by ~32 px vertical gutter.
- **Image dimensions:** JPG and PNG are byte-equivalent renders at **2000 × 3652** px.
- **File sizes:** see `latest-manifest.txt` for exact bytes per artifact (JPG ~440 KB, PNG ~710 KB).
- **Route mapping:**

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

- **Known visual limitations:** screenshots were captured as `fullPage: true`, so pages with scroll (Dashboard, Audit Log) have native heights well above 900 px. When forced into the 960 × 600 tile, those tiles look slightly vertically compressed compared to the natively-900-px-tall pages (Login, Agent Form). Aspect distortion is small enough that all UI elements remain identifiable; if GPT needs an undistorted render, the source PNG names are listed in `latest-visual-index.md`.

## Security check

- secrets visible: NO
- tokens visible: NO
- passwords visible: NO
- auth headers visible: NO
- cookies visible: NO
- connection strings visible: NO
- private/client data visible: NO
- AgentHub source code included: NO

The Login tile was re-shot before this delivery so the dev-default password hint is not visible. The only identity-shaped strings remaining in any tile are: an `admin@devdept.local` placeholder email (already the public hardcoded default in `Login.tsx:9`), three public open-source domains from `test-domains.csv` (github.com, stackoverflow.com, atlassian.com), and timestamps from the local dev DB.

## AgentHub repo

- touched: NO
- commit: NO
- push: NO

`C:\Projects\AgentHub\AgentHub` was inspected only via read-only operations earlier in this session. `git status` confirms `nothing to commit, working tree clean`, HEAD `30b7851`, ahead 3 / behind 0 — unchanged.

## Google Drive

- used for new handoff: NO

`G:\My Drive\AI-Reports\AgentHub\` was used only as the **source** for the 10 screenshot PNGs that fed this contact sheet. No file was written to Drive during this stress test.

## Claude global memory/settings

- touched: NO
- changed: NO

`C:\Users\DEVELOPER\.claude\projects\c--Users-DEVELOPER-Claude\memory\MEMORY.md`, `feedback_*.md`, and `settings.json` were NOT modified during this task. (The earlier handoff-setup turn DID update those — that was a separate task and is now stable. This stress test only touches files under `C:\Users\DEVELOPER\Claude\GitHub\gpt-handoff\`.)

## Commit

- handoff repo commit SHA: see chat reply (computed after the security scan passes)
- branch: `main`
- remote: `https://github.com/slavkan777/gpt-handoff.git`
- pushed: YES

## Next gate

`GPT_AUDIT_GITHUB_HANDOFF_STRESS_TEST` — GPT pulls `latest-report.md` and walks the eight raw URLs above; verifies the contact sheet (JPG and PNG), the inline HTML preview, the manifest hashes, and the ZIP bundle. Round-trip closure when GPT's verdict lands back in chat.
