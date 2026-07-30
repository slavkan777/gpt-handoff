REQUEST_ID: REQ-2026-06-10-agentfactory-repo-create-bootstrap-skeleton-v0-1
STATE: READY_FOR_CLAUDE
PROJECT: AgentFactory
GATE: AGENT_FACTORY_REPO_CREATE_BOOTSTRAP_SKELETON_V0.1
TASK_TYPE: repo-bootstrap-docs-only
SOURCE_REPO: slavkan777/pi-setup
SOURCE_BRANCH: main
HANDOFF_REPO: slavkan777/gpt-handoff
ACTIVE_REQUEST_PATH: AgentFactory/agent-factory-repo-create-bootstrap-skeleton-v0.1/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: AgentFactory/agent-factory-repo-create-bootstrap-skeleton-v0.1/report.md
LATEST_REPORT_PATH: AgentFactory/latest-report.md
LATEST_SUMMARY_PATH: AgentFactory/latest-summary.json
AIKB_UPDATE_REQUIRED: no
AIKB_UPDATE_POLICY: only after Slava says "зафиксируй"
CREATED_BY: architect-gpt
CREATED_AT: 2026-06-10

# AGENT_FACTORY_REPO_CREATE_BOOTSTRAP_SKELETON_V0.1

## 0. Mission

Execute one consolidated bootstrap gate for AgentFactory.

This gate is intentionally larger than the previous one to avoid many small prompts.

Goal:

1. Create the missing source repo `slavkan777/pi-setup` if it does not exist and if the current environment is authorized to do so.
2. Add a minimal docs-first AgentFactory skeleton.
3. Keep this local-only / docs-only / no-infrastructure.
4. Write one report back to `gpt-handoff`.

This is not AgentFactory fleet implementation. This is not Cloudflare. This is not dashboard. This is not credential sync.

## 1. Current state from previous accepted gate

Previous gate:

```text
REQUEST_ID: REQ-2026-06-10-agentfactory-context-bootstrap-v0-1
PROJECT: AgentFactory
GATE: AGENT_FACTORY_CONTEXT_BOOTSTRAP_GATE
STATUS: BLOCKED
```

Previous result:

```text
BLOCKED: source repo slavkan777/pi-setup not found or not accessible.
```

Reason: owner-authenticated GitHub API returned 404 and repo was absent from owner repo list.

Slava now wants to move faster and avoid 5000 small prompts. This gate explicitly authorizes repo creation for this exact repo only:

```text
slavkan777/pi-setup
```

## 2. Non-negotiable boundaries

Do NOT touch:

```text
slavkan777/InsuranceAIPlatform
InsuranceAIPlatform bridge paths
MCP Holding source paths
any unrelated repo
```

Do NOT create or configure:

```text
Cloudflare Worker
D1
Durable Object
fleet daemon
dashboard
tmux wrapper
headless VPS bootstrap
encrypted credential vault
auto-sync credentials
remote launch
paid provider resources
production deployment
```

Do NOT store:

```text
secrets
tokens
real credentials
private credential material
PII
```

Do NOT modify AIKB in this gate.

Do NOT create uncontrolled agent swarm.

Do NOT self-approve. Claude reports; GPT audits; Slava accepts.

## 3. Routing Lock

Allowed source/product repository:

```text
slavkan777/pi-setup
```

Allowed branch:

```text
main
```

Allowed source repo actions:

```text
1. If repo does not exist: create private GitHub repo `slavkan777/pi-setup` only.
2. Initialize default branch `main`.
3. Add minimal docs-first skeleton files listed below.
4. Commit and push the bootstrap skeleton.
```

Forbidden source repo actions:

```text
- no production code;
- no Cloudflare/fleet/dashboard/tmux code;
- no scripts that touch secrets/credentials;
- no paid resources;
- no external deployment;
- no deleting/resetting/force-pushing;
- no modifying unrelated files outside the explicit skeleton list.
```

Allowed handoff repo writes:

```text
AgentFactory/agent-factory-repo-create-bootstrap-skeleton-v0.1/report.md
AgentFactory/latest-report.md
AgentFactory/latest-summary.json
AgentFactory/_BRIDGE/LATEST_REPORT.md        optional mirror
AgentFactory/_BRIDGE/STATUS.json             optional indicator
```

Stop conditions:

1. If GitHub repo creation is not authorized or not possible from the environment, stop and report `BLOCKED: cannot create slavkan777/pi-setup from current environment`.
2. If `slavkan777/pi-setup` already exists but is inaccessible, stop and report `BLOCKED: source repo exists but is inaccessible`.
3. If `slavkan777/pi-setup` exists and contains unrelated content, stop and report `BLOCKED: source repo not empty / scope mismatch` with evidence.
4. If any command would expose a token/secret, stop.

## 4. Required minimal skeleton

Create this minimal tree in `slavkan777/pi-setup`:

```text
pi-setup/
  README.md
  docs/
    ai-workflows/
      PROJECT_AI_OPERATING_SYSTEM.md
    ai-learnings/
      LOG.md
    ai-reports/
      .gitkeep
  agents/
    creator.md
    cross-reviewer.md
    tester.md
    test-verifier.md
    improver.md
  workflows/
    task.md
    review.md
    recurse.md
  configs/
    README.md
  rules/
    README.md
  skills/
    README.md
  learnings/
    README.md
  scripts/
    README.md
  extensions/
    DEFERRED.md
  fleet/
    DEFERRED.md
  worker/
    DEFERRED.md
  dashboard/
    DEFERRED.md
```

No executable scripts are required in this gate. `scripts/README.md` is enough.

## 5. File content requirements

### README.md

Must state:

- Project: AgentFactory / pi.dev Setup.
- Purpose: self-improving, observable, governed multi-agent/fleet operating system on top of pi.dev.
- Source of truth: this repo.
- Current status: MVP-0 skeleton only.
- Governance: built under AIKB Project Bridge Protocol v1.2.
- Boundaries: no secrets, no Cloudflare/fleet/dashboard yet, no auto-sync credentials, no self-approval.
- First runnable target: `/task` workflow local-only, future gate.

### docs/ai-workflows/PROJECT_AI_OPERATING_SYSTEM.md

Must define:

- AgentFactory product roles vs governance seats.
- Product roles: Creator, Cross-reviewer, Tester, Test-verifier, Improver.
- Governance seats: Slava owner gate, GPT architect/auditor, Claude bounded executor.
- MVP phases:
  - MVP-0: local-only skeleton and documented workflows.
  - MVP-1: workflows + improve loop.
  - MVP-2: fleet backend plan, deferred.
  - MVP-3: dashboard + remote bootstrap plan, deferred.
- Forbidden zones.

### docs/ai-learnings/LOG.md

Must contain empty learning log with format:

```text
# AgentFactory — AI Learnings Log

Durable project-level lessons for AgentFactory.
Do not store secrets or raw transcripts.

## Entries

No entries yet.
```

### agents/*.md

Each agent file must be short and explicit:

- role;
- allowed actions;
- forbidden actions;
- why the role exists.

Important:

- `cross-reviewer.md` must say reviewer cannot modify source files.
- `improver.md` must say improver cannot touch app code or approve its own lessons.

### workflows/task.md

Must define the first future runnable workflow:

```text
/task = plan-known work without brainstorm.
Phases: request -> build -> break -> verify -> report -> audit -> improve.
```

Must say it is not executable automation yet; it is a workflow spec.

### workflows/review.md

Must define parallel review dimensions:

```text
correctness
architecture
security
```

Must say final report dedups findings and does not modify source files.

### workflows/recurse.md

Must include Failure Log rule:

```text
Do not repeat fixes that already failed.
```

### DEFERRED.md files

Must state the component is intentionally deferred and requires separate future gate.

## 6. Done state

This gate is done when:

1. `slavkan777/pi-setup` exists.
2. Default branch is `main`.
3. Minimal skeleton files listed above exist.
4. No forbidden infrastructure or secrets were created.
5. No InsuranceAIPlatform or unrelated repo was touched.
6. Commit SHA for `pi-setup` bootstrap is reported.
7. Report is written to `AgentFactory/agent-factory-repo-create-bootstrap-skeleton-v0.1/report.md`.
8. Latest mirrors are updated:
   - `AgentFactory/latest-report.md`
   - `AgentFactory/latest-summary.json`
9. Summary JSON includes source repo existence/accessibility and commit SHA.
10. Next safe step is stated as `AGENT_FACTORY_FIRST_RUNNABLE_TASK_WORKFLOW_GATE` or BLOCKED reason if not completed.

## 7. Required report format

Report header:

```text
REQUEST_ID: REQ-2026-06-10-agentfactory-repo-create-bootstrap-skeleton-v0-1
STATUS: READY_FOR_AUDIT | BLOCKED
TASK_TYPE: repo-bootstrap-docs-only
PROJECT: AgentFactory
GATE: AGENT_FACTORY_REPO_CREATE_BOOTSTRAP_SKELETON_V0.1
COMPLETED: <YYYY-MM-DD>
COMPLETED_BY: claude
```

Sections:

```text
## Current State
## Repo Creation / Accessibility
## Bootstrap Commit
## Files Created
## Done Criteria vs Evidence
## Boundaries Honored
## BLOCKED Reason
## Improve Step
## Next Safe Step
```

If blocked, make `## BLOCKED Reason` explicit.

If completed, include exact commit SHA and file list.

## 8. latest-summary.json shape

Write `AgentFactory/latest-summary.json`:

```json
{
  "requestId": "REQ-2026-06-10-agentfactory-repo-create-bootstrap-skeleton-v0-1",
  "project": "AgentFactory",
  "gate": "AGENT_FACTORY_REPO_CREATE_BOOTSTRAP_SKELETON_V0.1",
  "status": "READY_FOR_AUDIT_OR_BLOCKED",
  "completed": "YYYY-MM-DD",
  "completedBy": "claude",
  "sourceRepo": "slavkan777/pi-setup",
  "sourceBranch": "main",
  "sourceRepoAccessible": false,
  "sourceRepoCreated": false,
  "bootstrapCommit": "",
  "targetReportPath": "AgentFactory/agent-factory-repo-create-bootstrap-skeleton-v0.1/report.md",
  "latestReportPath": "AgentFactory/latest-report.md",
  "blockedReason": "",
  "filesChanged": [],
  "nextSafeStep": ""
}
```

## 9. Acceptance criteria for GPT audit

GPT will use:

```text
ACCEPT
ACCEPT_WITH_RISKS
REWORK_REQUIRED
BLOCKED
STALE_REPORT
```

Expected results:

- `ACCEPT` if repo was created/verified and skeleton exists with correct boundaries.
- `BLOCKED` if repo creation is not possible or permission is missing.
- `REWORK_REQUIRED` if source repo contains wrong files, InsuranceAIPlatform was touched, secrets were exposed, report paths are missing, or skeleton differs materially from the required structure.
- `STALE_REPORT` if REQUEST_ID / PROJECT / GATE mismatch.

## 10. Final instruction

Execute exactly this gate.

Do not ask Slava for more context.
Do not touch AIKB.
Do not touch InsuranceAIPlatform.
Do not create Cloudflare resources.
Do not create secrets or credentials.
Do not implement fleet/dashboard/tmux.
Do not go beyond the minimal docs-first skeleton.

Write the report and latest summary only to the allowed AgentFactory handoff paths.