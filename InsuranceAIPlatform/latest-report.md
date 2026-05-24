STATUS: FIGMA_DESIGN_BUILD_PARTIAL

Task slug: figma-design-build-2026-05-24
Project gate: Product concept / UX direction (Figma design only)
Handoff channel: GitHub (this repo)

# CURRENT STATE

Figma Design Build for InsuranceAIPlatform is partially executed and blocked on Figma's Starter-plan monthly tool-call cap.

What is on canvas right now:

- Page `00 — Product Concept` — **empty** (intended product brief board was rejected by the Starter cap before any node was created).
- Page `01 — Screens` — contains **one fully-designed frame**, `01 — Огляд автострахових випадків` (1440×900), with all 9 documented blocks.

What is NOT on canvas:

- Product Concept visual board on Page `00`.
- The 10 supporting screens on Page `01` (02 Автострахові випадки, 03 Робоче місце випадку, 04 Документи та фото, 05 AI-перевірки, 06 Ризики та перевірки, 07 Аудит і витрати, 08 Клієнти, 09 Поліси, 10 Транспортні засоби, 11 Налаштування).
- A screenshot/export captured via the MCP `get_screenshot` tool (also blocked by the cap).

# FIGMA FILE / LINK

- File URL (Drafts, owner-only): https://www.figma.com/design/RE4vswlgy6RUjluHQ9PJ55
- Dashboard frame URL (node-focused): https://www.figma.com/design/RE4vswlgy6RUjluHQ9PJ55?node-id=1-3

# LOCATION

Drafts of Slava's personal Figma workspace (starter tier, Full seat). Owner-only; not on a paid team project.

# PAGES CREATED

- `00 — Product Concept` — created, content not yet built (cap hit before content build).
- `01 — Screens` — created, contains one frame.

# FRAMES CREATED

- `01 — Огляд автострахових випадків` (1440×900) — fully designed per the locked 9-block spec.

# CORE SCREENS COMPLETED (Priority A)

- `01 — Огляд автострахових випадків` — **COMPLETE** (1 of 4 Priority A).
- `03 — Робоче місце випадку` — NOT BUILT (blocked).
- `05 — AI-перевірки` — NOT BUILT (blocked).
- `07 — Аудит і витрати` — NOT BUILT (blocked).

# SUPPORTING SCREENS COMPLETED (Priority B)

- 0 of 7 Priority B screens built (all blocked).

# SCREENSHOT EVIDENCE

- Not captured via MCP — `get_screenshot` is read-tool, monthly cap exhausted.
- The dashboard exists and is visually inspectable by opening the file URL above.
- Pending: a manual PNG export of the dashboard frame by Slava (60-second action via Figma UI: select frame → right sidebar → Export → 1x/2x PNG → drag PNG into chat). Once received it will be published under `latest-screens/dashboard-overview-2026-05-24.png` and the report republished with `STATUS: FIGMA_DESIGN_BUILD_DASHBOARD_DONE` (one Priority A screen is genuinely portfolio-grade even if the other 10 wait).

The earlier published `latest-screens/figma-write-test-2026-05-24.png` remains in this repo as historical evidence from the Access Gate test, not as evidence for the dashboard build.

# DESIGN SUMMARY

Dashboard `01 — Огляд автострахових випадків` content (built, in canvas):

1. Left sidebar (256w × 900h) — product title `InsuranceAIPlatform / AI-обробка автострахових випадків`, 11 nav items with active state on `Огляд`, bottom system status card listing 5 green-dotted services.
2. Top command bar (1184w × 56h) — search input `Пошук випадків, клієнтів, авто, документів...`, soft-blue env badge `Середовище: Azure Demo`, green-dot chip `Система готова`, primary action `Запустити демо` (Azure Blue), placeholder icon.
3. KPI row — 5 cards with top accent stripes: Нові ДТП (8, +2 сьогодні, blue), Очікують документів (12, 6 з високим пріоритетом, amber), AI-оброблено сьогодні (48, +18%, purple), Високий ризик (7, Потрібна людська перевірка, red), Середній час розгляду (18 хв, -12%, green).
4. Claim lifecycle strip — 7 stages (Реєстрація ДТП 8 → Збір документів 12 → Оцінка пошкоджень → AI-аналіз 14 → Перевірка покриття → Людське рішення 5 → Завершення 21) connected by chevron arrows, counters where defined.
5. Work queue table — 9 columns (Номер випадку / Клієнт / Авто / Тип події / Документи / AI-статус / Ризик / Наступна дія / Оновлено), 4 synthetic rows (CLM-1006…CLM-1009), CLM-1006 highlighted as selected (soft-blue BG, left Azure-Blue accent), badge palette per AI-status and risk.
6. Right AI analysis panel (top card, 320h) — selected case CLM-1006 (Роберт Джонсон / Toyota Camry 2021 / Auto Comprehensive), 4 stat chips (high risk red / 78% confidence purple / 6-of-7 docs amber / SLA: сьогодні), 4 key findings, 5 evidence chips, recommended action, two buttons (Відкрити огляд / Запитати дані).
7. Automation guardrails (128h) — no auto-approval (red dot), human review required (amber dot), 4 reason chips.
8. Agent timeline preview (200h) — 7 steps with status colors (3 завершено green / 1 попередження amber / 1 високий ризик red / 1 готова green / 1 очікує neutral).
9. Audit & cost mini panel (100h) — Модель Azure OpenAI Demo, Токени 4 261, Вартість $0.0187 (green), Час 18.9 с, plus mono-style chips Trace `trc_8f3d2a7e` and Run `run_8f3d2a7e`.

The 10 unbuilt screens are described in the brief but not represented in canvas.

# VISUAL STYLE USED

Microsoft 365 / Fluent UI / Azure Portal — light theme. White cards on `#F5F7FA` background, 1px `#E5E7EB` borders, 8–12 px corners, Inter typography (Regular / Medium / Semi Bold / Bold), spacing scale 4 / 8 / 12 / 16 / 24 / 32 px. Locked tokens applied throughout.

# BLOCKERS / LIMITATIONS

1. **Figma Starter monthly cap exhausted.** Per the official Figma MCP rate-limit page, Starter Dev/Full seats are capped at "Up to 6/month" tool calls. Build of the dashboard consumed multiple `use_figma` calls; the cap then blocked both `get_screenshot` and the next `use_figma` attempt. The cap is monthly and will reset in June 2026.
2. **Upgrade explicitly forbidden by Slava.** Confirmed in this task brief and in prior briefs — no plan upgrade.
3. **Screenshot evidence cannot be produced by MCP this cycle.** Manual PNG export from the Figma UI is required for any visual evidence until the cap resets or the plan changes.

# CHECK AGAINST ACCEPTANCE

- auto insurance claim focus visible: **YES** (in the one built screen)
- Product Concept created: **NO** (page exists, board content not built — cap hit before content)
- all 11 screen frames created: **NO** (1 of 11)
- core 4 screens fully designed: **NO** (1 of 4)
- supporting screens non-empty and credible: **NO** (0 of 7)
- chatbot-first avoided: **YES**
- generic CRM avoided: **YES**
- AI workflow visible: **YES** (dashboard)
- risk/evidence visible: **YES** (dashboard)
- human review visible: **YES** (dashboard)
- audit/cost visible: **YES** (dashboard)
- Ukrainian labels used: **YES**
- synthetic/demo data only: **YES** (CLM-1006…CLM-1009 + Roberto/Maria/Ivan/Olena, no real customers)
- no source repo touched: **YES**
- no secrets published: **YES** (no OAuth, no email/handle/planKey, no abs paths)

# NEXT SAFE STEP

Two parallel decisions for Slava:

A. **Manual export of the existing dashboard** (60-second action) → I republish `latest-report.md` with the screenshot link and demote the status to `FIGMA_DESIGN_BUILD_DASHBOARD_DONE` (which is more accurate for the one screen that is genuinely portfolio-grade).

B. **Path for the remaining 10 screens + Product Concept board** — pick one:
   - **B1.** Wait until June 2026 when the Starter cap resets. Then in 1–2 bulk `use_figma` calls I bundle 3–5 screens per call (each call can be up to 50 000 chars of plugin code). 11 screens fit in ~3 large calls; we'd still have read-budget for one `get_screenshot` per month.
   - **B2.** Upgrade the personal Figma plan to Professional ($16/seat/month per Figma pricing — Slava decides if value justifies cost). This unlocks 200 calls/day, 15/min, including reads.
   - **B3.** Hybrid — I keep building via Claude side (text spec, JSON design tokens, page-level descriptions written to AIKB) without touching MCP; on next cap window Slava decides whether to upgrade. No further Figma writes happen this month.

The brief allows partial completion explicitly ("If tool/time constraints appear, finish Priority A first, then Priority B"). One Priority A screen is finished. The remaining 3 Priority A + 7 Priority B are gated on B1/B2/B3 choice.

# Security block

- secrets / tokens / passwords / connection strings / private customer data / private source / internal abs paths in commit: **NO** across the board.
- Account-identifying metadata (email/handle/planKey): NOT published.
- File URL published is for a Drafts file — owner-only; clicking without auth returns no access.

# Source-repo block

- source-project repo touched: NO. source-repo commits: NO. source-repo pushes: NO.

# Drive block

- used for this handoff: NO.

# Visibility

- gpt-handoff: public.
- No private repo modified.

# Security scan result

PASS — regex sweep across the 4 changed files: no provider keys, no JWTs, no credentials, no PII (only Slava's first name, allowed), no callback URLs, no internal abs paths, no source code from private repos.

# Next gate

External reviewer (GPT) audits the partial dashboard: (a) confirms the one built screen is portfolio-grade and acceptance-aligned, (b) confirms the blocker is honestly described, (c) advises on B1 / B2 / B3 going-forward path.
