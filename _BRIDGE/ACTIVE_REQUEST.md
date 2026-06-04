REQUEST_ID: REQ-2026-06-04-rag-golden-dataset-readonly
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-readonly-architecture-audit
PROJECT: InsuranceAIPlatform
TARGET_REPORT_PATH: _BRIDGE/LATEST_REPORT.md and/or InsuranceAIPlatform/rag-golden-dataset-readonly/report.md
AIKB_UPDATE_REQUIRED: no
GATE: RAG_GOLDEN_DATASET_AND_SCHEMA_DISCUSSION_V0.1
MODE: READ-ONLY DISCUSSION / NO CHANGES

# PROJECT

InsuranceAIPlatform / Auto Insurance Claim AI Workbench.

This is a portfolio-grade .NET/Azure/React/AI Engineering project.
We are now discussing how to add a business-useful RAG + Vector Search + LLaMA layer.

# CURRENT CONTEXT

The project already has a strong insurance-platform foundation:
- React / TypeScript UI.
- Redux Toolkit / Redux-Saga workflow layer.
- .NET backend / BFF/API.
- service boundary direction.
- Azure deployment direction.
- SQL persistence direction.
- AI Analysis Service / provider abstraction direction.
- audit / cost / human-in-the-loop positioning.

But full production RAG, Vector DB / vector index, Graph DB and LLaMA provider are not yet implemented.

Slava wants to discuss the next big chapter before coding:
Create an **etalon / golden synthetic insurance database and evidence corpus** so RAG + Vector Search + LLaMA have high-quality data to work with.

# WHY THIS TASK EXISTS

We do not want to "just add Vector DB" as a technology toy.

We want to understand, from the business and architecture perspective:

1. What exact insurance business problems RAG solves.
2. What golden data must exist in the DB.
3. What documents/evidence/corpus must exist.
4. What schema changes may be needed later.
5. What should stay in SQL as source of truth.
6. What should go into vector search / vector index.
7. Where LLaMA fits.
8. What should be implemented first, second, third.
9. What should NOT be implemented yet.
10. How to keep Azure cost low.

# HARD RULES

DO NOT CHANGE ANY FILES.
DO NOT CREATE OR EDIT CODE.
DO NOT CREATE OR EDIT DB/MIGRATIONS.
DO NOT TOUCH AZURE.
DO NOT CREATE AI SEARCH / VECTOR DB / LLaMA RESOURCES.
DO NOT RUN DEPLOY.
DO NOT COMMIT.
DO NOT PUSH.
DO NOT READ OR PRINT SECRETS.
DO NOT READ OR PRINT API KEYS.
DO NOT USE REAL PII.

This is a read-only thinking/audit/planning request only.

# YOUR ROLE

Act as:
- senior .NET/Azure architect;
- insurance domain analyst;
- RAG systems architect;
- data modeler;
- AI Engineering / LLMOps reviewer;
- cost/governance critic.

Your job is to produce a clear recommendation report for Architect GPT and Slava.

# MAIN QUESTION

Should InsuranceAIPlatform start its RAG + Vector Search + LLaMA upgrade by first creating a high-quality **golden synthetic database / evidence corpus**?

If yes:
- what should be inside that DB/corpus?
- how big should it be?
- what schema/data model is needed?
- what is the right phased plan?
- how do we avoid overengineering?
- how do we keep Azure cost low?

# SPECIFIC THINGS TO ANALYZE

## 1. Business value

Explain where RAG is useful in this insurance system:
- policy coverage check;
- missing document detection;
- claim summary;
- risk explanation;
- similar claims search;
- human approval support;
- audit-friendly AI explanations.

For each use case, answer:
- who uses it;
- what pain it solves;
- what data it needs;
- what answer/evidence should be shown in UI;
- why SQL-only search is not enough;
- why vector search helps;
- what LLaMA adds.

## 2. Golden synthetic database

Propose the ideal "etalon" synthetic DB / corpus.

Include recommended counts:
- customers;
- policies;
- vehicles;
- claims;
- documents per claim;
- document types;
- chunks per document;
- policy clauses;
- audit notes;
- risk signals;
- evaluation questions.

Do NOT blindly propose huge data.
Explain what amount is enough for a strong portfolio demo and why.

## 3. Required domain entities / tables / schemas

Propose what data structures we will eventually need.

Separate them into:

A. Existing/source-of-truth insurance data:
- Customer;
- Policy;
- Vehicle;
- Claim;
- ClaimDocument;
- ClaimPhotoMetadata;
- RepairInvoice;
- PoliceReport;
- CustomerStatement;
- OperatorNote;
- AuditEvent;
- RiskSignal;
- AIAnalysisRun.

B. RAG-specific data:
- EvidenceDocument;
- EvidenceChunk;
- ChunkMetadata;
- CitationReference;
- RetrievalQuery;
- RetrievedEvidence;
- GroundedAnswer;
- RagEvaluationQuestion;
- ExpectedAnswer;
- RagAuditTrace.

C. Vector-search-specific data:
- embedding model name;
- vector index id;
- external index document id;
- chunk hash;
- indexing status;
- indexedAt;
- source version;
- language;
- claimId/policyId/customerId metadata filters.

## 4. SQL vs Vector Search responsibility

Explain clearly:
- what stays in SQL;
- what goes into vector index;
- what is duplicated as searchable metadata;
- what must never be only in vector DB;
- how to rebuild vector index from SQL/corpus.

Expected principle:
SQL = source of truth.
Vector Search = semantic retrieval index.
LLaMA = explanation/generation layer.
Audit = traceability.

## 5. Azure stack options

Evaluate options:

Option A: Azure AI Search vector/hybrid search.
Option B: PostgreSQL + pgvector.
Option C: Qdrant.
Option D: Neo4j / Graph DB later.
Option E: no vector DB first; mock retrieval first.

For each:
- fit for this project;
- cost;
- demo/interview value;
- complexity;
- when to use;
- whether to implement now or later.

Important:
Graph DB is probably future/later. Do not force it unless there is a strong business reason.

## 6. LLaMA placement

Explain how LLaMA should fit:

- LocalLlamaProvider via Ollama / LM Studio / llama.cpp for dev.
- AzureFoundryLlamaProvider disabled-by-default for Azure demo.
- ContainerAppsGpuLlamaProvider as future advanced option only.

Explain:
- why LLaMA should not be the source of truth;
- why retrieval must happen before generation;
- why MockProvider remains default;
- how to keep cost controlled.

## 7. UI impact

Propose how this should appear in the app.

Recommended UI area:
Claim Detail / Claim Workspace.

Possible panel:
"AI Evidence Assistant" or "Claim Evidence Intelligence".

Candidate buttons:
- Check policy coverage.
- Find missing documents.
- Explain risk.
- Find similar claims.
- Prepare approval summary.
- Ask custom question.

Each answer should show:
- answer;
- confidence;
- evidence cards;
- source document references;
- retrieved chunks;
- cost/tokens;
- audit trace;
- "AI advisory only, human final decision" boundary.

## 8. Testing and evaluator strategy

Propose how to test this correctly.

Need semantic tests:
- question -> retrieval returns expected sources;
- answer cites correct documents;
- no CLM-1006 fixture leak for other claims;
- missing document detection is correct;
- high-risk explanation does not accuse fraud;
- answer is grounded in retrieved chunks;
- audit/cost trace is persisted.

Include:
- MECHANICAL_PASS;
- SEMANTIC_PASS;
- PERSISTENCE_PASS;
- NEGATIVE_PASS.

## 9. Cost / Azure guardrails

We want:
- Static frontend always-on/free.
- Expensive services disabled/paused/on-demand.
- SQL cost controlled.
- AI Search not created until approved.
- LLaMA Azure endpoint disabled-by-default.
- Mock/local mode default.

Propose cost-control plan:
- feature flags;
- disabled-by-default providers;
- small index;
- minimal documents;
- budget alerts;
- manual demo activation;
- teardown/parking plan.

## 10. Phased implementation plan

Propose gates. Do not implement them.

Expected style:

1. RAG_BUSINESS_VALUE_AND_GOLDEN_DATASET_PLAN_V0.1
2. RAG_SCHEMA_AND_SEED_IMPLEMENTATION_V0.1
3. LOCAL_MOCK_RAG_RETRIEVAL_V0.1
4. CLAIM_AI_EVIDENCE_ASSISTANT_UI_V0.1
5. RAG_E2E_SEMANTIC_EVIDENCE_V0.1
6. AZURE_AI_SEARCH_VECTOR_INDEX_PLANNING_V0.1
7. AZURE_AI_SEARCH_VECTOR_INDEX_IMPLEMENTATION_DISABLED_BY_DEFAULT_V0.1
8. LOCAL_LLAMA_PROVIDER_V0.1
9. AZURE_LLAMA_PROVIDER_PLANNING_ONLY_V0.1
10. GRAPH_RELATIONSHIP_RISK_LAYER_FUTURE_V0.1

Adjust if you think a better sequence exists.

# OUTPUT FORMAT

Write a structured report with these sections:

1. Executive summary for Slava.
2. Business use cases.
3. Golden DB / corpus recommendation.
4. Proposed data model.
5. SQL vs Vector vs LLaMA responsibilities.
6. Recommended stack decision.
7. LLaMA options.
8. UI concept.
9. Test/evaluator plan.
10. Cost/security guardrails.
11. Phased gates.
12. Risks and anti-goals.
13. Final recommendation.
14. Questions for Slava, maximum 5.

# REPORT REQUIREMENTS

Report must be concise but complete.
Use tables where useful.
Do not paste huge source code.
Do not include secrets.
Do not claim anything was implemented.
Do not mark implementation as done.
This is a discussion/planning report only.

# STOP LINE

Stop after producing the report.
Do not edit source files.
Do not edit docs.
Do not update AIKB.
Do not commit.
Do not push.
Do not deploy.
