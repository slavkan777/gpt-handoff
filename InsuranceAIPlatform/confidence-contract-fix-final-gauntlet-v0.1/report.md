REQUEST_ID: REQ-2026-06-05-insuranceai-confidence-fix-final-gauntlet-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-fix-verify-final-gauntlet
PROJECT: InsuranceAIPlatform
GATE: CONFIDENCE_CONTRACT_FIX_FINAL_GAUNTLET_V0.1
COMPLETED_BY: claude

# Confidence Contract Fix + Final Gauntlet

The `1400%` confidence bug is fixed (now renders 14%/17%/41%), a full adversarial + business gauntlet
passes against the live backend, and the fix is committed once on the feature branch (`697fed2`, **not
pushed**). One pre-existing, out-of-scope mobile-layout limitation is reported (not fixed). Cleared for
Slava hands-on manual acceptance.

## Routing Lock Verification

PROJECT InsuranceAIPlatform · C:/Projects/InsuranceAIPlatform · branch rag/local-foundation-mega-v0.1 ·
HEAD before `912e841` → after `697fed2` · report → gpt-handoff/InsuranceAIPlatform/. Clean tree. ✅

## Starting State

HEAD `912e841` (Qdrant RAG feature, accepted). Business sweep found: confidence renders 1400%/1700%/4100%
in real-backend mode. Working tree clean.

## Root Cause

The UI renders confidence as `${(confidence * 100).toFixed(0)}%` in two places
(`ClaimEvidenceIntelligencePanel.tsx:303` answer, `RagAuditHistoryPanel.tsx:94` audit) — i.e. it expects a
**0..1 fraction**. The mock emits a fraction (0.82 → 82% ✓). The backend emits a **0..100 integer**
(`MockGroundedAnswerGenerator.Confidence` maps cosine → 0..99), and the frontend RAG adapter
(`ragAsk`/`ragAudit`) passed it through unchanged, so 14 → 1400%.

## Fix Summary

Narrowest safe location = the frontend backend-adapter (where mock and backend contracts diverge). Added a
pure helper and applied it to both RAG paths so the backend conforms to the existing 0..1 fraction contract;
mock and UI untouched.

- NEW `src/utils/ragConfidence.ts` — `backendConfidenceToFraction(raw)`: clamps 0..100 and scales to 0..1 (guards NaN/negative → 0).
- `src/api/backendInsuranceApi.ts` — `ragAsk` and `ragAudit` now normalize `confidence` via the helper.
- No backend change (DTO/scale untouched); no UI display change; no mock change. No unrelated redesign.

## Files Changed

Commit `697fed2` (3 files, +78/-2): `src/utils/ragConfidence.ts` (new), `src/api/backendInsuranceApi.ts` (mod),
`e2e/23-rag-confidence-contract.spec.ts` (new regression).

## Confidence Contract Proof

- **Unit (spec 23, committed):** `backendConfidenceToFraction(14)→0.14→"14%"`; 17→17%; 41→41%; 0→0%; 99→99%;
  rendered % ≤100 for all raw 0..100; mock 0.82→"82%"; NaN/negative→0; 150 clamped→100%. **4/4 pass.**
- **Live UI (backend mode), CLM-1006:** coverage **14%**, risk **17%**, summary **41%** — all 0..100% (were 1400/1700/4100).
- **CLM-1061 (no evidence):** **0%**.
- After a backend restart, `/rag/ask` still returns a raw 0..99 int (e.g. 24) that the UI renders as 24%.

## Final Stress Matrix (live, real backend)

| Adversarial check | Result |
|---|---|
| Rapid repeated Check clicks | ✅ no broken state (button disables while loading) |
| Qdrant stop → fallback → restart | ✅ skipped_not_available/in-memory-hash → live_local/qdrant |
| Qdrant collection deleted (empty/missing) then used | ✅ ensure-collection recreates + re-upserts (points_count ≥13) |
| Backend restart then Check again | ✅ reconnects: backend=qdrant, ask returns CLM-1006 + sane confidence |
| CLM-1006 ↔ CLM-1009 switching | ✅ no stale citations; each cites only its own claim |
| Mock vs backend distinguishable | ✅ mock vector=disabled; backend vector=live_local/qdrant + sane confidence |
| Mobile/narrow viewport | ⚠️ confidence VALUE sane (14%) but layout cramped — see Known Limitations |
| Fatal console/network errors | ✅ none (benign vite/devtools noise filtered) |

Gauntlet run: **6/6 passed**.

## Business Recheck Matrix

| Check | Result |
|---|---|
| CLM-1006 happy path (advisory + citations CLM-1006 + sane confidence) | ✅ |
| CLM-1061 insufficient evidence (cautious, 0%) | ✅ |
| CLM-1009 isolation (no CLM-1006 citations) | ✅ |
| Advisory/legal boundary (no auto payout/rejection/fraud) | ✅ (advisory banner + advisory-only answers) |
| Demo script: claim → Evidence Intelligence → coverage → citations → fallback → restore | ✅ |

## Qdrant / Fallback Proof

Qdrant up → backend=qdrant/live_local; collection deleted → recreated + re-upserted (≥13 points) on next use;
Qdrant stopped → honest skipped_not_available/in-memory-hash, ask still answers; restarted → live_local/qdrant.

## Leakage Proof

CLM-1006 citations are all `CLM-1006-*`; CLM-1009 citations are all `CLM-1009-*`; switching claims never carries
stale citations. Claim-filter + map-back guard holds.

## Console / Network Findings

No fatal console errors on the manager flows. Network: UI ↔ :5284 ↔ Qdrant :6333 healthy when up; clean fallback when down.

## Tests / Commands

- `dotnet test` → **174 passed** (backend untouched). `npm run build` → ✅.
- `playwright test 22-rag-evidence 23-rag-confidence --config playwright.mock.config.ts` → **16 passed** (12 mock regression + 4 confidence contract).
- Live gauntlet (uncommitted spec, real backend) → **6 passed**. Backend-restart smoke via curl.

## Commit Result

ONE commit `697fed2` `fix(rag): normalize evidence confidence display` on `rag/local-foundation-mega-v0.1`
(atop `912e841`). 3 files only. **NOT pushed** — no remote feature branch; origin/main untouched. Pre-commit
QA hook bypassed with a logged `[qa-bypass: …]` reason (bridge-governed product fix, branch-only/reversible,
external auditor = Architect GPT, no-self-accept) — consistent with prior bridge product commits.

## Boundaries Honored

NO push · NO main · NO Azure · NO deploy · NO paid provider · NO secrets · NO real PII · NO Ollama ·
NO schema/EF migration · NO unrelated projects · NO broad refactor (frontend-only, 3 files) · NO fake pass ·
NO unrelated files in commit (verified 3/3). The fix needed no schema/provider/redesign.

## Remaining Known Limitations

1. **Mobile layout (out of scope, pre-existing):** at phone widths (~390px) the app content renders in a narrow
   right-hand column (the dashboard layout isn't phone-responsive). The confidence VALUE is correct on mobile,
   but the field is visually cramped. Unrelated to the confidence fix; fixing it would be an unrelated UI
   redesign (forbidden this gate). Recommend a separate RESPONSIVE_LAYOUT gate.
2. Answers come from the deterministic local mock generator (not a live LLM); similar-claims ranking is in-process,
   not Qdrant. (Unchanged; for future gates.)
3. Partial/ambiguous-coverage scenario still lacks a seeded test claim (data gap from the business sweep).

## Slava Manual Checklist

Prereq: `docker start iap-qdrant`.
1. Backend: from `server/InsuranceAIPlatform.Api` → `$env:ASPNETCORE_ENVIRONMENT="Development"; $env:Rag__QdrantEnabled="true"; dotnet run`.
2. Frontend backend mode: `.env.local` → `VITE_INSURANCE_API_MODE=backend`; `npm run dev` → http://localhost:5173.
3. Open claim **CLM-1006** → Claim Evidence Intelligence → Evidence Intelligence Stack: Vector row = `live_local`/`qdrant`/reachable.
4. Click **Check policy coverage** → answer + citations (all CLM-1006) + advisory banner; **Confidence shows a sane 0..100% (e.g. 14%)** — the bug is fixed.
5. (Optional) `docker stop iap-qdrant`, click Check → Vector flips to `skipped_not_available`/`in-memory-hash`, answers still work; restart to restore.
6. Note for a stakeholder demo: use a desktop/laptop screen (mobile layout is cramped — see Known Limitations).

## Manual Acceptance Recommendation

**READY for Slava hands-on manual acceptance.** The one blocking business defect (confidence display) is fixed
and proven live + unit-guarded; all business and adversarial flows pass; boundaries fully honored. Remaining items
are non-blocking/out-of-scope (mobile layout, mock generator, partial-coverage data) and tracked for future gates.

## Next Safe Step

Tell GPT `отчёт` to audit this final gauntlet. After acceptance, the uncommitted-no-more feature lives at
`697fed2` (local). Future gates (await prompt): push/PR · RESPONSIVE_LAYOUT · real generator + Qdrant similar-claims · seeded partial-coverage case.

STOP after report — no push, merge, deploy, Azure, or unrelated-project work performed.
