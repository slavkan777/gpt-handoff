REQUEST_ID: REQ-2026-06-04-insuranceai-local-qdrant-ollama-runtime-completion-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-implementation
PROJECT: InsuranceAIPlatform
GATE: LOCAL_QDRANT_OLLAMA_RUNTIME_COMPLETION_AND_PRE_FINAL_AUTOMATION_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/local-qdrant-ollama-runtime-completion-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

# READ FIRST

You are Claude Code working as bounded local executor for InsuranceAIPlatform.
This request replaces the stale active request `LOCAL_RAG_INFRASTRUCTURE_UI_DEMO_V0.1`.
Do not use the previous active request or previous report as the current task contract.

Before editing, inspect the local product repo state and confirm:
- repository path: `C:/Projects/InsuranceAIPlatform` unless the local workspace differs;
- current branch;
- current HEAD;
- git status;
- whether existing uncommitted RAG infrastructure changes are present;
- whether Qdrant and Ollama are installed/running locally.

# WHY THIS TASK EXISTS

The previous gate added an Evidence Intelligence Stack panel and local RAG infrastructure status, but the reasoning runtime still reported `disabled / mock`, the vector layer was only a local hash embedding cache, and no fresh report exists for the intended Qdrant/Ollama/LLaMA runtime completion gate.
The workflow failure was stale bridge/report routing: the project-specific bridge still pointed to the old RAG infrastructure request.

# GOAL

Complete the next local-only RAG runtime step:

SQL source of truth → local evidence/vector index → Qdrant vector runtime where locally available → Ollama/LLaMA local reasoning runtime where locally available → audit/cost/history → human review.

This is a local demo/runtime gate, not a cloud/provider/deploy gate.

# DONE STATE

1. Project-specific bridge is honored:
   - report echoes this exact REQUEST_ID;
   - report gate is `LOCAL_QDRANT_OLLAMA_RUNTIME_COMPLETION_AND_PRE_FINAL_AUTOMATION_V0.1`;
   - report must not claim the old `LOCAL_RAG_INFRASTRUCTURE_UI_DEMO_V0.1` as current.

2. Runtime availability is mechanically checked, not guessed:
   - Qdrant availability checked via local process/container/HTTP endpoint if present;
   - Ollama availability checked via local process/HTTP endpoint if present;
   - local model list checked if Ollama is available;
   - if unavailable, report explicit status instead of pretending success:
     - `VECTOR_RUNTIME_SKIPPED_NOT_AVAILABLE`
     - `LLAMA_RUNTIME_SKIPPED_NOT_AVAILABLE`

3. Existing RAG infrastructure is preserved:
   - Evidence Intelligence Stack remains visible on AI Evidence page;
   - SQL source of truth status remains working;
   - existing local hash embedding cache remains safe fallback;
   - existing RAG, similar claims, audit history, citations, and tests remain green.

4. Add/complete local runtime integration only where practical and safe:
   - Qdrant integration must be local-only and disabled/fallback-safe when Qdrant is absent;
   - Ollama/LLaMA integration must be local-only and disabled/fallback-safe when Ollama/model is absent;
   - no paid provider call;
   - no Azure;
   - no API keys;
   - no secrets;
   - no real PII.

5. UI/API status must be honest:
   - AI Evidence / Evidence Intelligence Stack must show vector runtime and local reasoning runtime truthfully;
   - status labels must distinguish `LOCAL_REAL`, `LOCAL_UNAVAILABLE`, `DISABLED`, `MOCK/FALLBACK`, and `SKIPPED_NOT_AVAILABLE` where applicable;
   - no fake “live” label if runtime is not actually reachable.

6. Verification is executed and reported:
   - backend tests;
   - frontend build;
   - targeted Playwright/E2E for AI Evidence/RAG area;
   - local smoke for infrastructure endpoint(s);
   - negative check: no Azure/provider/secret/endpoint value leak;
   - forbidden-scope check.

7. Fresh sanitized report is published to all three project-specific paths:
   - `gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md`
   - `gpt-handoff/InsuranceAIPlatform/latest-report.md`
   - `gpt-handoff/InsuranceAIPlatform/local-qdrant-ollama-runtime-completion-v0.1/report.md`

# IN SCOPE

- Inspect existing local RAG/backend/frontend code.
- Add or complete local-only Qdrant adapter/status integration if safe.
- Add or complete local-only Ollama/LLaMA adapter/status integration if safe.
- Add fallback statuses if local runtimes are unavailable.
- Update existing RAG infrastructure DTOs/services/controllers/UI/types/sagas/tests only as needed.
- Add tests/smoke evidence.
- Publish sanitized report.

# OUT OF SCOPE

- No source commit.
- No source push.
- No deployment.
- No Azure resource creation.
- No Azure SDK adoption unless already present and strictly unused.
- No OpenAI/DeepSeek/paid provider call.
- No API-key work.
- No real customer data.
- No production payout/rejection behavior.
- No DB schema change unless the repo already has required schema and no migration is needed.
- No EF migration unless explicitly already part of current uncommitted local work; if a migration is required, STOP and report BLOCKED.

# FORBIDDEN

- Do not read, print, log, paste, or commit secret values.
- Do not use or expose `DEEPSEEK_API_KEY` value.
- Do not touch other projects, TwinCore, AgentHub, BusinessLab, AIKB, or global `_BRIDGE`.
- Do not modify `main`.
- Do not force-push.
- Do not create cloud resources.
- Do not claim Qdrant/Ollama/LLaMA is live unless mechanically verified.

# PRESERVE

- Existing portfolio/demo behavior.
- Existing CLM-1006 golden demo.
- Existing mock/fallback mode.
- Existing tests unless they must be extended for truthful runtime status.
- Project-specific bridge routing.

# STEP-BY-STEP EXECUTION MAP

1. Bootstrap check:
   - print repo path, branch, HEAD, git status summary;
   - inspect current active request and confirm this REQUEST_ID.

2. Runtime discovery:
   - check Qdrant locally using safe local checks only;
   - check Ollama locally using safe local checks only;
   - list local model names only if Ollama is available;
   - do not download models unless explicitly approved; if model missing, report unavailable.

3. Code inspection:
   - locate existing RAG DTOs/controller/service/frontend panel/slice/saga/types/tests;
   - identify minimal file scope.

4. Implementation:
   - make minimal bounded changes for truthful Qdrant/Ollama status and optional local call path;
   - keep fallback safe if runtime unavailable;
   - no schema/migration unless already available.

5. Verification:
   - run backend tests relevant to InsuranceAIPlatform;
   - run frontend build;
   - run targeted Playwright/E2E for RAG/AI Evidence;
   - run local smoke endpoint(s);
   - run grep/safety check for secrets/endpoint leaks;
   - run git diff/status check.

6. Report:
   - publish fresh report to all required project-specific paths;
   - include exact status of Qdrant/Ollama: LIVE_LOCAL, DISABLED, or SKIPPED_NOT_AVAILABLE;
   - include commands and results;
   - include files changed;
   - include not-touched list;
   - include next safe step.

# REPORT FORMAT

REQUEST_ID: REQ-2026-06-04-insuranceai-local-qdrant-ollama-runtime-completion-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-implementation
PROJECT: InsuranceAIPlatform
GATE: LOCAL_QDRANT_OLLAMA_RUNTIME_COMPLETION_AND_PRE_FINAL_AUTOMATION_V0.1
COMPLETED_BY: claude

## Current State
## Runtime Discovery
## DONE Criteria vs Evidence
## Files Changed
## Commands Run
## Verification Evidence
## Runtime Status Labels
## Boundaries Honored
## Known Limitations
## Not Touched
## Next Safe Step

# STOP LINE

Stop after implementation, verification, and report.
Do not ask Slava for manual checks.
Do not commit, push, deploy, or touch Azure.
