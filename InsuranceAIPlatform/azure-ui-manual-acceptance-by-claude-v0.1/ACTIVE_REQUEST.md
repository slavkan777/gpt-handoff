REQUEST_ID: REQ-2026-06-06-insuranceai-azure-ui-manual-acceptance-by-claude-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-azure-ui-manual-like-acceptance-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_UI_MANUAL_ACCEPTANCE_BY_CLAUDE_MEGA_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/azure-ui-manual-acceptance-by-claude-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
SOURCE_REPO_BRANCH: rag/local-foundation-mega-v0.1
AIKB_PROJECT_PATH: ai-kb/01_PROJECTS/InsuranceAIPlatform/
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/azure-ui-manual-acceptance-by-claude-v0.1/report.md

OPERATOR NOTE:
This is a manual-like Azure UI acceptance sweep performed by Claude before Slava does final owner manual testing. Do not change product code, Azure resources, database schema, users, secrets, or deployment. The previous gate deployed Azure dev/test fallback RAG successfully. This gate is about acting like a real user in the browser, trying many realistic flows, trying to break the UI, capturing screenshots/evidence, and reporting issues clearly.

CURRENT ACCEPTED AZURE STATE:
- Public SWA URL: https://kind-meadow-03cf73103.7.azurestaticapps.net/
- Backend Container App: https://iap-demo-api.bluehill-ebdd0494.westeurope.azurecontainerapps.io
- Backend revision expected: iap-demo-api--0000003
- Mode expected in Azure: backend frontend + RAG fallback, not local/mock-only UI.
- Expected RAG runtime: vector fallback `in-memory-hash`, local LLM disabled, providerMode `Mock`, no fake Qdrant/Ollama.
- Expected data: CLM-1006 has seeded RAG evidence; CLM-1012 is valid insufficient-evidence case; CLM-1061 is not a seeded Claim and may 404.

GOAL:
Run a thorough manual-like user acceptance test against the live Azure dev/test site using Playwright/browser automation and endpoint probes. Verify that the public UI behaves correctly across realistic business flows, RAG flows, negative/edge cases, navigation/reload/session behavior, and visual usability. Produce evidence and a clear defect list before Slava spends time doing final manual review.

DONE STATE:
1. Confirm routing lock and that this is test-only / no source or Azure mutation.
2. Open the live SWA public URL in a real browser automation context.
3. Log in using existing demo/dev credentials if already available from project seed/docs/runtime. Do NOT print credentials. Do NOT ask Slava to paste passwords. If no safe credential path exists, stop BLOCKED with exact missing item.
4. Verify the UI is connected to the Azure backend, not local mock-only mode.
5. Run the full test matrix below.
6. Capture screenshots and, when useful, traces/network logs as sanitized artifacts under the gate-specific handoff folder.
7. Report PASS/PARTIAL/FAILED per scenario, with exact console/network errors, broken UI states, visual issues, and repro steps.
8. Publish report to all three project-specific report paths.

STRICT CREDENTIAL RULE:
- Prefer existing seeded demo/dev login already used by the app/tests.
- Do not create a secret login known only to Claude.
- Do not print username/password values in report/chat if they are secrets.
- Do not store credentials in repo, gpt-handoff, screenshots, or logs.
- If a temporary dev/test user is absolutely needed, only create it through an already existing safe dev/test seed/admin path, do not expose its password, and delete it after testing if technically possible. If that safe path does not exist, stop BLOCKED.

TEST MATRIX:
A. Availability / backend mode
1. SWA loads with HTTP 200 and expected title/app shell.
2. Live JS bundle points to Azure backend FQDN, not localhost and not mock-only.
3. Backend `/health` 200.
4. Backend `/api/claims` 200 with SWA Origin and CORS OK.
5. Browser console: no fatal errors on initial load.
6. Network: no failed core API requests.

B. Authentication / session behavior
7. Login happy path works with demo/dev account.
8. Wrong credentials or empty fields show a user-safe validation/error, no crash.
9. Refresh after login preserves or safely restores session.
10. Direct URL to `/claims/CLM-1006` while logged in works or redirects correctly.
11. Logout, if UI has it, clears session and protects claim pages.

C. Dashboard / navigation
12. Dashboard renders without broken cards/placeholders.
13. Main navigation between dashboard, claims list, claim detail, and evidence page works.
14. Browser Back/Forward does not break state.
15. Hard reload on each important page works.
16. 404/unknown route has safe behavior or routes to app shell without blank screen.

D. Claims list
17. Claims list loads from backend, not static fixture-only UI.
18. At least several seeded claims render with IDs/status/customer/vehicle/amount where applicable.
19. Search/filter/sort controls, if present, work or are clearly disabled.
20. Empty search/filter state is understandable and not broken.
21. Clicking CLM-1006 opens correct claim detail.
22. Clicking another claim opens that claim's own data, not stale CLM-1006 data.

E. Claim detail CLM-1006
23. CLM-1006 detail renders expected customer/vehicle/claim info.
24. No obvious stale fixture leakage after navigating from another claim back to CLM-1006.
25. Tabs/sections load: overview, documents/evidence, AI/evidence intelligence where present.
26. Reload on CLM-1006 detail keeps same claim context.

F. Evidence Intelligence / RAG happy path
27. Open CLM-1006 Evidence Intelligence / AI Evidence page.
28. Infrastructure/status panel, if visible, honestly reflects fallback mode: in-memory/hash or disabled vector, no fake Qdrant/live, local LLM disabled, providerMode Mock when asking.
29. Click coverage/policy coverage action.
30. Answer card appears with advisory-only wording.
31. Confidence value/bar renders and is sane.
32. Citations render and are clickable/readable where UI supports it.
33. All visible citations are CLM-1006-scoped; no cross-claim citation IDs.
34. RAG audit/history endpoint/UI, if present, updates or remains stable without error.
35. Repeat the same coverage action twice; no duplicate weird UI, no stale spinner, no crash.

G. RAG business scenario questions for CLM-1006
Run available UI buttons or custom question input if present. If only fixed buttons exist, test all available fixed actions. Suggested questions/actions:
36. Coverage: is bumper/front damage likely covered?
37. Repair hours/cost anomaly: compare billed repair hours vs policy/catalog evidence.
38. Photo vs invoice consistency.
39. Driver statement vs claim application consistency.
40. Deductible / policy terms summary.
41. Overall claim summary for adjuster.
42. Human review recommendation.
For each: verify answer, confidence/citations, advisory wording, no final payout/fraud decision language.

H. Negative / edge RAG cases
43. Ask irrelevant question if custom input exists, e.g. weather/football/recipe. Expected: cautious refusal/insufficient relevance, not hallucinated claim decision.
44. Open valid low/zero evidence claim CLM-1012 if visible or direct route works. Ask coverage. Expected: insufficient evidence, confidence 0 or low, 0 citations or clear limitation.
45. Try non-existing CLM-1061 route/API if feasible. Expected: safe not-found, not crash.
46. Rapidly click RAG button 3-5 times. Expected: disabled/loading/debounced or stable multiple responses, no crash.
47. Navigate away while RAG request is pending. Expected: no app-level crash or unhandled promise errors.
48. Reload immediately after RAG answer. Expected: page reloads safely; audit may persist if designed, otherwise no crash.

I. Cross-claim leakage / stale state checks
49. Open CLM-1006, run RAG, capture citation IDs.
50. Navigate to another claim with evidence, run available AI/RAG action if available.
51. Return to CLM-1006. Verify answer/citations correspond to the current claim, not previous one.
52. Verify user-visible claim header, answer, and citation cards do not mismatch claim IDs/customers/vehicles.

J. API endpoint probes from deployed backend
Probe directly, preserving sanitized output:
53. `/health`.
54. `/api/claims`.
55. `/api/claims/CLM-1006/rag/infrastructure`.
56. `/api/claims/CLM-1006/rag/ask` with coverage question.
57. `/api/claims/CLM-1012/rag/ask` insufficient-evidence question if endpoint supports it.
58. Non-existing claim endpoint returns safe 404/structured error, not 500.

K. Visual / UX sanity
59. Desktop viewport 1440x900 screenshot for dashboard, claims list, CLM-1006 detail, evidence intelligence answer.
60. Narrow viewport around 390x844 or 412x915: app remains usable or known limitation documented.
61. No overlapping critical controls, hidden primary buttons, unreadable citation cards, infinite spinners, or blank panels.
62. Loading and error states are understandable.
63. Advisory-only wording is visible enough for legal/insurance safety.

L. Reliability / performance sanity
64. No request takes obviously excessive time in normal path; record rough timings for RAG ask and page load.
65. Run one full happy path twice in a fresh browser context; results stable.
66. Confirm no 401/403/CORS bursts after login or RAG actions.

ACCEPTANCE EXPECTATIONS:
- PASS if core Azure UI is usable, backend mode is proven, RAG CLM-1006 works with citations/confidence/advisory, insufficient evidence is safe, no console/network fatal errors, and no obvious visual blocker.
- PARTIAL if core path works but there are minor visual/UX issues, missing edge behavior, or non-blocking scenario failures.
- FAILED if login/core navigation/RAG answer/backend mode is broken, citations leak across claims, frontend is mock-only, or serious console/network errors occur.

ALLOWED CHANGES:
- Browser automation / Playwright scripts in a temporary local location if needed.
- Sanitized screenshots, traces, and report artifacts under `gpt-handoff/InsuranceAIPlatform/azure-ui-manual-acceptance-by-claude-v0.1/`.
- No product source commits. No Azure changes. No DB/schema/user changes unless using an already-existing safe dev/test seed/admin path for a temporary user is absolutely required; prefer not to create users.

STRICT BOUNDARIES:
NO source code changes. NO product repo commit/push. NO main. NO merge. NO Azure deploy/update/create/delete. NO DB schema changes. NO data deletion. NO new Azure resources. NO Qdrant/Ollama Azure. NO paid provider. NO secrets printed/stored. NO real PII. NO fake pass. NO claiming manual acceptance for Slava. Claude performs pre-acceptance testing only; Slava remains final manual acceptor.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-06-insuranceai-azure-ui-manual-acceptance-by-claude-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-azure-ui-manual-like-acceptance-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_UI_MANUAL_ACCEPTANCE_BY_CLAUDE_MEGA_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Site / Backend Mode Verification, Credential Handling Statement, Browser / Playwright Setup, Scenario Matrix Summary, Detailed Scenario Results A-L, Screenshots / Artifacts, Network Findings, Console Findings, RAG Evidence Proof, Citation / Leakage Proof, Visual / UX Findings, Performance Notes, Defects Found, Regression Risks, Boundaries Honored, Files / Artifacts Created, Commands Run, Slava Manual Checklist, Recommended Next Step.

STOP after report. Do not fix defects in this gate unless the fix is only a local test-script correction. Do not deploy or mutate Azure.