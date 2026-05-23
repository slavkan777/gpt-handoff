STATUS: FIGMA_DESIGN_BOOTSTRAP_BLOCKED_ON_MCP_INSTALL

Task slug: figma-design-bootstrap-2026-05-24
Project gate: Product concept / UX direction (Figma design only)
Handoff channel: GitHub (this repo)

# CURRENT STATE

Project skeleton initialized in the local vault. **No Figma file, page, or frame was created on canvas** — the design step is blocked on installing a personal Figma MCP server before any write-to-canvas can happen. A full pre-canvas specification (9 frame blocks, design tokens, Ukrainian copy, synthetic demo rows) is written into the local handoff document so the work resumes deterministically after install + Claude Code restart.

# FIGMA MCP STATUS

- Required: a personal Figma MCP exposing `use_figma` / `create_new_file` (write to canvas).
- Available in this session: only the Anthropic team-managed Figma MCP — out of scope for this task per local operating rule (separation between team and personal accounts).
- Personal Figma MCP: NOT yet installed in Claude Code.
- Slava chose the install path (install personal MCP) over relaxing the team-MCP restriction.
- Install command requested: `claude plugin install figma@claude-plugins-official` → Claude Code restart → `/plugin` → `figma` → Allow access.

# FIGMA FILE / LINK

Not yet created.

# PAGES CREATED

None.

# FRAME CREATED

None.

# DESIGN SUMMARY

Target product: **Auto Insurance Claim AI Workbench** — a focused operations product for processing motor-vehicle accident claims (ДТП).

Central business object: **Auto Insurance Claim** (автостраховий випадок).

Explicitly NOT:
- a generic insurance CRM / policy sales tool / broker portal;
- a chatbot;
- a Guidewire clone;
- a legal decision engine.

Portfolio framing: AI Infrastructure / AI Engineering interview-defensible work. Visual register: Microsoft 365 / Fluent UI / Azure Portal / Dynamics — light enterprise.

Design tokens are frozen; first-frame composition (9 blocks) is specified end-to-end including all Ukrainian UI labels and synthetic demo data; target canvas 1440×900 (1536×864 acceptable).

# MAIN UI BLOCKS PLANNED

(Not yet on canvas — all 9 are locked in the local spec.)

1. Left sidebar — product title + 11 nav items + system status card.
2. Top command bar — search input + environment badge + primary action button + icons.
3. KPI row — 5 metric cards (Нові ДТП, Очікують документів, AI-оброблено сьогодні, Високий ризик, Середній час розгляду).
4. Claim lifecycle strip — 7 stages with counters (Реєстрація ДТП → Завершення).
5. Work queue table — 9 columns × 4 synthetic rows (CLM-1006 … CLM-1009).
6. Right AI analysis panel — selected case header + key findings + evidence + recommended action.
7. Automation guardrails card — explicit no-auto-approve + 4 reasons.
8. Agent timeline preview — 7-step AI pipeline with statuses.
9. Audit & cost mini panel — model / tokens / estimated cost / latency / Run ID / Trace ID.

# VISUAL STYLE USED

Planned (not yet applied to canvas): Microsoft 365 / Fluent UI / Azure Portal — light theme. White cards, 8–12 px corners, subtle 1 px borders, subtle shadows. Typography: Segoe UI primary, Inter fallback. Spacing scale 4 / 8 / 12 / 16 / 24 / 32 px.

Frozen tokens (subset):

| Token | Hex | Semantics |
|---|---|---|
| Background | #F5F7FA | app shell |
| Surface | #FFFFFF | cards |
| Border | #E5E7EB | hairline |
| Azure Blue | #2563EB | workflow / platform |
| AI Purple | #7C3AED | AI analysis / agents / evidence |
| Success | #16A34A | low risk / completed |
| Warning | #F59E0B | missing info / review |
| Risk/Error | #DC2626 | high risk / blocker |

# CHECK AGAINST ACCEPTANCE

Acceptance is canvas-resident — every YES below requires a built frame.

- auto insurance claim focus visible: NO (canvas not built; locked in spec)
- chatbot-first avoided: PENDING (spec is non-chatbot; verifiable post-build)
- AI workflow visible: PENDING
- risk/evidence visible: PENDING
- human review visible: PENDING
- audit/cost visible: PENDING
- Ukrainian labels used: PENDING (spec uses Ukrainian throughout)
- synthetic/demo data only: YES (planned rows are synthetic personas — CLM-1006 Toyota Camry, CLM-1007 VW Golf, CLM-1008 Ford Focus, CLM-1009 Skoda Octavia — no real customers)

# EVIDENCE

- Pre-canvas design specification + DONE STATE: written in the local project handoff document (kept local; not published here — contains pre-canvas planning only).
- Figma link: not yet available.
- Screenshot / export: not yet available.

# BLOCKERS

1. Personal Figma MCP not yet installed in Claude Code — required for `use_figma` / `create_new_file` writes.
2. Anthropic team-managed Figma MCP is the only Figma write surface presently available in this session and is out of scope for this task per local operating rule.
3. Post-install Figma plan check is required. Per Figma's May 2026 documentation, write-to-canvas (`use_figma`) requires a Full seat on a paid plan; Dev / Free seats are read-only outside draft files. Slava's instruction excludes upgrade; if the seat tier blocks the write, the fallback is to author the frame in a draft file (write is allowed even on Dev seat there) and migrate.

# NEXT SAFE STEP

1. Slava runs `claude plugin install figma@claude-plugins-official`.
2. Restart Claude Code.
3. Authenticate via `/plugin` → Installed tab → `figma` → "Allow access".
4. Confirm `figma` shows `connected`.
5. Resume by saying "продовжуємо Figma InsuranceAIPlatform" — Claude reloads the local handoff and builds the frame end-to-end against the locked spec.
6. Post-build: publish an updated `latest-report.md` to this repo with the Figma file link, frame URL, and exported screenshot.

# Security block

- secrets in commit: NO
- tokens in commit: NO
- passwords in commit: NO
- connection strings in commit: NO
- private / customer data in commit: NO
- source code from private projects in commit: NO
- internal absolute machine paths in commit: NO

# Source-repo block

- any source-project repo touched: NO
- source-repo commits: NO
- source-repo pushes: NO

# Drive block

- used for this handoff: NO (GitHub handoff channel only)

# Visibility

- gpt-handoff visibility: public (this is the public-readable artifact)
- No private repo modified in this task

# Security scan result

PASS — regex sweep across the 2 changed files (`InsuranceAIPlatform/latest-report.md`, `InsuranceAIPlatform/latest-summary.json`): no provider keys, no JWTs, no private keys, no credentials, no PII (only Slava's first name, allowed), no absolute machine paths, no source code from private repos, no real customer data. The synthetic claim rows (Роберт Джонсон, Марія Коваль, Іван Петренко, Олена Шевченко) are demo personas constructed for this UI design task only.

# Next gate

External reviewer (GPT) audits this blocked-bootstrap status; confirms (a) the blocker is well-scoped (Figma MCP install), (b) the local handoff spec is sufficient for a deterministic post-install resume, (c) no policy violations in the public artifact, (d) the synthetic-data plan is acceptable for the portfolio frame. On approval, Slava executes the Figma MCP install and Claude resumes the design step.
