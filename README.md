# GPT Handoff

This repository is a **public, sanitized handoff buffer** between Claude (running locally in Claude Code) and GPT (running in chat). It is intentionally minimal: short reports, machine-readable status JSON, and at most one labelled contact-sheet image per project — everything GPT needs to start a review in a single fetch.

## What this repo is NOT

- It is **not** the source repository of any project listed here.
- It is **not** a place for source code dumps, configuration files, secrets, customer data, raw logs, or proprietary architecture details.
- It is **not** a long-term artifact archive. Older runs are removed by the retention rule below.

## Hard rules (zero exceptions)

No file in this repository may contain:

- API keys / tokens / OAuth secrets (any provider)
- JWT (`eyJ...`-style), refresh tokens, signed URLs that embed credentials
- Cookies, `Authorization:` headers, `Cookie:` headers
- Passwords (plaintext, hashed, or "example" placeholders that look real)
- Database connection strings (host + user + pass combos)
- Customer PII (real emails, phone numbers, full names)
- Internal hostnames, IPs, or absolute machine paths that leak machine layout
- Source code from any private repository

If anything sensitive sneaks in: **delete the file, force-push to scrub the public history, rotate the leaked credential.** Cropping or editing the next commit alone is not enough — public git history is forever.

## Layout

```
gpt-handoff/
├── README.md                          ← you are here
├── .gitignore
└── <ProjectName>/
    ├── latest-report.md               ← overwritten each delivery — GPT reads this first
    ├── latest-summary.json            ← machine-readable status
    ├── latest-visual-index.md         ← contact-sheet tile / route mapping
    ├── latest-contact-sheet.jpg       ← optional single-image visual handoff
    └── runs/
        └── <YYYY-MM-DD>-<task-slug>/  ← timestamped snapshot of all four files
            ├── report.md
            ├── summary.json
            ├── visual-index.md
            └── contact-sheet.jpg      (optional)
```

## Retention

For each `<ProjectName>/`:

- `latest-*` files are overwritten on every delivery.
- `runs/` keeps the **newest 5** timestamped folders; older ones are removed.
- `README.md` (this file) and sibling project folders are never deleted.

## How GPT reads this

1. GPT opens `<ProjectName>/latest-report.md` (raw URL listed inside the report).
2. Report links the machine-readable JSON summary and the contact-sheet JPEG via raw GitHub URLs.
3. The contact sheet is a single image with up to 10 tiles, each labelled with its route — one fetch covers the whole visual board, no need to walk individual screenshots.

## Active projects

| Project | Status | Latest task |
|---|---|---|
| `AgentHub` | active | `github-handoff-setup` (2026-05-21) |

## Trigger phrases

In chat with Claude, the user can say simply **`отчёт`** to instruct Claude to fetch and consume `<ProjectName>/latest-report.md` from this repository. When no project is named, the most recent active project is assumed (currently `AgentHub`).
