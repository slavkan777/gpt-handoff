REQUEST_ID=REQ-2026-06-24-uacg-prompt-review-v0-1
STATE=READY_FOR_CLAUDE
PROJECT=UniversalApiConnectorGenerator
GATE=SOURCE_SCAN_PROMPT_REVIEW_V0_1
WORKSPACE=C:\Projects\UniversalApiConnectorGenerator
MODE=READ_ONLY_REVIEW
TARGET_PROMPT=UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/REQ-2026-06-24-uacg-next-container-v0-1.md

ROUTE USED: DIRECT_MACRO_CONTAINER_REVIEW
ALLOWED: read bridge files, read local workspace, run read-only build/test checks if needed, write only Bridge report
FORBIDDEN: edit local source, edit prompt files, edit AIKB, touch unrelated folders, remote repo operations, external service calls, deployment

GOAL
Review the target container macro prompt before execution. Do not implement it.

READ
1. Bridge README
2. STATUS.json
3. TARGET_PROMPT
4. Local solution structure in C:\Projects\UniversalApiConnectorGenerator
5. Existing README/docs only if present

CHECKLIST
- Does the target prompt match current project state?
- Is scope too broad or unclear?
- Are containers ordered safely?
- Are stop conditions strong enough?
- Are build/test/smoke checks sufficient?
- Are docs for Igor clearly specified?
- Are forbidden areas protected?
- What should GPT patch before execution?

REPORT
Write to UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md

Include:
REQUEST_ID=REQ-2026-06-24-uacg-prompt-review-v0-1
STATUS=READY_FOR_GPT_AUDIT or BLOCKED
PROJECT=UniversalApiConnectorGenerator
GATE=SOURCE_SCAN_PROMPT_REVIEW_V0_1

Sections:
- Prompt Review Verdict
- Critical Issues
- Recommended Patches
- Container-by-Container Notes
- Risks
- Suggested Final Prompt Changes
- Execute / Do Not Execute Recommendation

Claude chat reply only:
Bridge report ready. Tell GPT: отчёт.
