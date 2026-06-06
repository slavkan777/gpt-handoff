REQUEST_ID: REQ-2026-06-06-insuranceai-push-pr-azure-preflight-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-remote-branch-and-azure-preflight
PROJECT: InsuranceAIPlatform
GATE: PUSH_PR_AZURE_PREFLIGHT_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/push-pr-azure-preflight-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/push-pr-azure-preflight-v0.1/report.md

OPERATOR NOTE:
Follow ROUTING LOCK strictly. Local RAG + Qdrant + Ollama is accepted. This gate moves from local-only to remote-branch readiness and Azure preflight planning. This is NOT an Azure deployment gate. Do not merge, do not deploy, do not touch production.

CONTEXT:
Accepted local state:
- branch: rag/local-foundation-mega-v0.1
- local RAG pipeline complete;
- local Qdrant vector retrieval complete;
- local Ollama reasoning provider complete with qwen2.5:1.5b;
- mock fallback preserved;
- latest accepted local source commit expected: 4067591 feat(rag): add local Ollama reasoning provider;
- previous report says 181 tests passed, Qdrant/Ollama/fallback verified, 30 scenario sweep 27/30 PASS, 3 model-quality REVIEW, 0 FAIL, 0 leakage.

KNOWN AZURE URL TO INSPECT:
https://kind-meadow-03cf73103.7.azurestaticapps.net/

GOAL:
Prepare the project for a safe Azure dev/test deployment by pushing the current feature branch or preparing a PR, and producing an Azure preflight plan based on the current repo, current Azure/static site observations, and required runtime architecture. Do not deploy yet.

DONE STATE:
1. Verify routing lock, repo path, branch, HEAD, remote, and git status.
2. Confirm current local HEAD includes accepted commits through the Ollama provider commit, expected around 4067591.
3. Run pre-push checks:
   - git diff/status clean or expected;
   - no unrelated files;
   - no secrets in changed files/configs;
   - backend build/tests or targeted confidence check if full suite is too expensive;
   - frontend build if relevant;
   - confirm no EF/schema migration was added.
4. If checks pass, push ONLY the feature branch `rag/local-foundation-mega-v0.1` to origin. Do NOT push main. Do NOT force-push. If push is blocked by auth, report exact blocker and stop that subpart.
5. If GitHub CLI/browser auth is already available, optionally create a draft PR from feature branch to main with a clear title/body. If PR creation requires manual auth or browser confirmation, report the exact command and do not invent status.
6. Inspect the public Azure Static Web App URL with browser/curl/Playwright if possible:
   - does the frontend load;
   - is it mock/demo mode or backend mode;
   - console/network errors;
   - whether API calls point to any backend;
   - whether current Azure app can support the new RAG/Qdrant/Ollama features or is frontend-only.
7. Azure CLI preflight READ-ONLY only:
   - check whether `az` is installed;
   - check `az account show` if already logged in;
   - if not logged in, ask Slava to run `az login` manually in his terminal. Do not ask for passwords.
   - if logged in, list relevant subscriptions/resource groups/static web apps/app services/container apps READ-ONLY. Do not create/update/delete resources.
8. Produce Azure deployment architecture options:
   A. Cheapest dev demo: Static Web App + backend API + Qdrant/fallback, no live Ollama in Azure.
   B. Dev live local-LLM demo: backend + Qdrant + Ollama container if feasible, with cost/CPU/memory caveats.
   C. Production-like later: managed LLM provider/OpenAI-compatible provider, requires separate owner approval and paid provider.
9. Recommend one safest next deploy gate with exact boundaries and rollback.
10. Publish fresh report to all three project-specific paths.

AZURE PREFLIGHT QUESTIONS TO ANSWER:
- What is currently deployed at the provided Azure Static Web Apps URL?
- Is it only frontend/static UI, or connected to a backend?
- What env vars/configs will be needed for dev deploy?
- Where should backend run: Azure App Service, Container Apps, Static Web Apps API, or other?
- Where should Qdrant run for dev: Container App, App Service container, external service, or fallback-only?
- Should Ollama be deployed to Azure now? If yes, what minimum resource/cost risk? If no, what local/managed-provider alternative?
- What is the rollback/stop-cost plan?
- What exact manual action does Slava need, if any, before deploy?

BOUNDARIES:
NO main push. NO merge. NO Azure deploy. NO Azure create/update/delete. NO production resources. NO paid provider. NO OpenAI/DeepSeek API keys. NO secrets in chat/files. NO real PII. NO schema/EF migration. NO force push. NO deleting resources. NO changing unrelated projects. NO pretending Azure login/deploy succeeded. If auth is required, ask Slava to do the auth flow manually without sharing credentials.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-06-insuranceai-push-pr-azure-preflight-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-remote-branch-and-azure-preflight
PROJECT: InsuranceAIPlatform
GATE: PUSH_PR_AZURE_PREFLIGHT_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Starting Repo State, Pre-Push Checks, Branch Push Result, PR Result, Azure URL Inspection, Azure CLI Read-Only Discovery, Current Azure Topology, Deployment Options, Recommended Dev/Test Architecture, Required Env Vars/Secrets, Cost/Risk Notes, Rollback/Stop-Cost Plan, Files Changed, Commands Run, Boundaries Honored, Blockers, Next Deploy Gate Proposal.

STOP after report. Do not deploy, merge, touch Azure resources, or work on unrelated projects.