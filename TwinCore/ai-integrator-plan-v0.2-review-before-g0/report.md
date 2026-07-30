REQUEST_ID: REQ-2026-06-12-twincore-ai-integrator-plan-v0-2-review-before-g0
STATUS: READY_FOR_AUDIT
TASK_TYPE: analysis-only / report-only
PROJECT: TwinCore
GATE: planning / plan-v0.2-review-before-g0
ACTIVE_REQUEST_PATH: TwinCore/ai-integrator-plan-v0.2-review-before-g0/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: TwinCore/ai-integrator-plan-v0.2-review-before-g0/report.md
LATEST_REPORT_PATH: TwinCore/latest-report.md
COMPLETED: 2026-06-12
COMPLETED_BY: claude

# TwinCore AI Integrator Plan v0.2 Review Before G0

## 1. Executive Verdict

**READY_FOR_G0_WITH_LIMITATIONS.**

Plan v0.2 (`FEATURE_PLANS/TWINCORE_AI_INTEGRATOR_IMPLEMENTATION_PLAN_V0_2.md`, ai-kb `dc2803b`)
correctly absorbs all four prior reports, states owner-proxy mode safely, fixes the hosting and UI
decisions in reviewable form, and scopes G0 narrowly. **No v0.2 revision is required.**

The limitations are five sharpening items, all absorbable into the future G0 ACTIVE_REQUEST
(drafted in §9) rather than into the plan text:

1. **G0 inherently requires one Azure DevOps write** (pushing the feature branch) — every request
   so far forbids all Azure writes; the G0 request must explicitly authorize exactly this one
   action, or the executor must BLOCK on the contradiction (§7-L1).
2. The `TwinCore.sln` escape hatch ("untouched **unless explicitly justified and approved**",
   plan:252) should be hardened to an unconditional STOP for G0 (§7-L2).
3. The fixture deliverable is ambiguous ("fixtures folder exists", plan:249, vs the tech design's
   four authored fixtures + hashes) — decide one way in the G0 request (§7-L3).
4. CI-pipeline creation collides with the no-Azure-writes boundary — G0 must require local
   build/test evidence only; pipeline creation is a separately authorized action (§7-L4).
5. Owner-proxy mode needs a visible **provisional-decision register** for Igor's return (§4-L5).

## 2. Current State Restored

- AIKB (`dc2803b`): TwinCore in `OWNER-PROXY MODE` (Igor unavailable; Slava/GPT make provisional,
  reversible decisions; "Igor approved" is a forbidden phrase) — `CURRENT_STATE.md:33-34,182`.
- Current framework evidence commit `8f935f08` recorded (`CURRENT_STATE.md:25`) — the stale
  zero-tests risk picture has been corrected as the readiness report requested.
- Evidence chain to date: planning dossier → plan v0.1 → plan review → tech design (ADR'd as
  baseline: `DECISIONS/ADR-2026-06-12-ai-integrator-technical-design-baseline.md`) → framework
  readiness (`FRAMEWORK_NOT_A_BLOCKER_WITH_SPLIT_HOSTING`) → plan v0.2 → this review.
- Gate state: planning only; implementation NOT open; G0 NOT open (plan:326-332). This review does
  not open it.
- Boundaries honored here: read-only analysis; writes restricted to the four authorized
  gpt-handoff paths; no AIKB writes; no source repo access needed for this gate.

## 3. v0.2 Consistency Review

**Internally consistent. Verified point-by-point against the three source reports:**

| v0.2 claim | Source | Status |
|---|---|---|
| Tech design decisions list (plan:152-166) | Tech design dossier §3-§13 | ✅ faithful (IterationRoot, element-relative paths, explicit ArraySelect, JSONPath subset, closed TransformKind, immutable approved profiles, generation pinning, SourceHash/SchemaHash/FieldKey, sanitization, sandbox, 4 fixtures, LLM cache/audit, zero runtime LLM) |
| ADR baseline matches the dossier | ADR tech-design-baseline | ✅ including "no implicit `[0]`" and `ManualCode` blocking generation |
| Readiness verdict + zero framework prerequisites (plan:183-193) | Readiness report §1/§5.1 | ✅ |
| Courtesy fixes list (plan:195-201) | Readiness §5.2 | ✅ all five, correctly framed as owner queue, not blockers |
| G0 layout (plan:226-238) | Readiness §11 | ✅ identical |
| G1 in-memory only with repository abstraction (plan:263) | Tech design D12 | ✅ |
| EF "before or at G2" (plan:273) | Tech design (EF at G2 entry) | ✅ compatible |
| G2 includes IterationRoot/ArraySelect/TransformKind UI (plan:267-271) | Tech design §4-§5 | ✅ |
| G3 multi-option quote assertion (plan:283) | Tech design §15 MultiOption | ✅ |
| G4 second provider with no factory-core change (plan:287-288) | Tech design §15 Repeatability | ✅ |

**Minor precision gaps (sharpen in G0 request, not in the plan):**

- §4.1 calls contracts "dependency-light" (plan:90); the accepted rule is precise: **BCL +
  `Microsoft.Extensions.*` abstractions only** — pin the exact wording in the G0 request so the
  skeleton's `.csproj` is checkable.
- The readiness report's rebase-cadence rule (rebase the feature branch at gate boundaries, never
  mid-gate) did not carry into v0.2's gate rules (plan:292-303) — add to the G0+ request template.
- G0 exit evidence omits build/test *command evidence* (it lists "empty skeleton builds",
  plan:247, without the evidence-tuple form) — §9 fixes this.

## 4. Owner-Proxy Mode Review

**Stated safely.** The mode (plan:26-49) has the three required properties: explicit trigger
(Igor unavailable), explicit reversibility, and a forbidden-phrase rule ("Igor approved") with an
allowed substitute ("Provisional owner-approved baseline by Slava. Reversible when Igor returns.").
`CURRENT_STATE.md:182,186` makes proxy-mode changes a tracked state dimension. Two additions:

- **L5 — provisional-decision register.** Decisions made under proxy mode are currently scattered
  (plan §4, §5, CURRENT_STATE fragments). Keep one explicit list — "decisions awaiting Igor
  ratification" — so the return conversation is a checklist, not an archaeology dig. Current
  entries: (1) split hosting model; (2) Blazor Server UI; (3) workbench isolation under
  `AiIntegrator/` + own sln; (4) G0 opening itself; (5) feature branch in Igor's Azure repo.
  Recommended location: a short section in `CURRENT_STATE.md` or a one-page ADR addendum.
- **Blast-radius cap, stated explicitly:** proxy mode covers **reversible** actions only. The
  G0 branch push qualifies (a branch is deletable without trace on `main`). NOT covered without
  Igor: merge to `main`, PRs into `main`, production anything, paid resources, publishing beyond
  the established bridge/snapshot channels. Recommend writing this cap into the register.

## 5. Split Hosting Review

**Correctly captured** (plan:84-118) and consistent with the readiness recommendation it cites:
three pieces (contracts+orchestrator / generated adapter packages / isolated workbench), with the
right negative rules — not in `TwinCore.sln`, no `TwinCore.Admin` extension, no framework edits
during G-gates, no `ServiceResponse`/framework base classes in generated adapters (plan:104-107).
The generated-adapter rules (plan:100-108) match the readiness §10 compatibility rules. No gaps.

## 6. UI Stack Decision Review

**Blazor Server is acceptable as a provisional decision.** The stated reasons (plan:134-140) match
the tech-design D14 rationale (single .NET toolchain, no model duplication, internal tool, API
layer keeps a future SPA swap bounded). Reversibility is real *only if* the API-first layering is
enforced from G1 — recommend the G1 request carry an explicit check: every workbench UI action
goes through the app's API/service layer, no domain logic in components. Risk noted and accepted:
if Igor returns preferring React or admin-portal integration, the rework is UI-only by
construction. Status `PROVISIONAL_OWNER_DECISION` + register entry (§4-L5) is the right framing.

## 7. G0 Scope Review

**Safe and narrow, with four sharpenings:**

- **L1 — the Azure-write contradiction must be resolved explicitly.** G0's own exit evidence
  requires "branch created from latest Azure `main`" (plan:244) — that is a push to Igor's Azure
  DevOps repo, an action every gate so far forbids ("Do not write to Azure DevOps", this request's
  own boundary). The G0 ACTIVE_REQUEST must contain an explicit, narrow authorization: create
  branch `feature/12695-ai-integrator-poc` from recorded `origin/main` SHA, commit **only**
  `AiIntegrator/**` paths, push **only** that branch. Anything beyond = BLOCKED. Without this
  explicit clause, the correct executor behavior is to stop on the contradiction.
- **L2 — remove the `TwinCore.sln` escape hatch for G0.** "Untouched unless explicitly justified
  and approved" (plan:252) invites in-gate judgment calls. For a setup-only gate there is no valid
  justification; harden to: `TwinCore.sln` and everything outside `AiIntegrator/` are read-only;
  any perceived need = STOP + report.
- **L3 — fixtures: decide folder vs artifacts.** Tech design §11 defines four designed fixtures
  (MockCarrierA/B/Messy/Hostile + golden sets, committed and hashed at G0); plan v0.2 G0 evidence
  only says "fixtures folder exists" (plan:249). Recommendation: **author all four at G0** — they
  are pure data, squarely inside "setup-only", and G1 immediately needs MockCarrierA. If the owner
  prefers a thinner G0, the request must say "folder + MockCarrierA only; B/Messy/Hostile by G3/G4
  entry" — either way, make it explicit.
- **L4 — CI.** Readiness §11 proposed a separate CI pipeline at G0; pipeline creation is an Azure
  DevOps write beyond the branch push. G0 should require **local** `dotnet build` / `dotnet test`
  evidence (command + exit code) only; CI pipeline creation becomes a separately authorized item
  (G0.5 or folded into G1's request with its own explicit authorization).

With L1-L4 absorbed, G0 is exactly what it should be: skeleton, fixtures, tool-scoped CLAUDE.md,
build evidence, zero product logic.

## 8. G0 Stop Conditions

The future G0 request should carry exactly these:

1. `REQUEST_ID` / `PROJECT` / `GATE` mismatch with STATUS.json → **BLOCKED / STALE_OR_WRONG_REPORT**.
2. Local `Twincore-framework` working tree dirty, or unexpected branch state at start → **STOP**
   (report state; never stash/reset/clean someone else's work).
3. `origin/main` fetch fails, or base SHA differs from the SHA recorded in the request → **STOP**
   (re-confirm base; no silent rebase onto a moved main).
4. Branch `feature/12695-ai-integrator-poc` already exists locally or on origin → **STOP** (report;
   do not reuse/force).
5. Any required change outside `AiIntegrator/**` (including `TwinCore.sln`, root files, `Src/**`,
   `.claude/**`) → **STOP**.
6. Push rejected / branch protected / permission error → **STOP** (no force-push, no retry with
   different remote).
7. Any secret material would enter a commit (scan before commit) → **STOP**.
8. Skeleton build/test cannot pass without touching anything outside `AiIntegrator/**` → report
   **PARTIAL** with evidence; no workarounds through framework files.
9. Any instruction ambiguity that widens scope → **STOP and ask**, not interpret-and-proceed.
10. After push: remote branch SHA ≠ local HEAD → report **FAILED** verification.

## 9. Draft Future G0 ACTIVE_REQUEST Checklist

When Slava says `open G0`, the request should contain:

**Identity & routing**
- [ ] `REQUEST_ID: REQ-<date>-twincore-ai-integrator-g0-setup-v0-1`; `PROJECT: TwinCore`;
      `GATE: implementation-setup / G0`; suggested slug `TwinCore/ai-integrator-g0-setup-v0.1/`.
- [ ] Routing lock: source repo = Azure DevOps `Twincore-framework`; write scope =
      **branch `feature/12695-ai-integrator-poc`, paths `AiIntegrator/**` only**; report repo =
      `gpt-handoff/TwinCore/...` (4 standard paths); forbidden = `main`, all other branches,
      `TwinCore.sln`, `Src/**`, root files, AIKB, global `_BRIDGE`.

**Explicit authorizations (the L1 clause)**
- [ ] Create local branch from `origin/main` @ `<recorded SHA>` (request pins the SHA or says
      "latest, record it").
- [ ] Commit to that branch, `AiIntegrator/**` only.
- [ ] Push exactly that branch to Azure origin once; record before/after SHAs.
- [ ] Nothing else on Azure: no PR, no pipeline, no policies, no work-item edits.

**Pre-flight verification (before any change)**
- [ ] `git status` clean; current checkout noted and restored after work (or work in a separate
      worktree to leave `slava/11338` checkout untouched).
- [ ] `git fetch --all --prune` ok; record `origin/main` SHA; default branch confirmed `main`.
- [ ] Confirm target branch does not exist locally/remotely.
- [ ] Confirm `AiIntegrator/` does not already exist on `main`.

**Deliverables**
- [ ] Layout exactly per plan v0.2 §8/G0 (sln + 5 src projects + tests + fixtures + CLAUDE.md).
- [ ] Tool-scoped `CLAUDE.md` stating root TwinCore docs/skills/conventions do not apply inside
      `AiIntegrator/` (and that `Src/CLAUDE.md` examples must not be copied).
- [ ] Contracts project dependency rule: BCL + `Microsoft.Extensions.*` abstractions only.
- [ ] Fixtures per the L3 decision (recommended: all four + golden sets, with recorded hashes).
- [ ] Own `Directory.Packages.props` (CPM) for the AiIntegrator solution — optional but
      recommended for consistency with framework practice.

**Evidence (tuple form: command + exit / artifact + hash / log excerpt)**
- [ ] `dotnet build AiIntegrator.sln` exit 0; `dotnet test` exit 0 (empty/smoke test counts).
- [ ] `git diff --stat <base>..HEAD -- . ':!AiIntegrator'` is **empty** (nothing outside the
      folder changed) and `TwinCore.sln` untouched.
- [ ] Branch push verified: `git ls-remote` SHA == local HEAD.
- [ ] Secret scan of the new tree: 0 findings.
- [ ] Fixture hashes listed.
- [ ] Report to the 4 standard paths + STATUS.json (state, request id, nextActor GPT).

**Stop conditions** — the §8 list verbatim.

## 10. Remaining AIKB Updates Before G0

1. **Provisional-decision register** (§4-L5) — small addition to `CURRENT_STATE.md` or an ADR
   addendum; the only genuinely recommended pre-G0 AIKB change.
2. Optional: a one-line hosting/UI ADR (split hosting + Blazor provisional) if the owner wants
   decisions as ADR files rather than plan sections — current plan §4-§5 coverage is adequate.
3. Bridge hygiene, outside AIKB: `TwinCore/latest-summary.json` is still stale (describes the
   first planning gate; it has been outside every subsequent request's write scope). Either GPT
   refreshes it or the G0 request adds it to the write list.
4. Nothing else — `CURRENT_STATE.md` already reflects 8f935f0 evidence, proxy mode, and the gate
   sequence correctly.

## 11. What Must NOT Be Done Yet

- No G0 opening from this review (explicitly not opened here); no branch, no folder, no skeleton,
  no fixture files, no code.
- No Azure DevOps writes of any kind until the G0 request's explicit L1 authorization.
- No framework edits — including the five courtesy fixes (owner's queue).
- No merge/PR to `main` under owner-proxy mode (outside its blast-radius cap, §4).
- No UI implementation commitments beyond the provisional decision record.
- No AIKB writes from this gate (the §10 items are for GPT after Slava's acceptance).
- No "Igor approved" phrasing anywhere downstream.

## 12. Final Recommendation

Accept plan v0.2 as the operative pre-G0 baseline. Fold L1-L4 into the G0 ACTIVE_REQUEST using the
§9 checklist verbatim, add the §4-L5 provisional-decision register to AIKB, and G0 is safe to open
on Slava's explicit `open G0`. Verdict: **READY_FOR_G0_WITH_LIMITATIONS** — the limitations live in
the G0 request's wording, not in the plan.

Report written only to the four authorized paths (gate report, `latest-report.md`,
`_BRIDGE/LATEST_REPORT.md`, `_BRIDGE/STATUS.json`). Nothing else touched; G0 not opened.
