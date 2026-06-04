REQUEST_ID: REQ-2026-06-04-rag-completion-mega-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-implementation-continuation
PROJECT: InsuranceAIPlatform
GATE: RAG_COMPLETION_MEGA_V0.1
MODE: IMPLEMENTATION_CONTINUATION_WITH_FULL_VERIFICATION / NO COMMIT / NO PUSH / NO AZURE_DEPLOY
COMPLETED: 2026-06-04
COMPLETED_BY: claude

## Current State

The local RAG "Claim Evidence Intelligence" foundation is now **complete and fully verified
on the developer machine** — backend, frontend, golden corpus, evaluation harness, cross-claim
similar-claims search, the disabled-by-default local-LLaMA seam, and a Playwright UI E2E suite
with browser screenshots. Everything runs **100% locally** (deterministic embeddings + in-SQL
cosine retrieval + a grounded mock generator). No cloud, no paid AI, no Azure resource was
created or touched. All product-repo changes remain **uncommitted** on feature branch
`rag/local-foundation-mega-v0.1` per the request MODE.

Provider label for the answer path: **MOCK** (advisory-only grounded generator). The real-LLM
seam (LocalLlama/Ollama) is wired but **disabled by default** and was confirmed to fall back to
MOCK when no local model is running.

## Current Gate

`RAG_COMPLETION_MEGA_V0.1` — acceptance bar (from request): builds/tests pass + UI E2E or manual
browser evidence + cross-claim similar works safely + corpus/eval expanded beyond starter + no
boundary violations. **All five are met** (see Verification + E2E). One UI label — PERSISTENCE
(reload) — is an intentional SKIP at the frontend layer; backend persistence is verified at the
DB + integration level (see Known Limitations). If the auditor treats UI-reload persistence as
mandatory for this gate, downgrade to PARTIAL_READY_FOR_AUDIT; otherwise READY_FOR_AUDIT.

## What Changed (since the foundation report)

This continuation built on the published foundation (REQ ...rag-local-foundation-mega) and added:

**A — Golden corpus expanded beyond the starter set**
- 6 claims (CLM-1006 … CLM-1011), 8 policy clauses, **50 evidence chunks**, **21 evaluation questions**.
- Chunk distribution: CLM-1006=13, CLM-1007=6, CLM-1008=6, CLM-1009=7, CLM-1010=10, CLM-1011=8.
- Scenarios span: covered ДТП, exclusion (not-covered), missing police report, missing invoice,
  inflated invoice, photo/description mismatch, low-risk, high-risk advisory, approval summary, similar-claim.
- Seeder is **additive / idempotent** (top-up by existing-id HashSets — no deletes, no destructive cleanup).
  File: `server/InsuranceAIPlatform.Services.AiAnalysis/Rag/Persistence/RagSeeder.cs`.

**B — Cross-claim similar-claims search (safe)**
- New `Rag/Retrieval/SimilarClaimsRanker.cs`: builds an L2-normalized **claim-level centroid** from each
  claim's cached chunk embeddings, ranks other claims by centroid cosine, **excludes self**, returns
  **claim-level cards only** (`ClaimId`, `Score`, `Reason`, shared `MatchingCategories`) — **never** any
  other claim's evidence text.
- `Rag/IRagChunkSource.cs` + `DbRagChunkSource.cs`: added `GetAllChunksAsync` (centroids); per-claim
  retrieval stays strictly scoped via `GetClaimChunksAsync` (leakage guard intact).
- Service: `RagService.FindSimilarClaimsAsync`. Contract: `SimilarClaim` record.
- API: `GET /api/claims/{claimId}/rag/similar-claims?topK=` → `SimilarClaimsResponseDto`
  (`server/InsuranceAIPlatform.Api/Controllers/RagController.cs:104-119`).

**C — Evaluation harness (retrieval "ruler")**
- New `server/InsuranceAIPlatform.Tests/RagEvalHarnessTests.cs`: aggregate **recall@4 ≥ 0.6** over the
  gold question set **and** asserts mustNotCite chunks (cross-claim) are **never** retrieved.

**D — Local-LLaMA / Ollama seam (disabled by default)**
- `Rag/Generation/LocalLlamaGroundedAnswerGenerator.cs`: real-LLM seam behind
  `RagOptions.LocalLlamaEnabled=false`; falls back to the MOCK grounded generator when disabled or
  when no local model answers. CI/build/tests never require a running model.

**E — Frontend "Claim Evidence Intelligence" panel completed**
- `src/components/claim/ClaimEvidenceIntelligencePanel.tsx`: 6 use-case buttons + custom question;
  "Find similar claims" renders **claim-level cards** (id / score / matching categories / "Open claim"
  navigation) with **no evidence text**.
- Redux/Saga: `src/features/rag/{ragSlice,ragSaga,ragSelectors}.ts` — added
  fetchSimilarClaims / similarClaimsReceived / similarClaimsFailed.
- API layer: `ragSimilarClaims` added to `insuranceApi.types.ts`, `mockInsuranceApi.ts`,
  `backendInsuranceApi.ts`. Mock returns synthetic claim-level cards (no evidence text).

**F — Playwright UI E2E + screenshots**
- New spec `e2e/22-rag-evidence.spec.ts`; mock-only config `playwright.mock.config.ts`
  (no .NET webserver). API mode pinned to MOCK via `.env.development.local`.
- No new `data-testid` needed — stable selectors already present
  (`rag-panel`, `rag-btn-*`, `rag-answer-card`, `rag-advisory-banner`, `rag-answer-text`,
  `rag-citations-table`, `rag-meta-provider`, `rag-similar-claims`, `rag-similar-card-*`).

## Verification (evidence)

| Check | Command / source | Result |
|---|---|---|
| Backend + all tests | `dotnet test` (InsuranceAIPlatform.Tests) | **158 / 158 passed**, exit 0 |
| Seeder counts (unit) | `RagSeederTests.cs:25-28` | 8 clauses / 50 chunks / 13 CLM-1006 / 21 questions |
| Eval recall@4 + no leak | `RagEvalHarnessTests.cs:24-54` | recall@4 ≥ 0.6, 0 mustNotCite retrieved |
| Similar-claims unit | `RagSimilarClaimsTests.cs` | 3/3: excludes self, ordered, shared categories, unknown→empty |
| Frontend build | `npm run build` | exit 0 |
| Real SQL re-seed (LocalDB) | DbMigrator run + count report | PolicyClause=8, EvidenceChunk=50, RagEvaluationQuestion=21, RagAuditTrace=2 |
| HTTP smoke — ASK | `POST /api/claims/CLM-1006/rag/ask` | 200, 4 citations, ProviderMode=**Mock**, AdvisoryOnly=true |
| HTTP smoke — SIMILAR | `GET /api/claims/CLM-1006/rag/similar-claims` | CLM-1010 (0.66) / CLM-1011 (0.53) / CLM-1009 (0.53); **no evidence-text fields** |
| HTTP smoke — LEAK GUARD | `GET /api/claims/CLM-1009/rag/evidence-search` | 0 chunks from any other claim |
| HTTP smoke — EVAL | `GET /api/claims/CLM-1006/rag/evaluation-questions` | 4 questions returned |
| LocalLlama seam | options `LocalLlamaEnabled=false`; Ollama probe | **disabled default**; Ollama **SKIPPED_NOT_AVAILABLE** → MOCK fallback |

## E2E (Playwright — browser, MOCK mode)

- **Run:** `npx playwright test 22-rag-evidence --config playwright.mock.config.ts --reporter=list` → **exit 0**
- **Result: 7 passed, 1 skipped.** Screenshots written under `test-results/` (verified on disk, ~88–130 KB each).

| # | Label | Assertion | Result | Screenshot |
|---|---|---|---|---|
| 1 | MECHANICAL_PASS | CLM-1006 panel + all 6 use-case buttons render | PASS | rag-01-mechanical-panel-render.png |
| 2 | SEMANTIC_PASS | "Check policy coverage" → answer card + citations + advisory banner | PASS | rag-02-semantic-coverage-answer.png |
| 3 | SEMANTIC_PASS | "Find missing documents" → answer renders | PASS | rag-03-semantic-missing-docs.png |
| 4 | SEMANTIC_PASS | "Explain risk" → answer renders | PASS | rag-04-semantic-risk-answer.png |
| 5 | NEGATIVE_PASS | risk answer contains **no** fraud-accusation words | PASS | rag-05-negative-no-fraud-word.png |
| 6 | NEGATIVE_PASS | "Find similar claims" → claim-level cards only, **no** evidence text | PASS | rag-06-negative-similar-no-evidence.png |
| 7 | PERSISTENCE_PASS | audit row survives reload | **SKIPPED** (intentional) | — |
| 8 | Cross-claim isolation | CLM-1009 coverage answer cites CLM-1009, not CLM-1006 evidence | PASS | rag-07-cross-claim-clm1009-isolation.png |

## Manual / Browser UI

Browser-rendered evidence is the 7 Playwright screenshots above (real Chromium render, MOCK data).
Screenshot 02 shows the advisory banner + answer text citing CLM-1006 + Provider=Mock + a 2-row
citations table. Screenshot 06 shows 3 similar-claim cards with **no** citations table. Screenshot 07
shows a CLM-1009 answer containing "CLM-1009" and not CLM-1006 document ids (isolation holds in the UI).

## Boundaries Honored

- **No commit. No push. No Azure deploy.** Product-repo changes uncommitted on `rag/local-foundation-mega-v0.1`.
- **No Azure resource** (no AI Search / Foundry endpoint / Container Apps GPU) created or touched.
- **No paid AI, no real DeepSeek-by-default.** Answer path is MOCK; LocalLlama disabled by default.
- **No running LLaMA/Ollama required** for build/tests/CI (verified seam falls back to MOCK).
- **No secrets** read, printed, logged, pasted, or committed. **No real PII / customer data** — corpus is synthetic.
- **No destructive ops** — seeder is additive; no DB/table/resource deleted; no force-push; AIKB untouched.
- **origin/main not touched** in the product repo.

## Known Limitations

1. **PERSISTENCE_PASS is a SKIP at the UI layer.** The panel uses in-memory Redux (`ragSlice`); on
   reload, panel state resets to idle and there is no audit-history sub-component on the `ai-evidence`
   route to re-hydrate. **Backend persistence is verified** (RagService.AskAsync persists a
   `RagAuditTrace`; LocalDB showed RagAuditTrace=2; `GET .../rag/audit` returns rows). To turn the skip
   into a green PERSISTENCE_PASS: render an audit-history panel from `GET /rag/audit` and un-skip test #7.
2. **Answer generator is MOCK (advisory-only).** Real grounded generation requires enabling the
   LocalLlama seam with a local model — out of scope for this no-paid-AI gate.
3. **Retrieval is lexical-deterministic** (feature-hashing embeddings), chosen for zero-dependency
   determinism, not semantic-embedding quality. Good enough for the eval threshold; a real embedding
   model is a future swap behind `IEmbeddingProvider`.

## Next Safe Step

Hand to Architect GPT for audit. On approval, the natural follow-ups (each its own gated request):
(a) audit-history UI panel + un-skip PERSISTENCE test; (b) optional LocalLlama enablement smoke on a
machine with Ollama; (c) only then any Azure/cloud planning. No commit/push until explicitly authorized.

## Not Touched

TwinCore framework repo; Azure (any resource); product-repo git history (no commit/push); AIKB;
secrets; the active TwinCore bridge request and its uncommitted report (left intact — not mine to publish).
