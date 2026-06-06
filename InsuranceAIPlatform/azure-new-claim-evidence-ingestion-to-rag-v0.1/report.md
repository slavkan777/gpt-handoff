REQUEST_ID: REQ-2026-06-06-insuranceai-azure-new-claim-evidence-ingestion-to-rag-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-azure-new-claim-evidence-ingestion-to-rag
PROJECT: InsuranceAIPlatform
GATE: AZURE_NEW_CLAIM_EVIDENCE_INGESTION_TO_RAG_MEGA_V0.1
COMPLETED_BY: claude

# New-claim evidence ingestion → RAG — VERDICT: READY_FOR_AUDIT

One-line: the highest-value create-from-zero gap is **closed**. A document uploaded (with text) to any claim is now turned into **claim-scoped `EvidenceChunks`** via the existing deterministic embedder, so a newly-created claim gets a **cited RAG answer drawn from its own uploaded evidence** — with **zero leakage** from seeded CLM-1006/1007/1012, the no-evidence case still safely "insufficient", and the seeded claims unchanged. Implemented behind the **existing** document-upload UI/API (no schema change, no new migration), 185/185 tests green, deployed to the existing dev/test Container App in fallback mode (Mock + in-memory-hash), live-smoke proven end-to-end. Feature branch only; main untouched. **Slava remains final acceptor.**

## Routing Lock Verification
- PROJECT InsuranceAIPlatform; remote `slavkan777/InsuranceAIPlatform`; branch `rag/local-foundation-mega-v0.1`; HEAD before `4067591`, after `c457734`. `origin/main` untouched (`69e6731`). No main/merge/force. Azure dev/test only.

## Starting State
- Tree clean at `4067591`. Azure active revision `iap-demo-api--0000003` (rollback target). Last gate (create-from-zero) = PARTIAL_WITH_PRODUCT_GAPS with the documented gap: uploaded docs don't become RAG evidence.

## Discovery: Existing Document / RAG Flow
- **Upload**: `POST /api/claims/{claimId}/documents/upload` (`UploadDocumentContentRequest{Kind,Title,DocType,Content}`) persists real text content to the Documents domain + audit + outbox. UI: `UploadDocumentContentModal` (`upload-doc-*` testids) already sends `content`.
- **RAG**: `EvidenceChunk` (ai_analysis) carries Text + cached deterministic embedding; `RagSeeder.BuildChunk` is the construction pattern; `DbRagChunkSource` retrieves with a strict `WHERE ClaimId == claimId` filter (the built-in leakage guard); `RagService`/generator produce cited, advisory answers; `/rag/infrastructure` exposes per-claim chunk counts.
- **Seam chosen** (minimal, product-shaped): after `documents/upload` succeeds, create claim-scoped EvidenceChunks from that text via the existing embedder. Reuses the existing upload UI/API → full UX, **no new table, no migration**.

## Implementation Summary
- New `IEvidenceIngestionService` + `EvidenceIngestionService` (AiAnalysis/Rag/Ingestion): splits text into bounded paragraph chunks (≤24 chunks, ≤800 chars), embeds each with the local `DeterministicEmbeddingProvider`, stores as `EvidenceChunk` rows mirroring `RagSeeder.BuildChunk`. ChunkId = `{claimId}-uploaded-{title-slug}-{docTail}-{n}`. Strictly additive + per-key idempotent; `ClaimId` = target claim only.
- Registered in `AddRagFoundation` DI.
- Wired into `ClaimCommandsController.UploadDocumentContent`: after the document write, best-effort ingestion (failure degrades to a warning; the upload still succeeds). Chunk count surfaced in the response message.

## Files Changed
- NEW `server/InsuranceAIPlatform.Services.AiAnalysis/Rag/Ingestion/IEvidenceIngestionService.cs`
- NEW `server/InsuranceAIPlatform.Services.AiAnalysis/Rag/Ingestion/EvidenceIngestionService.cs`
- MOD `server/InsuranceAIPlatform.Services.AiAnalysis/Rag/RagServiceCollectionExtensions.cs` (register service)
- MOD `server/InsuranceAIPlatform.Api/Controllers/ClaimCommandsController.cs` (inject + call + message)
- NEW `server/InsuranceAIPlatform.Tests/EvidenceIngestionServiceTests.cs` (4 tests)
- (5 files, +307/−2; commit `c457734`)

## Migration / Schema Statement
- **No schema change, no new EF migration.** Reuses the existing `EvidenceChunks` table (`AddRagFoundation`). Verified the model already has all needed columns; no `dotnet ef migrations add` was run.

## Tests Run
- `dotnet test` (full suite) → **185 passed / 0 failed** (181 prior + 4 new). New `EvidenceIngestionServiceTests`: (1) creates claim-scoped chunks with embeddings; (2) idempotent on re-ingest (0 dupes); (3) never touches/references another claim's evidence (seeded CLM-1006 chunk untouched, new chunks contain no other-claim id); (4) empty/whitespace text creates nothing. Full-app WebApplicationFactory endpoint tests still green (confirms the new controller DI resolves).

## Local Smoke Evidence
- Consolidated: the ingestion logic is covered by the 4 unit tests; the wired controller/DI by the existing endpoint tests (all green). The authoritative end-to-end is the live Azure smoke below (same deployed image/path a manager uses), avoiding a redundant LocalDB instance.

## Commit / Push State
- Committed to `rag/local-foundation-mega-v0.1` (`feat(rag): ingest claim documents into evidence chunks`, `c457734`). Pushed `4067591..c457734` to origin feature branch. **No main, no merge, no force.** Secret scan clean.

## Azure Deploy State
- `docker build server/Dockerfile` (from `c457734`) → GHCR `ghcr.io/slavkan777/insuranceai-api:rag-ingest-c457734-20260606233951` (digest `c062dd58…`). `az containerapp update` → new revision **`iap-demo-api--0000004`** (Healthy/Running/100%) with `Rag__QdrantEnabled=false` + `Rag__LocalLlamaEnabled=false` preserved. No SWA redeploy (no frontend change — the upload UI was already present). Rollback target: `iap-demo-api--0000003`.

## Azure Smoke Matrix
| # | Check | Result |
|---|---|---|
| A | `/health`, `/api/claims`, `/rag/infrastructure` | 200 / 200 / 200, honest fallback (vector=in-memory-hash, LLM disabled, Mock) |
| B | create claim + upload evidence | CLM-1029 created; upload → **"4 RAG evidence chunk(s) ingested for CLM-1029"** |
| C | RAG on new claim (API question) | `Mock`, **confidence 38**, **4 citations all `CLM-1029-uploaded-*`**, advisory; answer cites the uploaded text |
| C-UI | full UI path | CLM-1031 created in UI → upload modal → "2 chunks ingested" → coverage → **2 citations `CLM-1031-uploaded-*`** (cited; conf 0 for that question — honest hash-embedding behavior) |
| D | negative (no evidence) | CLM-1030 → conf 0, **0 citations**, "insufficient evidence / human review" |
| D | non-existing | CLM-9999 → **404 `CLAIM_NOT_FOUND`** (not 500) |
| E | regression CLM-1006 | conf 40, 4 citations all `CLM-1006-*` (seeded intact) |
| E | regression CLM-1012 | conf 0, 0 citations (still insufficient) |
| F | diagnostic chunk counts | CLM-1029=4, CLM-1031=2, CLM-1030=0, CLM-1006=13; SQL total **56** (=50 seeded + 6 new) |

## New Claim Evidence Ingestion Proof
- Upload response messages explicitly report ingested chunk counts (4 for CLM-1029, 2 for CLM-1031). `/rag/infrastructure` (approved diagnostic, not direct SQL) confirms `embeddedChunks` = 4 / 2 for those claims and 0 for the no-evidence claim. Persistence is via the **application runtime** (deployed app's managed connection), not ad-hoc SQL.

## New Claim RAG Citation Proof
- CLM-1029 coverage: 4 citations, every chunkId `CLM-1029-uploaded-doc-197e7950-{0..3}`; answer text is the uploaded evidence (bumper damage, repair $1500, Auto Comprehensive coverage). CLM-1031 (UI): 2 citations, both `CLM-1031-uploaded-*`. Advisory banner present.

## Negative / No-Evidence Proof
- CLM-1030 (created, no upload): `Mock`, confidence 0, 0 citations, "Недостатньо релевантних доказів… Рекомендується перегляд людиною." Old safe behavior preserved.

## Cross-Claim Leakage Proof
- API: `leakage_new1_to_seeded = false`, `new1_all_scoped = true`. UI: `leakToSeeded = false`. No new-claim answer ever cites CLM-1006/1007/1012; the retrieval `ClaimId == claimId` filter + ClaimId-only chunk writes guarantee isolation. Unit test (3) asserts the same at the data layer.

## Regression Proof: CLM-1006 / CLM-1012
- CLM-1006 still returns its seeded citations (`CLM-1006-application/coverage-check/police/policy-terms`, conf 40). CLM-1012 still insufficient (0 citations). Seeded `EvidenceChunks` count unchanged at 50 (total 56 includes only the 6 new synthetic chunks).

## Screenshots / Artifacts
Under `gpt-handoff/InsuranceAIPlatform/azure-new-claim-evidence-ingestion-to-rag-v0.1/`: `1-created-claim-detail.png`, `2-evidence-uploaded.png`, `3-rag-cited-answer-new-claim.png`, `4-negative-no-evidence.png`, `5-narrow-evidence.png` (synthetic data, no secrets/PII).

## Network / Console Findings
- Create/upload/RAG all 200. Console noise = benign `404`s (freshly-created claims' empty sub-resources) + `net::ERR_ABORTED` (rapid nav). No JS exceptions, no 500s, no auth errors.

## Rollback / Cleanup State
- No rollback needed (smoke passed). Rollback target recorded: `iap-demo-api--0000003` (image `…14d5c81`→ actually previous RAG image `rag-fallback-4067591-…`); revision-activate or image-redeploy both available. No temp firewall rule created (ingestion runs in-app, no workstation SQL access needed). No seeded data touched. **Leftover synthetic (no delete UI):** claims CLM-1029/1030/1031 + their uploaded docs + EvidenceChunks + the create-flow customers; all `E2E-RAG`-prefixed/synthetic — listed for optional later cleanup.

## Boundaries Honored
NO main · NO merge · NO production · NO paid provider · NO Azure Qdrant/Ollama (kept disabled) · NO OpenAI/DeepSeek/Azure OpenAI key · NO secrets printed/stored · NO real PII (all `E2E-RAG` synthetic) · NO direct DB business writes (app runtime only) · NO mutation of seeded CLM-1006/1007/1012 · NO fake pass (citations real, >0, claim-scoped) · NO force push · NO broad policy/vehicle/amount/status rewrite.

## Deviations
- Phase 4 (separate local smoke) consolidated into unit + endpoint tests + the live Azure smoke (explained above) — no separate LocalDB API instance, to avoid redundant state.
- Confidence under the hash-embedding fallback depends on question↔evidence token overlap: a well-matched question scores 38 (API), a less-matched fixed button scores 0 but still cites the claim's own chunks. Honest fallback characteristic, not a defect.

## Defects / Product Gaps Remaining
- Confidence is modest/variable in fallback mode (no live vector DB / LLM) — a managed embedder/LLM behind the existing seam would raise it (separate future gate).
- Chunking is simple paragraph-split (demo-scale); fine for synthetic evidence.
- Other create-from-zero gaps from the prior gate remain out of scope here (policy creation, standalone vehicle, amount/status fields, delete UI).

## Slava Manual Checklist
1. Open a NEW claim (or create one) → **Documents & photos** → "Upload document" → paste some Ukrainian evidence text → save (toast shows "N RAG evidence chunk(s) ingested").
2. Go to **AI Evidence** → "Coverage" → confirm the answer now cites YOUR uploaded text, scoped to that claim, advisory-only.
3. Open a claim with no uploaded evidence → confirm it still says "insufficient evidence".
4. Spot-check CLM-1006 still shows its original seeded citations.

## Recommended Next Step
- Architect GPT audit via «отчёт». This makes the create-from-zero flow demo-complete (intake → evidence → cited AI). Optional future gates: managed embedder/LLM behind the seam for higher confidence; auto-suggest evidence chunking; cleanup of `E2E-*` synthetic records.

STOP after report. No policy/vehicle/amount/status/delete/real-provider work in this gate.
