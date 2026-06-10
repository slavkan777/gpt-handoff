REQUEST_ID: REQ-2026-06-10-agentfactory-mvp1-workflows-improve-loop-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: implementation-docs-only
PROJECT: AgentFactory
GATE: AGENT_FACTORY_MVP1_WORKFLOWS_IMPROVE_LOOP_GATE
COMPLETED: 2026-06-10
COMPLETED_BY: claude

## Current State

MVP-1 workflow documentation is formalized in `slavkan777/pi-setup` as one consolidated docs-only gate. Stop-conditions were clean before work (branch `main`, empty tree, local=origin 0/0, base `9f1cbd1`, no conflicting workflow files — `feature.md`/`quick.md`/`improve-loop.md` did not exist). AgentFactory moves from "MVP-0 skeleton + one proven `/task`" to "MVP-1-ready workflow docs": all five workflows + the improve loop are specified, mutually consistent, and linked from README and the operating doc. No automation, no infrastructure, no secrets.

## Source Changes

| File | Change |
|---|---|
| `workflows/feature.md` | NEW — `/feature` spec |
| `workflows/quick.md` | NEW — `/quick` spec |
| `workflows/improve-loop.md` | NEW — improve-loop spec |
| `workflows/recurse.md` | UPDATED — explicit Failure Log template added (+18 lines) |
| `workflows/review.md` | UPDATED — explicit inspect-only rule (+1 line) |
| `workflows/task.md` | UPDATED — cross-reference to improve-loop (1 line) |
| `README.md` | UPDATED — workflow index pointer (+2 lines) |
| `docs/ai-workflows/PROJECT_AI_OPERATING_SYSTEM.md` | UPDATED — MVP-1 marked formalized with file links; gate history section; MVP-2/3 stay DEFERRED |
| `docs/ai-reports/mvp1-workflows-improve-loop-v0.1.md` | NEW — in-repo run note |

`docs/ai-learnings/LOG.md` intentionally unchanged (no real lesson — see Improve Step).

## Workflow Specs Added / Updated

- **`/feature`** — purpose, when-to-use, all 10 required phases (brainstorm → review brainstorm → plan → review plan → build → break → verify → report → improve → owner audit/acceptance), all 5 required rules verbatim in substance: no unrequested features; review between major phases; stop on scope expansion; no self-approval; **GPT audit is still required**.
- **`/quick`** — purpose, when-to-use, explicit **when-NOT-to-use**, 5 phases (request → build → verify → report → improve/no-lesson), rules: low-risk bounded only; no architecture drift; no secrets/infrastructure; **escalate to `/task`//`/feature` if risk appears**.
- **`improve-loop`** — purpose (better future sessions **without self-approval**); inputs (report evidence, failure log, reviewer findings, owner decision); outputs (lesson/rule/template/automation candidates); storage exactly per gate: project lessons → `docs/ai-learnings/LOG.md`, durable AIKB promotion **only after «зафиксируй»**, **no durable learnings in gpt-handoff**; rules: Improver proposes/doesn't approve, GPT audits, Slava accepts/promotes, no AIKB writes from AgentFactory gates, `IMPROVE STEP: no lesson` when empty.
- **`/task`** — already aligned with the proven run (`request -> build -> break -> verify -> report -> audit -> improve`); only a cross-reference added; not over-expanded.
- **`/review`** — dimensions correctness/architecture/security preserved; dedup + no-source-modification preserved; explicit "**inspect-only** unless another gate explicitly allows changes" added.
- **`/recurse`** — do-not-repeat rule preserved; the explicit per-attempt **Failure Log template** added exactly as required (ATTEMPT N: Request/Action taken/Result/Evidence/Why failed/Root cause/Do not repeat + REQUIRED CHANGE: New hypothesis/Different approach/New acceptance check).

## Improve Loop

Formalized in `workflows/improve-loop.md` (content above). Key invariants encoded: proposes-not-approves, owner-only promotion, AIKB only via «зафиксируй», gpt-handoff never a durable home, no manufactured lessons.

## Verification Evidence

- Stop-conditions: branch=main; `git status --porcelain` empty; sync 0/0; before-SHA `9f1cbd15a6bb068fc5c09bb3221130daf5782994`; no conflicting files.
- `git status --short` (pre-commit): exactly the 9 allowed paths (5 M + 4 ??) — full list matches the Source Changes table; nothing outside the allowed write list.
- `git diff --stat`: README +2; OS doc +9/-3; recurse +18; review +1; task 1-line change.
- Secret scan over all 9 files → **CLEAN** (no token patterns, no realistic fakes).
- DEFERRED check: `git diff --name-only -- extensions/ fleet/ worker/ dashboard/` → 0 files.
- Executable scripts check: `find` for `*.sh|*.ps1|*.py` → 0.
- `docs/ai-learnings/LOG.md` diff → 0 (unchanged).
- Push: `9f1cbd1..16104cf main -> main`; post-push local vs origin 0/0.

## Done Criteria vs Evidence

| # | Criterion | Evidence |
|---|---|---|
| 1 | feature.md exists with phases | NEW file, 10 phases |
| 2 | quick.md exists with boundaries | NEW file, when-NOT-to-use + escalation |
| 3 | improve-loop.md non-self-approving | NEW file, proposes-not-approves + owner-only promotion |
| 4 | task/review/recurse consistent | minimal consistency updates (diff above) |
| 5 | README points to specs | +2 lines workflow index |
| 6 | OS doc reflects MVP-1 + deferrals | MVP-1 marked formalized; MVP-2/3 DEFERRED preserved; roles/seats + no-self-approval intact |
| 7 | Only allowed files changed | status output = allowed list exactly |
| 8 | No executable automation/scripts | find → 0 |
| 9 | No secrets | scan CLEAN |
| 10 | No Cloudflare/fleet/dashboard/tmux/worker | DEFERRED untouched, no new dirs |
| 11 | Source SHA reported | `16104cf32aca25d40072d68e1e352c89b01e227b` |
| 12 | Handoff report + summary written | this publish |
| 13 | Next safe step stated | below |

## Boundaries Honored

Docs-only · no automation/scripts · no Cloudflare/D1/DO/fleet/dashboard/tmux/worker/remote-launch/vault · no secrets/tokens (scan clean) · no paid resources · no delete/reset/force-push · no unrelated rewrites · no AIKB writes · no InsuranceAIPlatform contact · no self-approval (GPT audit + owner acceptance follow).

## BLOCKED Reason

N/A because the gate completed successfully.

## Improve Step

IMPROVE STEP: no lesson — clean formalization run. (One reusable observation already recorded in prior gates' reports remains valid: files created via shell heredoc require an explicit read before editor-tool edits in this environment; it did not cause failure or rework here.)

## Next Safe Step

Owner's choice under the now-formalized specs:
1. **First `/review` run** — inspect-only three-dimension review (correctness/architecture/security) over the whole `pi-setup` tree, producing a deduped findings report (proves the second workflow); or
2. **First `/quick` run** — a trivial bounded docs task to prove the lightweight path end-to-end.
MVP-2 (fleet backend) and MVP-3 (dashboard/remote) remain DEFERRED behind separate future gates.
