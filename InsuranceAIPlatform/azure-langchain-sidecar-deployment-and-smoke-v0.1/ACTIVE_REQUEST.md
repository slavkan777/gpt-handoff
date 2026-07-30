REQUEST_ID: REQ-2026-06-08-insuranceai-azure-langchain-sidecar-deployment-and-smoke-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-azure-langchain-sidecar-deployment-and-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_LANGCHAIN_SIDECAR_DEPLOYMENT_AND_SMOKE_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/azure-langchain-sidecar-deployment-and-smoke-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
REQUEST_ID: REQ-2026-06-08-insuranceai-azure-langchain-sidecar-deployment-and-smoke-v0-1
GATE: AZURE_LANGCHAIN_SIDECAR_DEPLOYMENT_AND_SMOKE_V0.1
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
SOURCE_REPO_BRANCH: rag/local-foundation-mega-v0.1
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/azure-langchain-sidecar-deployment-and-smoke-v0.1/report.md
FORBIDDEN TARGETS: main, production, unrelated repos, global bridge as canonical, AIKB writes, claude-vault, credentials, real PII.

OWNER DECISION:
Slava approved finishing the LangChain feature. Use a separate dev/test Azure Container App for the Python LangChain sidecar. Do not use the second-container-in-existing-backend topology unless the separate app path is impossible; if impossible, stop and report BLOCKED.

READ FIRST:
1. gpt-handoff/InsuranceAIPlatform/langchain-advanced-claim-analytics-sidecar-v0.1/report.md
2. gpt-handoff/InsuranceAIPlatform/latest-report.md
3. ai-kb/01_PROJECTS/InsuranceAIPlatform/PROJECT_PROFILE.md
4. ai-kb/01_PROJECTS/InsuranceAIPlatform/CURRENT_STATE.md
5. ai-kb/01_PROJECTS/InsuranceAIPlatform/TASK_LEDGER.md
6. Source repo current state on rag/local-foundation-mega-v0.1

CURRENT STATE:
The previous LangChain gate implemented a Python FastAPI LangChain sidecar, .NET AdvancedAiReview endpoint, and React Advanced AI Review panel. It pushed commit 2259946 to rag/local-foundation-mega-v0.1 and reported PARTIAL because Azure sidecar hosting required owner approval. Azure currently still runs the accepted fallback backend revision iap-demo-api--0000004.

GOAL:
Make the Advanced AI Review button work on the live Azure demo. The live flow must be: React button -> .NET endpoint /api/claims/{claimId}/advanced-ai-review -> claim + claim-scoped EvidenceChunks -> Python FastAPI LangChain sidecar -> structured advisory report -> UI panel with coverage assessment, anomalies/risk signals, missing documents, recommended next action, confidence, provider/mode, advisoryOnly=true, and claim-scoped citations.

DONE STATE:
1. Repo/branch/report routing is verified.
2. Source branch is 2259946 or descendant.
3. Sidecar container packaging exists and is minimal; add Dockerfile/.dockerignore only if missing.
4. Local checks pass before Azure changes: sidecar health/smoke, .NET tests, frontend build, and source scan over changed files.
5. Sidecar image is built and pushed to approved registry with a unique tag.
6. New dev/test Container App for the sidecar is deployed in the existing InsuranceAIPlatform Azure environment/resource group where possible.
7. .NET backend Container App is updated with AdvancedAiReview__Enabled=true and AdvancedAiReview__SidecarBaseUrl pointing to the sidecar.
8. SWA/frontend is redeployed if the current live site does not contain the Advanced AI Review panel/button.
9. Live Azure smoke proves the button returns a structured LangChain advisory review with citations only from the current claim.
10. Negative/no-evidence behavior remains safe.
11. Existing core RAG remains working and honestly labelled as fallback; CLM-1006 regression still works.
12. Rollback target is recorded before changes and rollback is executed if critical smoke fails.
13. Report is published to project-specific gpt-handoff paths and latest-summary.json is corrected for this gate.

IN SCOPE:
- Branch rag/local-foundation-mega-v0.1 only.
- Minimal sidecar containerization/deploy support.
- Feature-branch commit/push only if files change and checks pass.
- Azure dev/test deploy of exactly one sidecar Container App.
- Update existing iap-demo-api env/revision to call the sidecar.
- Redeploy existing SWA only if needed for the button/panel.
- Synthetic E2E-LANGCHAIN data only.
- Screenshots/artifacts under gpt-handoff/InsuranceAIPlatform/azure-langchain-sidecar-deployment-and-smoke-v0.1/.

FORBIDDEN:
No main. No merge. No production. No force push. No paid LLM/provider. No Azure Qdrant/Ollama/OpenAI/DeepSeek resource. No credentials in files/logs/reports. No real PII. No schema migration unless STOP and explain. No direct DB business writes. Do not replace .NET RAG. Do not mutate seeded CLM-1006/1007/1012. No final payout/fraud/legal decision. No unrelated UI rewrite. Do not edit AIKB in this gate; propose AIKB updates in the report only.

PHASES:
0. Verify routing/repo/branch/current HEAD/Azure starting revision. Stop on mismatch.
1. Inspect sidecar and deploy prerequisites. Add minimal Dockerfile/.dockerignore only if needed.
2. Run local verification: sidecar health/smoke, dotnet tests, frontend build, image build, source scan.
3. Commit/push source changes to feature branch only if needed.
4. Build and push the sidecar image.
5. Deploy separate sidecar Container App, preferably internal ingress on port 8090 in the same existing Container Apps environment. Stop if the existing environment/resource group cannot be identified safely.
6. Update iap-demo-api env vars and deploy a new healthy backend revision.
7. Redeploy SWA if the live frontend does not show the Advanced AI Review UI.
8. Run Azure smoke: baseline health, create/use synthetic claim, upload evidence, base RAG citation check, advanced review API check, UI button/panel check, negative no-evidence check, CLM-1006 regression.
9. Roll back/disable if critical smoke fails. Do not leave the public demo misleading or unhealthy.
10. Publish report.

SMOKE MATRIX:
A. /health, /api/claims, /rag/infrastructure are healthy.
B. New synthetic claim with uploaded evidence has claim-scoped EvidenceChunks through existing upload/RAG path.
C. POST /api/claims/{claimId}/advanced-ai-review returns framework=langchain, advisoryOnly=true, providerMode honest, confidence present, sections present, citations scoped to that claim, no CLM-1006/1007/1012 leakage.
D. Live UI shows the separate Advanced AI Review button and structured report panel after click.
E. No-evidence case returns safe insufficient/fallback and no fabricated citations.
F. CLM-1006 core RAG still works; existing seeded workflow not broken.

ROLLBACK:
Record prior iap-demo-api active revision and relevant AdvancedAiReview env values before changes. If backend health or safety smoke fails, restore previous backend revision/env. If the new sidecar app is unusable, scale it to zero or delete only if it was created by this gate. Never delete seeded records.

REPORT:
Publish sanitized report to:
- gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
- gpt-handoff/InsuranceAIPlatform/latest-report.md
- gpt-handoff/InsuranceAIPlatform/latest-summary.json
- gpt-handoff/InsuranceAIPlatform/azure-langchain-sidecar-deployment-and-smoke-v0.1/report.md
- screenshots/artifacts under the same gate folder if produced.

Report must start:
REQUEST_ID: REQ-2026-06-08-insuranceai-azure-langchain-sidecar-deployment-and-smoke-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | ROLLED_BACK | FAILED
TASK_TYPE: project-azure-langchain-sidecar-deployment-and-smoke
PROJECT: InsuranceAIPlatform
GATE: AZURE_LANGCHAIN_SIDECAR_DEPLOYMENT_AND_SMOKE_V0.1
COMPLETED_BY: claude

Required sections: Routing Lock Verification; Starting State; Owner Decision / Topology; Source Changes; Commands Run; Local Verification; Image Build / Push; Azure Resources Touched; Sidecar Deployment State; Backend Deployment State; SWA Deployment State; Azure Smoke Matrix; API Proof; UI Proof; Claim-Scoped Citation Proof; Negative/Fallback Proof; Regression Proof; Screenshots/Artifacts; Cost/Resource Notes; Rollback/Cleanup State; Boundaries Honored; Deviations; Defects/Gaps; Slava Manual Checklist; Recommended Next Step.

FINAL VERDICT RULE:
READY_FOR_AUDIT only if live Azure sidecar + .NET + UI button/panel smoke passes with claim-scoped citations and advisory safety proven. Otherwise use PARTIAL/BLOCKED/ROLLED_BACK/FAILED honestly.

FINAL LINE:
GitHub handoff ready. Tell GPT: отчёт.
