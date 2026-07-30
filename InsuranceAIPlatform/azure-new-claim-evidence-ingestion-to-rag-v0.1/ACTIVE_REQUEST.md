REQUEST_ID: REQ-2026-06-06-insuranceai-azure-new-claim-evidence-ingestion-to-rag-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-azure-new-claim-evidence-ingestion-to-rag
PROJECT: InsuranceAIPlatform
GATE: AZURE_NEW_CLAIM_EVIDENCE_INGESTION_TO_RAG_MEGA_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/azure-new-claim-evidence-ingestion-to-rag-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
REQUEST_ID: REQ-2026-06-06-insuranceai-azure-new-claim-evidence-ingestion-to-rag-v0-1
GATE: AZURE_NEW_CLAIM_EVIDENCE_INGESTION_TO_RAG_MEGA_V0.1
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
SOURCE_REPO_BRANCH: rag/local-foundation-mega-v0.1
AIKB_PROJECT_PATH: ai-kb/01_PROJECTS/InsuranceAIPlatform/
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/azure-new-claim-evidence-ingestion-to-rag-v0.1/report.md
FORBIDDEN TARGETS:
- main / production / unrelated branches
- unrelated source repos or unrelated project folders
- global gpt-handoff/_BRIDGE as canonical evidence
- AIKB unless this gate explicitly needs a small state note in report only
- claude-vault
- secrets / raw logs / real PII

READ FIRST:
1. gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
2. gpt-handoff/InsuranceAIPlatform/latest-report.md
3. gpt-handoff/InsuranceAIPlatform/azure-create-from-zero-full-flow-v0.1/report.md
4. ai-kb/01_PROJECTS/InsuranceAIPlatform/PROJECT_PROFILE.md
5. ai-kb/01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md and TASK_LEDGER.md, but treat them as stale if they conflict with latest gpt-handoff evidence.
6. Source repo current state: branch, HEAD, status, remote, existing RAG/document/upload implementation, tests, Azure deploy scripts.

WHY THIS GATE EXISTS:
The previous create-from-zero acceptance proved that a manager can create a synthetic customer and claim on live Azure, and the new claim persists safely. However, new claims cannot get cited AI analysis because uploaded/attached documents do not become RAG EvidenceChunks. AI/RAG therefore correctly returns insufficient evidence with 0 citations. This gate closes that highest-value demo gap: create claim -> add/upload synthetic evidence -> evidence becomes claim-scoped EvidenceChunks -> RAG returns cited answer for the newly-created claim without leaking CLM-1006/1007/1012 evidence.

CURRENT ACCEPTED STATE:
- Public SWA: https://kind-meadow-03cf73103.7.azurestaticapps.net/
- Backend: https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io
- Current Azure mode: backend + RAG fallback.
- Azure Qdrant/Ollama are NOT live and must remain disabled.
- RAG fallback is honest: vector=in-memory/hash fallback, providerMode=Mock, local LLM disabled.
- Azure backend revision before this gate expected: iap-demo-api--0000003.
- Last create-from-zero gate: PARTIAL_WITH_PRODUCT_GAPS.
- Known gap to fix now: document/text/uploaded synthetic evidence does not create EvidenceChunks for newly-created claims.

GOAL:
Implement and verify a production-shaped but demo-scale evidence ingestion path so newly-created synthetic claims can receive claim-scoped cited AI/RAG analysis after adding/uploading synthetic documents/evidence. Keep Azure in fallback mode. Do not add paid providers, Azure Qdrant, Azure Ollama, production data, or direct DB writes outside approved app/migration paths.

DONE STATE:
1. Existing document/upload/evidence flow is inspected and mapped.
2. A bounded ingestion path exists from synthetic document/text/upload metadata into RAG EvidenceChunks for the target claim.
3. EvidenceChunks created for a newly-created claim are claim-scoped and do not alter seeded CLM-1006/1007/1012 evidence.
4. Retrieval/RAG can answer at least coverage/summary/custom question for the newly-created claim with citations belonging only to that new claim.
5. If evidence is absent, the old safe insufficient-evidence behavior remains.
6. Local build/tests/smokes pass.
7. Azure dev/test is updated only if local verification passes and rollback target is recorded.
8. Live Azure smoke proves: create synthetic claim -> add/upload synthetic evidence -> RAG cited answer on new claim -> no seeded citation leakage.
9. Report is published to all required project-specific gpt-handoff paths.
10. Slava remains final manual acceptor.

SELECTED APPROACH:
Product-grade bounded mega-gate. Prefer using existing tables and existing upload/document API/UI where possible. Avoid new EF migration unless absolutely necessary. If no schema change is needed, do not generate one. If a schema change is truly required, STOP and explain why before applying it.

RISK PROFILE:
High-risk areas: corrupting seeded evidence, leaking seeded citations into new claims, fake-pass with 0 citations, direct DB writes, accidental production/Azure Qdrant/Ollama/provider expansion. Be strict.

ALLOWED SCOPE:
- Read/write source repo only on branch rag/local-foundation-mega-v0.1.
- Implement backend/frontend/test changes required for evidence ingestion.
- Use synthetic data only, prefix all created live test data with E2E-RAG-<timestamp>.
- Use existing Azure dev/test resources only.
- Deploy only to existing dev/test SWA/Container App after local green checks.
- Use existing Azure SQL only through application runtime, EF migrations if already existing, or DbMigrator only if explicitly justified and non-destructive.
- Create temporary local scripts/artifacts and sanitized screenshots/reports under gpt-handoff/InsuranceAIPlatform/azure-new-claim-evidence-ingestion-to-rag-v0.1/.

FORBIDDEN SCOPE:
- NO main.
- NO merge.
- NO production.
- NO paid provider.
- NO Azure Qdrant/Ollama.
- NO OpenAI/DeepSeek/Azure OpenAI key.
- NO secrets printed/stored.
- NO real PII.
- NO direct DB writes by ad-hoc SQL for business data.
- NO destructive cleanup except optional owner-approved cleanup limited to this gate's own E2E-RAG synthetic records.
- NO mutation of seeded CLM-1006/1007/1012 records/evidence.
- NO fake pass if citations are 0 or belong to another claim.
- NO broad rewrite of policy/vehicle/amount/status flows in this gate.

INTERNAL PHASES:

PHASE 0 — Routing / state verification
- Verify repo path, remote, branch, HEAD, clean/dirty status.
- Verify active request REQUEST_ID/PROJECT/GATE.
- Verify latest create-from-zero report and current Azure endpoints.
- Record starting git state and Azure rollback revision if deploy may occur.
STOP if branch/remote/routing mismatch.

PHASE 1 — Existing implementation discovery
- Inspect current document upload/request missing doc/document metadata flow.
- Inspect current RAG EvidenceChunks model, RagSeeder, retrieval, reindex endpoint, ask endpoint, audit traces.
- Identify the minimal seam where uploaded/added document text can become EvidenceChunks.
- Report discovery summary in final report.
STOP if no safe ingestion seam exists without large redesign; propose minimal alternative.

PHASE 2 — Local implementation
Implement the smallest coherent product-shaped path. Acceptable examples:
- When a document/evidence item is uploaded/added with text/metadata for a claim, create one or more EvidenceChunks for that ClaimId.
- Or add a bounded synthetic evidence ingestion API/action tied to claim documents if upload lacks text content.
- Ensure chunk IDs are unique and claim-prefixed, e.g. <ClaimId>-uploaded-<safe-slug>-<n>.
- Ensure deterministic embedding uses existing local hash embedder.
- Ensure reindex/search sees the new chunks.
- Ensure audit/trace remains claim-scoped.
Do not touch unrelated features.

PHASE 3 — Local tests
Required tests:
- Unit/integration: evidence ingestion creates EvidenceChunks for target ClaimId.
- Retrieval: new claim with ingested evidence returns only its own chunks.
- Negative: new claim without evidence still returns insufficient evidence / 0 citations.
- Leakage: new claim RAG answer never cites CLM-1006/1007/1012.
- Existing seeded CLM-1006 RAG still works.
- If UI create/open/update is involved, include MECHANICAL_PASS / SEMANTIC_PASS / PERSISTENCE_PASS / NEGATIVE_PASS evidence per AIKB rule.
Run build/test suites appropriate to changed scope. If a full suite is too costly, run targeted + explain; but do not skip all tests.
STOP if tests fail and cannot be safely fixed within this gate.

PHASE 4 — Local/manual-like smoke
Using local backend/frontend or API smoke:
1. Create synthetic customer/claim or use a new synthetic claim created through app API/UI.
2. Add/upload synthetic evidence document/text.
3. Run RAG coverage/summary/custom question.
4. Assert citations are only from the new claim's chunks.
5. Assert no CLM-1006/1007/1012 citations.
6. Assert answer is advisory-only.

PHASE 5 — Commit/push decision inside same mega-gate
If local implementation and tests are green:
- Commit to feature branch rag/local-foundation-mega-v0.1 only.
- Push feature branch only.
- NO main, NO merge, NO force push.
If working tree contains unrelated edits or secret scan fails, STOP.
Commit message suggestion: feat(rag): ingest claim documents into evidence chunks

PHASE 6 — Azure dev/test deploy
Allowed only after local green + commit/push:
- Record rollback revision for iap-demo-api.
- Build backend Docker image from current feature branch.
- Push GHCR image.
- Update existing Container App iap-demo-api as a new revision.
- Keep Rag__QdrantEnabled=false and Rag__LocalLlamaEnabled=false.
- Rebuild/deploy existing SWA backend-mode bundle only if frontend changed or required.
- No new Azure resources.
- No Azure Qdrant/Ollama.
Rollback if critical smoke fails.

PHASE 7 — Azure smoke / acceptance matrix
Against live SWA/backend:
A. Health/API baseline
- /health 200.
- /api/claims 200.
- /rag/infrastructure 200, honest fallback labels.

B. Create from zero + evidence ingestion
- Create synthetic customer/claim via UI/API only, prefix E2E-RAG-<timestamp>.
- Add/upload synthetic evidence/document text for the new claim.
- Verify document/evidence appears after reload if UI supports it.
- Verify EvidenceChunks exist indirectly through RAG response and/or approved app diagnostic endpoint, not ad-hoc direct SQL business write.

C. RAG proof on new claim
- Run coverage or custom question requiring the uploaded evidence.
- Expected: providerMode Mock, confidence > 0 if relevant evidence matched, citations > 0, every citation chunkId/claimId belongs to the newly-created claim.
- No CLM-1006/1007/1012 citation leakage.
- Advisory-only/human-review wording present.

D. Negative proof
- New claim without evidence still returns insufficient evidence / low or 0 confidence / 0 citations.
- Non-existing claim remains structured 404, not 500.

E. Regression proof
- CLM-1006 still returns seeded citations.
- CLM-1012 still insufficient if no evidence.
- Existing manager seeded workflow is not broken.

F. UI / visual proof
- Screenshots: created claim detail, evidence upload/add result, RAG cited answer, negative no-evidence answer, narrow viewport if UI touched.
- No blank panels, no infinite spinner, no obvious visual blocker.

PHASE 8 — Cleanup / rollback
- If Azure smoke critical path fails, rollback Container App to previous revision and report ROLLED_BACK.
- Do not delete seeded data.
- Do not destructively cleanup E2E-RAG data unless a safe app-level delete/archive exists and is scoped only to records created by this gate. Otherwise list created IDs for later cleanup.
- Remove any temporary firewall rule if one was created. Prefer not to create one.

STOP CONDITIONS:
- Routing lock mismatch.
- Not on rag/local-foundation-mega-v0.1.
- Any attempt to use main/production/paid provider/Azure Qdrant/Ollama.
- Need for new schema/migration not clearly justified.
- Need for direct SQL business data writes.
- Secret detected in repo/log/report.
- RAG answer for new claim cites CLM-1006/1007/1012.
- Created claim shows stale fixture data.
- Azure smoke fails after deploy and rollback cannot be verified.

REPORT FORMAT:
Publish sanitized report to:
- gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
- gpt-handoff/InsuranceAIPlatform/latest-report.md
- gpt-handoff/InsuranceAIPlatform/azure-new-claim-evidence-ingestion-to-rag-v0.1/report.md

Report must start exactly:
REQUEST_ID: REQ-2026-06-06-insuranceai-azure-new-claim-evidence-ingestion-to-rag-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | ROLLED_BACK | BLOCKED | FAILED
TASK_TYPE: project-azure-new-claim-evidence-ingestion-to-rag
PROJECT: InsuranceAIPlatform
GATE: AZURE_NEW_CLAIM_EVIDENCE_INGESTION_TO_RAG_MEGA_V0.1
COMPLETED_BY: claude

Required sections:
- Routing Lock Verification
- Starting State
- Discovery: Existing Document/RAG Flow
- Implementation Summary
- Files Changed
- Migration/Schema Statement
- Tests Run
- Local Smoke Evidence
- Commit / Push State
- Azure Deploy State
- Azure Smoke Matrix
- New Claim Evidence Ingestion Proof
- New Claim RAG Citation Proof
- Negative / No-Evidence Proof
- Cross-Claim Leakage Proof
- Regression Proof: CLM-1006 / CLM-1012
- Screenshots / Artifacts
- Network / Console Findings
- Rollback / Cleanup State
- Boundaries Honored
- Deviations
- Defects / Product Gaps Remaining
- Slava Manual Checklist
- Recommended Next Step

FINAL VERDICT OPTIONS:
- READY_FOR_AUDIT if full DONE state met and Azure smoke passed.
- PARTIAL if ingestion works locally but not Azure, or UI missing but API path works, or evidence citations work with limitations.
- ROLLED_BACK if deploy failed and rollback succeeded.
- BLOCKED if safe implementation requires owner decision.
- FAILED if data corruption, leakage, or rollback failure occurs.

FINAL LINE IN CLAUDE CHAT:
GitHub handoff ready. Tell GPT: отчёт.

STOP after report. Do not continue to policy/vehicle/amount/status/delete/real-provider work in this gate.
