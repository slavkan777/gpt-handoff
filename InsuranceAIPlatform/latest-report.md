# InsuranceAIPlatform — Latest gate report

**Gate:** PRODUCT_I18N_EN_DEFAULT_UA_SWITCH_DEPLOY_AND_PUSH_MASTER_V0.1
**Type:** product-wide i18n (English default + Ukrainian switch) + product-copy rewrite + existing-SWA redeploy + commit/push to `dev`
**Date (UTC):** 2026-05-30
**Verdict:** **PRODUCT_I18N_DEPLOYED_PARTIAL_PUSHED** ✅
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`

**Source `dev`:** `211d50f` → **`89e8516`** (pushed) · **origin/main `69e67312` (untouched)**

**Full report:** [product-i18n-en-default-ua-switch-deploy-and-push-v0.1/report.md](product-i18n-en-default-ua-switch-deploy-and-push-v0.1/report.md)

---

## Bottom line

The site now reads as a real B2B insurance claims platform — **English by default**, with a visible **EN / UA switcher** (top-right, next to the profile avatar; persisted in `localStorage`). **All UI chrome across the whole app** is bilingual: login (rebuilt as a product landing with hero + value bullets), navigation, dashboard, claims list, claim workspace + 7 sub-pages, customer directory, guided walkthrough, claim tabs, 6 modals. **Every piece of old "portfolio / walking-skeleton / architecture-diagram" copy is removed** and replaced with product positioning — verified absent in the live bundle.

Rebuilt in backend mode and **redeployed to the existing SWA** (live API integration preserved — no regression to mock). Independent **Opus `/qa-inspector` CLEARED 9/9**. Two commits pushed to **origin/dev**. **No** Azure resource / backend / SQL / AI / `main` change (RG still 9 resources, idle ≈ $0).

**Why PARTIAL (honest):** a narrow, documented set of **synthetic data-layer strings** (golden-claim content, dashboard fallback labels, logic-coupled filter values in `src/data/mock/*`) stays in source language. On the live site those panels are fed by the **API seed**, so localizing them needs **backend seed changes** — out of scope for this frontend gate. UI chrome is 100% bilingual.

**Live demo:** https://kind-meadow-03cf73103.7.azurestaticapps.net — English on first load; click **UA** to switch.

**Proof:** 12/12 headless-Chromium assertions + 4 screenshots (`docs/portfolio/screenshots/i18n-0{1..4}-*.png`).

## Commits

| Commit | Message |
|---|---|
| `ce03776` | `docs: add Azure portfolio proof pack` (14 files) |
| `89e8516` | `feat: add English default and Ukrainian product copy` (51 files) |

## Limitations (unchanged + new)

- New: synthetic data-layer strings + logic-coupled filter values remain source-language (documented; live panels are API-seed-fed → need backend seed work).
- Prior: list endpoints `/api/claims` + `/api/customers` still **500** (Azure SQL deferred → SPA seed fallback); AI **Mock**-only; demo auth client-side.

**Next recommended gate:** `PRODUCT_I18N_QA_POLISH_V0.1` (data-layer i18n + value→label maps + backend seed localization) — or `AZURE_SQL_OPTIONAL_GATE_V0.1`.

---

The full REPORT BACK FORMAT block is in the [full report](product-i18n-en-default-ua-switch-deploy-and-push-v0.1/report.md).
