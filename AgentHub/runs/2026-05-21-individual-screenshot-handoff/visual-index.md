# AgentHub UI Visual Index — Individual Screenshot Handoff

VISUAL_STATUS: AVAILABLE
VISUAL_PRIMARY: individual-raw-screenshots
CONTACT_SHEET_PRIMARY: false

The primary visual source of truth is now 10 individual raw PNG screenshots, one per screen, published under `AgentHub/latest-screens/`. Each row in the table below has a raw GitHub URL that opens the original screenshot directly. No contact-sheet generation step sits between the source and GPT — if a single screen has any issue, GPT can flag it without affecting the others.

## Screen table

| # | Screen | Route | Raw URL | Status |
|---|---|---|---|---|
| 01 | Login | `/login` | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-screens/01-login.png | AVAILABLE |
| 02 | Dashboard | `/` | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-screens/02-dashboard.png | AVAILABLE |
| 03 | Agent Library | `/agents` | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-screens/03-agent-library.png | AVAILABLE |
| 04 | Create Agent Type | `/agents/new` | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-screens/04-create-agent-type.png | AVAILABLE |
| 05 | Agent Details | `/agents/2` | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-screens/05-agent-details.png | AVAILABLE |
| 06 | Agent Edit | `/agents/2/edit` | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-screens/06-agent-edit.png | AVAILABLE |
| 07 | Run Form | `/agents/2/runs/new` | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-screens/07-run-form.png | AVAILABLE |
| 08 | Run Details | `/agents/2/runs/5` | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-screens/08-run-details.png | AVAILABLE |
| 09 | Run Results | `/agents/2/runs/5/results` | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-screens/09-run-results.png | AVAILABLE |
| 10 | Audit Log | `/audit-log` | https://raw.githubusercontent.com/slavkan777/gpt-handoff/main/AgentHub/latest-screens/10-audit-log.png | AVAILABLE |

## Source dimensions

| # | File | Dimensions | Bytes |
|---|---|---|---|
| 01 | 01-login.png | 1440 × 900 | 10,837 |
| 02 | 02-dashboard.png | 1440 × 1282 | 62,705 |
| 03 | 03-agent-library.png | 1440 × 1096 | 53,284 |
| 04 | 04-create-agent-type.png | 1440 × 1145 | 41,008 |
| 05 | 05-agent-details.png | 1440 × 900 | 28,494 |
| 06 | 06-agent-edit.png | 1440 × 901 | 25,914 |
| 07 | 07-run-form.png | 1440 × 1009 | 30,380 |
| 08 | 08-run-details.png | 1440 × 1396 | 42,253 |
| 09 | 09-run-results.png | 1440 × 900 | 34,068 |
| 10 | 10-audit-log.png | 1440 × 1826 | 69,016 |

`AVAILABLE` = the original Playwright capture is published as a raw PNG and reachable at the URL above.
`NOT_AVAILABLE` = the screen could not be captured; the file is absent from `latest-screens/` and no raw URL exists.
`UNSAFE_EXCLUDED` = the original capture contained a credential or PII and was intentionally not published.

No screen in this delivery is `NOT_AVAILABLE` or `UNSAFE_EXCLUDED`.

## DEPRECATED — earlier contact sheets

The following files remain in the repo at `AgentHub/` but are **no longer the primary visual artifact**:

- `latest-contact-sheet.jpg` — DEPRECATED, NOT PRIMARY
- `latest-contact-sheet.png` — DEPRECATED, NOT PRIMARY
- `latest-preview.html` — references the deprecated contact sheet; kept for history, not primary
- `latest-manifest.txt` — covers both the new `latest-screens/` content AND the deprecated contact sheet entries (deprecated rows are explicitly flagged)
- `latest-package.zip` — bundles only the new individual-screenshot artifacts; the deprecated contact-sheet files are intentionally not re-bundled

GPT should not use the deprecated contact-sheet files for visual review. Open the 10 raw screenshot URLs above directly.

## Security check (visual + textual)

Every PNG in `latest-screens/` was re-inspected before publication. The Login capture (`01-login.png`) is the re-shot version with the DEV-default password hint hidden in DOM prior to screenshot — the password literal is NOT rendered. All other captures contain no secrets, no API keys, no JWT, no cookies, no auth headers, no connection strings, no production / staging URLs, no source code, and no private / client data.

Already-public strings still visible — none of these are secrets:

- `admin@devdept.local` — placeholder email, hardcoded default in `Login.tsx:9`
- three open-source domains in test data: github.com, stackoverflow.com, atlassian.com
- local dev DB timestamps

**Composition status:** PASS.

## How to consume

GPT should fetch each PNG raw URL directly. Recommended workflow:

1. Open `latest-report.md` for context.
2. Open `latest-summary.json` to confirm machine-readable status.
3. Open every raw URL in the screen table above for individual visual review.
4. Comment on any screen by `(row #, screen name, route)`.

No layout / composition step sits between the source and GPT — if a particular screenshot needs to be re-shot, it can be replaced in isolation without affecting any other.
