REQUEST_ID: NONE
STATE: IDLE
TASK_TYPE: project-bootstrap
PROJECT: UniversalApiConnectorGenerator
GATE: ARCHITECTURE_DISCOVERY
ACTIVE_REQUEST_PATH: UniversalApiConnectorGenerator/_BRIDGE/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: UniversalApiConnectorGenerator/_BRIDGE/LATEST_REPORT.md
LATEST_REPORT_PATH: UniversalApiConnectorGenerator/latest-report.md
CREATED: 2026-06-23
CREATED_BY: architect-gpt

# No Active Claude Request

The project-specific bridge is initialized, but no Claude execution is authorized.

ROUTING LOCK:

- PROJECT: UniversalApiConnectorGenerator
- SOURCE_REPO: PENDING_CREATION
- SOURCE_REPO_REMOTE: slavkan777/universal-api-connector-generator (planned)
- SOURCE_REPO_BRANCH: main (planned)
- HANDOFF_REPO: slavkan777/gpt-handoff
- HANDOFF_PROJECT_PATH: gpt-handoff/UniversalApiConnectorGenerator/
- ACTIVE_REQUEST: gpt-handoff/UniversalApiConnectorGenerator/_BRIDGE/ACTIVE_REQUEST.md
- LATEST_REPORT: gpt-handoff/UniversalApiConnectorGenerator/_BRIDGE/LATEST_REPORT.md
- GATE_REPORT: not opened

FORBIDDEN TARGETS:

- Twincore-framework and TwinCore/AiIntegrator source
- unrelated project folders
- global gpt-handoff/_BRIDGE as authoritative channel
- AIKB writes from Claude
- claude-vault changes
- source implementation
- commit/push/PR
- paid APIs, credentials, live UPS, deployment

STOP:

Do not execute. Wait for an approved architecture contract, first vertical slice, and a new REQUEST_ID written by Architect GPT.
