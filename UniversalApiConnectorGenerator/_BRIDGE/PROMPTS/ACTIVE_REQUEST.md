REQUEST_ID: REQ-2026-06-25-uacg-ai-integrator-azure-preflight-v0-1
STATE: READY_FOR_CLAUDE
PROJECT: UniversalApiConnectorGenerator
GATE: AI_INTEGRATOR_AZURE_PREFLIGHT_V0_1
TASK_TYPE: external-repo-access-preflight-readonly

PROMPT_PATH:
UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/ACTIVE_REQUEST.md

CANONICAL_PROMPT_PATH:
UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/REQ-2026-06-25-uacg-ai-integrator-azure-preflight-v0-1.md

REPORT_PATH:
UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md

TARGET_AZURE_REPO:
https://dev.azure.com/twincore-net/twincore-framework/_git/AI-Integrator

LOCAL_SOURCE_WORKSPACE:
C:\Projects\UniversalApiConnectorGenerator

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

ROUTE USED: DIRECT_MACRO
WHY: this is an external Azure DevOps repository access/preflight gate only. It is not an implementation, import, commit, push, PR, or merge gate.

ALLOWED:
- read this Bridge prompt
- read UniversalApiConnectorGenerator Bridge README / STATUS / current request if available
- read local workspace C:\Projects\UniversalApiConnectorGenerator
- run local read-only/build/test verification commands
- check Azure DevOps repo access read-only
- run git read-only discovery commands
- optionally create a temporary read-only clone under a clearly named temp/preflight folder only for inspection
- write the final Bridge report

FORBIDDEN:
- no commit
- no push
- no PR
- no merge
- no branch creation in Azure repo
- no source code edits
- no project file edits
- no copying/importing project files into Azure repo yet
- no TwinCore-framework source edits
- no old AiIntegrator/ConnectorGen continuation
- no G2 continuation
- no AIKB writes
- no claude-vault edits
- no secrets/PAT/password logging
- no live UPS/provider/API calls
- no DeepSeek or paid LLM calls
- no deployment
- no production-readiness claim

FIRST SAFE ACTION:
Read the Bridge contract, verify REQUEST_ID / PROJECT / GATE / PROMPT_PATH / REPORT_PATH, then verify local workspace path and Azure repo access read-only.

# GOAL

Perform a read-only preflight before publishing the local Universal API Connector Generator standalone solution into Igor's Azure DevOps repo:

https://dev.azure.com/twincore-net/twincore-framework/_git/AI-Integrator

Determine:
- whether the Azure repo is accessible;
- default branch;
- existing repo tree;
- whether repo is empty or already contains files;
- whether a standalone solution can safely be imported later;
- where the solution should be placed;
- what should be included/excluded;
- what the next import/publish gate should do.

Do not publish anything in this gate.

# CONTEXT

Igor provided the Azure DevOps repo and said:

- "закинь в це репо"
- "бо це окремий sln"
- "залиєш напишеш, це не терміново"

Interpretation:
- AI-Integrator repo is the target repository for publishing.
- UniversalApiConnectorGenerator remains a standalone solution.
- This does NOT mean editing TwinCore-framework source projects.
- This does NOT mean continuing old G0/G1/G2 work blindly.
- This does NOT authorize push/PR/import yet.

Current accepted product boundary:
- Project is Universal API Connector Generator.
- It is not a Rate-only generator.
- Rate / UPS Rating is only the first proof case.
- Runtime generated connectors must have zero LLM dependency.
- No live UPS calls.
- No production-readiness claim.
- Slava is final acceptance.
- GPT is architect/auditor.
- Claude is bounded executor only.

# IMPORTANT AIKB CONTEXT TO RESPECT

UniversalApiConnectorGenerator was previously separated from TwinCore as a standalone technical project.

Older AiIntegrator/ConnectorGen/TwinCore.ConnectorGen.sln planning is historical/superseded for delivery location.

However, Igor has now explicitly provided a new Azure DevOps repo named AI-Integrator as the target repo.

Therefore:
- Target repo may now be AI-Integrator.
- Architecture remains standalone.
- Do not edit TwinCore source projects.
- Do not reference TwinCore source projects.
- Do not import into an existing TwinCore solution.
- Do not assume old G0/G1/G2 branch/workflow still applies unless repo evidence proves it and the next gate explicitly approves it.

# READ FIRST

1. UniversalApiConnectorGenerator/_BRIDGE/README.md
2. This active request
3. UniversalApiConnectorGenerator/_BRIDGE/STATUS.json if available
4. Local workspace:
   C:\Projects\UniversalApiConnectorGenerator
5. Azure DevOps repo metadata/tree read-only:
   https://dev.azure.com/twincore-net/twincore-framework/_git/AI-Integrator

# STOP CONDITIONS

Stop with BLOCKED if:

- Bridge cannot be read or final report cannot be written
- REQUEST_ID / PROJECT / GATE mismatch
- local workspace C:\Projects\UniversalApiConnectorGenerator is missing
- Azure repo requires manual login / PAT / MFA and read-only access cannot be confirmed safely
- any command would expose secrets/tokens/passwords
- Azure repo has existing unrelated content and import plan is ambiguous
- any step would require commit/push/PR/branch creation/source edits
- any step would modify TwinCore-framework source projects
- any step would modify old AiIntegrator/G0/G1/G2 work

# PREFLIGHT CHECKS

## 1. Local workspace check

Verify:

- C:\Projects\UniversalApiConnectorGenerator exists
- UniversalApiConnectorGenerator.sln exists
- expected folders/projects exist:
  - src/ConnectorGenerator.Cli
  - src/ConnectorGenerator.Domain
  - src/ConnectorGenerator.Application
  - src/ConnectorGenerator.Infrastructure
  - src/ConnectorGenerator.Profiles.Rate
  - tests/ConnectorGenerator.Tests
  - docs/
- current target framework used locally
- whether local workspace is already a git repo
- whether local workspace has unexpected dirty/generated artifacts

Run safe commands if possible:

- dotnet --list-sdks
- dotnet restore
- dotnet build
- dotnet test

Capture:
- command
- exit code
- key output lines
- test count
- warnings/errors count

## 2. Local publish-readiness check

Determine what should be included in a future import:

Likely include:
- UniversalApiConnectorGenerator.sln
- src/
- tests/
- docs/
- README.md if present
- .gitignore if present
- required fixtures/sample source files if they are part of tests/UAT
- minimal placeholder folders only if needed and safe

Likely exclude:
- bin/
- obj/
- .vs/
- user-specific settings
- logs runtime files
- output generated run artifacts unless explicitly intended
- secrets
- local temp files
- generated packages unless specifically part of reviewed fixtures
- any private credentials/API keys/PATs

Do not change files in this gate.

## 3. Azure repo access check

Check read-only access to:

https://dev.azure.com/twincore-net/twincore-framework/_git/AI-Integrator

Use safe commands only. Examples:

- git ls-remote <repo-url>
- git remote checks if already cloned
- optional temporary read-only clone for inspection only, if safe and needed

Do not print or store secrets.

Determine:

- ACCESS_OK: yes/no
- AUTH_MODE_OBSERVED: already-authenticated / needs-login / unknown
- DEFAULT_BRANCH: main/master/other/unknown
- REMOTE_URL_CONFIRMED: yes/no
- REPO_EMPTY: yes/no/unknown
- EXISTING_TOP_LEVEL_FILES:
- EXISTING_SOLUTIONS:
- EXISTING_BRANCHES_VISIBLE:
- ANY_RISK_OF_OVERWRITE:
- IS_THIS_REPO_SEPARATE_FROM_TWINCORE_SOURCE: yes/no/unknown based on observed tree

## 4. Azure repo tree assessment

If repo is empty:
- recommend importing standalone solution at repo root in next gate.

If repo is not empty:
- inspect tree read-only.
- identify whether existing files look related/unrelated.
- recommend either:
  A. root import if safe;
  B. subfolder import if repo has existing structure;
  C. BLOCKED until Slava/Igor clarifies exact placement.

Do not guess if evidence is weak.

## 5. Next gate proposal

Prepare a recommended next gate plan.

If repo is empty:
- proposed next gate: AI_INTEGRATOR_INITIAL_IMPORT_V0_1
- proposed branch: feature/uacg-initial-import
- proposed PR target: default branch
- proposed commit message:
  initial Universal API Connector Generator standalone solution
- proposed import location:
  repo root

If repo is not empty:
- proposed next gate depends on actual tree.
- recommend exact location only if safe.
- otherwise mark BLOCKED_NEEDS_TARGET_PATH_CONFIRMATION.

The next gate must still be separate from this one and must explicitly authorize:
- branch creation
- file import/copy
- build/test after import
- commit
- push
- optional PR

# REPORT REQUIREMENTS

Write final sanitized report to:

UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md

Report must include:

REQUEST_ID: REQ-2026-06-25-uacg-ai-integrator-azure-preflight-v0-1
STATUS: READY_FOR_GPT_AUDIT or BLOCKED
PROJECT: UniversalApiConnectorGenerator
GATE: AI_INTEGRATOR_AZURE_PREFLIGHT_V0_1
TARGET_AZURE_REPO: https://dev.azure.com/twincore-net/twincore-framework/_git/AI-Integrator
LOCAL_SOURCE_WORKSPACE: C:\Projects\UniversalApiConnectorGenerator

Sections:

1. Route Used
2. Routing Lock Verification
3. Bridge Verification
4. Local Workspace Verification
5. Local Build/Test Evidence
6. Local Publish-Readiness Assessment
7. Azure Repo Access Result
8. Azure Repo Branch/Tree Result
9. Repo Empty or Existing Content Assessment
10. Proposed Safe Import Location
11. Proposed Next Gate
12. Proposed Branch / Commit / PR Plan
13. Files/Folders To Import
14. Files/Folders To Exclude
15. Risks / Ambiguities
16. Blockers / Manual Actions Needed
17. Boundary Check
18. Next Safe Step

# REQUIRED BOUNDARY CHECK IN REPORT

Include explicit yes/no for:

- Source code edited in local project: yes/no
- Azure repo modified: yes/no
- Branch created: yes/no
- Commit created: yes/no
- Push performed: yes/no
- PR created: yes/no
- TwinCore-framework source edited: yes/no
- Old AiIntegrator/G0/G1/G2 continued: yes/no
- Secrets printed/stored: yes/no
- Live UPS/provider call: yes/no
- Deployment/production claim: yes/no

Expected answer for all above in this gate should be NO.

# CHAT REPLY ONLY

After writing the Bridge report, reply in Claude chat only:

Bridge report ready. Tell GPT: отчёт.
