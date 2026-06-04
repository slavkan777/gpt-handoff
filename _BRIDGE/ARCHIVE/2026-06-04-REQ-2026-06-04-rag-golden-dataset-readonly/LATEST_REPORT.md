REQUEST_ID: REQ-2026-06-04-rag-golden-dataset-readonly
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-readonly-architecture-audit
PROJECT: InsuranceAIPlatform
GATE: RAG_GOLDEN_DATASET_AND_SCHEMA_DISCUSSION_V0.1
MODE: READ-ONLY DISCUSSION — no files/code/DB/Azure changed
COMPLETED_BY: claude

# RAG + Vector Search + LLaMA — Golden Dataset & Schema Planning (read-only)

> Grounded in the **actual** repo at `C:\Projects\InsuranceAIPlatform\` (.NET 9 / EF Core 9,
> 7 bounded contexts, React+Redux-Saga) and in the measured Azure cost audit
> (`REQ-2026-06-03-azure-sql-cost-audit-readonly`). Nothing was changed. No secrets/PII printed.

---

## 1. Executive summary for Slava

**Yes — start with the golden synthetic dataset + evidence corpus + a gold evaluation set. Do not stand up a vector DB first.**

Three reasons:

1. **You can't measure or demo RAG without a ruler.** A RAG system is only "good" relative to a
   gold Q→expected-sources→expected-answer set. Build the ruler (eval set) before the machine.
2. **The substrate already exists.** `ClaimDocument.Content` (nvarchar(max)) already stores
   document text; CLM-1006 already has 7 documents. The gap is *curated, chunked, citable evidence*
   + a *gold Q&A set* — pure data work, **zero new infra, zero Azure spend**.
3. **It de-risks every later decision.** With a golden corpus + eval harness you can swap
   Mock → in-SQL cosine → Azure AI Search and *prove* parity. Without it you are guessing.

**Headline anti-overengineering call:** at the corpus size that actually fits this portfolio
(~**150–400 chunks**), **you do not need a vector database at all** for v1. Embeddings cached in a
SQL column + in-process cosine similarity is correct, free, and fully interview-defensible. Azure
AI Search becomes a *Phase-6 "because the live demo wants the Azure logo"* decision — not a
technical necessity. Keep the project at the measured **~$1/mo** until a live demo needs more.

Strongest portfolio story: *"eval-driven RAG with a golden dataset, grounded citations, and
regression tests, provider-agnostic from Mock → local Llama → DeepSeek → Azure AI Search, all
disabled-by-default for cost."* That beats *"I wired up a vector DB."*

---

## 2. Business use cases

Current AI (`MockAiProvider` default; `RealDeepSeekAiProvider` opt-in) is **stateless chat
completion** — no retrieval, no citations. RAG adds grounded, cited, auditable evidence.

| Use case | Who | Pain solved | Data needed | UI evidence shown | Why SQL-only fails | Why vectors help | What LLaMA/LLM adds |
|---|---|---|---|---|---|---|---|
| **Policy coverage check** | Adjuster | "Is this damage covered?" | PolicyClause text, claim facts | clause citation + verdict | clauses are prose, not columns | semantic match damage↔clause | plain-language coverage explanation |
| **Missing-document detection** | Adjuster/ops | incomplete file | required-doc checklist vs present docs | "missing: police report" + why | needs semantic doc-type match | matches "ДТП протокол" ≈ police report | explains *why* each doc is needed |
| **Claim summary** | Adjuster/manager | long file, no time | all claim docs | 5-line grounded summary + sources | no summarization in SQL | retrieves the few relevant chunks | the summarization itself |
| **Risk explanation** | Adjuster/SIU | "why high risk?" | AiRiskSignal + evidence | factors + cited evidence | scores have no narrative | links signal→source chunk | narrative, **advisory, no fraud accusation** |
| **Similar-claims search** | SIU/manager | precedent/fraud rings | corpus across claims | top-k similar + why | keyword misses paraphrase | embedding similarity is the core | summarizes the similarity |
| **Approval support** | Approver (human) | decision packet | everything above | one packet + audit trace | — | — | assembles the human-in-the-loop packet |
| **Audit-friendly explanation** | Compliance | traceability | RagAuditTrace | query→chunks→answer→cost | — | — | reproducible grounded answer |

Boundary (already enforced in `RealDeepSeekAiProvider` guardrails): **AI is advisory only; the
human approves/denies.** RAG must *strengthen* this — every answer carries citations a human checks.

---

## 3. Golden DB / corpus recommendation

**Principle: depth over breadth.** Keep the 200 synthetic customers/policies/vehicles you already
have (they create dashboard realism); invest depth in a small set of fully-fleshed "golden RAG
claims."

| Element | Recommended count | Why this is enough |
|---|---|---|
| Customers / policies / vehicles | **200 (keep as-is)** | already seeded; more adds cost, not signal |
| **Golden RAG claims (deep)** | **4–6** (CLM-1006 + 3–5) | enough for "similar claims" to be real; deep > 200 shallow |
| Other claims (shallow) | 10–15 (keep CLM-1007..1020) | dashboard variety only |
| Documents per golden claim | **6–10** (CLM-1006 has 7 ✓) | mirrors a real auto-claim file |
| Document types | **7–8** | application, policy schedule, **policy clauses**, police report, repair invoice, damage-photo captions, customer statement, adjuster note |
| **Chunks per document** | **3–12** (~250–400 tokens each) | clause-/paragraph-aligned |
| **Total chunks (corpus)** | **~150–400** | sweet spot: real retrieval, brute-force cosine OK, ~2 MB embeddings |
| Policy clauses | **15–30** | powers coverage check + exclusions/limits/deductible |
| Audit notes | 3–6 per golden claim | audit trail realism |
| Risk signals | reuse `AiRiskSignal` | already modeled |
| **Gold evaluation questions** | **30–60 Q&A pairs** | covers all 6 use cases + negatives |

**Why not huge:** embedding cost, index cost, and review effort scale with size while *demo
signal* saturates early. 300 well-curated, cross-referenced chunks demonstrate retrieval quality,
citation accuracy, and cross-claim isolation better than 50k random rows — and stay free.

---

## 4. Proposed data model

**Rule: SQL is source of truth; map onto what exists; don't split entities reality already unifies.**
The repo models PoliceReport / RepairInvoice / CustomerStatement / photo-metadata as
`ClaimDocument` rows with a `Kind`/`DocType` discriminator — **keep that**, don't create 5 new tables.

**A. Source-of-truth (mostly exists today):**

| Entity | Status | Action |
|---|---|---|
| SyntheticCustomer, Policy, Vehicle, Claim | ✅ exists | none |
| ClaimDocument (`Content`, `Kind`, `DocType`, `AiConfidence`) | ✅ exists | reuse as the document store |
| AuditEvent, AiRiskSignal, AiAnalysisRun | ✅ exists | reuse; AuditEvent already covers OperatorNote |
| **PolicyClause** | 🆕 small new table | clauseId, policyId/productCode, type (coverage/exclusion/limit/deductible), text, ordinal — powers coverage check |

**B. RAG-specific (new; all in SQL):**

| Table | Key fields | Note |
|---|---|---|
| **EvidenceChunk** | chunkId, documentId→ClaimDocument, claimId, ordinal, text, tokenCount, **chunkHash**, language, sourceVersion | the retrievable unit |
| **ChunkEmbedding** | chunkId, embeddingModel, vector (blob/`float[]`), dim, createdAt | **separate** so re-embedding never rewrites chunk rows |
| **RagEvaluationQuestion** | questionId, useCase, claimId, text, language | the gold eval set |
| **ExpectedAnswer** | questionId, expectedAnswerText, expectedSourceChunkIds[], mustNotCiteChunkIds[] | grader truth + negative guard |
| **RetrievalQuery** | queryId, claimId, userId, text, topK, createdAt | a real retrieval event |
| **RetrievedEvidence** | queryId, chunkId, score, rank | what came back |
| **GroundedAnswer** | answerId, queryId, answerText, confidence, model, promptTokens, completionTokens, costMicros | the generated answer |
| **CitationReference** | answerId, chunkId, documentId, snippet | what the UI shows |
| **RagAuditTrace** | traceId, queryId, answerId, providerMode, retrievalMs, genMs, totalCostMicros, createdAt | reproducibility |

**C. Vector-index metadata** (columns on `ChunkEmbedding` + a thin `VectorIndexEntry` when AI Search
is used): embeddingModelName, vectorIndexId, externalIndexDocId, chunkHash, indexingStatus,
indexedAt, sourceVersion, language, and filter keys claimId / policyId / customerId.

---

## 5. SQL vs Vector vs LLaMA responsibilities

| Layer | Owns | Rule |
|---|---|---|
| **SQL Server (source of truth)** | claims, policies, clauses, documents, chunks, **embeddings cache**, eval set, audit, cost | nothing lives *only* in the vector index |
| **Vector index (derived)** | similarity lookup over chunk vectors | 100% **rebuildable from SQL**; a cache, not a database of record |
| **LLM / LLaMA (generation)** | explanation, summary, grounded answer | **never** source of truth; **retrieval-before-generation** always |
| **Audit** | query→chunks→answer→tokens→cost | every answer reproducible |

**Rebuild path:** `SQL chunks → embed (cached by chunkHash) → upsert into index`. Drop the index any
time; rebuild deterministically. This is the property that lets AI Search be disabled-by-default.

---

## 6. Recommended stack decision

| Option | Fit | Cost | Demo/interview value | Complexity | When | Now? |
|---|---|---|---|---|---|---|
| **In-SQL embeddings + in-process cosine** | ⭐ ideal for ~300 chunks | **$0** (≈2 MB) | high ("knew not to overengineer") | low | **v1 default** | **YES** |
| **B. PostgreSQL + pgvector** | good OSS local story | $0 local | high | med (adds Postgres beside SQL Server) | local OSS alt | optional |
| **A. Azure AI Search (hybrid)** | the enterprise logo | **paid** (Free/Basic tier; teardown) | very high for live Azure demo | med | live demo only | **later (P6–P7), disabled-by-default** |
| **C. Qdrant** | great product | low self-host | medium | med (another service) | if scale grows | no |
| **D. Neo4j / Graph** | relationship/fraud rings | paid/host | niche-high | high | future | **no — no business reason yet** |
| **E. Mock retrieval first** | proves seam | $0 | enables eval harness | trivial | P3 | **YES (before even cosine)** |

**Decision:** E → in-SQL cosine for v1; AI Search as a Phase-6 flourish behind a flag; pgvector only
if you want an explicit OSS local story; **Qdrant/Neo4j deferred.** "Similar claims" is satisfied by
vector cosine — it does **not** justify a graph DB.

---

## 7. LLaMA options

Extend the existing `IAiProvider` pattern — RAG generation is just another provider mode.

| Provider (new) | Runtime | Default | Cost | Use |
|---|---|---|---|---|
| **MockAiProvider** | in-proc | ✅ **stays default** | $0 | tests, CI, offline demo |
| **RealDeepSeekAiProvider** | api.deepseek.com | opt-in (exists) | cheap, flagged | cheapest "real LLM" today |
| **LocalLlamaProvider** | Ollama / LM Studio / llama.cpp | opt-in (dev) | $0 (local GPU/CPU) | the real-LLM dev loop |
| **AzureFoundryLlamaProvider** | Azure AI Foundry serverless Llama | **disabled-by-default** | paid per-token | Azure live demo |
| **ContainerAppsGpuLlamaProvider** | ACA GPU | future only | **expensive** | advanced/never-for-portfolio |

Rules: **retrieval before generation** (LLM only sees retrieved chunks); **LLaMA is never source of
truth**; **Mock stays default**; cost controlled by flags + token caps + caching.

---

## 8. UI concept

Anchor: existing **`AiEvidencePage.tsx`** (tab `ai-evidence`) + **`aiReviewSlice`** + existing BFF
`POST /api/claims/{id}/ai-analysis/run` (extend, don't rebuild). New saga polls/streams; no
architectural change.

**Panel "Claim Evidence Intelligence"** — buttons: `Check policy coverage` · `Find missing
documents` · `Explain risk` · `Find similar claims` · `Prepare approval summary` · `Ask a question`.

Each answer card shows: **answer · confidence · evidence cards (doc title + snippet + open-source
link) · retrieved chunk ids · cost/tokens · audit-trace id · persistent banner "AI advisory only —
human makes the final decision."**

---

## 9. Test / evaluator plan

Extend Playwright (`14-ai-deep.spec.ts`) + a new `22-rag-evidence.spec.ts`, driven by the gold set.

| Pass type | What it asserts |
|---|---|
| **MECHANICAL_PASS** | panel renders, buttons clickable, cards display |
| **SEMANTIC_PASS** | question → retrieval returns **expected source chunks**; answer **cites correct documents**; answer is **grounded in retrieved chunks** (no unsupported claims) |
| **PERSISTENCE_PASS** | `GroundedAnswer` + `RagAuditTrace` + cost survive reload (DB-backed) |
| **NEGATIVE_PASS** | **CLM-1006 evidence must NOT surface for CLM-1007** (fixture-leak guard via `mustNotCiteChunkIds`); missing-doc detection correct; **high-risk explanation never accuses fraud** (advisory guardrail) |

Eval harness: iterate `RagEvaluationQuestion` → run retrieval+generation → score retrieval
(recall@k of expected chunks) and grounding (citations ⊆ retrieved). Track as a regression metric
across provider swaps (Mock vs cosine vs AI Search) to prove parity.

---

## 10. Cost / security guardrails (tied to measured audit)

From `REQ-2026-06-03-azure-sql-cost-audit-readonly`: SQL is **Serverless GP_S_Gen5_1, auto-pause
60 min, currently Paused, ~$1/mo**, Container App **scales to zero**, **AI Search not created**, SWA
**Free**. Preserve all of that.

| Guardrail | Mechanism |
|---|---|
| Frontend always-free | SWA Free (exists) |
| SQL cost flat | serverless auto-pause stays; embeddings add ~2 MB (negligible) |
| **AI Search not created until approved** | Bicep `enableAi=false` (exists); created only at P7, **teardown after demo** |
| LLaMA Azure off | `AzureFoundryLlamaProvider` disabled-by-default; local Ollama = $0 |
| Real LLM gated | `AiProvider:RealCallsEnabled=false` (exists); token caps + timeout |
| Embeddings cheap | one-time batch, **cached by chunkHash**, never re-embed unchanged chunks; local embed model possible |
| Mock default | `AiProvider:Mode=Mock` (exists) |
| Budget alerts | Bicep budget module + manual demo activation + parking/teardown plan |
| Secrets | `DEEPSEEK_API_KEY` env, presence-checked only (exists); Key Vault for Azure; never in repo |
| PII | synthetic only; no real names/VINs/plates |

---

## 11. Phased gates (refined from the proposed 10 — do NOT implement yet)

| # | Gate | Note |
|---|---|---|
| 1 | `RAG_BUSINESS_VALUE_AND_GOLDEN_DATASET_PLAN_V0.1` | **this report → approval** |
| 2 | `RAG_SCHEMA_AND_GOLDEN_SEED_IMPLEMENTATION_V0.1` | PolicyClause + EvidenceChunk + ChunkEmbedding + eval set; deterministic seed |
| 3 | `LOCAL_MOCK_RAG_RETRIEVAL_V0.1` | `IAiRagProvider` seam + in-SQL cosine + mock generation |
| 4 | `LOCAL_LLAMA_PROVIDER_V0.1` | **moved earlier** — real LLM + real retrieval in dev before any Azure |
| 5 | `CLAIM_AI_EVIDENCE_ASSISTANT_UI_V0.1` | AiEvidencePage panel |
| 6 | `RAG_E2E_SEMANTIC_EVIDENCE_V0.1` | eval harness + 4 pass types |
| 7 | `AZURE_AI_SEARCH_VECTOR_INDEX_PLANNING_V0.1` | only if a live Azure demo is wanted |
| 8 | `AZURE_AI_SEARCH_VECTOR_INDEX_IMPL_DISABLED_BY_DEFAULT_V0.1` | created + torn down per demo |
| 9 | `AZURE_LLAMA_PROVIDER_PLANNING_ONLY_V0.1` | planning only |
| 10 | `GRAPH_RELATIONSHIP_RISK_LAYER_FUTURE_V0.1` | future; only with a real fraud-ring business case |

Only change vs proposal: **promote Local LLaMA ahead of the UI gate** so the dev loop is
real-retrieval + real-LLM before touching Azure.

---

## 12. Risks and anti-goals

**Anti-goals:** vector DB before the eval set · splitting ClaimDocument into 5 tables reality already
unifies · embeddings as the only home of any data · 200 deep claims (depth over breadth) · always-on
AI Search / ACA GPU · graph DB without a fraud-ring case.

**Risks & mitigations:** synthetic realism → keep clause/coverage/claim facts internally consistent ·
eval overfitting → hold out a few questions · multilingual (golden data has RU like "Роберт Джонсон")
→ pick an embedding model strong in RU+EN, decide eval language (Q5) · cross-claim leakage → the
NEGATIVE_PASS guard is mandatory · cost creep → every paid resource flag-gated + teardown.

---

## 13. Final recommendation

1. **Approve gate 1.** Build the **golden dataset + evidence corpus + 30–60 gold Q&A eval set** first
   (data only, $0).
2. **v1 retrieval = in-SQL embeddings + cosine.** No vector DB. Prove it with the eval harness.
3. **Provider-agnostic generation:** Mock default → Local Llama (Ollama) for the real dev loop →
   DeepSeek opt-in. Azure Foundry Llama disabled-by-default.
4. **Defer all paid Azure RAG infra** (AI Search) behind `enableAi=false`; create only for a live
   demo, tear down after. **Graph DB = future.**
5. Keep the project at the measured **~$1/mo** until a live demo genuinely needs the Azure logo.

This is the lowest-cost path that still yields the strongest, most defensible portfolio narrative.

---

## 14. Questions for Slava (max 5)

1. **Demo target:** is a *live Azure* demo (justifies the AI Search logo, gates 7–8) required, or is
   local-only sufficient for interviews? — drives whether paid RAG infra ever gets built.
2. **Generation LLM for RAG:** extend existing **DeepSeek**, add **local Llama (Ollama)**, or both?
   (proposed default: Mock + Local Llama for dev, DeepSeek opt-in.)
3. **Golden RAG claims depth:** just **CLM-1006**, or **4–6** deep claims so "find similar claims" is
   meaningful? (recommend 4–6.)
4. **Eval/corpus language:** golden data is partly Russian — should the gold Q&A be **EN, RU, or
   both**? (affects embedding-model choice.)
5. **Scope of v1:** is **"find similar claims"** in scope for v1 (the main reason to want real
   vectors), or deferred so v1 is coverage/missing-docs/summary only?

---

## Boundaries honored

- No files/code/migrations created or edited. No DB changes. No Azure calls. No deploy.
- No commit/push in the product repo. No AIKB update.
- No secrets/API keys read or printed (provider key referenced by name only). No real PII — synthetic only.
- Read-only exploration of `C:\Projects\InsuranceAIPlatform\` to ground the report.

## Not touched

- Source repo files, EF migrations, appsettings, Bicep, Azure resources, DeepSeek key, vector/AI Search/LLaMA resources (none created).

## STOP LINE

Report only — a discussion/planning artifact. Nothing implemented; nothing marked done. Awaiting
Architect GPT audit («отчёт») and Slava's answers to the 5 questions before any gate-2 implementation.
