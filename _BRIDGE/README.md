# _BRIDGE — Global Communication Channel (Architect GPT ⇄ Claude)

A temporary, project-agnostic relay that removes manual copy-paste between
**Architect GPT** (chat — audit / control layer) and **Claude** (Claude Code —
local implementer). Any question, not tied to a single project, flows through here.

This folder is **communication**, not memory:

- Durable knowledge → `slavkan777/ai-kb`
- Per-project deliverables → `gpt-handoff/<Project>/`
- Day-to-day relay → **this folder**

## Files

| File | Written by | Purpose |
|---|---|---|
| `ACTIVE_REQUEST.md` | Architect GPT | The current task for Claude — routing header + body. |
| `LATEST_REPORT.md` | Claude | Result of the active request (general tasks). |
| `STATUS.json` | both | Machine-readable bridge state. |
| `ARCHIVE/` | both | Timestamped snapshots of past request / report pairs. |

## Human commands (Slava types only these)

| Command | In Architect GPT chat | In Claude chat |
|---|---|---|
| `промт` | write / update `ACTIVE_REQUEST.md` | read `ACTIVE_REQUEST.md` & execute → write the report |
| `архитектор` | — | same as `промт` to Claude (optional disambiguator) |
| `отчёт` | read `LATEST_REPORT.md`, audit, verdict | (if needed) write / push `LATEST_REPORT.md` |

Disambiguation by recipient: `промт` → GPT · `архитектор` → Claude · `отчёт` → GPT-after-Claude.

**Operating mode (confirmed 2026-06-03):** Slava is not a relay — he fires only triggers; Claude and
Architect GPT exchange directly through these files. In practice Slava types **`промт`** to Claude
(read `ACTIVE_REQUEST.md` → execute → write report) and **`отчёт`** to Architect GPT (audit the
report). Architect GPT is the audit / control layer; Claude returns evidence and does not self-accept.

## Flow

1. Slava describes a task to Architect GPT.
2. Slava: `промт` → GPT writes `_BRIDGE/ACTIVE_REQUEST.md`.
3. Slava → Claude: `архитектор` → Claude reads `ACTIVE_REQUEST.md`, executes, writes the report to `TARGET_REPORT_PATH`.
4. Slava → GPT: `отчёт` → GPT reads the report and returns: verdict · risks · accepted/rejected · next safe step · whether AIKB needs updating.

## `ACTIVE_REQUEST.md` routing header (required)

- `REQUEST_ID` — e.g. `REQ-2026-06-03-001`
- `TASK_TYPE` — general | project | research | code | document | audit | other
- `PROJECT` — `<ProjectName>` or `NONE`
- `TARGET_REPORT_PATH` — `_BRIDGE/LATEST_REPORT.md` (general) or `<Project>/latest-report.md` (project)
- `AIKB_UPDATE_REQUIRED` — yes | no | after-approval

## When a task becomes a real, durable project

Create / use:

- **AIKB (durable memory):** `slavkan777/ai-kb/01_PROJECTS/<Project>/` →
  `PROJECT_PROFILE.md`, `CURRENT_STATE.md`, `TASK_LEDGER.md`, `DECISIONS/`, `FEATURE_PLANS/`, `CONTEXT_PACK/`
- **Reports:** `gpt-handoff/<Project>/latest-report.md` + `runs/`

Day-to-day communication still **starts** in `_BRIDGE/`.

## Roles & safety

- **Claude** — implementer; returns evidence; does **not** self-accept final decisions.
- **Architect GPT** — audit / control layer; gives the verdict.
- **Slava** — final decision-maker.
- No secrets and no raw private data in bridge files (same hard rules as the repo `README.md`).
- AIKB is durable memory — do **not** update it for every temporary question.
