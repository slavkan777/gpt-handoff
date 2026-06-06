REQUEST_ID: REQ-2026-06-06-insuranceai-azure-create-from-zero-full-flow-v0-1
STATUS: PARTIAL_WITH_PRODUCT_GAPS
TASK_TYPE: project-azure-create-from-zero-full-flow-acceptance
PROJECT: InsuranceAIPlatform
GATE: AZURE_CREATE_FROM_ZERO_FULL_FLOW_ACCEPTANCE_MEGA_V0.1
COMPLETED_BY: claude

# Create-from-zero full-flow acceptance — VERDICT: PARTIAL_WITH_PRODUCT_GAPS

One-line: a manager **can** create from zero a **customer** and a **claim** through the live Azure UI, with safe validation/cancel/duplicate handling, real backend persistence (reload + direct URL + fresh browser), and **safe, claim-scoped AI** on the new claim (correctly "insufficient evidence", **no leakage from seeded CLM-1006/1007/1012**). The flow is **not yet a complete lifecycle**: there is **no document→RAG-evidence ingestion** (new claims can never get cited AI analysis), **no policy creation**, **no standalone vehicle entity** (vehicle is free-text on the claim), and the claim form has **no amount/damage-area/status fields**; created records have **no delete UI** (traceable by `E2E-ZERO` prefix). All test data synthetic; no source/Azure/DB-direct/seeded-record mutation. **Slava remains final acceptor.**

## Routing Lock Verification
- PROJECT InsuranceAIPlatform, branch `rag/local-foundation-mega-v0.1`. Data created ONLY through the app's own UI/API; no source edit, no commit, no Azure resource change, no DB-direct write, no DB schema change. Seeded CLM-1006/1007/1012 read-only (comparison only).

## Environment Verification
- Live SWA + backend revision `iap-demo-api--0000003` (RAG fallback). Backend create endpoints exercised: POST `/api/customers`, POST `/api/claims`. providerMode `Mock`, vector `in-memory-hash`.

## Creation Capability Discovery
| Capability | Status |
|---|---|
| New Customer | ✅ UI (`create-customer-open` on `/customers`) + API POST `/api/customers` (server allocates `CUST-T0xxx`, `IsSynthetic=true`, refuses real-PII markers) |
| New Claim | ✅ UI (`new-claim-open` on `/claims`) + API POST `/api/claims` (idempotency key + disabled-submit guard) |
| New Vehicle | ⚠️ **No standalone entity** — vehicle is a free-text field + VIN captured inline on the claim |
| New Policy | ❌ **No create flow** — `PolicyCoveragePage` is view-only; claim form has no policy select |
| Add/Upload Documents | ✅ UI (`upload-doc-open`, `request-missing-doc-open`) + API `/documents/upload`, `/document-metadata` |
| Add RAG Evidence (EvidenceChunks) | ❌ **Not wired** — only `RagSeeder` populates evidence; `/rag/.../reindex` re-embeds *existing* chunks; uploaded docs do NOT become RAG evidence |
| AI/RAG actions | ✅ available on every claim (coverage/risk/missing_docs/summary/similar/custom) |

## Synthetic Prefix / Created IDs
- Prefix: `E2E-ZERO-<timestamp>` (runs `…234348` and `…363`).
- **Customers created:** `CUST-T0207` (API probe), `CUST-T0208` (UI), + 1 from the main UI run (uncaptured id, ~`CUST-T0206`, name `E2E-ZERO-…-Customer`).
- **Claims created:** `CLM-1026` (full), `CLM-1027` (minimal), `CLM-1028` (duplicate-test). All `E2E-ZERO`-prefixed, all `CUST-T0001`-linked in the main run (because the script didn't pass the freshly-created customerId — a test artifact, not a product issue; the claim form *does* accept a customerId).

## Detailed Results A–S
**A. Capability discovery — done** (table above): customer+claim creation in UI; vehicle inline; policy/evidence-ingestion missing.
**B. Form validation — PASS.** Customer: empty name blocked (HTML5 `required`, no POST); 220-char name → React error "Full name must not exceed 200 characters" (no POST); server also guards (`MISSING_REQUIRED_FIELDS`, `FIELD_TOO_LONG`). Claim: empty submit blocked (required `vehicle`+`location`), modal stays open, no record created. No partial/bad record on validation failure.
**C. Cancel/back — PASS.** Filling then Cancel on both customer and claim forms created **no** record (no POST fired). No draft mode (acceptable).
**D. Create customer — PASS.** UI created `CUST-T0208` (HTTP 200, `IsSynthetic=true`), **searchable** in the customers list; API path independently created `CUST-T0207`. Persists.
**E. Vehicle — PARTIAL (product shape).** No standalone vehicle create; vehicle (`E2E-ZERO… Toyota Corolla 2023`) + VIN (`VIN-<ts>`) captured inline on the claim and shown on the claim detail. No duplicate-VIN constraint to test (free-text).
**F. Policy — GAP.** No policy creation; no policy selection on the claim form. Existing claims show policy context (e.g., POL-2025-AC-4421 on CLM-1006) but new claims don't bind a policy. Documented as product gap.
**G. Create full claim — PASS.** `CLM-1026` created from zero; detail shows the created vehicle + `E2E-ZERO` prefix; appears in list; opens detail; **reload + direct-URL** both show the created data (verified with proper waits).
**H. Minimal claim — PASS.** `CLM-1027` created with only required vehicle+location; defaults applied (event type ДТП, today's date). List/detail/reload OK.
**I. Edge data — PASS (within form limits).** Ukrainian/non-Latin vehicle, VIN, location accepted; long text accepted. Note: the form has **no amount/damage-area/status fields**, so amount/boundary-date-on-amount edges are N/A.
**J. Duplicate/idempotency — PASS.** Double-click Save created **exactly one** claim (`CLM-1028`) — submit button `disabled` during in-flight + idempotency key. No duplicate chaos.
**K. Documents/evidence — PARTIAL.** Document upload + missing-doc-request UI exist on the claim; however uploaded documents do **not** flow into RAG evidence (no ingestion → no `EvidenceChunks`). Evidence *attach* exists; **RAG evidence ingestion is the gap**.
**L. AI/RAG on new full claim — PASS (safe).** `CLM-1026` AI Evidence → coverage + summary both: `providerMode=Mock`, **confidence 0, 0 citations**, "Недостатньо релевантних доказів… Рекомендується перегляд людиною." **No citation references CLM-1006/1007/1012.** Claim-scoped + safe.
**M. AI/RAG on minimal claim — PASS (safe).** Same insufficient-evidence behavior on the minimal claim (no evidence → safe refusal).
**N. Fresh browser persistence — PASS.** New browser context + re-login → `CLM-1026` opens with its `E2E-ZERO` data via list search and direct URL. Confirms **Azure backend persistence**, not local UI memory.
**O. Stale fixture regression — PASS.** New claim → CLM-1006 (shows Роберт Джонсон) → back to new claim (shows `E2E-ZERO`, **no** Роберт Джонсон). No fixture leakage.
**P. End-to-end manager flow — see Manager Outcome Table below.**
**Q. Cleanup / traceability — PARTIAL.** No delete/archive UI for created records. All created records carry the `E2E-ZERO` prefix and IDs are listed for later cleanup. Seeded demo records verified **unchanged** (CLM-1006 still Роберт Джонсон/Toyota Camry).
**R. Visual/UX — PASS.** Screenshots for create-customer, create-claim detail, validation error, new-claim AI evidence, narrow viewport. Forms readable, validation clear, no overlap/blank panel; narrow 390×844 usable.
**S. Product readiness — see Verdict.**

## Manager Outcome Table (create-from-zero)
| Field | Value |
|---|---|
| Created customer | `CUST-T0208` (`E2E-ZERO-…-UICust`), searchable, synthetic |
| Vehicle | inline on claim: `E2E-ZERO… Toyota Corolla 2023`, VIN `VIN-<ts>` (no standalone entity) |
| Policy | none (no creation/selection flow) |
| New claim | `CLM-1026` (full) + `CLM-1027` (minimal), persisted |
| Evidence status | none — no ingestion path; RAG = insufficient |
| AI/RAG result | safe: conf 0, 0 citations, human-review wording, claim-scoped |
| Next action | manager can intake a claim; to get AI analysis, evidence ingestion must be added |
| Safe for automated final decision | **NO** |

## Validation / Cancel / Duplicate Findings
- Validation: client (HTML5 required + React length) **and** server (`MISSING_REQUIRED_FIELDS`/`FIELD_TOO_LONG`) — defense in depth. Cancel: no record. Duplicate: single record on double-click. **All safe.**

## Persistence / Reload / Direct URL Proof
- `CLM-1026`: reload ✅ (shows `E2E-ZERO`), direct URL ✅ (shows `E2E-ZERO` + "Toyota Corolla 2023", no Роберт Джонсон), fresh browser context ✅. `CUST-T0208` searchable ✅.

## New Claim AI/RAG Proof
- `CLM-1026` coverage + summary: `Mock`, **0 confidence, 0 citations**, insufficient-evidence wording, advisory-only. Scopes empty → **no seeded-claim citations**.

## Stale Fixture / Cross-Claim Leakage Proof
- new→CLM-1006→new round-trip: new claim renders only its own `E2E-ZERO` data; **zero** CLM-1006 customer/evidence leakage. RAG on new claim never cites CLM-1006/1007/1012.

## Cleanup / Leftover Synthetic Data
- **No delete UI.** Leftover synthetic records (safe to remove later): customers `CUST-T0206?`(uncaptured)/`CUST-T0207`/`CUST-T0208`; claims `CLM-1026`/`CLM-1027`/`CLM-1028`. All `E2E-ZERO`/synthetic, `IsSynthetic=true`, no real PII/money. Seeded demo data unchanged.

## Visual / UX Findings
- Create modals are clean and readable (labeled fields, sandbox note, clear primary/secondary buttons). Validation errors visible. Created claim detail renders full workspace. Narrow viewport usable. No blocking visual issue.

## Ranked Defects / Product Gaps
1. **(High, for full demo) No document→RAG-evidence ingestion** — new claims can never get cited AI analysis; RAG corpus is seeded-only. Blocks the "intake → AI review with citations" story for new claims (current behavior is *safe* — honest "insufficient" — but limited).
2. **(Medium) No policy creation/selection** in the create flow.
3. **(Medium) No standalone vehicle entity** — vehicle is free-text on the claim; no customer→vehicle→policy chaining; no VIN/plate uniqueness.
4. **(Medium) Claim form lacks amount / damage-area / status fields** — limits richness of created claims.
5. **(Low) No delete/archive** for created records (mitigated: `E2E-ZERO` prefix + `IsSynthetic`).
6. **(Low/cosmetic) Freshly-created claims emit benign `404` console lines** for their not-yet-existing sub-resources (documents/evidence/ai-analysis) — handled gracefully.

## Product Readiness Verdict
**PARTIAL_WITH_PRODUCT_GAPS.** The **claim-intake create-from-zero core is real and safe**: a manager creates a customer and a claim, data persists in Azure, validation/cancel/duplicate are safe, and AI on the new claim is correctly insufficient + claim-scoped (no leakage). It is **not** a complete lifecycle: no evidence-ingestion-for-RAG, no policy creation, no standalone vehicle, no amount/status fields, no delete. Per this gate's own criteria ("RAG ingestion for new claims is missing", "claim creation works only with existing … policy", "evidence attach … ", "cleanup/delete missing but traceable") → PARTIAL, not CREATE_FLOW_READY. **Not NOT_READY** — nothing crashes, persists correctly, no leakage, no unsafe decisions.

## Screenshots / Artifacts
Under `gpt-handoff/InsuranceAIPlatform/azure-create-from-zero-full-flow-v0.1/`: `create-customer-result.png`, `create-claim-full-detail.png`, `claim-validation-error.png`, `new-claim-ai-evidence.png`, `narrow-claims.png` (synthetic data, no secrets/PII).

## Network / Console Findings
- Create POSTs returned 200 with synthetic IDs + "No real PII, no real money" messages. Console noise = benign `404`s (freshly-created claims' empty sub-resources) + `net::ERR_ABORTED` (rapid create→navigate). **Zero JS exceptions, zero 500s, zero 401/403.**

## Boundaries Honored
NO source change · NO product commit/push · NO main · NO merge · NO Azure deploy/update/create/delete · NO DB schema change · NO DB-direct write (only app API) · NO mutation of seeded CLM-1006/1007/1012 · NO new Azure resources · NO real PII (all `E2E-ZERO`/synthetic) · NO secrets printed/stored · NO fake pass · NO claiming final acceptance for Slava.

## Files / Artifacts Created
- Temp/local (not committed to product repo): Playwright create-flow + re-verify scripts, result JSONs, 5 screenshots. Screenshots + this report published to the gate folder in gpt-handoff only. **Live backend:** synthetic `E2E-ZERO` customers + claims listed above (no delete path).

## Commands Run (high level)
- Playwright create-from-zero session + focused re-verify (login, capability discovery, validation, cancel, create customer/claim, duplicate, persistence, fresh-context, RAG-on-new-claim, stale-fixture, narrow); `curl` POST `/api/customers` + GET probes; code inspection of create modals/controllers. App-API writes only.

## Slava Manual Create-Flow Checklist
1. `/customers` → "Create customer" → fill a synthetic name → save → confirm toast + searchable.
2. `/claims` → "New claim" → fill vehicle + location (+ optional customer/VIN) → save → lands on new claim detail.
3. Reload + paste the new claim URL in a fresh tab → confirm it persists.
4. Open the new claim's AI Evidence → "Coverage" → confirm it says **insufficient evidence** (expected — no evidence ingested).
5. Decide priority: do you want **document→RAG-evidence ingestion** (so new claims get real AI analysis) as the next build item?

## Recommended Next Step
- Architect GPT audit via «отчёт». If the demo needs "create a claim AND get AI analysis on it", the top next gate is **evidence-ingestion for new claims** (document/text → `EvidenceChunks` via the existing deterministic embedder + `reindex`), then re-run this create-flow gate. Optional: cleanup of `E2E-ZERO` synthetic records.

STOP — holding after report. No defect fixes, no deploy, no Azure/DB mutation, no provider work.
