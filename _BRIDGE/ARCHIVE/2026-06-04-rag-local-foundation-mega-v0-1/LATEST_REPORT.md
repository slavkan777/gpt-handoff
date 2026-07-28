REQUEST_ID: REQ-2026-06-04-rag-local-foundation-mega-v0-1
VERDICT: PARTIAL_READY_FOR_AUDIT
PROJECT: InsuranceAIPlatform
GATE: RAG_LOCAL_FOUNDATION_MEGA_V0.1
MODE: IMPLEMENTATION_WITH_VERIFICATION / NO COMMIT / NO PUSH / NO AZURE
COMPLETED_BY: claude

CURRENT STATE: Local RAG foundation implemented end-to-end inside the real repo
(`C:\Projects\InsuranceAIPlatform`) on an isolated branch `rag/local-foundation-mega-v0.1`.
Backend + frontend build green; 154/154 tests pass; EF migration applies to real SQL Server (LocalDB)
and seeds; a live HTTP smoke against the real API returns grounded, cited, claim-scoped answers.
**Nothing committed or pushed** — all changes are uncommitted on the feature branch for review.

CURRENT GATE: RAG_LOCAL_FOUNDATION_MEGA_V0.1 (implementation + verification).

WHAT CHANGED (high level):
- New RAG capability folded into the existing **AiAnalysis** bounded context (schema `ai_analysis`) —
  no new project/bounded context (per "avoid unnecessary bounded contexts").
- 4 new tables, deterministic local embeddings + in-SQL cosine retrieval, grounded mock generation,
  disabled-by-default LocalLlama seam, 4 API routes, a "Claim Evidence Intelligence" React panel,
  17 new tests. SQL is source of truth; the embedding cache is rebuildable.

VERIFICATION (all commands actually executed):
- `dotnet build` (solution) → **0 errors, 0 warnings**.
- `dotnet test` (solution) → **Passed 154, Failed 0** (137 pre-existing + 17 new RAG).
- `dotnet ef migrations add AddRagFoundation` → migration `20260604161120_AddRagFoundation` (4 tables).
- `dotnet run --project DbMigrator` (real LocalDB) → `ai_analysis` migration applied; seeded
  **PolicyClause=6, EvidenceChunk=15, RagEvaluationQuestion=6** (all other contexts unaffected).
- Frontend `npm run build` (`tsc -b && vite build`) → **exit 0**, 152 modules, panel wired.
- Live HTTP smoke (booted real API on :5179 against LocalDB, then stopped):
  - `POST /api/claims/CLM-1006/rag/ask` → **200**, providerMode=`Mock`, citations=4, retrievedChunkIds=4,
    advisoryOnly=true, costMicros=0, citation[0]=`CLM-1006-policy-terms#0` (coverage→clause, correct).
  - `POST /api/claims/CLM-1007/rag/ask` → retrieved=3, **CLM-1006-leaked=0** (HTTP-level NEGATIVE_PASS).
  - `GET /api/claims/CLM-1006/rag/evaluation-questions` → 4.

BOUNDARIES HONORED:
- **No commit, no push** — branch `rag/local-foundation-mega-v0.1`, **0 commits ahead of origin/dev**.
- No Azure anything; no AI Search / Foundry / GPU; no deploy.
- Real DeepSeek/LLaMA **not** enabled (Mock remains default; LocalLlama seam disabled-by-default).
- No secrets/API keys read or printed; no real PII (synthetic Ukrainian text only).
- No destructive migration (additive tables only); existing demo flows / CLM-1006 golden scenario / Mock-default preserved.
- Only LocalDB (local dev SQL) was written — synthetic, additive, idempotent.

KNOWN LIMITATIONS (honest — these gate the PARTIAL verdict):
1. **"Find similar claims" is claim-scoped in v1.** Retrieval filters strictly by claimId (this IS the
   leakage guard), so cross-claim similarity is intentionally not yet implemented. It needs a separate
   cross-claim path that returns claim ids + scores only (no evidence text) — documented next step.
2. **Eval set is a 6-question starter** (toward the 30-60 target). Expansion path = add rows with the
   same builders. (Request explicitly allowed a "smaller starter set with explicit expansion path".)
3. **Full Playwright E2E not run.** Covered instead by the live HTTP smoke + 17 backend tests +
   frontend typecheck/build. Wiring an automated UI e2e (multi-process: API+SWA+DB) is the next gate.
4. **Confidence values are illustrative.** The deterministic hash embedding ranks correctly but yields
   modest cosine scores (e.g. confidence 15 on a paraphrased coverage query) — a real embedding model
   (future gate) raises absolute confidence without changing the architecture.

NEXT SAFE STEP: Architect-GPT audit of this branch + diff. If accepted, a separate **commit-only** gate
(explicit file list, no new edits) lands the branch; then optionally `RAG_E2E_SEMANTIC_EVIDENCE_V0.1`
(Playwright) and the cross-claim "similar" path. Do not commit/push/deploy until approved.

---

## Files read (grounding, read-only)
IAiProvider / AiProviderMode / MockAiProvider / AiAnalysisDbContext(+Factory/Extensions/Seeder) /
AiAnalysisRun / AiProviderOptions / Documents ClaimDocument + DocumentsSeeder / Api Program.cs /
AiAnalysisController / ClaimsControllerBase / DbMigrator Program.cs / SeedConstants / appsettings(.Development) /
MockAiProviderTests / PersistenceSeedTests / CommandTestWebApplicationFactory.

## Data model (10 requested entities → 4 tables, merges documented in code)
- **PolicyClause** (coverage/exclusion/limit/deductible clauses).
- **EvidenceChunk** — the retrievable unit; merges `ChunkEmbedding` as a rebuildable cache (`EmbeddingJson`,
  `EmbeddingModel`, `ChunkHash` re-embed gate). Source of truth in SQL.
- **RagEvaluationQuestion** — merges `ExpectedAnswer` (expected sources + must-not-cite + keywords).
- **RagAuditTrace** — merges `RetrievalQuery`/`RetrievedEvidence`/`GroundedAnswer`/`CitationReference`
  (query + retrieved ids + answer + citations JSON + cost/tokens) per answer event.
All in schema `ai_analysis`; cross-context refs (claimId/documentId/policyId) are strings — no shared entity
(boundary test `No_entity_type_is_shared_across_two_contexts` still passes).

## Migration
`server/.../Services.AiAnalysis/Migrations/20260604161120_AddRagFoundation.cs` (+ Designer + snapshot) —
creates `PolicyClauses`, `EvidenceChunks`, `RagEvaluationQuestions`, `RagAuditTraces`. Additive; applied
to LocalDB successfully.

## Seed counts (deterministic, idempotent, synthetic)
PolicyClause=6 (Auto Comprehensive) · EvidenceChunk=15 (CLM-1006=9, CLM-1007=3, CLM-1008=3) ·
RagEvaluationQuestion=6 (4×CLM-1006, 2×CLM-1007). Scenarios covered: covered ДТП, exclusion clause,
missing police/photo, inflated invoice (+38% vs benchmark), missing invoice, complete claim, advisory
risk (no fraud accusation), approval summary.

## Retrieval / embedding / generation
- `DeterministicEmbeddingProvider` — FNV-1a signed feature-hashing, 256-dim, L2-normalized; no external call.
- `RagRetrievalService.Rank` — cosine top-k, deterministic tie-break; `DbRagChunkSource` filters strictly
  by claimId (leakage guard at the source).
- `MockGroundedAnswerGenerator` (default) — retrieval-before-generation; cites only retrieved chunks;
  advisory footer; risk use-case never asserts fraud; honest "insufficient evidence" path.
- `LocalLlamaGroundedAnswerGenerator` — disabled-by-default seam; safe fallback to mock; no network call, no secret.

## API routes (camelCase JSON, advisory-only)
- `POST /api/claims/{claimId}/rag/ask`
- `GET  /api/claims/{claimId}/rag/evidence-search?q=&topK=`
- `GET  /api/claims/{claimId}/rag/evaluation-questions`
- `GET  /api/claims/{claimId}/rag/audit?limit=`
DTOs return answer, confidence, citations (chunkId/documentId/kind/snippet/score), retrievedChunkIds,
providerMode, prompt/completion tokens, costMicros, retrievalMs, advisoryOnly, traceId, correlationId.

## Frontend
`ClaimEvidenceIntelligencePanel.tsx` inside `AiEvidencePage.tsx`; `features/rag/` slice+saga+selectors;
api types + mock + backend client; store + rootSaga registration; EN/UA i18n. Six buttons (coverage,
missing_docs, risk, similar, summary, custom) + custom input; answer + confidence + evidence/citation
cards + retrieved chunk ids + cost/tokens + audit trace id + "AI advisory only — human decides" banner.
Loading/error/empty states. `npm run build` exit 0.

## Tests (17 new; labels)
- MECHANICAL: panel builds; API responds (HTTP smoke); endpoints' service layer responds.
- SEMANTIC: embedding determinism + related>unrelated; retrieval ranks matching chunk first; answer cites
  only retrieved chunks; coverage query retrieves the policy clause.
- PERSISTENCE: seed survives fresh read (InMemory); RagAuditTrace persists after ask and is read back;
  migration applies + seeds on real LocalDB.
- NEGATIVE: **CLM-1006 evidence never leaks into CLM-1007** (data-level, service-level, AND HTTP-level);
  risk explanation never accuses fraud; no-evidence → low confidence honest answer.

## Commands run
`dotnet build` · `dotnet test` (154/154) · `dotnet ef migrations add` · `dotnet run --project DbMigrator`
· `npm run build` · live API HTTP smoke (start → POST/GET → stop).

## Git status (product repo)
Branch `rag/local-foundation-mega-v0.1`; **0 commits ahead of origin/dev**; 11 tracked files modified
(+518), 11 new files/folders untracked (Rag/, RagController + Contracts/Rag, 2 migration files, 3 test
files, features/rag, ClaimEvidenceIntelligencePanel, i18n/messages/rag.ts). Uncommitted by design.

## Risks
- Deterministic embedding is intentionally simple (portfolio-cost-zero); swap for a real model later via
  the same `IEmbeddingProvider` seam. Keep `Rag:EmbeddingDimensions` consistent between seed + runtime.
- "Similar claims" cross-claim path must return ids/scores only (never cross-claim evidence text) to keep
  the leakage guard intact.

## STOP LINE
Stopped after implementation + verification + this report. No commit, no push, no deploy, no AIKB update,
no Azure. Awaiting Architect-GPT audit and Slava approval before the commit-only gate.
