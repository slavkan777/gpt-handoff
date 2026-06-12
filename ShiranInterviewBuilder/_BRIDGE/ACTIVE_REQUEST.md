REQUEST_ID: REQ-2026-06-12-shiran-interview-builder-local-finalization
PROJECT: ShiranInterviewBuilder
GATE: LOCAL_FINALIZATION_WITH_AIKB_AND_HANDOFF
STATE: EXECUTED — see report.md in this folder
CREATED_BY: architect-gpt (relayed via operator chat)
EXECUTED_BY: claude
SANITIZATION: client context, person names, contact numbers and absolute workstation paths are REDACTED from this public mirror; the full request and client context live in the private AIKB (`01_PROJECTS/ShiranInterviewBuilder/`).

# TASK (mirror, sanitized)

Timeboxed Product Engineer home task: merge the prepared Interview Builder implementation from the local reference draft (`v3-draft/InterviewBuilder_Slava_submission_draft_v3`, read-only) into the active project folder, then verify and fix only critical issues until the project builds and runs locally. No GitHub push of assignment code. Final deliverable = clean local submission package.

## DONE STATE

1. Project-specific AIKB infrastructure exists or a clear blocker is reported.
2. Project-specific gpt-handoff report paths exist or a clear blocker is reported.
3. Implementation merged into the active project folder.
4. `docker compose up --build` succeeds.
5. Web app starts on http://localhost:5001.
6. Python mocked question generator service is healthy.
7. Interview Builder page supports the required end-to-end flow (position/interview choose-or-create, skill, question with suggestions-while-typing, AI draft via Python mock, component type, mandatory flag, high-score guidance, submit, ordered sequence, persistence after refresh).
8. README_SUBMISSION.md, TASK_BREAKDOWN.md, AI_CORRESPONDENCE.md present and accurate.
9. No secrets, real PII, bin/obj folders, or unrelated artifacts in the final submission.

## STRICT BOUNDARIES (enforced)

Work only inside the active project folder; reference draft read-only; no pushes of assignment code to GitHub; no new frontend frameworks; no architecture rewrite; no broad refactoring; no Docker port changes; no secrets/PII/build-artifacts in submission; no completion claims without build/runtime/manual evidence. Additional product clarification mid-gate: preserve existing visual identity/scaffold (logo, sidebar, navigation, homepage, global styling) — feature UI only, using existing styles.

## REPORT PATHS

- `ShiranInterviewBuilder/local-finalization/report.md`
- `ShiranInterviewBuilder/latest-report.md`
- `ShiranInterviewBuilder/_BRIDGE/LATEST_REPORT.md`
- `ShiranInterviewBuilder/latest-summary.json`
