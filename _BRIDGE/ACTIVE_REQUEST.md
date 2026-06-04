REQUEST_ID: REQ-2026-06-04-rag-local-foundation-mega-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-implementation
PROJECT: InsuranceAIPlatform
TARGET_REPORT_PATH: _BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/rag-local-foundation-mega-v0.1/report.md
AIKB_UPDATE_REQUIRED: after-approval
CREATED: 2026-06-04T00:00:00+03:00
CREATED_BY: architect-gpt
GATE: RAG_LOCAL_FOUNDATION_MEGA_V0.1
MODE: IMPLEMENTATION_WITH_VERIFICATION / NO COMMIT / NO PUSH / NO AZURE_DEPLOY

# PROJECT
InsuranceAIPlatform / Auto Insurance Claim AI Workbench.
Expected local repo path: C:\Projects\InsuranceAIPlatform\

# CURRENT CONTEXT
A read-only planning report was accepted as planning input: REQ-2026-06-04-rag-golden-dataset-readonly.
Conclusion: start the RAG + Vector Search + LLaMA chapter with a golden synthetic dataset + evidence corpus + local retrieval foundation. Do NOT start by creating paid Azure AI Search, Graph DB, or Azure LLaMA resources.

Key rules:
1. SQL remains source of truth.
2. Vector search/index is derived and rebuildable.
3. For v1, do NOT create a separate Vector DB.
4. Use local/in-SQL embeddings or deterministic local embeddings + cosine similarity for about 150-400 chunks.
5. Azure AI Search is deferred to later disabled-by-default demo gate.
6. Graph DB is future-only unless real fraud-ring/multi-hop business case appears.
7. LLaMA is explanation/generation only, never source of truth.
8. Mock provider remains default.
9. Local LLaMA/Ollama seam may be added disabled-by-default.
10. All data must be synthetic, audit-friendly, evidence/citation based.

# WHY THIS TASK EXISTS
Slava wants one mega molecular prompt instead of 10 small prompts.
Goal: implement local RAG foundation inside InsuranceAIPlatform so the project can demonstrate:
- RAG business value;
- golden insurance evidence corpus;
- SQL-backed chunks/citations/audit;
- local vector-like semantic retrieval;
- LLaMA provider seam disabled-by-default;
- UI proof through Claim Evidence Intelligence;
- semantic/evidence tests.

# ABSOLUTE BOUNDARIES
DO NOT commit.
DO NOT push.
DO NOT deploy.
DO NOT touch origin/main.
DO NOT create Azure resources.
DO NOT create Azure AI Search.
DO NOT create Azure Foundry endpoint.
DO NOT create Container Apps GPU.
DO NOT enable real LLaMA in Azure.
DO NOT enable real DeepSeek by default.
DO NOT read, print, log, paste, or commit secrets or API keys.
DO NOT use real PII or real customer data.
DO NOT perform destructive cleanup.
DO NOT delete DB/resources.
DO NOT force-push.
DO NOT make paid Azure calls.
DO NOT update AIKB.

Allowed:
- Edit local source files inside InsuranceAIPlatform.
- Add EF model/migration/seed changes if needed.
- Add local deterministic/mock embedding and RAG retrieval.
- Add disabled-by-default LocalLlama/Ollama provider seam.
- Add UI panel.
- Add tests.
- Run local build/test/e2e where practical.
- Publish sanitized report to gpt-handoff.

# STOP CONDITIONS
Stop and report BLOCKED if:
- repo path cannot be confirmed;
- branch/state is unsafe;
- unrelated unstaged changes would be overwritten;
- implementation requires secrets, Azure paid service, main branch, destructive migration, or real provider enablement;
- build cannot be run due to environment;
- scope explodes beyond local RAG foundation.

# READ FIRST
Read before editing:
1. Repo root docs / README / solution structure.
2. Existing AI provider abstraction: IAiProvider / MockAiProvider / RealDeepSeekAiProvider or equivalent.
3. Existing AI Analysis Service / AiAnalysis bounded context.
4. Documents / Claims / AuditCost / Approval services.
5. EF DbContexts and migrations.
6. Seed / DbMigrator logic.
7. ClaimDocument model and seeded documents.
8. Existing AiEvidence UI page and Redux/Saga flow.
9. Existing Playwright E2E semantic assertion patterns, especially CLM-1006 leakage guard.
10. Existing gpt-handoff reporting conventions.

Echo this request id in the report:
REQ-2026-06-04-rag-local-foundation-mega-v0-1

# DONE STATE
Done only if practical items are satisfied or explicitly reported as blocked with evidence.

## 1. Local RAG foundation
- SQL is source of truth.
- Evidence chunks exist.
- Chunk embeddings or deterministic vector-like representation exists.
- Retrieval returns top-k chunks.
- Retrieval can filter by claimId / policyId / customerId where applicable.
- No data lives only in vector index/cache.

## 2. Golden synthetic RAG corpus
- Keep existing broad synthetic data if present.
- Enrich CLM-1006 and add 3-5 additional deep golden claims if practical.
- 6-10 documents per deep claim where practical.
- Add policy clauses.
- Add evidence chunks.
- Target 30-60 evaluation Q&A pairs, or a smaller starter set with explicit expansion path.

Scenario coverage:
- covered ДТП claim;
- not covered due to exclusion;
- missing police report;
- inflated repair invoice;
- photo/document mismatch;
- similar claims;
- high-risk advisory explanation, no fraud accusation;
- approval summary.

## 3. RAG persisted data
Implement/adapt minimal entities/tables using existing conventions:
- PolicyClause;
- EvidenceChunk;
- ChunkEmbedding or deterministic embedding cache;
- RagEvaluationQuestion;
- ExpectedAnswer;
- RetrievalQuery;
- RetrievedEvidence;
- GroundedAnswer;
- CitationReference;
- RagAuditTrace.
If some are merged/simplified, document why.

## 4. Retrieval services
Add/adapt:
- IRagRetrievalService or equivalent;
- IEmbeddingProvider or equivalent;
- deterministic/mock embedding provider for CI/dev;
- cosine or similarity ranking;
- top-k retrieval;
- retrieval/audit persistence.

## 5. LLaMA provider seam
- Add LocalLlamaProvider / Ollama provider or equivalent if practical.
- Disabled-by-default.
- Mock provider remains default.
- No real LLaMA call required for tests.
- No provider secrets.
- Failure fallback safe/honest.

## 6. API/BFF
Expose safe local RAG functionality, naming adapted to current API style:
- POST /api/claims/{claimId}/rag/ask
- GET /api/claims/{claimId}/rag/evidence-search
- GET /api/claims/{claimId}/rag/evaluation-questions
- GET /api/claims/{claimId}/rag/audit
Report the final route list.
Return DTOs with answer, confidence, citations, evidence snippets, traceId, cost/tokens if available, advisory-only flag.

## 7. Frontend UI
Add visible Claim Evidence Intelligence / AI Evidence Assistant UI, preferably inside existing AI Evidence / Claim Workspace page.
Buttons:
- Check policy coverage;
- Find missing documents;
- Explain risk;
- Find similar claims;
- Prepare approval summary;
- Ask custom question.
Each answer shows answer, confidence, evidence cards, cited document/source references, retrieved chunk IDs/source markers, cost/tokens if available, audit trace id, and banner: AI advisory only — human makes the final decision.
Integrate with Redux/Saga if current frontend architecture requires it.

## 8. Tests / semantic assertions
Add focused tests with evidence labels in report:
- MECHANICAL_PASS: panel renders / endpoints respond / buttons work.
- SEMANTIC_PASS: retrieval returns expected source chunks, answer cites correct docs, answer grounded in retrieved chunks.
- PERSISTENCE_PASS: GroundedAnswer/RagAuditTrace/cost survives reload or DB read.
- NEGATIVE_PASS: CLM-1006 evidence does not leak into other claims; high-risk explanation never accuses fraud; missing-doc detection is correct.

Backend tests should cover seed, retrieval, citations subset, audit persistence.
Frontend/E2E if practical should cover panel, question, answer, citations, leakage guard.

## 9. Verification
Run what is practical:
- dotnet build
- dotnet test
- frontend build
- targeted Playwright/E2E
- full Playwright only if practical
Do not claim success unless command actually ran. If failing, attempt reasonable in-scope fixes, then stop/report exact blocker if not resolved.

## 10. Handoff
Publish sanitized report to:
- _BRIDGE/LATEST_REPORT.md
- InsuranceAIPlatform/rag-local-foundation-mega-v0.1/report.md
- InsuranceAIPlatform/latest-report.md if project convention requires
- latest-summary.json if practical
No secrets, no API keys, no raw connection strings, no real PII.

# IN SCOPE
Local source implementation, EF/schema/migrations if needed, deterministic synthetic seed/corpus, SQL-backed RAG entities, mock/local embeddings, in-process cosine similarity, local retrieval API, disabled-by-default LocalLlama/Ollama provider seam, UI panel, Redux/Saga integration if needed, tests/evaluator, gpt-handoff report.

# OUT OF SCOPE
Azure AI Search creation, Azure Foundry LLaMA, Container Apps GPU, Azure deploy, paid AI resources, real provider default enablement, Graph DB, production-scale RAG, real data, production auth hardening, commit/push, main branch, AIKB update.

# PRESERVE
Existing demo flows, CLM-1006 golden scenario, SQL persistence, Mock provider default, DeepSeek opt-in disabled-by-default safety, E2E Semantic Assertion Rule, Azure cost posture, public UI layout where possible, branch/git history.

# MOLECULAR SUBPHASES

## Subphase 0 — Bootstrap / safety inspection
Confirm repo root, branch, HEAD, git status, origin/main/dev, untracked/modified files. Confirm no unrelated changes would be overwritten. Stop if unsafe.

## Subphase 1 — Final micro-plan
Before editing, decide smallest safe design: existing models/services reused, new entities, migration strategy, API route strategy, UI strategy, test strategy. Avoid unnecessary bounded contexts. Reuse ClaimDocument if it already represents policy/report/invoice/customer statement content.

## Subphase 2 — Data model + EF/schema
Implement minimal RAG schema. SQL source of truth. Chunks reference ClaimDocument and Claim/Policy. Embeddings cache by chunkId/chunkHash. No external vector index required. No destructive migration or unrelated schema rewrites.

## Subphase 3 — Golden synthetic seed/corpus
Add deterministic seed for golden claims, policy clauses, documents, chunks, eval Q&A. Depth over breadth. Document any reduced starter scope.

## Subphase 4 — Local retrieval + embedding
Implement deterministic embedding/pseudo-embedding and cosine/similarity retrieval. Filter by metadata. Persist retrieval audit.

## Subphase 5 — Grounded generation
Retrieval before generation. Answer may cite only retrieved chunks. Mock default. LLaMA/Ollama seam disabled-by-default. No real provider calls in tests.

## Subphase 6 — API/BFF
Add safe routes and DTOs consistent with existing API style.

## Subphase 7 — Frontend UI
Add Claim Evidence Intelligence panel, actions, loading/error/success, answer/citation cards, advisory banner.

## Subphase 8 — Tests/E2E
Add backend + UI/E2E tests where practical. Include MECHANICAL/SEMANTIC/PERSISTENCE/NEGATIVE labels in report.

## Subphase 9 — Verification
Run build/test commands. Report exact results.

## Subphase 10 — Report
Publish sanitized gpt-handoff report. Include files read/changed, schema/migration, seed counts, routes, UI, provider changes, tests, commands, git status, risks, next safe step.

# REPORT VERDICT
Use exactly one:
- READY_FOR_AUDIT
- PARTIAL_READY_FOR_AUDIT
- BLOCKED
- FAILED

# REPORT TOP BLOCK
CURRENT STATE:
CURRENT GATE:
WHAT CHANGED:
VERIFICATION:
BOUNDARIES HONORED:
KNOWN LIMITATIONS:
NEXT SAFE STEP:

# STOP LINE
After implementation + verification + report, STOP.
Do not commit. Do not push. Do not deploy. Do not update AIKB. Do not create Azure resources. Do not enable paid AI. Do not proceed to Azure AI Search. Do not proceed to Graph DB.
