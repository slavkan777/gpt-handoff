REQUEST_ID: REQ-2026-06-10-agentfactory-mvp1-workflows-improve-loop-v0-1
STATE: READY_FOR_CLAUDE
PROJECT: AgentFactory
GATE: AGENT_FACTORY_MVP1_WORKFLOWS_IMPROVE_LOOP_GATE
TASK_TYPE: implementation-docs-only
SOURCE_REPO: slavkan777/pi-setup
SOURCE_BRANCH: main
HANDOFF_REPO: slavkan777/gpt-handoff
ACTIVE_REQUEST_PATH: AgentFactory/agent-factory-mvp1-workflows-improve-loop-v0.1/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: AgentFactory/agent-factory-mvp1-workflows-improve-loop-v0.1/report.md
LATEST_REPORT_PATH: AgentFactory/latest-report.md
LATEST_SUMMARY_PATH: AgentFactory/latest-summary.json
AIKB_UPDATE_REQUIRED: no
AIKB_UPDATE_POLICY: only after Slava says "зафиксируй"
CREATED_BY: architect-gpt
CREATED_AT: 2026-06-10

# AGENT_FACTORY_MVP1_WORKFLOWS_IMPROVE_LOOP_GATE

## 0. Mission

Execute one consolidated docs-only MVP-1 gate for AgentFactory.

Goal: formalize the missing MVP-1 workflow specs and improve-loop rules in `slavkan777/pi-setup`, without building automation or infrastructure.

This gate should move AgentFactory from MVP-0 skeleton + first `/task` proof to MVP-1-ready workflow documentation.

Do not split into many small tasks. Do this as one bounded docs-only gate.

## 1. Current state

Already accepted:

```text
1. Repo `slavkan777/pi-setup` exists and is private.
2. MVP-0 docs-first skeleton exists.
3. First `/task` workflow was executed end-to-end.
4. No-secrets rule exists at `rules/NO_SECRETS.md`.
5. README links the no-secrets rule.
```

Known commits:

```text
Bootstrap skeleton: f75d0366f3b68816fbf6470ff1eb3b8e9530df76
First /task workflow: 9f1cbd15a6bb068fc5c09bb3221130daf5782994
```

## 2. Scope

Implement docs-only MVP-1 workflow formalization.

Allowed source repo changes:

```text
workflows/feature.md                 # create if missing
workflows/quick.md                   # create if missing
workflows/improve-loop.md            # create
workflows/task.md                    # update only if needed for consistency
workflows/review.md                  # update only if needed for consistency
workflows/recurse.md                 # update only if needed for consistency
docs/ai-workflows/PROJECT_AI_OPERATING_SYSTEM.md  # update MVP-1 section/links
README.md                            # add short MVP-1 workflow index pointer
docs/ai-reports/mvp1-workflows-improve-loop-v0.1.md  # in-repo run note/evidence
```

Optional only if a real process lesson is discovered:

```text
docs/ai-learnings/LOG.md
```

Forbidden source repo changes:

```text
- no executable automation;
- no production code;
- no scripts;
- no Cloudflare Worker/D1/Durable Object;
- no fleet daemon;
- no dashboard;
- no tmux wrapper;
- no remote launch;
- no credential vault;
- no secrets/tokens/credentials;
- no paid resources;
- no deleting/resetting/force-pushing;
- no unrelated rewrites;
- no AIKB changes;
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
docs/ai-workflows/PROJECT_AI_OPERATING_SYSTEM.md
docs/ai-learnings/LOG.md
rules/NO_SECRETS.md
agents/*.md
workflows/*.md
extensions/DEFERRED.md
fleet/DEFERRED.md
worker/DEFERRED.md
dashboard/DEFERRED.md
```

Write allowed in source repo:

```text
workflows/feature.md
workflows/quick.md
workflows/improve-loop.md
workflows/task.md
workflows/review.md
workflows/recurse.md
docs/ai-workflows/PROJECT_AI_OPERATING_SYSTEM.md
README.md
docs/ai-reports/mvp1-workflows-improve-loop-v0.1.md
docs/ai-learnings/LOG.md only if a real lesson is recorded
```

Report repository:

```text
slavkan777/gpt-handoff
```

Allowed handoff writes:

```text
AgentFactory/agent-factory-mvp1-workflows-improve-loop-v0.1/report.md
AgentFactory/latest-report.md
AgentFactory/latest-summary.json
AgentFactory/_BRIDGE/LATEST_REPORT.md        optional mirror
AgentFactory/_BRIDGE/STATUS.json             optional indicator
```

Stop conditions:

1. If `slavkan777/pi-setup` is missing or inaccessible, stop and report BLOCKED.
2. If current branch is not `main`, stop unless you can safely checkout/pull `main` without losing work.
3. If repo has unexpected dirty/uncommitted state, stop and report BLOCKED.
4. If there are existing conflicting workflow files that make safe docs-only update ambiguous, stop and report BLOCKED.
5. If any action might expose a token/secret, stop.

## 4. Required workflow specs

### workflows/feature.md

Define `/feature` as a larger workflow for new capability development.

Must include:

```text
Purpose
When to use
Phases:
1. brainstorm
2. review brainstorm
3. plan
4. review plan
5. build
6. break
7. verify
8. report
9. improve
10. owner audit/acceptance
Rules:
- no unrequested features;
- review between major phases;
- stop on scope expansion;
- no self-approval;
- GPT audit is still required.
```

### workflows/quick.md

Define `/quick` as small bounded docs/local-only task workflow.

Must include:

```text
Purpose
When to use
When NOT to use
Phases:
1. request
2. build
3. verify
4. report
5. improve/no lesson
Rules:
- only for low-risk bounded work;
- no architecture drift;
- no secrets/infrastructure;
- if risk appears, escalate to /task or /feature.
```

### workflows/improve-loop.md

Define the improve loop.

Must include:

```text
Purpose: make future sessions better without self-approval.
Inputs: report evidence, failure log, reviewer findings, owner decision.
Outputs: lesson candidate, rule candidate, template candidate, automation candidate.
Storage:
- project lessons: docs/ai-learnings/LOG.md
- durable AIKB promotion: only after Slava says “зафиксируй”
- no durable learnings in gpt-handoff
Rules:
- Improver proposes, does not approve;
- GPT audits;
- Slava accepts/promotes;
- no AIKB writes from AgentFactory gates;
- if no lesson, write `IMPROVE STEP: no lesson`.
```

### workflows/task.md

Ensure existing `/task` spec is aligned with the proven first workflow:

```text
request -> build -> break -> verify -> report -> audit -> improve
```

Do not over-expand. Keep it usable.

### workflows/review.md

Ensure `/review` remains inspect-only unless another gate explicitly allows changes.

Must include dimensions:

```text
correctness
architecture
security
```

Must say reviewer dedups findings and does not modify source files.

### workflows/recurse.md

Ensure `/recurse` includes explicit Failure Log and do-not-repeat rule.

Must include:

```text
ATTEMPT N:
- Request
- Action taken
- Result
- Evidence
- Why failed
- Root cause
- Do not repeat

REQUIRED CHANGE:
- New hypothesis
- Different approach
- New acceptance check
```

## 5. README / Operating doc updates

README.md should get a short workflow index pointer, not a huge rewrite.

Example:

```text
Workflow specs: see `workflows/` for `/task`, `/feature`, `/quick`, `/review`, `/recurse`, and `workflows/improve-loop.md`.
```

`docs/ai-workflows/PROJECT_AI_OPERATING_SYSTEM.md` should be updated to:

```text
- mark MVP-1 as workflow-specs + improve-loop formalization;
- link or mention the new workflow files;
- preserve MVP-2/MVP-3 as DEFERRED;
- preserve product-role vs governance-seat separation;
- preserve no-self-approval.
```

## 6. Verification requirements

Minimum verification:

```text
git status --short
git diff --stat
git diff -- README.md docs/ai-workflows/PROJECT_AI_OPERATING_SYSTEM.md workflows/feature.md workflows/quick.md workflows/improve-loop.md workflows/task.md workflows/review.md workflows/recurse.md docs/ai-reports/mvp1-workflows-improve-loop-v0.1.md docs/ai-learnings/LOG.md
check that no files outside allowed list changed
check that no secret-like strings were introduced
check that DEFERRED.md files are unchanged
check that no executable scripts were added
```

Use equivalent PowerShell/Git Bash commands if needed.

## 7. Done state

This gate is accepted when:

1. `workflows/feature.md` exists and defines `/feature` phases.
2. `workflows/quick.md` exists and defines `/quick` boundaries.
3. `workflows/improve-loop.md` exists and defines non-self-approving improve loop.
4. `workflows/task.md`, `review.md`, and `recurse.md` are consistent with MVP-1 rules.
5. README points to workflow specs.
6. `PROJECT_AI_OPERATING_SYSTEM.md` reflects MVP-1 workflow formalization and preserves deferrals.
7. Only allowed files changed.
8. No executable automation/scripts added.
9. No secrets/tokens/credentials added.
10. No Cloudflare/fleet/dashboard/tmux/worker implementation added.
11. Source commit SHA is reported.
12. Handoff report and latest summary are written.
13. Next safe step is stated.

## 8. Required report format

Report header:

```text
REQUEST_ID: REQ-2026-06-10-agentfactory-mvp1-workflows-improve-loop-v0-1
STATUS: READY_FOR_AUDIT | BLOCKED
TASK_TYPE: implementation-docs-only
PROJECT: AgentFactory
GATE: AGENT_FACTORY_MVP1_WORKFLOWS_IMPROVE_LOOP_GATE
COMPLETED: <YYYY-MM-DD>
COMPLETED_BY: claude
```

Report sections:

```text
## Current State
## Source Changes
## Workflow Specs Added / Updated
## Improve Loop
## Verification Evidence
## Done Criteria vs Evidence
## Boundaries Honored
## BLOCKED Reason
## Improve Step
## Next Safe Step
```

If blocked, make `## BLOCKED Reason` explicit.

## 9. latest-summary.json shape

Write `AgentFactory/latest-summary.json`:

```json
{
  "requestId": "REQ-2026-06-10-agentfactory-mvp1-workflows-improve-loop-v0-1",
  "project": "AgentFactory",
  "gate": "AGENT_FACTORY_MVP1_WORKFLOWS_IMPROVE_LOOP_GATE",
  "status": "READY_FOR_AUDIT_OR_BLOCKED",
  "completed": "YYYY-MM-DD",
  "completedBy": "claude",
  "sourceRepo": "slavkan777/pi-setup",
  "sourceBranch": "main",
  "sourceCommitBefore": "",
  "sourceCommitAfter": "",
  "filesChanged": [],
  "targetReportPath": "AgentFactory/agent-factory-mvp1-workflows-improve-loop-v0.1/report.md",
  "latestReportPath": "AgentFactory/latest-report.md",
  "blockedReason": "",
  "nextSafeStep": ""
}
```

## 10. Acceptance criteria for GPT audit

GPT will use:

```text
ACCEPT
ACCEPT_WITH_RISKS
REWORK_REQUIRED
BLOCKED
STALE_REPORT
```

Expected results:

- ACCEPT if MVP-1 docs are formalized, only allowed files changed, and evidence is sufficient.
- ACCEPT_WITH_RISKS if completed but some docs need later polish.
- REWORK_REQUIRED if workflow specs are missing, self-approval appears, durable learnings are placed in gpt-handoff, forbidden files changed, or verification is insufficient.
- BLOCKED if repo inaccessible or unsafe state prevents work.
- STALE_REPORT if REQUEST_ID / PROJECT / GATE mismatch.

## 11. Final instruction

Execute exactly this gate.

Do not ask Slava for more context.
Do not touch AIKB.
Do not touch InsuranceAIPlatform.
Do not implement automation.
Do not implement Cloudflare/fleet/dashboard/tmux/worker.
Do not add secrets or secret-looking examples.
Do not go beyond the allowed docs-only MVP-1 workflow formalization.

Write the report and latest summary only to the allowed AgentFactory handoff paths.