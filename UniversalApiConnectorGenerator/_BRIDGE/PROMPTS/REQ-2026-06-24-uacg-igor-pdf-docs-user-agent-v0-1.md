REQUEST_ID=REQ-2026-06-24-uacg-igor-pdf-docs-user-agent-v0-1
STATE=READY_FOR_CLAUDE
PROJECT=UniversalApiConnectorGenerator
GATE=IGOR_PDF_DOCS_AND_USER_AGENT_UAT_V0_1
WORKSPACE=C:\Projects\UniversalApiConnectorGenerator
TARGET=net9.0 temporary
FINAL_TARGET=net10.0 later migration

ROUTE USED: DIRECT_MACRO_CONTAINER with user-agent UAT checks
ALLOWED: edit documentation files only, generate PDF/HTML/Markdown docs under docs/, run local build/test/CLI/user-scenario commands, create UAT outputs under output/, write Bridge report
FORBIDDEN: source code edits, project file edits, AIKB edits, TwinCore/AiIntegrator/claude-vault edits, remote repo operations, external provider calls, secrets, deployment, production claims

GOAL
Create a polished Ukrainian documentation package for Igor and run a realistic user-agent validation across multiple user scenarios. Fix the two known IGOR_DEMO_GUIDE_UA.md doc-lag issues. Produce a detailed Ukrainian PDF if local tooling is available. If PDF tooling is unavailable, produce a polished Markdown and HTML version and mark PDF as BLOCKED_BY_LOCAL_PDF_TOOLING, not a product failure.

CURRENT ACCEPTED FACTS
- Product is Universal API Connector Generator, not Rate-only generator.
- Rate / UPS-style rating is the first proof case.
- Current target is net9.0 temporary; final target net10.0 later migration.
- Current local UAT passed: restore/build/test, inspect, generate, emitted package build, verify, negative flows.
- Current tests are 29/29.
- Known docs fixes: IGOR_DEMO_GUIDE_UA.md must say 29 tests, and bare generate without --source returns usage + exit 0; review-required exit 2 requires generate --source <path>.

GLOBAL STOP
Stop with BLOCKED if workspace mismatch, solution missing, docs missing, baseline build/test fail before docs edits, or any required action would edit source code or leave the workspace.

CONTAINER 0 PREFLIGHT
Verify workspace. Verify solution/docs exist. Run dotnet restore, dotnet build, dotnet test. Confirm current tests are 29/29 or more. Capture baseline.

CONTAINER 1 DOC FIX
Edit only documentation. Fix docs/IGOR_DEMO_GUIDE_UA.md:
- expected tests count must be 29, not 21;
- bare generate without --source must be documented as usage + exit 0;
- generate --source <fixture> must be documented as review-required + exit 2.
Do not change source code.

CONTAINER 2 IGOR FULL GUIDE UA
Create docs/IGOR_FULL_GUIDE_UA.md as a detailed Ukrainian guide for Igor:
- project purpose and business goal;
- what Universal API Connector Generator means;
- why Rate/UPS rating is only first proof case;
- solution architecture and project responsibilities;
- key folders/classes/concepts in plain Ukrainian;
- exact local prerequisites;
- how to open in Visual Studio;
- restore/build/test commands;
- inspect scenario;
- generate review package scenario;
- verify scenario;
- where output artifacts are;
- what generated package contains;
- how to build emitted package;
- limitations: placeholder models, no live calls, no LLM, no production claim;
- exit codes table;
- troubleshooting;
- next roadmap: schema-property enrichment, transport/mapping slice, net10 migration, GitHub publish.

CONTAINER 3 PDF/HTML EXPORT
Attempt to create docs/IGOR_FULL_GUIDE_UA.pdf from docs/IGOR_FULL_GUIDE_UA.md using only already installed local tooling. Do not install new tools without approval. Also create docs/IGOR_FULL_GUIDE_UA.html if possible. If PDF cannot be produced, keep Markdown and HTML if possible, and report exact missing tooling. PDF failure alone is not product failure.

CONTAINER 4 USER-AGENT SCENARIO MATRIX
Create docs/USER_AGENT_SCENARIOS_UA.md describing and then executing as much as possible:
1. First-time local developer opens project and runs build/test.
2. Developer runs inspect on valid fixture and reviews scan artifacts.
3. Developer runs generate with default rate profile and reviews output.
4. Developer builds emitted package.
5. Developer runs verify --package.
6. Developer makes mistakes: missing source, no source, outside-workspace source.
7. Developer reads Ukrainian guide and checks whether commands match actual behavior.
8. Developer checks limitations and understands this is review package, not production connector.
Mark clone-from-GitHub as NOT_TESTED / BLOCKED_BY_NO_REPO until repo exists.

CONTAINER 5 REAL USER AGENT EXECUTION
Run the scenarios from Container 4 exactly as a user would. Use clean terminal commands. Capture command, exit code, important output, artifact paths, and any mismatch. Do not fix source code. If a doc mismatch remains, report it.

CONTAINER 6 FINAL VERIFY
Run dotnet build and dotnet test again after docs edits. Run at least inspect valid, generate valid, build emitted package, verify generated package. Confirm docs-only changes did not break product behavior.

CONTAINER 7 REPORT
Write UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md with:
REQUEST_ID=REQ-2026-06-24-uacg-igor-pdf-docs-user-agent-v0-1
STATUS=READY_FOR_GPT_AUDIT or BLOCKED
PROJECT=UniversalApiConnectorGenerator
GATE=IGOR_PDF_DOCS_AND_USER_AGENT_UAT_V0_1

Sections:
- Docs Fix Summary
- Files Created or Modified
- Ukrainian PDF/HTML Status
- Igor Guide Summary
- User-Agent Scenario Matrix Result
- Commands Run and Exit Codes
- Build/Test Result
- Documentation Accuracy Result
- Remaining Blockers
- Boundary Check
- Next Safe Step

Claude chat reply only:
Bridge report ready. Tell GPT: отчёт.
