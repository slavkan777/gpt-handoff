REQUEST_ID: REQ-2026-06-25-uacg-ai-integrator-initial-import-v0-1
STATE: READY_FOR_CLAUDE
PROJECT: UniversalApiConnectorGenerator
GATE: AI_INTEGRATOR_INITIAL_IMPORT_V0_1
TASK_TYPE: external-repo-import-branch-push-pr

PROMPT_PATH:
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

# CONTEXT

The previous preflight gate AI_INTEGRATOR_AZURE_PREFLIGHT_V0_1 found:

- Azure repo is accessible read-only.
- Default branch is main.
- Repo has only .gitignore and README.md.
- No existing solution is present.
- Repo is effectively empty/dedicated.
- Local UniversalApiConnectorGenerator builds successfully.
- Tests pass 29/29.
- Recommended import location: repo root.
- One pre-import fix is required: rename legacy personal-named demo-guide file to USER_DEMO_GUIDE_UA.md.
- README.md and .gitignore already exist in Azure repo and must be reconciled, not blindly overwritten.
- Import must exclude bin/obj/.vs/logs/output/runtime data/secrets.

Igor said:
- "закинь в це репо"
- "бо це окремий sln"
- "залиєш напишеш, це не терміново"

Interpretation:
- AI-Integrator repo is the target repo.
- UniversalApiConnectorGenerator remains standalone.
- Do not edit TwinCore-framework source projects.
- Do not import into TwinCore solution.
- Do not continue old G0/G1/G2 work.

# STOP CONDITIONS

Stop with BLOCKED if:

- Bridge cannot be read or final report cannot be written
- REQUEST_ID / PROJECT / GATE mismatch
- local workspace C:\Projects\UniversalApiConnectorGenerator is missing
- Azure repo cannot be cloned/opened safely
- Azure authentication requires secrets/PAT entry in chat/report
- current Azure repo is no longer effectively empty and contains unexpected source/solution files
- main is not the default branch anymore and target branch is ambiguous
- feature branch already exists with unrelated content
- build/test fails after import and cannot be fixed within the allowed scope
- any step would require merge to main, policy bypass, Boards edit, pipeline change, secrets, live provider calls, or production deployment
- any step would modify TwinCore-framework source projects or TwinCore.sln

# EXECUTION PLAN

## 1. Verify Bridge and routing

Verify:
- REQUEST_ID matches this prompt
- PROJECT = UniversalApiConnectorGenerator
- GATE = AI_INTEGRATOR_INITIAL_IMPORT_V0_1
- REPORT_PATH is writable
- STATUS.json points to this gate or active prompt clearly points to this request

If mismatch, stop with BLOCKED / ROUTING_LOCK_MISMATCH.

## 2. Verify local source workspace

Check:
- C:\Projects\UniversalApiConnectorGenerator exists
- UniversalApiConnectorGenerator.sln exists
- src/ exists
- tests/ exists
- docs/ exists

Run:
- dotnet restore
- dotnet build
- dotnet test

If local source does not build/test before import, stop with BLOCKED.

## 3. Prepare Azure repo working copy

Use a clean working directory, for example:
C:\Projects\AI-Integrator

If the folder already exists:
- verify it is the correct Azure repo
- verify remote URL matches TARGET_AZURE_REPO
- verify working tree clean
- fetch latest origin/main

If missing:
- clone TARGET_AZURE_REPO into C:\Projects\AI-Integrator

Then:
- checkout main
- pull latest
- create branch feature/uacg-initial-import from origin/main

Do not modify main directly.

## 4. Import source-only tree

Import into repo root:
- UniversalApiConnectorGenerator.sln
- src/
- tests/
- docs/
- README.md reconciled with existing Azure README
- .gitignore merged with existing VisualStudio .gitignore
- required fixtures/sample source files used by tests/UAT

Exclude:
- bin/
- obj/
- .vs/
- .idea/
- *.user
- *.suo
- logs/
- output/
- data/ runtime contents
- temp files
- secrets
- local user settings
- generated run artifacts
- generated packages unless they are explicitly part of reviewed fixtures

## 5. Required doc privacy rename

Before commit, handle the legacy personal-named demo-guide file:

- rename it to docs/USER_DEMO_GUIDE_UA.md
- update all references in docs/README/architecture/user guide/scenarios from the old filename to USER_DEMO_GUIDE_UA.md
- search for the old personal name in README.md, docs/, src/, tests/

No personal names should remain in imported repo unless there is a clearly justified historical reference. Prefer zero matches.

If personal-name references cannot be safely removed, stop and report BLOCKED_PRIVACY_REVIEW_REQUIRED.

## 6. README.md reconciliation

The Azure repo already has README.md. Do not blindly overwrite it.

Create a useful project README that includes:
- project name: Universal API Connector Generator
- short purpose
- note: standalone solution
- note: not Rate-only; Rate/UPS is first proof case
- requirements: .NET 9 SDK for current import; .NET 10 final target later
- commands: dotnet restore, dotnet build, dotnet test, inspect/generate/verify examples
- limitations: no live UPS calls, no credentials, no production claim, generated runtime has zero LLM dependency
- docs links: docs/USER_FULL_GUIDE_UA.md, docs/USER_AGENT_SCENARIOS_UA.md, docs/USER_DEMO_GUIDE_UA.md

## 7. .gitignore reconciliation

Merge project ignore rules into existing VisualStudio .gitignore.

Must exclude at minimum:
bin/
obj/
.vs/
.idea/
*.user
*.suo
logs/
output/
data/*.db
data/*.db-*
*.log
.env
*.secret*
secrets.*
appsettings.*.local.json

Do not ignore source/docs/tests/fixtures needed for build/test.

## 8. Verify imported repo

From C:\Projects\AI-Integrator run:
- git status
- dotnet restore
- dotnet build
- dotnet test

Optional safe CLI smoke:
- dotnet run --project src/ConnectorGenerator.Cli -- inspect
- dotnet run --project src/ConnectorGenerator.Cli -- verify

If fixture paths are known and safe:
- run one inspect/generate/verify scenario with local fixture only

Capture command, exit code, key output.

## 9. Security / privacy scan

Before commit, search imported repo for risky material:
- token/password/API key style strings
- DEEPSEEK_API_KEY values
- UPS credentials
- personal name from legacy docs
- absolute local user paths where not needed
- bin/obj/log/output artifacts accidentally staged

Use safe local grep/PowerShell/git grep commands. Do not print secret values if found; print only file path + category.

If real secret is found, stop with BLOCKED_SECRET_FOUND and do not commit.

## 10. Commit and push

If verification passes:
- git add only allowed files
- git status --short
- git commit -m "initial Universal API Connector Generator standalone solution"
- git push -u origin feature/uacg-initial-import

Do not force push.

## 11. Create PR if possible

Create PR from feature/uacg-initial-import to main.

PR title:
Initial Universal API Connector Generator standalone solution

PR description must include:
- What was added
- Why standalone
- Current target net9.0 temporary / net10.0 later
- Build/test evidence
- No TwinCore-framework source edits
- No live UPS/provider calls
- No production-readiness claim
- Docs included
- Review notes / limitations

If PR creation fails because of access/tooling:
- do not retry destructively
- report PR_BLOCKED_BY_ACCESS
- include pushed branch URL or exact branch name

# REPORT REQUIREMENTS

Write final sanitized report to:
UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md

Report must include:
REQUEST_ID: REQ-2026-06-25-uacg-ai-integrator-initial-import-v0-1
STATUS: READY_FOR_GPT_AUDIT or BLOCKED
PROJECT: UniversalApiConnectorGenerator
GATE: AI_INTEGRATOR_INITIAL_IMPORT_V0_1
TARGET_AZURE_REPO: https://dev.azure.com/twincore-net/twincore-framework/_git/AI-Integrator
TARGET_BRANCH: feature/uacg-initial-import
PR_TARGET_BRANCH: main
LOCAL_SOURCE_WORKSPACE: C:\Projects\UniversalApiConnectorGenerator
TARGET_WORKSPACE: C:\Projects\AI-Integrator

Sections:
1. Route Used
2. Routing Lock Verification
3. Starting State
4. Files Imported
5. Files Excluded
6. Privacy Rename Result
7. README/.gitignore Reconciliation
8. Commands Run and Exit Codes
9. Build/Test Result
10. CLI Smoke Result
11. Security/Privacy Scan Result
12. Git Status Before Commit
13. Commit Result
14. Push Result
15. PR Result
16. Boundary Check
17. Blockers / Deviations
18. Next Safe Step

# REQUIRED BOUNDARY CHECK IN REPORT

Include explicit yes/no:
- Local source workspace edited: yes/no
- Azure repo branch created: yes/no
- Azure repo main modified directly: yes/no
- Commit created on feature branch: yes/no
- Push performed to feature branch: yes/no
- PR created: yes/no
- Merge performed: yes/no
- TwinCore-framework source edited: yes/no
- TwinCore.sln edited: yes/no
- Old AiIntegrator/G0/G1/G2 continued: yes/no
- Secrets printed/stored: yes/no
- Live UPS/provider call: yes/no
- DeepSeek/paid LLM call: yes/no
- Deployment/production claim: yes/no
- AIKB edited: yes/no
- claude-vault edited: yes/no

Expected:
- feature branch may be created
- commit/push/PR may be yes if successful
- main/merge/TwinCore/secrets/live/deploy/AIKB/claude-vault must be NO

# CHAT REPLY ONLY

After writing the Bridge report, reply in Claude chat only:

Bridge report ready. Tell GPT: отчёт.
