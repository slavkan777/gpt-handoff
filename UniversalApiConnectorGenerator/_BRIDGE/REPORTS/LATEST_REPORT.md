REQUEST_ID: REQ-2026-06-24-uacg-manual-real-user-uat-v0-1
STATUS: READY_FOR_GPT_AUDIT
PROJECT: UniversalApiConnectorGenerator
GATE: MANUAL_REAL_USER_UAT_V0_1
MODE: MANUAL_UAT_READ_ONLY
TARGET_FRAMEWORK_USED: net9.0
FINAL_TARGET_NOTE: net10.0 later migration gate
PROMPT_PATH: UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/REQ-2026-06-24-uacg-manual-real-user-uat-v0-1.md
REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
COMPLETED: 2026-06-24
COMPLETED_BY: claude

# Manual Real-User UAT v0.1 — READY_FOR_GPT_AUDIT

Acted as a new user and validated the product manually from the docs + CLI after the Generate Review
Package gate. Read-only: no source edits, no docs edits, no fixes. All 9 containers (0–8) executed.
Baseline green, every documented exit code reproduced, emitted package builds 0/0. Two non-blocking
documentation-lag mismatches found in `docs/IGOR_DEMO_GUIDE_UA.md` (reported, not fixed — read-only gate).

## Route Used

ROUTE: DIRECT_MACRO_CONTAINER_UAT (read-only, evidence-checked), per the active request. No subagents,
no /army-start. No self-acceptance: STATUS = READY_FOR_GPT_AUDIT.

## Bridge Intake + Pointer-Mismatch Flag (non-blocking)

- STATUS.json (authoritative machine state) and `PROMPTS/REQ-2026-06-24-uacg-manual-real-user-uat-v0-1.md`
  agree EXACTLY: requestId / gate / state(READY_FOR_CLAUDE) / promptPath / mode(MANUAL_UAT_READ_ONLY) /
  targetFramework(net9.0). The request executed matches STATUS.json with zero mismatch.
- FLAG: `PROMPTS/ACTIVE_REQUEST.md` (the README-nominated canonical pointer) is STALE — it still contains
  the already-completed foundation-skeleton request `REQ-2026-06-24-uacg-local-foundation-skeleton-net9-v0-2`
  (GATE LOCAL_FOUNDATION_SKELETON_V0_1). That request's own STOP-BEFORE-START ("workspace contains unexpected
  existing product files") would itself block, confirming it is stale. The README "Current state" block is
  likewise stale (says GATE ARCHITECTURE_DISCOVERY / REQUEST_ID NONE / STATE IDLE).
- RESOLUTION: trusted STATUS.json (machine-authoritative, per README step 2) + the internally-consistent
  REQ-named file; executed the UAT gate. Recommend Architect GPT re-sync `ACTIVE_REQUEST.md` and the README
  "Current state" block to the live gate. This matches the pointer-desync handling accepted in prior gates.

## Workspace Verification

- WORKSPACE = `C:\Projects\UniversalApiConnectorGenerator` (matches request). PWD confirmed.
- Solution + docs + fixtures present: `UniversalApiConnectorGenerator.sln`, `README.md`,
  `docs/IGOR_DEMO_GUIDE_UA.md`, `docs/ARCHITECTURE_UA.md`, `tests/fixtures/api/rate-minimal.yaml`,
  `tests/fixtures/api/malformed.yaml`.
- Projects present: Cli, Domain, Application, Infrastructure, Profiles.Rate, Tests.
- All `.csproj` declare `<TargetFramework>net9.0</TargetFramework>` (no net10.0 anywhere).
- `dotnet --list-sdks`: `8.0.422`, `9.0.304` (no .NET 10 SDK → net9.0 authorized & correct).

## Container Results

| Container | Result |
|---|---|
| 0 Preflight | PASS — workspace/docs/solution verified; restore 0; build 0/0/0; test 29/29 |
| 1 Documentation walkthrough | PASS w/ findings — documented commands runnable; 2 docs-lag mismatches (see below) |
| 2 Inspect user flow | PASS — exit 0; 4 artifacts; key facts match |
| 3 Generate user flow | PASS — default & --profile rate both exit 2; all artifacts + package files present |
| 4 Generated package build | PASS — emitted csproj has no external/project refs; build exit 0, 0/0 |
| 5 Verify user flow | PASS — verify --package exit 0; verify no-args exit 0 (legacy stub) |
| 6 Negative user flows | PASS — missing/unsafe exit 4; no-source usage exit 0 |
| 7 Real-user verdict | PASS (local) — clone-from-GitHub NOT TESTED / BLOCKED_BY_NO_REPO |
| 8 Report | this file |

## Commands Run and Outputs (exact)

```text
dotnet --list-sdks                      -> 8.0.422 ; 9.0.304
dotnet restore <sln>                    -> exit 0  ("All projects are up-to-date for restore.")
dotnet build   <sln> -c Debug           -> exit 0  ("Build succeeded. 0 Warning(s) 0 Error(s)")
dotnet test    <sln> -c Debug           -> exit 0  ("Passed! Failed:0 Passed:29 Total:29, 341 ms")

inspect  --source tests/fixtures/api/rate-minimal.yaml        -> exit 0  ("inspect OK. Artifacts: ...\output\20260624152243-68ea8649\scan")
generate --source tests/fixtures/api/rate-minimal.yaml         -> exit 2  ("review package created (review required, 6 marker(s)). Package: ...\generate\package")
generate --source tests/fixtures/api/rate-minimal.yaml --profile rate -> exit 2  (6 marker(s))
dotnet build <run>/generate/package/Ups.Rate.Connector.csproj  -> exit 0  ("Build succeeded. 0/0")
verify   --package output/20260624152306-c113d9dd              -> exit 0  ("structurally valid review package at ...\generate")
verify                                                          -> exit 0  ("stub OK (nothing to verify in skeleton).")
generate --source tests/fixtures/api/nope.yaml                 -> exit 4  ("SRC_MISSING: Source path does not exist.")
generate            (no --source)                              -> exit 0  ("Usage: generate --source <path...>")
inspect             (no --source)                              -> exit 0  ("Usage: inspect --source <path...>")
inspect  --source C:/Windows/win.ini                           -> exit 4  ("SRC_ESCAPE: Source path is outside the workspace root.")
<no args>  (stdin </dev/null, non-interactive)                 -> exit 0  (menu printed: product name, profile, workspace, commands)
```

## Build/Test Result

- Build: SUCCEEDED, 0 warnings, 0 errors (6 projects, net9.0). Test: 29/29 PASSED (341 ms). Exit 0/0.

## CLI Smoke Result (exit codes vs documented catalog)

| Invocation | Exit | Documented | Match |
|---|---|---|---|
| inspect --source <valid> | 0 | 0 | ✅ |
| generate --source <valid> [--profile rate] | 2 | 2 (review required) | ✅ |
| verify --package <run> | 0 | 0 | ✅ |
| verify (no args) | 0 | 0 (stub) | ✅ |
| generate --source <missing> | 4 | 4 | ✅ |
| inspect --source <outside workspace> | 4 | 4 | ✅ |
| generate / inspect (no --source) | 0 | 0 (usage) | ✅ |
| no args (non-interactive) | 0 | 0 (menu) | ✅ |

## Inspect Artifacts (Container 2)

`output/<run>/scan/`: `source-inventory.json`, `provider-schema-document.json`, `scan-manifest.json`,
`diagnostics.json`. Key facts confirmed: Title="Rate Minimal Fixture";
SelectedOperationKey="POST /rating/{version}/{requestoption}"; SchemaNames=["RateRequest","RateResponse"];
scan-manifest ParserPackage="Microsoft.OpenApi.Readers", ParserVersion="1.6.24", TargetFramework="net9.0";
diagnostics=[].

## Generate Artifacts + Package (Containers 3–4)

`output/<run>/generate/`: `connector-package-plan.json`, `generation-manifest.json`, `diagnostics.json`,
`file-hashes.json`, `REVIEW_CHECKLIST_UA.md`, and `package/`. `package/`: `Ups.Rate.Connector.csproj`,
`README.md`, `UpsRateConnectorOptions.cs`, `IUpsRateConnector.cs`, `UpsRateConnector.cs`,
`UpsRateConnectorMapper.cs`, `UpsRateConnectorRegistration.cs`, `Models/RateRequest.cs`,
`Models/RateResponse.cs`. `.csproj` contains NO PackageReference and NO ProjectReference; emitted package
builds exit 0 (0/0). REVIEW_CHECKLIST_UA.md states honest limits ("НЕ production: без реальних викликів,
без секретів, без LLM").

## Documentation Mismatches (read-only — REPORTED, not fixed)

1. `docs/IGOR_DEMO_GUIDE_UA.md` §1 expects `Passed! Total: 21`; actual is **29**. (README correctly says
   "29 автотестів".) Cosmetic stale expected-count.
2. `docs/IGOR_DEMO_GUIDE_UA.md` §4 ("Решта команд") documents bare `generate` as `# exit 2
   (review-required, нічого не генерується)`; actual bare `generate` (no `--source`) returns **exit 0**
   with a usage hint. Exit 2 now requires `--source`. README table is correct (`generate` без `--source` →
   `0`); the demo-guide §4 line is a stale remnant from the pure-stub foundation era. §6 of the same guide
   (the real `generate --source ... --profile rate` demo) is correct.

Impact: LOW / non-blocking. Product behavior is correct and stricter than the stale lines; only the demo
guide §1 and §4 need a small refresh. A new user following §6 (current generate demo) sees correct behavior.

## Boundary Check

- READ-ONLY honored: no source edits, no docs edits, no AIKB/TwinCore/claude-vault/CLAUDE.md/settings edits.
- Writes confined to workspace `output/` (UAT run artifacts) + this single Bridge report on the remote.
- No remote repo operations beyond writing the Bridge report. No PR/branch/source push.
- No external/provider/UPS calls, no secrets, no network beyond local dotnet (restore was up-to-date).
- No deployment, no production claim. No self-acceptance.

## Independent QA Verification

An independent Opus inspector (Argus, read-only, separate context) re-ran the full evidence set and
returned **CLEARED**: items 1–8 reproduced exactly with quoted command output; both documentation
mismatches independently confirmed TRUE; every numerical/exit-code claim matched. One ⚠️ note (not a
defect): the workspace is **not a git repository** (no `.git`), so the read-only guarantee was confirmed
via file mtimes instead of `git status` — all source/doc files were last modified ≥40 min before the UAT
window, and every UAT write is confined to `output/<run-id>/` folders. This independently corroborates the
Container-7 `BLOCKED_BY_NO_REPO` finding (no local or remote git for this project yet). This is verification
of evidence integrity, not final acceptance — final acceptance remains GPT + Slava.

## UAT Verdict (Container 7)

PASS (local, read-only). A new user CAN, from the docs: open locally, restore, build, test (29/29),
inspect (exit 0 + 4 artifacts), generate (exit 2 + reviewable package), build the emitted package (0/0,
zero dependencies), run verify, and understand the honest limits (placeholder models, no live calls, no LLM,
net9.0 temporary / net10.0 later) from README + ARCHITECTURE_UA + REVIEW_CHECKLIST_UA.

NOT TESTED: clone-from-GitHub — `BLOCKED_BY_NO_REPO` (no GitHub repository exists for this project yet).
This is an environment gap, not a product failure.

The only friction for a new user is the two stale demo-guide lines (§1 count, §4 bare-generate exit code).

## Blockers

None blocking acceptance of the read-only UAT. Open items for GPT/Slava:
- (hygiene) Architect GPT to re-sync `ACTIVE_REQUEST.md` + README "Current state" to the live gate.
- (docs) authorize a small docs-fix gate for the two IGOR_DEMO_GUIDE_UA.md mismatches.

## Next Safe Step

1. Slava writes `отчёт`; Architect GPT audits this report against the workspace/output evidence.
2. Suggested next gates (each its own bounded request): a tiny docs-fix gate (demo-guide §1 count → 29,
   §4 bare-generate → exit 0 usage); optionally a GitHub-repo-creation gate to unblock the clone-from-GitHub
   UAT path; then schema-property enrichment so generated models are no longer name-only.
3. Separate later gate: net9.0 → net10.0 migration once a .NET 10 SDK is installed.
