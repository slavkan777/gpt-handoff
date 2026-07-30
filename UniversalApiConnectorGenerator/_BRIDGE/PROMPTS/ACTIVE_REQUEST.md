REQUEST_ID: REQ-2026-06-25-uacg-ai-integrator-initial-import-v0-1
STATE: READY_FOR_CLAUDE
PROJECT: UniversalApiConnectorGenerator
GATE: AI_INTEGRATOR_INITIAL_IMPORT_V0_1
TASK_TYPE: external-repo-import-branch-push-pr

PROMPT_PATH:
UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md

CANONICAL_PROMPT_PATH:
UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/REQ-2026-06-25-uacg-ai-integrator-initial-import-v0-1.md

REPORT_PATH:
UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md

TARGET_AZURE_REPO:
https://dev.azure.com/twincore-net/twincore-framework/_git/AI-Integrator

LOCAL_SOURCE_WORKSPACE:
C:\Projects\UniversalApiConnectorGenerator

TARGET_BRANCH:
feature/uacg-initial-import

PR_TARGET_BRANCH:
main

# BRIDGE COMMUNICATION LOCK

This project communicates only through the GitHub Bridge:

gpt-handoff/UniversalApiConnectorGenerator/_BRIDGE/

Rules:
1. Do not use chat text as the task specification except the short command telling you to read the Bridge prompt.
2. The command `промт` means: read the canonical prompt from Bridge.
3. Execute only the active Bridge request.
4. Do not invent or expand the task.
5. Write the final report to:
   UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
6. In Claude chat reply only:
   Bridge report ready. Tell GPT: отчёт.
7. Do not paste the full report in chat.
8. GPT will audit the Bridge report. Slava gives final acceptance.

If Bridge cannot be read or report cannot be written, stop with:
BLOCKED / BRIDGE_UNAVAILABLE

# ROUTING LOCK

ROUTE USED: TITAN_ELITE_4
WHY: this is a bounded external repository import/write gate with build/test/commit/push/PR evidence. It is not a merge, deployment, production, live-provider, or framework-edit gate.

ALLOWED:
- read this Bridge prompt
- read UniversalApiConnectorGenerator Bridge README / STATUS / current request
- read local source workspace C:\Projects\UniversalApiConnectorGenerator
- clone or open Azure repo AI-Integrator
- create branch feature/uacg-initial-import from origin/main
- import source-only UniversalApiConnectorGenerator standalone solution into repo root
- rename legacy personal-named documentation file to USER_DEMO_GUIDE_UA.md
- update documentation references caused by that rename
- reconcile/merge existing README.md and .gitignore safely
- exclude runtime/build/generated artifacts
- run restore/build/test in the imported repo
- run basic CLI smoke if available and safe
- commit to feature/uacg-initial-import
- push feature/uacg-initial-import
- create PR to main if permission/tooling allows
- write the final Bridge report

FORBIDDEN:
- no merge to main
- no direct commit to main
- no force push
- no branch deletion
- no Azure pipeline changes
- no Azure policy changes
- no Azure Boards/work-item edits
- no production/deployment
- no live UPS/provider/API calls
- no DeepSeek or paid LLM calls
- no secrets/PAT/password logging
- no TwinCore-framework source edits
- no TwinCore.sln edits
- no old AiIntegrator/G0/G1/G2 continuation
- no broad refactor
- no architecture changes outside the approved standalone solution
- no generated runtime dependency on TwinCore or LLM
- no production-readiness claim
- no AIKB writes
- no claude-vault edits

FIRST SAFE ACTION:
Read the Bridge contract, verify REQUEST_ID / PROJECT / GATE / PROMPT_PATH / REPORT_PATH, then verify the Azure repo and local source workspace before any write.

# GOAL

Import the local Universal API Connector Generator standalone solution into Igor's Azure DevOps repo AI-Integrator as a separate solution at repo root.

Create a branch, import only safe source/documentation/test files, run build/test, commit, push, and create a PR into main if access allows.

Do not merge.

# REPORT REQUIREMENTS

Write final sanitized report to:
UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md

Report must include REQUEST_ID, STATUS, PROJECT, GATE, target repo, target branch, PR target, files imported/excluded, privacy rename, README/.gitignore reconciliation, commands with exit codes, build/test result, CLI smoke, security/privacy scan, git status, commit result, push result, PR result, boundary check, blockers/deviations, and next safe step.

# CHAT REPLY ONLY

After writing the Bridge report, reply in Claude chat only:

Bridge report ready. Tell GPT: отчёт.
