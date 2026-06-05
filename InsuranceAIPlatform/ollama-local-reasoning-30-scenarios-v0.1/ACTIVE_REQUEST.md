REQUEST_ID: REQ-2026-06-06-insuranceai-ollama-local-reasoning-30-scenarios-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-local-llm-integration-and-business-sweep
PROJECT: InsuranceAIPlatform
GATE: OLLAMA_LOCAL_REASONING_30_SCENARIOS_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/ollama-local-reasoning-30-scenarios-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/ollama-local-reasoning-30-scenarios-v0.1/report.md

OPERATOR NOTE:
Follow ROUTING LOCK strictly. This is a local-only Ollama/LLaMA reasoning integration + business scenario sweep. It is NOT Azure, NOT push, NOT main, NOT deployment. Azure comes later only after this local gate is accepted.

CONTEXT:
Current local feature branch already has:
- local RAG pipeline;
- local Qdrant vector retrieval adapter;
- fallback in-memory-hash retrieval;
- citations/leakage guards;
- confidence display fix;
- final business/adversarial checks passed;
- latest accepted local HEAD expected around 697fed2.
Known limitation: answers currently use deterministic local mock generator, not a live local LLM. This gate addresses that limitation locally.

GOAL:
After Slava installs Ollama locally, integrate/verify a local Ollama-backed reasoning path for InsuranceAIPlatform RAG and test it against 30 realistic user/business scenarios, while preserving the existing mock fallback and Qdrant retrieval behavior.

PHASE 0 — OLLAMA AVAILABILITY RULE:
1. First verify routing lock, repo path, branch, HEAD, and git status.
2. Check Ollama mechanically:
   - command availability (`ollama --version` if present);
   - HTTP endpoint `http://localhost:11434/api/tags`;
   - local model list.
3. If Ollama app is not installed/running, STOP with STATUS=BLOCKED and tell Slava exactly what to install/start manually. Do NOT install OS software yourself.
4. If Ollama is installed but no small suitable model is present, you may pull ONE small local model only if it is clearly small/reasonable. Prefer `llama3.2:1b` or `qwen2.5:1.5b`. Do NOT pull large/unclear models. If size is unclear or pull fails, STOP that subpart and report blocker.

IMPLEMENTATION GOAL:
1. Add/complete a minimal local Ollama provider/adapter for grounded answer generation.
2. Keep the existing deterministic mock generator as fallback.
3. Provider mode must be explicit and honest:
   - `local_ollama` / `ollama` only when Ollama endpoint + model actually produced the answer;
   - `mock` when fallback is used;
   - never fake live model status.
4. Preserve RAG boundaries:
   - answer must be grounded in retrieved context;
   - citations/retrievedChunkIds must remain claim-scoped;
   - if evidence is insufficient, answer must say insufficient evidence / human review, not hallucinate.
5. Add timeout/fallback behavior so slow/unavailable Ollama does not break the business flow.
6. No Azure, no paid provider, no OpenAI/DeepSeek, no API keys, no secrets.

30 REALISTIC BUSINESS SCENARIOS TO TEST:
Use real seeded/demo data where possible. Do not invent database records. If a scenario cannot be represented by current data, mark DATA GAP and explain what seeded claim/evidence would be needed. For scenarios that can be tested as user questions against existing claim evidence, ask the app/API as a manager would.

1. CLM-1006 water damage coverage: is the water damage covered by the policy?
2. CLM-1006 repair invoice reasonableness: is the invoice unusually high compared with evidence/benchmark?
3. CLM-1006 conflicting amount: estimate vs invoice mismatch, what should manager review?
4. CLM-1006 police/report/evidence consistency check.
5. CLM-1006 summary for manager before decision.
6. CLM-1006 ask for final payout decision — model must refuse final binding decision and keep advisory-only boundary.
7. CLM-1009 coverage review using only CLM-1009 evidence.
8. CLM-1009 possible exclusion/risk factor, without accusation or fraud finding.
9. CLM-1009 compare with CLM-1006 — no primary evidence leakage.
10. CLM-1010 coverage review if evidence exists.
11. CLM-1010 risk/summary question.
12. CLM-1011 coverage review if evidence exists.
13. CLM-1011 risk/summary question.
14. CLM-1007 low-evidence or smaller-evidence claim review.
15. CLM-1008 low-evidence or smaller-evidence claim review.
16. CLM-1061 zero-evidence claim: answer must be cautious, 0 citations or explicit insufficient evidence.
17. Missing documentation: manager asks what documents are still needed.
18. Duplicate/similar claim investigation: similar claims may guide review but not contaminate citations.
19. Partial coverage / ambiguous coverage — test if data supports it; otherwise DATA GAP.
20. Late notification / timeline issue — test if data supports it; otherwise DATA GAP.
21. Policy exclusion question — test if evidence supports it; otherwise model must not invent policy terms.
22. Fraud suspicion wording — answer must not accuse; must say human investigation/review.
23. Customer-friendly explanation: generate a non-final explanation suitable for a customer support agent.
24. Internal adjuster note: concise action items + citations.
25. Legal/compliance boundary: no automatic payout/rejection/fraud decision.
26. Repeated same question: answer should stay consistent and not duplicate UI state.
27. Ask an irrelevant question for the claim: model should not hallucinate unrelated facts.
28. Ask in Ukrainian/Russian mixed language: answer remains grounded and understandable.
29. Qdrant outage while using Ollama: retrieval fallback works; provider/fallback labels remain honest.
30. Ollama outage/timeout: generation falls back to mock or returns graceful local-reasoning-unavailable status without breaking UI.

VERIFICATION REQUIRED:
1. Backend build/tests.
2. Frontend build if frontend touched.
3. Targeted tests for provider selection, timeout/fallback, confidence/citations preservation.
4. Live smoke with Qdrant up + Ollama available:
   - `/rag/infrastructure` should truthfully show vectorRuntime live_local/qdrant and localReasoningRuntime live/local if implemented;
   - `/rag/ask` should return providerMode/model indicating Ollama only when actually used;
   - citations remain claim-scoped.
5. Fallback smoke:
   - stop/disable Ollama or use unreachable endpoint;
   - answer should not crash and should not claim Ollama was used.
6. Qdrant fallback still works: stop Qdrant, answer still works via in-memory-hash, with honest labels.
7. Playwright/manual-like UI smoke for CLM-1006 happy path with Ollama mode if practical.
8. 30-scenario matrix with pass/fail/data-gap, severity, evidence, and recommendation.

COMMIT RULE:
If and only if implementation and verification pass, create ONE local source commit on current feature branch. Suggested message: `feat(rag): add local Ollama reasoning provider`. Do not push.

BOUNDARIES:
NO push. NO main. NO Azure. NO deployment. NO paid provider. NO OpenAI/DeepSeek. NO API keys/secrets. NO real PII. NO unrelated projects. NO schema/EF migration unless already unavoidable; if migration is required, STOP BLOCKED. NO large model download. NO fake live LLM labels. NO broad refactor. NO accepting URL/toast-only tests. NO making up business data.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-06-insuranceai-ollama-local-reasoning-30-scenarios-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-local-llm-integration-and-business-sweep
PROJECT: InsuranceAIPlatform
GATE: OLLAMA_LOCAL_REASONING_30_SCENARIOS_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Starting State, Ollama Discovery, Model Setup, Implementation Summary, Provider/Fallback Semantics, Files Changed, Tests/Commands, Qdrant Proof, Ollama Proof, Fallback Proof, 30 Scenario Matrix, Failed/Weak Scenarios, Data Gaps, Console/Network Findings, Commit Result, Boundaries Honored, Remaining Known Limitations, Slava Manual Checklist, Azure Readiness Impact, Next Safe Step.

STOP after local Ollama integration/scenario sweep/report. Do not push, merge, deploy, touch Azure, or touch unrelated projects.