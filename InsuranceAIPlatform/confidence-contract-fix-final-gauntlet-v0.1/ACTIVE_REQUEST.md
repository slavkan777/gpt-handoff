REQUEST_ID: REQ-2026-06-05-insuranceai-confidence-fix-final-gauntlet-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: project-fix-verify-final-gauntlet
PROJECT: InsuranceAIPlatform
GATE: CONFIDENCE_CONTRACT_FIX_FINAL_GAUNTLET_V0.1
TARGET_REPORT_PATH: InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: InsuranceAIPlatform/confidence-contract-fix-final-gauntlet-v0.1/report.md
LATEST_REPORT_PATH: InsuranceAIPlatform/latest-report.md
CREATED_BY: architect-gpt

ROUTING LOCK:
PROJECT: InsuranceAIPlatform
SOURCE_REPO: C:/Projects/InsuranceAIPlatform
SOURCE_REPO_REMOTE: slavkan777/InsuranceAIPlatform
HANDOFF_PROJECT_PATH: gpt-handoff/InsuranceAIPlatform/
ACTIVE_REQUEST: gpt-handoff/InsuranceAIPlatform/_BRIDGE/ACTIVE_REQUEST.md
LATEST_REPORT: gpt-handoff/InsuranceAIPlatform/_BRIDGE/LATEST_REPORT.md
GATE_REPORT: gpt-handoff/InsuranceAIPlatform/confidence-contract-fix-final-gauntlet-v0.1/report.md

OPERATOR NOTE:
Follow ROUTING LOCK strictly. This is the last combined fix + verification gauntlet before Slava manual acceptance. Avoid endless small gates. Fix the known confidence bug, aggressively verify the main business/technical paths, and commit once only if clean. Do not push.

CONTEXT:
Previous accepted gates:
- Qdrant RAG feature committed locally on feature branch as 912e841.
- Playwright manual-acceptance sweep passed.
- Business-process sweep found one real product bug: manager-facing confidence renders as 1400%/1700%/4100% in real-backend mode because mock and real confidence scales differ.

KNOWN BUG TO FIX:
Confidence scale mismatch:
- UI component multiplies confidence by 100 for display.
- Mock data uses confidence as 0..1 fraction.
- Real backend path passes confidence as 0..100 integer, causing 14 => 1400%.
Required result: rendered confidence must be sane, normally 0..100%, in both mock and backend modes.

GOAL:
Fix the confidence contract bug and perform one final adversarial/business/technical gauntlet sufficient to clear the project for Slava hands-on manual acceptance, without starting another loop.

DONE STATE:
1. Verify routing lock, repo path, branch, HEAD, and current clean/expected working tree.
2. Fix confidence normalization in the narrowest safe place so mock and real backend render consistently as 0..100%.
3. Add/adjust tests to prevent regression:
   - backend-mode DTO confidence 14/17/41 renders as 14%/17%/41%, not 1400%/1700%/4100%;
   - mock/fraction confidence 0.82 still renders as 82%;
   - rendered confidence never exceeds 100% for normal 0..100 backend values.
4. Run targeted frontend tests/E2E for Claim Evidence Intelligence in backend mode where practical, and existing mock regression.
5. Run quick backend/API smoke as needed to ensure no regression in CLM-1006 RAG infrastructure and ask path.
6. Run final adversarial stress sweep without broad code changes:
   - rapid repeated Check clicks;
   - Qdrant stop/start fallback and recovery;
   - Qdrant collection empty/missing then recreate/upsert if safe;
   - backend restart then user clicks Check again;
   - CLM-1006 <-> CLM-1009 switching, no stale citations/leakage;
   - mock/backend mode confusion: ensure mock mode is distinguishable and backend mode shows sane confidence;
   - mobile/narrow viewport readability for confidence, citations, advisory banner;
   - no fatal console/network errors.
7. Re-run the business-critical checks from the previous sweep:
   - CLM-1006 happy path;
   - CLM-1061 insufficient/no evidence cautious answer;
   - CLM-1009 isolation;
   - advisory/legal boundary no auto payout/rejection/fraud finding;
   - demo script: dashboard -> claim -> Evidence Intelligence -> Check policy coverage -> citations -> fallback -> restore.
8. If and only if all verification passes, create ONE local source commit on current feature branch. Suggested message: `fix(rag): normalize evidence confidence display`.
9. Publish a fresh sanitized report to all three project-specific paths.

ALLOWED FIX SCOPE:
- Frontend confidence normalization/mapping and tests.
- Backend DTO confidence only if clearly the safer contract location.
- Test/spec updates needed for confidence and final gauntlet.
- No unrelated UI redesign.

BOUNDARIES:
NO push. NO main. NO Azure. NO deployment. NO paid provider. NO secrets. NO real PII. NO Ollama. NO schema/EF migration. NO unrelated projects. NO broad refactor. NO making up business data. NO fake pass. NO accepting URL/toast-only tests. NO commit if tests/smoke fail. If the fix requires schema, provider, or broad redesign, stop and report BLOCKED.

STOP CONDITIONS:
Stop with FAILED/BLOCKED if:
- confidence still exceeds 100% in backend mode;
- CLM-1006 citations leak foreign claim chunks;
- fallback fails when Qdrant stops;
- backend mode cannot be forced reliably;
- build/tests fail and cannot be fixed narrowly;
- unrelated files would be included in commit;
- any forbidden target/action is required.

REPORT FORMAT:
REQUEST_ID: REQ-2026-06-05-insuranceai-confidence-fix-final-gauntlet-v0-1
STATUS: READY_FOR_AUDIT | PARTIAL | BLOCKED | FAILED
TASK_TYPE: project-fix-verify-final-gauntlet
PROJECT: InsuranceAIPlatform
GATE: CONFIDENCE_CONTRACT_FIX_FINAL_GAUNTLET_V0.1
COMPLETED_BY: claude

Sections: Routing Lock Verification, Starting State, Root Cause, Fix Summary, Files Changed, Confidence Contract Proof, Final Stress Matrix, Business Recheck Matrix, Qdrant/Fallback Proof, Leakage Proof, Console/Network Findings, Tests/Commands, Commit Result, Boundaries Honored, Remaining Known Limitations, Slava Manual Checklist, Manual Acceptance Recommendation, Next Safe Step.

STOP after report. Do not push, merge, deploy, touch Azure, or touch unrelated projects.