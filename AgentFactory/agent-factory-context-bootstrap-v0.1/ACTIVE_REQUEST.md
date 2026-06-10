REQUEST_ID: REQ-2026-06-10-agentfactory-context-bootstrap-v0-1
STATE: READY_FOR_CLAUDE
PROJECT: AgentFactory
GATE: AGENT_FACTORY_CONTEXT_BOOTSTRAP_GATE
TASK_TYPE: inspect-only
SOURCE_REPO: slavkan777/pi-setup
SOURCE_BRANCH: main
HANDOFF_REPO: slavkan777/gpt-handoff
ACTIVE_REQUEST_PATH: AgentFactory/agent-factory-context-bootstrap-v0.1/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: AgentFactory/agent-factory-context-bootstrap-v0.1/report.md
LATEST_REPORT_PATH: AgentFactory/latest-report.md
LATEST_SUMMARY_PATH: AgentFactory/latest-summary.json
AIKB_UPDATE_REQUIRED: no
AIKB_UPDATE_POLICY: only after Slava says "зафиксируй"
CREATED_BY: architect-gpt
CREATED_AT: 2026-06-10

# AGENT_FACTORY_CONTEXT_BOOTSTRAP_GATE

## 0. Mission

Read and execute this request as a bounded inspect/planning-only gate for AgentFactory.

Do not code. Do not create infrastructure. Do not create paid resources. Do not touch InsuranceAIPlatform. Do not improvise beyond this scope.

The goal is to turn the studied Agent Factory / pi.dev Setup context into a realistic build plan and repository bootstrap strategy, while first verifying whether the expected source repo exists and is accessible.

## 1. Current state

AgentFactory has been registered in AIKB as a separate project.

Canonical project intent:

```text
Build a self-improving, observable, governed multi-agent/fleet operating system on top of pi.dev.
```

AgentFactory must remain separate from:

```text
InsuranceAIPlatform
MCP Holding
Cloude/AIKB governance work
other product/source repos
```

Expected source/product repo:

```text
slavkan777/pi-setup
```

Bridge/report repo:

```text
slavkan777/gpt-handoff
```

Canonical request path:

```text
AgentFactory/agent-factory-context-bootstrap-v0.1/ACTIVE_REQUEST.md
```

Canonical report path:

```text
AgentFactory/agent-factory-context-bootstrap-v0.1/report.md
```

Project latest mirrors:

```text
AgentFactory/latest-report.md
AgentFactory/latest-summary.json
```

## 2. Source context already studied by GPT/Slava

Agent Factory was studied from `agent-factory.md` and follow-up handoff context.

Core idea:

```text
Agent Factory = one-repo source of truth + five-agent team + workflow orchestration + improve loop + extensions + fleet infrastructure.
```

Target one-repo source of truth:

```text
pi-setup
```

Expected contents:

```text
configs
agents
workflows
extensions
dashboard
fleet daemon
Cloudflare worker
install/bootstrap scripts
learnings
rules
skills
```

Agent team:

```text
Creator        — Claude Opus; builds/brainstorms/plans/codes; cannot add unrequested features.
Cross-reviewer — GPT; reviews every step from another model; cannot modify files.
Tester         — GPT; drives app/headless browser/screenshots; tries to break, not confirm.
Test-verifier  — Claude; verifies tester coverage.
Improver       — Claude; writes learnings/rules/skills; cannot touch app code.
```

Workflow concept:

```text
/feature: brainstorm -> review -> plan -> review -> code -> review -> test -> verify -> improve -> review
/task: no brainstorm
/quick: code -> review -> test -> improve
/recurse: work -> test -> evaluate -> retry with failure log
/review: correctness + architecture + security reviewers -> dedup/merge report
```

Important mechanisms:

```text
- clean context per subagent;
- review between phases;
- failure log: do not repeat fixes that already failed;
- improve step: every session makes the next session smarter;
- local session logging;
- optional extensions later: session bridge, context compressor, usage logger, notification ping;
- optional fleet later: Cloudflare Worker/D1, Durable Object WebSocket relay, fleet daemon, dashboard, tmux wrapper, headless bootstrap.
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
slavkan777/pi-setup repository metadata and files if the repo exists and is accessible.
This ACTIVE_REQUEST.md file.
AIKB project facts if already available to you.
```

Write allowed in source repo:

```text
NONE.
```

Write allowed in handoff repo:

```text
AgentFactory/agent-factory-context-bootstrap-v0.1/report.md
AgentFactory/latest-report.md
AgentFactory/latest-summary.json
AgentFactory/_BRIDGE/LATEST_REPORT.md        optional mirror
AgentFactory/_BRIDGE/STATUS.json             optional indicator
```

Forbidden repositories:

```text
slavkan777/InsuranceAIPlatform
unrelated repos
production/client repos
```

Forbidden actions:

```text
- no source repo code/file changes;
- no source repo commit/push;
- no repo creation;
- no deleting/cleaning/resetting files;
- no secrets;
- no tokens;
- no real credentials;
- no credential vault;
- no auto-sync credentials;
- no Cloudflare resource creation;
- no paid provider setup;
- no production deployment;
- no dashboard/fleet daemon/tmux implementation;
- no remote launch;
- no autonomous self-approval;
- no AIKB writes.
```

Stop condition:

If `slavkan777/pi-setup` is missing, inaccessible, empty in a way that prevents inspection, or does not match the expected AgentFactory scope, stop planning from repo evidence and write a `BLOCKED` report with exact reason and next action.

Required blocked message for missing/inaccessible repo:

```text
BLOCKED: source repo slavkan777/pi-setup not found or not accessible.
```

You may still include a clearly marked `INFERRED PLAN FROM PROVIDED CONTEXT` section if repo evidence is unavailable, but do not claim it is repository evidence.

## 4. Done state

This gate is done when the report provides all of the following:

1. Repo accessibility check completed for `slavkan777/pi-setup`.
2. Current source repo state is identified from repository evidence, or BLOCKED if repo missing/inaccessible.
3. AgentFactory is confirmed as separate from InsuranceAIPlatform.
4. Target `pi-setup` folder structure is proposed.
5. MVP-0 / MVP-1 / MVP-2 / MVP-3 are separated.
6. First runnable slice is defined.
7. Local-only mode vs Cloudflare/fleet mode is separated.
8. Risks and blocked zones are listed.
9. Exact next safe step is stated.
10. Report is written to `AgentFactory/agent-factory-context-bootstrap-v0.1/report.md`.
11. Latest mirrors are updated: `AgentFactory/latest-report.md` and `AgentFactory/latest-summary.json`.
12. No source code, infrastructure, credentials, Cloudflare resources, production deployment, or AIKB files are changed.

## 5. Required report format

Write the report in Markdown.

Header:

```text
REQUEST_ID: REQ-2026-06-10-agentfactory-context-bootstrap-v0-1
STATUS: READY_FOR_AUDIT | BLOCKED
TASK_TYPE: inspect-only
PROJECT: AgentFactory
GATE: AGENT_FACTORY_CONTEXT_BOOTSTRAP_GATE
COMPLETED: <YYYY-MM-DD>
COMPLETED_BY: claude
```

Sections:

```text
## Current State
## Repo Accessibility Check
## BLOCKED Reason
## Repository Evidence
## Inferred Plan From Provided Context
## Target Repo / Folder Structure
## MVP-0 Local-Only
## MVP-1 Workflows + Improve Loop
## MVP-2 Fleet Backend Plan
## MVP-3 Dashboard + Remote Bootstrap Plan
## First Runnable Slice
## Risks / Boundaries
## Done Criteria vs Evidence
## Files Changed
## Latest Summary JSON
## Improve Step
## Next Safe Step
```

Rules:

- If repo is missing/inaccessible, set `STATUS: BLOCKED` and make `## BLOCKED Reason` explicit.
- If repo exists, cite exact files/paths inspected.
- If a section is not applicable, write `N/A because...`.
- `Improve Step` should be conditional. If no lesson: `IMPROVE STEP: no lesson`.
- Do not weaken GPT audit by claiming self-verification is enough.

## 6. latest-summary.json shape

Write `AgentFactory/latest-summary.json` with this shape:

```json
{
  "requestId": "REQ-2026-06-10-agentfactory-context-bootstrap-v0-1",
  "project": "AgentFactory",
  "gate": "AGENT_FACTORY_CONTEXT_BOOTSTRAP_GATE",
  "status": "READY_FOR_AUDIT_OR_BLOCKED",
  "completed": "YYYY-MM-DD",
  "completedBy": "claude",
  "sourceRepo": "slavkan777/pi-setup",
  "sourceBranch": "main",
  "sourceRepoAccessible": false,
  "targetReportPath": "AgentFactory/agent-factory-context-bootstrap-v0.1/report.md",
  "latestReportPath": "AgentFactory/latest-report.md",
  "blockedReason": "",
  "filesChanged": [],
  "nextSafeStep": ""
}
```

`sourceRepoAccessible` must be true only if the repo was actually accessible and inspected.

## 7. Acceptance criteria for GPT audit

GPT will audit this report using:

```text
ACCEPT
ACCEPT_WITH_RISKS
REWORK_REQUIRED
BLOCKED
STALE_REPORT
```

Expected accepted outcomes:

- `ACCEPT` if repo exists and a grounded inspect/planning report is complete.
- `BLOCKED` if `slavkan777/pi-setup` is missing/inaccessible and the report correctly stops.
- `REWORK_REQUIRED` if Claude invents repo facts, touches forbidden paths, mixes InsuranceAIPlatform, or fails to write required report paths.
- `STALE_REPORT` if REQUEST_ID / PROJECT / GATE mismatch.

## 8. Final instruction

Execute exactly this gate.

Do not ask Slava for more context.
Do not code.
Do not create repos.
Do not modify source repo.
Do not touch AIKB.
Do not touch InsuranceAIPlatform.
Do not create Cloudflare resources.

Write the report and latest summary only to the allowed AgentFactory handoff paths.