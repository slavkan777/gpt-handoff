REQUEST_ID: REQ-2026-06-17-twincore-phase-0-3-generator-architecture-research
STATE: READY_FOR_CLAUDE
TASK_TYPE: research-architecture
PROJECT: TwinCore
GATE: Phase 0.3 / Draft Generator Algorithm research
ACTIVE_REQUEST_PATH: TwinCore/phase-0.3-universal-connector-generator-architecture-research/ACTIVE_REQUEST.md
TARGET_REPORT_PATH: TwinCore/phase-0.3-universal-connector-generator-architecture-research/report.md
LATEST_REPORT_PATH: TwinCore/latest-report.md
PROJECT_BRIDGE_REPORT_PATH: TwinCore/_BRIDGE/LATEST_REPORT.md
PROJECT_STATUS_PATH: TwinCore/_BRIDGE/STATUS.json
GLOBAL_COMPATIBILITY_REPORT_PATH: _BRIDGE/LATEST_REPORT.md
AIKB_UPDATE_REQUIRED: no, candidate only after GPT audit and Slava approval
CREATED: 2026-06-17T19:07:00+03:00
CREATED_BY: GPT Architect

# TWINCORE BRIDGE ACTIVE REQUEST

## MANDATORY AGENT ARMY ROUTING

Claude must enter Agent Army routing first.
No direct Claude execution before project resolution and route classification.
This task is research/planning/audit work, not implementation.
Use `/army-start` route semantics for this task.
TRIVIAL_DIRECT is not applicable unless Agent Army explicitly classifies the task as trivial, which is unlikely.

## PROMPT HANDOFF

Prompt origin:
Git file: `slavkan777/gpt-handoff:TwinCore/phase-0.3-universal-connector-generator-architecture-research/ACTIVE_REQUEST.md@main`

Claude must read:
Repository: `slavkan777/gpt-handoff`
Branch: `main`
File: `TwinCore/phase-0.3-universal-connector-generator-architecture-research/ACTIVE_REQUEST.md`

Claude must write report to:
Repository: `slavkan777/gpt-handoff`
Branch: `main`
File: `TwinCore/phase-0.3-universal-connector-generator-architecture-research/report.md`

Claude should also update compatibility report paths:
- `TwinCore/latest-report.md`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`
- `TwinCore/_BRIDGE/STATUS.json`
- `_BRIDGE/LATEST_REPORT.md` as global compatibility mirror only

Commit/push:
Allowed only for bridge report files in `slavkan777/gpt-handoff`.
Not allowed for TwinCore source/runtime or AIKB durable state.

Expected final output from Claude:
- Agent Army route used
- current branch
- file path read
- file path written
- commit hash, if committed
- push status, if pushed
- missing sources, if any
- no code/runtime changes made

## ROUTING LOCK

Target repository:
`slavkan777/gpt-handoff`

Local workspace, if applicable:
Claude may use its own workspace, but must verify repository identity before writing.

Target branch:
`main`

Allowed action:
research-only, docs/report-only

Allowed target files/paths:
- `TwinCore/phase-0.3-universal-connector-generator-architecture-research/report.md`
- `TwinCore/latest-report.md`
- `TwinCore/latest-summary.json`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`
- `TwinCore/_BRIDGE/STATUS.json`
- `_BRIDGE/LATEST_REPORT.md` as fallback mirror only

Forbidden repositories:
- TwinCore source/runtime repositories
- `slavkan777/ai-kb` durable updates unless Slava later explicitly says `зафиксируй`
- any production/customer/provider repository

Forbidden paths/actions:
- No generator code
- No CLI
- No WPF
- No TwinCore runtime changes
- No credentials
- No paid API calls
- No production integration
- No source repo commits
- No AIKB writes

Expected artifact:
`TwinCore/phase-0.3-universal-connector-generator-architecture-research/report.md`

Stop condition:
If repository, branch, project identity, gate, or allowed paths do not match this lock, stop before writing and report `BLOCKED`.

## PROJECT

TwinCore / Universal API Connector Generator.

Important framing:
This is not a Rate API generator.
This is a Universal API / Integration Connector Generator.
Rate API / UPS Rating API is only the first proof case.

Current gate:
Phase 0.3 / Draft Generator Algorithm research.
No implementation is open.

## READ FIRST

1. Resolve project identity as TwinCore.
2. Read AIKB/control/project context needed for TwinCore.
3. Read latest TwinCore state/handoff/task ledger if available.
4. Read prior reports if available:
   - market-products-review
   - tool-trial-nswag-kiota
   - manual-carrier-provider-spike
   - latest-report / bridge reports if present
5. Confirm current gate is Phase 0.3 planning, not implementation.
6. Confirm Agent Army baseline/routing requirement from available rules.

If any requested source is missing, list it explicitly and continue only from available evidence.
Do not guess missing AIKB content.

## TASK

Deeply research and propose architecture for Universal API / Integration Connector Generator for logistics carriers.

Research these areas:

1. Free/open-source libraries for reading/parsing all input formats.
2. Real carrier integration formats:
   - REST
   - OpenAPI / Swagger
   - Postman collections
   - SOAP / WSDL
   - XML
   - JSON examples
   - PDF / DOCX / HTML docs
   - CSV
   - EDI X12
   - EDIFACT
   - SFTP / batch files
   - curl examples
   - HAR / traffic captures
3. Extraction Backend Registry:
   - primary extractor
   - fallback extractor
   - forensic multi-extractor mode
   - confidence scoring
   - conflict detection
4. Format Intelligence Library:
   - format rules
   - carrier patterns
   - logistics vocabulary
   - domain model patterns
   - ambiguity rules
   - forbidden autonomy rules
5. AI usage model:
   - AI is not an oracle
   - AI is semantic model discovery layer
   - AI proposes candidate domain models, mappings, unresolved questions
   - AI must not silently approve business-critical decisions
6. NormalizedIntegrationContract:
   - why broader than NormalizedApiContract
   - support REST, SOAP, EDI, SFTP/batch, files, examples, docs
7. Fact vs inference separation:
   - source evidence
   - extractor evidence
   - AI inference evidence
   - review status
8. Validation / test / governance tools before generation.
9. MVP scope vs extension points.
10. Risks:
   - license
   - maintenance
   - runtime dependency
   - Java/Python/Node/GPU dependencies
   - hallucination
   - security
   - credentials/secrets
   - paid APIs
   - untrusted WSDL/docs
   - legal/business mapping mistakes

## STARTING HYPOTHESIS TO VERIFY AND IMPROVE

Architecture modules:
1. Source Intake
2. Source Detector
3. ExtractionBackendRegistry
4. Extraction Orchestrator
5. Raw Material Normalizer
6. Format Intelligence Library
7. Semantic Model Discovery
8. NormalizedIntegrationContract
9. Profile Fit Layer
10. Connector Package Planner
11. Validation / Governance Layer
12. Evidence / Ambiguity / Review Gate

Candidate free/open-source tools to verify:

Documents:
- Apache Tika
- MarkItDown
- Docling
- Unstructured
- pdfplumber
- PyMuPDF
- Camelot
- Tabula

OCR:
- Tesseract
- OCRmyPDF
- PaddleOCR
- RapidOCR
- EasyOCR
- docTR
- Surya OCR

OpenAPI / REST:
- Swagger Parser
- NSwag
- Kiota
- OpenAPI Generator
- Swagger Codegen
- Scalar OpenAPI Parser
- Spectral
- Prism
- Schemathesis

Postman:
- Postman Collection SDK
- postman-to-openapi
- Newman

SOAP / WSDL / XML:
- dotnet-svcutil
- WCF Web Service Reference
- XmlSchemaClassGenerator
- xmlschema
- Trang
- xmltoxsd

EDI / legacy logistics:
- EDI.Net
- EdiEngine
- X12Parser
- pyx12
- pydifact
- Smooks EDI cartridge

Examples / schema inference:
- quicktype
- GenSON
- schema-infer
- genson-js
- xmlschema
- Trang

## EXPECTED REPORT

Create one markdown report in:

`TwinCore/phase-0.3-universal-connector-generator-architecture-research/report.md`

Also update compatibility paths:

- `TwinCore/latest-report.md`
- `TwinCore/latest-summary.json`
- `TwinCore/_BRIDGE/LATEST_REPORT.md`
- `TwinCore/_BRIDGE/STATUS.json`
- `_BRIDGE/LATEST_REPORT.md` as global fallback only

Do not write AIKB. If you believe an AIKB update is needed, include it as `AIKB_UPDATE_CANDIDATE` in the report.

## REPORT MUST INCLUDE

1. Request metadata:
   - REQUEST_ID
   - PROJECT
   - GATE
   - STATUS
   - ACTIVE_REQUEST_PATH
   - TARGET_REPORT_PATH
2. Agent Army route used and agents involved.
3. Executive summary.
4. Recommended architecture modules.
5. Free/open-source tool catalog by category.
6. Default/fallback order per format.
7. ExtractionBackendRegistry design.
8. Forensic mode design.
9. Format Intelligence Library design.
10. Semantic Model Discovery design.
11. NormalizedIntegrationContract proposal.
12. Fact vs inference model.
13. Confidence/evidence model.
14. Review gate rules.
15. Validation/governance tools.
16. MVP scope.
17. Later extension points.
18. Risks and mitigations.
19. Open questions for Slava/Igor.
20. Source links / citations for researched tools.
21. Missing sources, if any.
22. Not touched / boundaries honored.
23. Next safe step.
24. Improve Step:
   - if no durable lesson: `IMPROVE STEP: no lesson`
   - otherwise propose candidate only; do not apply AIKB update.

## IMPORTANT DECISIONS TO EVALUATE

- Should MVP stay limited to OpenAPI YAML/JSON + examples + manual hints?
- Should NSwag remain first C# baseline?
- Should Kiota stay deferred behind abstraction?
- Should EDI/SFTP stay future extension?
- Should AI output DraftNormalizedIntegrationContract only, never ApprovedContract?
- Should Format Intelligence Library be designed now as structured knowledge/rules even before implementation?
- Should Phase 0.3 define exact shapes for AmbiguityLog and EvidenceLedger?

## ACCEPTANCE CRITERIA

- Agent Army route/evidence is used.
- No implementation changes.
- No TwinCore runtime changes.
- No AIKB writes.
- Report is saved to the canonical TwinCore bridge report path.
- Research cites official/primary sources where possible.
- Final answer states:
  - route used
  - report path
  - key recommendation
  - unresolved questions
  - missing sources, if any
  - no code/runtime changes were made

After report: STOP. GPT will audit. Slava/Igor remain owner acceptance gate.
