REQUEST_ID: REQ-2026-06-05-insuranceai-qdrant-retrieval-adapter-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-implementation
PROJECT: InsuranceAIPlatform
GATE: QDRANT_RETRIEVAL_ADAPTER_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/qdrant-retrieval-adapter-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/qdrant-retrieval-adapter-v0.1/report.md

OPERATOR NOTE:
Сори, путаница вышла с проектами: раньше не было явно указано, какой проект, какая source-папка и куда писать отчёт. Follow ROUTING LOCK strictly. Rule is fixed in AIKB: 00_CONTROL_CENTER/REQUEST_ROUTING_LOCK_RULE.md and START_HERE.md.

CONTEXT:
Previous gate proved Qdrant is live locally as a runtime on localhost:6333, container iap-qdrant. It also clearly stated Qdrant is not yet the real retrieval backend. The current code can show backend=qdrant when reachable, but retrieval still uses the in-process deterministic index. This gate must remove that semantic gap.

GOAL:
Implement a real local Qdrant retrieval adapter for InsuranceAIPlatform RAG so Qdrant is not only probe-live, but actually stores/queryes evidence vectors when enabled and reachable. Keep fallback safe when Qdrant is unavailable.

DONE STATE:
1. Verify routing lock, repo path, branch, HEAD, and working tree.
2. Confirm local Qdrant is reachable or start/reuse the existing iap-qdrant container if available.
3. Add minimal Qdrant adapter/seam for upsert and search of claim evidence chunks using existing embeddings.
4. When Rag__QdrantEnabled=true and Qdrant is reachable, reindex/upsert CLM-1006 evidence into Qdrant and query Qdrant for retrieval/similar evidence path where appropriate.
5. When Qdrant is disabled or unreachable, preserve the existing in-memory-hash fallback.
6. API/UI status must be semantically honest: backend=qdrant only when Qdrant is actually used for retrieval, not merely probe reachable.
7. Add tests proving enabled+reachable uses Qdrant adapter path; unreachable/disabled uses fallback; no fake live backend labels.
8. Run backend tests, frontend build if touched, targeted RAG/Evidence E2E or smoke, and live CLM-1006 smoke against Qdrant.
9. Publish report to all three project-specific paths.

BOUNDARIES:
No source commit/push. No Azure/cloud. No paid providers. No secrets. No real PII. No main branch. No unrelated project edits. No schema migration unless already unavoidable; if migration is needed, stop and report BLOCKED. No Ollama work in this gate. No fake backend=qdrant if retrieval is not served by Qdrant.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-05-insuranceai-qdrant-retrieval-adapter-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-implementation
PROJECT: InsuranceAIPlatform
GATE: QDRANT_RETRIEVAL_ADAPTER_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Current Repo State, Qdrant Runtime Check, Implementation Summary, Retrieval Semantics, Files Changed, Commands Run, Verification Evidence, Tests, Boundaries Honored, Known Limitations, Not Touched, Next Safe Step.

STOP after implementation, verification, and report. Do not commit, push, deploy, touch Azure, or touch unrelated projects.
