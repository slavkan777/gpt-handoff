REQUEST_ID: REQ-2026-06-10-agentfactory-first-review-run-v0-1
STATE: READY_FOR_CLAUDE
PROJECT: AgentFactory
GATE: AGENT_FACTORY_FIRST_REVIEW_RUN_GATE
TASK_TYPE: audit
SOURCE_REPO: slavkan777/pi-setup
SOURCE_BRANCH: main
HANDOFF_REPO: slavkan777/gpt-handoff
ACTIVE_REQUEST_PATH: AgentFactory/agent-factory-first-review-run-v0.1/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: AgentFactory/agent-factory-first-review-run-v0.1/report.md
LATEST_REPORT_PATH: AgentFactory/latest-report.md
LATEST_SUMMARY_PATH: AgentFactory/latest-summary.json
AIKB_UPDATE_REQUIRED: no
AIKB_UPDATE_POLICY: only after Slava says "зафиксируй"
CREATED_BY: architect-gpt
CREATED_AT: 2026-06-10

# AGENT_FACTORY_FIRST_REVIEW_RUN_GATE

## 0. Mission

Execute the first formal `/review` workflow run over the whole `slavkan777/pi-setup` tree.

This is an inspect-only gate. Do not modify the source repo.

Goal: validate the MVP-0/MVP-1 docs foundation after these accepted gates:

```text
1. Repo bootstrap skeleton: f75d0366f3b68816fbf6470ff1eb3b8e9530df76
2. First /task workflow: 9f1cbd15a6bb068fc5c09bb3221130daf5782994
3. MVP-1 workflows/improve-loop: 16104cf32aca25d40072d68e1e352c89b01e227b
```

Review dimensions:

```text
correctness
architecture
security
```

Do not implement fixes. Do not update source files. Write a deduped report only.

## 1. Current state

AgentFactory is now at MVP-1 docs-ready state:

```text
- pi-setup exists and is private.
- MVP-0 skeleton exists.
- /task workflow was proven once end-to-end.
- no-secrets rule exists.
- MVP-1 workflow specs are formalized.
- MVP-2 fleet backend remains DEFERRED.
- MVP-3 dashboard/remote bootstrap remains DEFERRED.
```

## 2. Routing Lock

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
Entire repository tree for audit only.
```

Write allowed in source repo:

```text
NONE.
```

Report repository:

```text
slavkan777/gpt-handoff
```

Allowed handoff writes:

```text
AgentFactory/agent-factory-first-review-run-v0.1/report.md
AgentFactory/latest-report.md
AgentFactory/latest-summary.json
AgentFactory/_BRIDGE/LATEST_REPORT.md        optional mirror
AgentFactory/_BRIDGE/STATUS.json             optional indicator
```

Forbidden actions:

```text
- no source repo edits;
- no source repo commit/push;
- no AIKB writes;
- no InsuranceAIPlatform changes;
- no Cloudflare/fleet/dashboard/tmux/worker implementation;
- no scripts/automation implementation;
- no secrets/tokens/credentials;
- no paid resources;
- no deleting/resetting/force-pushing;
- no self-approval.
```

Stop conditions:

1. If `slavkan777/pi-setup` is missing or inaccessible, stop and report BLOCKED.
2. If current branch is not `main`, stop unless you can safely inspect `main` without changing repo state.
3. If repo has unexpected dirty/uncommitted state, do not alter it; report BLOCKED or inspect remote only.
4. If any action might expose a token/secret, stop.

## 3. Required review scope

Inspect at minimum:

```text
README.md
rules/NO_SECRETS.md
docs/ai-workflows/PROJECT_AI_OPERATING_SYSTEM.md
docs/ai-learnings/LOG.md
docs/ai-reports/*.md
agents/*.md
workflows/*.md
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

## 4. Review dimensions

### Correctness review

Check:

```text
- Do all documented workflows match the accepted AgentFactory intent?
- Are `/task`, `/feature`, `/quick`, `/review`, `/recurse`, and `improve-loop` internally consistent?
- Does README point to the right files?
- Are deferrals clear and not contradicted elsewhere?
- Are project roles separated from governance roles?
- Does the docs tree match the intended MVP-0/MVP-1 state?
```

### Architecture review

Check:

```text
- Is the repo structured as a clean one-repo source of truth?
- Is MVP-0/MVP-1 local-only boundary preserved?
- Are MVP-2/MVP-3 separated and deferred cleanly?
- Is the improve loop non-self-approving?
- Are agent roles composable without creating uncontrolled swarm behavior?
- Are future automation seams obvious without implementing automation now?
```

### Security review

Check:

```text
- No secrets, tokens, credentials, private keys, cookies, or vault material.
- No realistic fake secrets.
- No scripts or executable automation introduced.
- No Cloudflare/fleet/dashboard/tmux/worker implementation.
- No credential sync or remote bootstrap enabled.
- No language that implies secrets can be committed later.
- No path that weakens the no-secrets rule.
```

## 5. Finding severity

Classify every finding:

```text
BLOCKER       -> must fix before any next workflow run
HIGH          -> should fix before MVP-1 accepted
MEDIUM        -> fix soon but not blocking first local proof
LOW           -> polish
OBSERVATION   -> note only
```

Deduplicate findings across correctness/architecture/security.

If no findings in a category, say explicitly:

```text
No findings.
```

## 6. Required verdict

Claude must not self-accept. But it must recommend one of:

```text
RECOMMEND_ACCEPT
RECOMMEND_ACCEPT_WITH_RISKS
RECOMMEND_REWORK
RECOMMEND_BLOCKED
```

GPT will make the actual audit verdict.

## 7. Done state

This gate is done when:

1. `pi-setup` was inspected read-only.
2. Correctness review completed.
3. Architecture review completed.
4. Security review completed.
5. Findings are severity-classified and deduped.
6. Report states whether source repo was modified; expected answer: no.
7. Report states whether any secrets/executable automation/infra were found.
8. Report includes recommended next safe step.
9. Handoff report and latest summary are written.

## 8. Required report format

Report header:

```text
REQUEST_ID: REQ-2026-06-10-agentfactory-first-review-run-v0-1
STATUS: READY_FOR_AUDIT | BLOCKED
TASK_TYPE: audit
PROJECT: AgentFactory
GATE: AGENT_FACTORY_FIRST_REVIEW_RUN_GATE
COMPLETED: <YYYY-MM-DD>
COMPLETED_BY: claude
```

Sections:

```text
## Current State
## Review Scope
## Correctness Review
## Architecture Review
## Security Review
## Deduped Findings
## Verification Evidence
## Source Modification Check
## Recommended Verdict
## Done Criteria vs Evidence
## Boundaries Honored
## BLOCKED Reason
## Improve Step
## Next Safe Step
```

## 9. latest-summary.json shape

Write `AgentFactory/latest-summary.json`:

```json
{
  "requestId": "REQ-2026-06-10-agentfactory-first-review-run-v0-1",
  "project": "AgentFactory",
  "gate": "AGENT_FACTORY_FIRST_REVIEW_RUN_GATE",
  "status": "READY_FOR_AUDIT_OR_BLOCKED",
  "completed": "YYYY-MM-DD",
  "completedBy": "claude",
  "sourceRepo": "slavkan777/pi-setup",
  "sourceBranch": "main",
  "sourceCommitInspected": "",
  "sourceModified": false,
  "findingCounts": {
    "blocker": 0,
    "high": 0,
    "medium": 0,
    "low": 0,
    "observation": 0
  },
  "recommendedVerdict": "",
  "targetReportPath": "AgentFactory/agent-factory-first-review-run-v0.1/report.md",
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

- ACCEPT if the review found no BLOCKER/HIGH issues and the report is well-evidenced.
- ACCEPT_WITH_RISKS if only LOW/MEDIUM polish findings exist and boundaries were honored.
- REWORK_REQUIRED if BLOCKER/HIGH findings exist or evidence is weak.
- BLOCKED if repo cannot be inspected safely.
- STALE_REPORT if REQUEST_ID / PROJECT / GATE mismatch.

## 11. Final instruction

Execute exactly this gate.

Do not ask Slava for more context.
Do not modify `pi-setup`.
Do not touch AIKB.
Do not touch InsuranceAIPlatform.
Do not implement automation.
Do not create infra.
Do not add secrets.
Do not self-accept.

Write the report and latest summary only to the allowed AgentFactory handoff paths.