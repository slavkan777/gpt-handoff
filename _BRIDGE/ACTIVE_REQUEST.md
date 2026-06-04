REQUEST_ID: REQ-2026-06-04-rag-completion-mega-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-implementation-continuation
PROJECT: InsuranceAIPlatform
TARGET_REPORT_PATH: _BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/rag-completion-mega-v0.1/report.md
AIKB_UPDATE_REQUIRED: after-approval
CREATED: 2026-06-04T00:00:00+03:00
CREATED_BY: architect-gpt
GATE: RAG_COMPLETION_MEGA_V0.1
MODE: IMPLEMENTATION_CONTINUATION_WITH_FULL_VERIFICATION / NO COMMIT / NO PUSH / NO AZURE_DEPLOY

# PROJECT
InsuranceAIPlatform / Auto Insurance Claim AI Workbench.
Expected local repo: C:\Projects\InsuranceAIPlatform\

# CURRENT CONTEXT
Previous gate: REQUEST_ID: REQ-2026-06-04-rag-local-foundation-mega-v0-1, status PARTIAL_READY_FOR_AUDIT.
Reported state:
- Branch: rag/local-foundation-mega-v0.1
- Local RAG foundation implemented end-to-end.
- No commit, no push, no Azure.
- Backend/frontend build green.
- dotnet test: 154/154 pass.
- EF migration AddRagFoundation applied to local SQL Server LocalDB.
- Seed: PolicyClause=6, EvidenceChunk=15, RagEvaluationQuestion=6.
- Live API smoke: RAG ask works for CLM-1006, no CLM-1006 leakage into CLM-1007.
- UI panel implemented: Claim Evidence Intelligence.
- LocalLlama/Ollama seam disabled-by-default.
- Known limitations: claim-scoped similar only, starter eval set, no full Playwright UI E2E, simple deterministic embedding.

Slava instruction: finish the RAG + Vector-like + Local LLaMA chapter as far as possible locally before Slava final manual review. Claude may do automated and manual-like browser verification. Slava performs final human acceptance at the end. Use one mega molecular task, not many small prompts.

# CORE GOAL
Complete the local RAG foundation to a strong portfolio/interview-ready local state:
1. Expand golden RAG corpus and eval set.
2. Implement true cross-claim similar-claims search safely.
3. Strengthen deterministic/local vector-like retrieval.
4. Verify/complete LocalLlama/Ollama provider seam and fallback.
5. Add/complete Playwright UI E2E semantic evidence.
6. Do manual-like browser verification through Claude if possible.
7. Produce complete report with evidence and manual test checklist for Slava.

# NON-NEGOTIABLE BOUNDARIES
DO NOT commit.
DO NOT push.
DO NOT deploy.
DO NOT touch origin/main.
DO NOT create Azure resources.
DO NOT create Azure AI Search.
DO NOT create Azure Foundry endpoint.
DO NOT create Container Apps GPU.
DO NOT enable paid AI.
DO NOT enable real DeepSeek by default.
DO NOT require LLaMA/Ollama to be running for CI/build/tests.
DO NOT read, print, log, paste, or commit secrets/API keys.
DO NOT use real PII or real customer data.
DO NOT delete DB/resources.
DO NOT perform destructive cleanup.
DO NOT force-push.
DO NOT update AIKB.

Allowed:
- Continue editing existing local uncommitted RAG branch/worktree.
- Add local source changes.
- Add additive EF migration/seed changes if needed.
- Run local DB migration/seeding.
- Run backend/frontend builds/tests.
- Run Playwright UI E2E.
- Run local API/frontend for manual-like browser verification.
- If Ollama is installed/running locally, perform optional smoke; otherwise report SKIPPED_NOT_AVAILABLE without failing.
- Publish sanitized report to gpt-handoff.

# STOP CONDITIONS
Stop and report BLOCKED if:
- branch/repo state is unsafe;
- unrelated local changes exist that are not part of RAG work;
- fixes require Azure, paid services, secrets, main branch, destructive migrations, or real provider default enablement;
- implementation would overwrite unknown user work;
- build/test failures cannot be fixed within RAG scope;
- Playwright/browser cannot run due to environment limitations.

# READ FIRST / VERIFY BEFORE EDITING
1. Read current ACTIVE_REQUEST.md metadata and echo REQUEST_ID.
2. Inspect repo root, git branch, git status, HEAD, origin/dev, origin/main, changed/untracked files.
3. Read previous RAG report from _BRIDGE/LATEST_REPORT.md if available.
4. Inspect implemented RAG files: entities/DbContext/migration/seed, services, embedding/retrieval/generator, RagController/routes, frontend features/rag and ClaimEvidenceIntelligencePanel, previous tests.
5. Confirm no commit/push/deploy.

# DONE STATE

## A. Golden corpus expansion
Upgrade starter corpus. Target if practical:
- 4-6 deep golden claims.
- 50-150 EvidenceChunks minimum.
- 20-30 RagEvaluationQuestion minimum.
- enough policy clauses for coverage/exclusion/limit/deductible.
- scenarios: covered ДТП claim, not covered due to exclusion, missing police report, missing repair invoice, inflated repair invoice, photo/document mismatch, complete low-risk claim, high-risk advisory without fraud accusation, approval summary, similar claims.
If full target is too large, deliver largest safe deterministic expansion and explain why. Seed deterministic, idempotent, synthetic, non-destructive.

## B. Cross-claim similar-claims search
Implement safe cross-claim similarity:
- route or parameter for similar-claims search;
- returns claimId / score / reason / matching categories;
- does NOT expose another claim's raw evidence text unless user opens that claim;
- normal ask/evidence retrieval remains claim-scoped;
- uses vector-like similarity over claim summaries/chunks or derived claim-level representations;
- covered by tests.
Suggested endpoint: GET /api/claims/{claimId}/rag/similar-claims?topK=

## C. Retrieval/evaluation strengthening
Add/strengthen tests/eval harness:
- recall@k or equivalent for expected sources;
- expected chunks appear for key questions;
- citations subset of retrieved evidence;
- mustNotCite chunks do not appear;
- no CLM-1006 leakage into non-CLM-1006 answers;
- risk answer never says fraud as final fact;
- missing-document answer identifies missing evidence;
- coverage answer cites policy clause.

## D. LocalLlama/Ollama seam verification
LocalLlama remains disabled-by-default. Required:
- config flag exists/default disabled or mock;
- safe timeout/fallback;
- tests do not require network/Ollama;
- if Ollama is installed/running locally, optional smoke;
- if unavailable, report SKIPPED_NOT_AVAILABLE and keep gate green.
Do not install Ollama, download models, require weights, or call paid provider.

## E. Frontend completion
Ensure Claim Evidence Intelligence UI demonstrates coverage, missing docs, risk, similar claims, approval summary, custom question, citations/evidence cards, trace id/audit/cost/tokens if available, advisory banner, error/loading states. Similar claims show safe claim-level cards, not cross-claim evidence text.

## F. Playwright / UI E2E semantic evidence
Add/complete targeted Playwright E2E with labels:
- MECHANICAL_PASS
- SEMANTIC_PASS
- PERSISTENCE_PASS
- NEGATIVE_PASS
Target scenarios: open CLM-1006 AI Evidence panel, run coverage check, verify answer+citation+policy clause, run missing docs/risk, verify advisory banner, run similar claims and verify claim-level results, open non-CLM-1006 and verify no CLM-1006 evidence, verify audit/answer persists after reload if implemented.
If Playwright cannot run, run manual-like browser test and report blocker. Do not fake PASS.

## G. Manual-like browser verification by Claude
After automated tests, run app locally if possible:
- start backend;
- start frontend or built app;
- open claim page;
- click RAG buttons;
- inspect UI;
- check console/page errors if possible;
- capture screenshots/artifact paths if convention supports;
- stop processes.
Report URLs, actions, visible result, console/page errors, screenshots/artifacts.

## H. Verification
Run practical verification: git status before/after, dotnet build, dotnet test, DB migration/seed if changed, frontend build, targeted Playwright, live API smoke, manual-like UI smoke. Do not claim command passed if it did not run.

## I. Report
Publish sanitized report to:
- _BRIDGE/LATEST_REPORT.md
- InsuranceAIPlatform/rag-completion-mega-v0.1/report.md
- InsuranceAIPlatform/latest-report.md if convention uses it
- latest-summary.json if practical
Report must include REQUEST_ID, verdict, branch/HEAD/status, files read/changed, migration/seed changes, corpus counts before/after, routes, UI changes, LocalLlama status, tests, commands, E2E evidence labels, manual-like UI verification, not touched, risks/limitations, next safe step.

# ACCEPTANCE BAR
READY_FOR_AUDIT only if builds/tests pass, targeted UI E2E or equivalent manual-like browser evidence is provided, cross-claim similar search works safely or explicitly blocked, corpus/eval expanded beyond starter set, and no boundaries violated.
If one major item remains incomplete, use PARTIAL_READY_FOR_AUDIT and explain exact remaining gate.

# REPORT TOP BLOCK
CURRENT STATE:
CURRENT GATE:
WHAT CHANGED:
VERIFICATION:
E2E / MANUAL UI:
BOUNDARIES HONORED:
KNOWN LIMITATIONS:
NEXT SAFE STEP:

# STOP LINE
After implementation + verification + report: STOP. Do not commit, push, deploy, update AIKB, create Azure resources, enable paid AI, proceed to Azure AI Search, or proceed to Graph DB.
