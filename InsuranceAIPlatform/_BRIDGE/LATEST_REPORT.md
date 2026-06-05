REQUEST_ID: REQ-2026-06-05-insuranceai-playwright-manual-acceptance-sweep-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-manual-acceptance-simulation
PROJECT: InsuranceAIPlatform
GATE: PLAYWRIGHT_MANUAL_ACCEPTANCE_SWEEP_V0.1
COMPLETED_BY: claude

# Playwright Manual-Acceptance Sweep — Qdrant RAG

Playwright drove the LIVE UI + API as a human-like tester across all requested case groups. **8/8 case
groups pass; 0 product bugs found.** One test-harness finding (frontend mock/backend mode gotcha) is
documented for Slava's hands-on run. No product code changed; no commit; no push.

## Routing Lock Verification

| Lock | Expected | Observed | OK |
|---|---|---|---|
| PROJECT / SOURCE_REPO | InsuranceAIPlatform / C:/Projects/InsuranceAIPlatform | same | ✅ |
| Branch / HEAD | rag/local-foundation-mega-v0.1 / 912e841 | same, clean tree | ✅ |
| HANDOFF path | gpt-handoff/InsuranceAIPlatform/ | report only | ✅ |

Working tree returned to pristine `912e841` after the sweep (temp spec/config deleted, `.env.local` reverted).

## Environment Setup

- Qdrant `iap-qdrant` (1.18.2) reused on :6333.
- Backend: built DLL with `Rag__QdrantEnabled=true`, `ASPNETCORE_ENVIRONMENT=Development`, :5284 (spawn-time env).
- Frontend: Vite dev on :5173 with `VITE_INSURANCE_API_MODE=backend` (forced via webServer env + temporary `.env.local` flip; reverted to `mock` after).
- Discovered a second chunk-bearing claim for the leakage case: **CLM-1009** (7 chunks); CLM-1006 has 13.

## Playwright Strategy

- Uncommitted config `playwright.acceptance.config.ts` + spec `e2e/_acceptance-sweep.spec.ts` (both deleted after the run).
- Single worker, serial; real backend; the fallback case used `child_process` to `docker stop/start iap-qdrant` mid-run.
- Discriminators chosen to avoid false matches: the vector row always renders a localized note containing
  "(Qdrant)" and "in-process … fallback", so status was asserted via the **status badge** (`rag-infra-vector-status`)
  and the backend value via case-sensitive substring (lowercase `qdrant` vs `in-memory-hash`).

## Test Matrix

| Case | Description | Result |
|---|---|---|
| A | App loads, demo login, navigate to CLM-1006 evidence; no fatal console errors | ✅ PASS |
| B | CLM-1006 happy path: vector live_local/qdrant, answer + advisory, citations + retrieved chunks all CLM-1006, provider=Mock | ✅ PASS |
| C | Infra API == UI (backend=qdrant, reachable, live_local); Qdrant collection points_count≥13; repeat-Check stable | ✅ PASS |
| D | Fallback: stop Qdrant → skipped_not_available + in-memory-hash (no fake qdrant), ask still answers; restart → qdrant | ✅ PASS |
| E | CLM-1009 retrieval returns only CLM-1009 chunks; no CLM-1006 leakage | ✅ PASS |
| F | Rapid clicks + nav away/back + reload: no crash, no blank screen | ✅ PASS |
| G | Desktop (1280×800) + mobile (390×844): vector row + coverage answer visible/usable | ✅ PASS |
| H | Regression: spec22 mock 12/12; real-backend sweep = 7/7 | ✅ PASS |

Sweep run: **7 passed (50.3s)**. Regression spec22 (mock): **12 passed (56.8s)**.

## Passed Cases

All A–H above. Semantic highlights (not just URL/toast):
- Every retrieved-chunk badge and every citation Chunk cell for CLM-1006 start with `CLM-1006-`; for CLM-1009, all start with `CLM-1009-` and none contain `CLM-1006`.
- Provider chip reads `Mock` (no fake live model claimed); advisory banner present on every answer.
- Vector status badge flips live_local→skipped_not_available→live_local exactly tracking Qdrant up/down.

## Failed / Blocked Cases

None (product). One **first-attempt harness failure**, root-caused and fixed (see Console/Network Findings).

## Screenshots / Artifacts

Per-step screenshots were written to `test-results/sweep-*.png` during the run. Note: `test-results/` is
ephemeral — a subsequent Playwright run (the spec22 regression) clears the output dir by default, so the
sweep PNGs were overwritten. The authoritative evidence is the per-case pass/fail above + the API/Qdrant
proofs below. `test-results/` is gitignored and nothing was committed.

## Console / Network Findings

- Case A asserted no fatal console errors (benign noise filtered: favicon, vite HMR, React devtools). **None found.**
- **Harness finding (NOT a product bug):** the first sweep attempt showed the Vector row as `disabled`. Root cause:
  the frontend served **mock mode** (a stale/leftover Vite or `.env.local=mock` precedence), and the mock fixture
  hard-codes `vectorRuntime.status='disabled'`. Fixed by forcing `VITE_INSURANCE_API_MODE=backend` (webServer env)
  and a fresh Vite. **Acceptance implication for Slava:** if the Vector row shows `disabled` during hands-on, the UI
  is in mock mode — ensure `.env.local` has `VITE_INSURANCE_API_MODE=backend` and no stale Vite is reused.

## Qdrant UI/API Proof

- `GET /api/claims/CLM-1006/rag/infrastructure` → `vectorRuntime: {status:"live_local", backend:"qdrant", reachable:true}` — UI badge agrees (live_local) and row shows backend `qdrant`.
- `GET http://localhost:6333/collections/insurance_evidence` → `points_count ≥ 13`.
- Repeated Check clicks keep the panel consistent (no duplicate/broken state).

## Fallback Proof

Live, same backend process (Qdrant enabled): `docker stop iap-qdrant` → UI Check → badge `skipped_not_available`,
row backend `in-memory-hash`, **no lowercase `qdrant` backend value** (note's "(Qdrant)" is capitalized); coverage
answer still renders from the fallback with CLM-1006 chunks. `docker start iap-qdrant` → Check → badge back to
`live_local`, backend `qdrant`.

## Cross-Claim Leakage Findings

None. CLM-1009's coverage answer retrieved only `CLM-1009-*` chunks; zero `CLM-1006` chunk ids in its retrieved
list or citation table. CLM-1006's flow likewise surfaced only its own chunks. The claim-filter + map-back guard holds through the UI.

## Product Bugs Found

**None.** No product source change is warranted by this sweep.

## Files Changed

None committed. Transient, since-deleted helpers: `playwright.acceptance.config.ts`, `e2e/_acceptance-sweep.spec.ts`.
`.env.local` (gitignored) was flipped to `backend` for the run and reverted to `mock`. Product HEAD `912e841` unchanged.

## Commands Run

- `npx playwright test --config playwright.acceptance.config.ts` → 7 passed.
- `npx playwright test 22-rag-evidence --config playwright.mock.config.ts` → 12 passed.
- `curl` infra/Qdrant probes; `docker stop/start iap-qdrant` (fallback case).

## Boundaries Honored

NO product source changes · NO commit · NO push · NO main · NO Azure · NO deploy · NO paid provider · NO secrets ·
NO real PII · NO Ollama · NO schema migration · NO unrelated projects · NO fake pass (every case asserts semantic
data, not just URL/toast). Bug-fixing was out of scope and not needed (no bugs).

## Manual Acceptance Recommendation

**READY for Slava's hands-on acceptance.** All automated human-like flows pass against the live Qdrant-backed UI.
Use the checklist from the previous gate (qdrant-to-manual-acceptance-mega) — Section B (UI) one caveat: confirm the
frontend is in backend mode (Vector row should read `live_local`/`qdrant`, not `disabled`). Qdrant + backend behaved
correctly under happy-path, fallback, cross-claim, rapid-interaction, reload, and responsive conditions.

## Next Safe Step

Tell GPT `отчёт` to audit this sweep. After Slava's manual acceptance: candidate gates (await prompt) —
push/PR gate · real embedding model + incremental upsert · `similar-claims` via Qdrant · owner-approved Ollama.

STOP after report — no product change, commit, push, deploy, or unrelated-project work performed.
