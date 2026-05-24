STATUS: PENPOT_SCREEN_01_DASHBOARD_BUILT_MANUAL_SCREENSHOT_REQUIRED

Task slug: penpot-screen-01-dashboard-build
Project gate: Penpot Design Build Gate — Screen 01 (Dashboard)
Handoff channel: GitHub (this repo)

# SCREEN BUILT

**Board name:** `01 — Огляд автострахових випадків`
**Penpot file:** `InsuranceAIPlatform — Auto Claim AI Workbench`
**Board position on canvas:** x=40, y=320 (placed below the existing MCP Access Test frame; not overlapping it)
**Board size:** 1440 × 900 (desktop)
**Total shapes inside the board:** 379

# PRODUCT DIRECTION REPRESENTED

The screen visually proves the product is an **AI-assisted workbench for auto insurance accident claims** — not a generic insurance suite, not a CRM, not a chatbot, not a sales portal.

The central business object on screen is the **Автостраховий випадок (auto-insurance claim)**, and the central scenario (ДТП → фото пошкоджень → заява клієнта → поліцейський звіт → рахунок СТО → перевірка поліса → AI-аналіз → ризики → людське погодження → audit/cost trace) is visible through the claim lifecycle strip, the claim queue table, the AI-analysis side panel for the selected case, and the audit & cost panel at the bottom.

# REQUIRED BLOCKS COVERED

All 9 required blocks are represented on the board:

| # | Block | Status |
|---|---|---|
| 1 | Left sidebar (brand, 11-item navigation, "Стан системи" status card with 5 green items) | DONE |
| 2 | Top command bar (search, "Середовище: Azure Demo" pill, "Система готова" pill, "Запустити демо" CTA, bell with badge, help, avatar) | DONE |
| 3 | KPI row (5 cards: Нові ДТП 8, Очікують документів 12, AI-оброблено сьогодні 48, Високий ризик 7, Середній час розгляду 18 хв — each with delta) | DONE |
| 4 | Claim lifecycle strip ("Життєвий цикл автострахового випадку") with 6 stages and per-stage counters (8 → 12 → 14 → 7 → 5 → 21) and connector arrows | DONE |
| 5 | Main claims table ("Черга автострахових випадків") with 9 columns × 5 rows, filter chips ("Усі" active), "+ Створити випадок" button. First row CLM-1006 highlighted as selected | DONE |
| 6 | Right AI analysis panel for CLM-1006: header with 78% confidence pill, case meta (Тип, Авто, Поліс, Ризик, Документи, SLA), model-confidence bar, "Ключові знахідки" (4 items), "Докази" (5 chips), amber "Рекомендована дія" callout, action buttons "Відкрити огляд" + "Запитати дані" | DONE |
| 7 | Automation Guardrails card ("Обмеження автоматизації · AI не приймає остаточних рішень") — red-accented card showing Авто-погодження=НІ + Людська перевірка=ОБОВ'ЯЗКОВА + 4 reason rows | DONE |
| 8 | Agent timeline ("Хід AI-перевірки") — vertical 7-step stepper with state badges (Завершено / Попередження / Високий ризик / Готова / Очікує) | DONE |
| 9 | Audit & cost mini panel ("Аудит і витрати AI-запуску") — 6 fields (Модель: Azure OpenAI Demo, Токени: 4 261, Вартість: $0.0187, Час: 18.9 с, Trace ID, Run ID) | DONE |

# DESIGN SUMMARY

Layout follows enterprise dashboard convention: ~260 px left sidebar, 56 px full-width top command bar, ~820 px main content column (page header → KPIs → lifecycle → table → bottom strip with timeline + audit), ~360 px right AI panel for the selected case + guardrails. Consistent 16/24 px spacing rhythm. Clear typographic hierarchy across page-H1, section headers, table body, badges, and key-value labels.

# VISUAL STYLE

Modern Microsoft enterprise feel: light theme, Fluent-flavored chips and buttons, clean white surfaces on a light grey background (`#F5F7FA`), restrained use of color as semantic signal. Color meaning is applied consistently:

- **Blue** (`#2563EB`, soft `#EFF6FF`) — platform / workflow / readiness (env pill, selected nav, primary CTA, selected row accent).
- **Purple** (`#7C3AED`, soft `#F5F3FF`) — AI analysis / evidence / agents (confidence pill, evidence chips, model section).
- **Green** (`#16A34A`, soft `#ECFDF5`) — completed / approved / healthy (system status, ready pill, "Низький ризик", completed timeline steps, "AI-перевірено", improvement deltas).
- **Amber** (`#F59E0B`, soft `#FFFBEB`) — needs review / low confidence / missing info (recommendation callout, "Середній ризик", "Потрібна перевірка", warning timeline step).
- **Red** (`#DC2626`, soft `#FEF2F2`) — high risk / guardrail (high-risk badge, bell unread, Guardrails card border + accents).

# UKRAINIAN UI LABELS

All visible UI strings are Ukrainian. Technical identifiers (`CLM-1006`, `trc_8f3d2a7e`, `run_8f3d2a7e`, `Azure OpenAI Demo`, `+18%`, `BMW`, `Toyota`, etc.) intentionally remain in their canonical form per the brief.

# AI WORKFLOW / EVIDENCE / RISK / HUMAN REVIEW / AUDIT GOVERNANCE — visibility

- **AI workflow** — visible in the lifecycle strip (AI-аналіз stage), the AI-статус column in the table, the right-panel agent timeline (7 named AI-pipeline steps), and the audit panel (tokens / time / Trace ID / Run ID).
- **Evidence** — visible as 5 evidence chips in the right panel (поліцейський звіт, фото пошкоджень, рахунок СТО, умови полісу, лист клієнта).
- **Risk** — visible as the dedicated table column (color-coded pills), the right-panel "Ризик: Високий" meta, the lifecycle "Перевірка ризиків" stage with 7 cases, and the KPI "Високий ризик: 7".
- **Human review** — visible in the table action column ("Погодити", "Запросити фото", ...), explicit timeline step "Людська перевірка — Очікує", and most prominently in the Guardrails card stating "AI не приймає остаточних рішень · Людська перевірка ОБОВ'ЯЗКОВА".
- **Audit & cost governance** — visible in the bottom audit panel with model name, token count, dollar cost, processing time, Trace ID, Run ID.

# WHAT WAS NOT DONE

- **PNG screenshot not auto-published.** Export preview was generated for visual confirmation only; binary publish path remains the documented manual fallback per the prior PENPOT_ACCESS_TEST_PARTIAL_EXPORT_PREVIEW_OK acceptance.
- **No other screens built.** Only `01 — Огляд автострахових випадків`. The other 10 screens are out of scope for this gate.
- **No code, no Azure deployment, no source repo touched.** This is design only.
- **No new Penpot file created.** The existing `MCP Access Test — InsuranceAIPlatform` frame is also intact (not deleted).

# GITHUB HANDOFF UPDATED

- `InsuranceAIPlatform/latest-report.md` — YES (this file).
- `InsuranceAIPlatform/latest-summary.json` — YES.
- `InsuranceAIPlatform/latest-screens/` — UNCHANGED (no new PNG published in this gate).

# SECURITY

- MCP URL exposed: NO.
- MCP key exposed: NO.
- userToken exposed: NO.
- secrets exposed: NO.
- source repo touched: NO (no access to Azure / AgentHub / BusinessLab / DevDept / Twincore-framework).
- source commit: NO.
- source push: NO.

The shape data inside Penpot contains only the simulated business strings shown on screen (mock claim numbers, mock customer names, mock vehicle models, mock IDs). No real customer data, no API keys, no tokens, no internal absolute paths, no account-identifying metadata.

# BLOCKERS

None. The board is ready for visual review.

# NEXT SAFE STEP

Operator action (≤ 30 seconds):

1. Open Penpot in the browser, navigate to the file `InsuranceAIPlatform — Auto Claim AI Workbench`.
2. Select the board `01 — Огляд автострахових випадків` (sits below the existing `MCP Access Test` frame on the same canvas).
3. Use Penpot's UI export (PNG, 1×) and save to `C:\Users\DEVELOPER\Claude\GitHub\gpt-handoff\InsuranceAIPlatform\latest-screens\penpot-screen-01-dashboard-2026-05-24.png`.
4. Send the manual screenshot to GPT for visual audit. Verdict: **ACCEPT / REWORK / BLOCKED**.

On ACCEPT — Claude will move to Screen 02 build.
On REWORK — Claude will revise per the specific feedback (no full rebuild expected; targeted patches).
On BLOCKED — Claude will pause and gather more requirements before continuing.

# Source-repo block

- Any source-project repo touched: NO. Source-repo commits: NO. Source-repo pushes: NO.

# Drive block

- Used for this handoff: NO.

# Visibility

- gpt-handoff: public. No private repo modified.

# Security scan result

PASS — regex sweep across the 2 changed files: no JWT-shaped substrings, no MCP URLs carrying credentials in query parameters, no email/handle/planKey, no callback URLs, no provider API keys, no source code from private repos, no internal absolute machine paths beyond `C:\Users\DEVELOPER\Claude\GitHub\...` (the user's own public-handoff working copy, intentionally referenced as a target path for the manual screenshot drop).

# Next gate

External reviewer (Slava + GPT) performs visual audit of the Penpot screen using the manual screenshot, returns ACCEPT / REWORK / BLOCKED. Then Screen 02 build or revision loop.
