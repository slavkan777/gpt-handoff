REQUEST_ID: REQ-2026-06-10-agentfactory-first-runnable-task-workflow-v0-1
STATE: READY_FOR_CLAUDE
PROJECT: AgentFactory
GATE: AGENT_FACTORY_FIRST_RUNNABLE_TASK_WORKFLOW_GATE
TASK_TYPE: implementation-docs-only
SOURCE_REPO: slavkan777/pi-setup
SOURCE_BRANCH: main
HANDOFF_REPO: slavkan777/gpt-handoff
ACTIVE_REQUEST_PATH: AgentFactory/agent-factory-first-runnable-task-workflow-v0.1/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: AgentFactory/agent-factory-first-runnable-task-workflow-v0.1/report.md
LATEST_REPORT_PATH: AgentFactory/latest-report.md
LATEST_SUMMARY_PATH: AgentFactory/latest-summary.json
AIKB_UPDATE_REQUIRED: no
AIKB_UPDATE_POLICY: only after Slava says "зафиксируй"
CREATED_BY: architect-gpt
CREATED_AT: 2026-06-10

# AGENT_FACTORY_FIRST_RUNNABLE_TASK_WORKFLOW_GATE

## 0. Mission

Execute the first real, bounded AgentFactory `/task` workflow end-to-end in `slavkan777/pi-setup`.

This gate proves the local-only MVP-0 loop:

```text
request -> build -> break -> verify -> report -> audit -> improve
```

The actual product change must be tiny and docs-only.

Do not implement automation. Do not implement agents. Do not implement Cloudflare/fleet/dashboard/tmux. Do not touch secrets. Do not touch InsuranceAIPlatform. Do not touch AIKB.

## 1. Current state

Previous accepted gate created `slavkan777/pi-setup` as a private repo and committed the docs-first MVP-0 skeleton.

Bootstrap commit:

```text
f75d0366f3b68816fbf6470ff1eb3b8e9530df76
```

Skeleton includes:

```text
README.md
docs/ai-workflows/PROJECT_AI_OPERATING_SYSTEM.md
docs/ai-learnings/LOG.md
docs/ai-reports/.gitkeep
agents/*.md
workflows/task.md
workflows/review.md
workflows/recurse.md
configs/README.md
rules/README.md
skills/README.md
learnings/README.md
scripts/README.md
extensions/DEFERRED.md
fleet/DEFERRED.md
worker/DEFERRED.md
dashboard/DEFERRED.md
```

## 2. Scope of this first runnable `/task`

Implement one small docs-only feature inside `pi-setup`:

```text
Add an explicit no-secrets rule and link it from README.
```

Allowed source repo changes:

```text
rules/NO_SECRETS.md          # new file
README.md                    # add a short pointer/link to NO_SECRETS.md
docs/ai-reports/first-runnable-task-workflow-v0.1.md  # optional internal run note/evidence if useful
```

Optional if genuinely useful:

```text
docs/ai-learnings/LOG.md     # only if you add a real lesson from this gate; otherwise leave unchanged
```

Forbidden source repo changes:

```text
- no production code;
- no executable scripts;
- no Cloudflare/fleet/dashboard/tmux/worker implementation;
- no credentials;
- no tokens;
- no secrets;
- no env examples containing fake secrets that look real;
- no deleting/resetting/force-pushing;
- no unrelated file rewrites;
- no governance changes in AIKB;
- no InsuranceAIPlatform changes.
```

## 3. Routing Lock

Source/product repository:

```text
slavkan777/pi-setup
```

Source/product branch:

```text
main
```

Read allowed:

```text
README.md
rules/README.md
workflows/task.md
workflows/review.md
workflows/recurse.md
agents/*.md
docs/ai-workflows/PROJECT_AI_OPERATING_SYSTEM.md
docs/ai-learnings/LOG.md
```

Write allowed in source repo:

```text
rules/NO_SECRETS.md
README.md
docs/ai-reports/first-runnable-task-workflow-v0.1.md
docs/ai-learnings/LOG.md only if a real lesson is recorded
```

Report repository:

```text
slavkan777/gpt-handoff
```

Allowed handoff writes:

```text
AgentFactory/agent-factory-first-runnable-task-workflow-v0.1/report.md
AgentFactory/latest-report.md
AgentFactory/latest-summary.json
AgentFactory/_BRIDGE/LATEST_REPORT.md        optional mirror
AgentFactory/_BRIDGE/STATUS.json             optional indicator
```

Stop conditions:

1. If `slavkan777/pi-setup` is missing or inaccessible, stop and report BLOCKED.
2. If current branch is not `main`, stop unless you can safely checkout/pull `main` without losing work.
3. If repo has unexpected dirty/uncommitted state, stop and report BLOCKED.
4. If `rules/NO_SECRETS.md` already exists, do not duplicate; inspect it and either update minimally or report already done.
5. If any action might expose a token/secret, stop.

## 4. Required `/task` workflow simulation

Run the task explicitly through these phases and report each phase:

### Phase 1 — Request

Restate the task:

```text
Add a docs-only no-secrets rule and link it from README.
```

### Phase 2 — Build

Implement only the allowed docs change.

Minimum required content for `rules/NO_SECRETS.md`:

```text
# No Secrets Rule

Purpose: keep AgentFactory safe by preventing secrets, tokens, credentials, API keys, private keys, cookies, session dumps, or credential vault material from entering this repository.

Rules:
- Do not commit real secrets, tokens, credentials, private keys, cookies, session dumps, or credential vault material.
- Do not include fake secrets that look real.
- Use environment-variable names only when examples are needed.
- Keep credential-sync, encrypted vault, Cloudflare credentials, and remote bootstrap behind separate future security gates.
- If a task requires secrets, stop and ask for a security gate rather than improvising.

Status: mandatory for all future AgentFactory gates.
```

README pointer should be short, for example:

```text
Security boundary: see `rules/NO_SECRETS.md`. AgentFactory must not store secrets, tokens, credentials, or credential-vault material in this repo.
```

### Phase 3 — Break

Try to break the result. Check at minimum:

```text
- Is the rule too broad or vague?
- Does the README pointer exist?
- Did any forbidden file change?
- Are there secret-looking strings?
- Did the change accidentally imply Cloudflare/fleet implementation is allowed now?
```

### Phase 4 — Verify

Run verification appropriate for docs-only repo.

Minimum checks:

```text
git status --short
git diff --stat
git diff -- README.md rules/NO_SECRETS.md docs/ai-reports/first-runnable-task-workflow-v0.1.md docs/ai-learnings/LOG.md
find/grep for obvious secret-like patterns if available
confirm no Cloudflare/fleet/dashboard/tmux/worker implementation files were added beyond existing DEFERRED.md placeholders
```

Use equivalent PowerShell/Git Bash commands if needed.

### Phase 5 — Report

Write the required report to the handoff paths.

### Phase 6 — Improve

Only record a learning if there is a real lesson. Otherwise write:

```text
IMPROVE STEP: no lesson
```

Do not write to AIKB.

## 5. Done state

This gate is accepted when:

1. `slavkan777/pi-setup` is accessible on `main`.
2. `rules/NO_SECRETS.md` exists with the required no-secrets policy.
3. `README.md` links or points to `rules/NO_SECRETS.md`.
4. The `/task` workflow phases are represented in the report: request/build/break/verify/report/improve.
5. Only allowed source files changed.
6. No secrets/tokens/credentials were added.
7. No Cloudflare/fleet/dashboard/tmux/worker implementation was added.
8. Source commit SHA is reported.
9. Handoff report and latest summary are written.
10. Next safe step is stated.

## 6. Required report format

Report header:

```text
REQUEST_ID: REQ-2026-06-10-agentfactory-first-runnable-task-workflow-v0-1
STATUS: READY_FOR_AUDIT | BLOCKED
TASK_TYPE: implementation-docs-only
PROJECT: AgentFactory
GATE: AGENT_FACTORY_FIRST_RUNNABLE_TASK_WORKFLOW_GATE
COMPLETED: <YYYY-MM-DD>
COMPLETED_BY: claude
```

Report sections:

```text
## Current State
## /task Workflow Execution
### Request
### Build
### Break
### Verify
### Report
### Improve
## Source Changes
## Source Commit
## Verification Evidence
## Done Criteria vs Evidence
## Boundaries Honored
## BLOCKED Reason
## Next Safe Step
```

If blocked, make `## BLOCKED Reason` explicit.

## 7. latest-summary.json shape

Write `AgentFactory/latest-summary.json`:

```json
{
  "requestId": "REQ-2026-06-10-agentfactory-first-runnable-task-workflow-v0-1",
  "project": "AgentFactory",
  "gate": "AGENT_FACTORY_FIRST_RUNNABLE_TASK_WORKFLOW_GATE",
  "status": "READY_FOR_AUDIT_OR_BLOCKED",
  "completed": "YYYY-MM-DD",
  "completedBy": "claude",
  "sourceRepo": "slavkan777/pi-setup",
  "sourceBranch": "main",
  "sourceCommitBefore": "",
  "sourceCommitAfter": "",
  "filesChanged": [],
  "targetReportPath": "AgentFactory/agent-factory-first-runnable-task-workflow-v0.1/report.md",
  "latestReportPath": "AgentFactory/latest-report.md",
  "blockedReason": "",
  "nextSafeStep": ""
}
```

## 8. Acceptance criteria for GPT audit

GPT will use:

```text
ACCEPT
ACCEPT_WITH_RISKS
REWORK_REQUIRED
BLOCKED
STALE_REPORT
```

Expected results:

- ACCEPT if no-secrets rule and README pointer exist, only allowed files changed, verification evidence is sufficient, and no boundaries were violated.
- ACCEPT_WITH_RISKS if completed but evidence is thin or minor non-blocking issues exist.
- REWORK_REQUIRED if required files are missing, README not linked, forbidden files changed, or verification is insufficient.
- BLOCKED if repo inaccessible or unsafe state prevents work.
- STALE_REPORT if REQUEST_ID / PROJECT / GATE mismatch.

## 9. Final instruction

Execute exactly this gate.

Do not ask Slava for more context.
Do not touch AIKB.
Do not touch InsuranceAIPlatform.
Do not implement automation.
Do not implement Cloudflare/fleet/dashboard/tmux/worker.
Do not add secrets or secret-looking examples.
Do not go beyond the allowed docs-only task.

Write the report and latest summary only to the allowed AgentFactory handoff paths.