REQUEST_ID: REQ-2026-06-14-twincore-rate-api-carrier-docs-research-v0-1
STATE: READY_FOR_CLAUDE
TASK_TYPE: research-spike
PROJECT: TwinCore
GATE: research / specification support
TARGET_REPORT_PATH: TwinCore/_BRIDGE/LATEST_REPORT.md
PROJECT_REPORT_PATH: TwinCore/rate-api-carrier-docs-research-v0.1/report.md
COMPATIBILITY_REPORT_PATH: TwinCore/latest-report.md
AIKB_UPDATE_REQUIRED: after-gpt-audit-and-slava-approval
CREATED: 2026-06-14T00:00:00+00:00
CREATED_BY: GPT Architect

# ROUTING LOCK

Target project: TwinCore
Source repo: none for this gate. Do not edit Azure DevOps source repo.
AIKB repo: slavkan777/ai-kb
Handoff repo: slavkan777/gpt-handoff
Active request path: TwinCore/rate-api-carrier-docs-research-v0.1/ACTIVE_REQUEST.md
Bridge active request path: TwinCore/_BRIDGE/ACTIVE_REQUEST.md
Primary report path: TwinCore/rate-api-carrier-docs-research-v0.1/report.md
Latest report mirror: TwinCore/_BRIDGE/LATEST_REPORT.md
Compatibility report mirror: TwinCore/latest-report.md
Forbidden targets: Azure source repo writes, PRs, main branch, source code edits, carrier credentials, paid API calls, production claims, AIKB writes.

# READ FIRST

Read these AIKB files before research:

1. slavkan777/ai-kb/00_CONTROL_CENTER/START_HERE.md
2. slavkan777/ai-kb/00_CONTROL_CENTER/PROJECT_BRIDGE_PROTOCOL.md
3. slavkan777/ai-kb/00_CONTROL_CENTER/SPECIFICATION_FIRST_RULE.md
4. slavkan777/ai-kb/00_CONTROL_CENTER/SPECIFICATION_ENCYCLOPEDIA.md
5. slavkan777/ai-kb/01_PROJECTS/TwinCore/CURRENT_STATE.md
6. slavkan777/ai-kb/01_PROJECTS/TwinCore/SPECIFICATIONS/RATE_API_CONNECTOR_GENERATOR/README.md
7. slavkan777/ai-kb/01_PROJECTS/TwinCore/SPECIFICATIONS/RATE_API_CONNECTOR_GENERATOR/00_MASTER_SDD_SPEC.md
8. slavkan777/ai-kb/01_PROJECTS/TwinCore/SPECIFICATIONS/RATE_API_CONNECTOR_GENERATOR/01_INTEGRATION_EXTERNAL_SYSTEM_SPEC.md
9. slavkan777/ai-kb/01_PROJECTS/TwinCore/SPECIFICATIONS/RATE_API_CONNECTOR_GENERATOR/02_INTERNAL_API_CONTRACT_SPEC.md
10. slavkan777/ai-kb/01_PROJECTS/TwinCore/SPECIFICATIONS/RATE_API_CONNECTOR_GENERATOR/03_GENERATION_OUTPUT_SPEC.md
11. slavkan777/ai-kb/01_PROJECTS/TwinCore/SPECIFICATIONS/RATE_API_CONNECTOR_GENERATOR/QUESTIONS.md

# CURRENT STATE

TwinCore has a draft SDD package for Task 12695: a simple AI-assisted module/framework for fast integration of logistics Rate API carriers.

Core task:

- many logistics APIs must be connected: FedEx, UPS, USPS, DHL, Canada Post, Purolator, and others;
- the tool accepts Swagger/OpenAPI, SOAP/WSDL, documentation, and examples;
- the tool generates production-oriented C# API client, provider/adapter class, request mapper, response mapper, tests, authorization/configuration, retry/timeout support;
- runtime Rate API must not depend on LLM;
- LLM should be minimized and used only where deterministic parsing/generation is insufficient.

# GOAL

Perform internet research on carrier and shipping API providers to support the SDD specification.

Research approximately the first 40 relevant logistics/shipping carriers and aggregator APIs.

For each provider, identify what public developer material is available and how suitable it is for the first version of the TwinCore Rate API Connector Generator.

# DONE STATE

Produce a research report that answers:

1. Which carriers/aggregators have public Swagger/OpenAPI or downloadable API specs?
2. Which have SOAP/WSDL/XSD?
3. Which have REST documentation but no confirmed OpenAPI file?
4. Which require developer portal login/manual check?
5. Which have good examples/Postman collections/SDKs/code samples useful for generation?
6. Which providers are best candidates for the first MVP?
7. Which providers should be deferred?
8. What should the small initial tool include?
9. What should the small initial tool explicitly not include?
10. How can LLM calls be minimized in this project?

# RESEARCH SCOPE

Start with these direct carriers:

1. FedEx
2. UPS
3. USPS
4. DHL Express
5. Canada Post
6. Purolator
7. Royal Mail
8. Australia Post
9. New Zealand Post
10. La Poste / Colissimo
11. Chronopost
12. DPD
13. GLS
14. Hermes / Evri
15. Yodel
16. PostNL
17. Correos
18. Poste Italiane
19. Japan Post
20. Singapore Post
21. Hongkong Post
22. China Post
23. Aramex
24. SF Express
25. Yamato Transport
26. Sagawa Express
27. OnTrac
28. LaserShip
29. Canpar
30. Loomis Express

Also research shipping aggregators / multi-carrier APIs:

31. Shippo
32. EasyPost
33. ShipEngine
34. ShipStation
35. AfterShip
36. Sendcloud
37. ShippyPro
38. Metapack
39. nShift
40. Easyship

If a provider is hard to verify quickly, mark it as NEEDS_MANUAL_CHECK. Do not invent facts.

# CLASSIFICATION MODEL

For each provider, classify documentation/spec availability using these categories:

A. OpenAPI / Swagger confirmed
B. REST API docs confirmed, OpenAPI not confirmed
C. SOAP / WSDL / XSD confirmed
D. SDK / code samples / Postman collection confirmed
E. Documentation/examples only
F. Developer portal login required
G. Not enough public evidence
H. Aggregator API with standardized multi-carrier rate model

Also classify MVP suitability:

- FIRST_MVP_CANDIDATE
- GOOD_SECOND_CANDIDATE
- SOAP_TEST_CANDIDATE
- DOCS_ONLY_TEST_CANDIDATE
- AGGREGATOR_REFERENCE_MODEL
- DEFER
- NEEDS_MANUAL_CHECK

# EVIDENCE REQUIREMENTS

For each provider, include:

- provider name;
- carrier vs aggregator;
- official developer/docs URL if found;
- public evidence for OpenAPI/Swagger/WSDL/docs/SDK/examples;
- whether Rate API / pricing / quote / rates endpoint exists;
- generation difficulty: low / medium / high / unknown;
- recommended status for MVP;
- short note explaining why.

Use official provider documentation whenever possible. If using third-party docs, mark it clearly as third-party evidence.

# OUTPUT STRUCTURE

Write the final report in Markdown with these sections:

1. Request identity
2. Method and limitations
3. Executive summary
4. Classification legend
5. Provider research matrix: first 40 providers
6. Best first MVP candidates
7. Best SOAP/WSDL test candidates
8. Best OpenAPI/Swagger candidates
9. Best docs-only/LLM-assisted candidates
10. Best aggregator reference models
11. Providers requiring manual developer portal check
12. Recommendation for the small initial tool
13. What the small initial tool should NOT include
14. LLM minimization strategy
15. Suggested updates to SDD spec
16. Risks and unknowns
17. Next safe step

# SMALL INITIAL TOOL — ANSWER REQUIRED

In the report, explicitly propose what the first small tool should include.

Expected starting hypothesis:

- input upload/import: OpenAPI/Swagger, WSDL, docs/examples;
- provider classification;
- rate endpoint detection;
- schema/field browser;
- basic mapping profile;
- C# API client generation or wrapper generation;
- provider adapter skeleton;
- request/response mapper skeleton;
- appsettings/options sample;
- retry/timeout placeholders or implementation;
- basic tests from fixtures;
- report/checklist of unresolved decisions.

Also explicitly state what the first small tool should not include:

- no real carrier credentials;
- no production deployment;
- no runtime LLM;
- no shipment creation/tracking/labels/payments;
- no automatic PR/main merge;
- no guarantee of production approval without human review.

# LLM MINIMIZATION — ANSWER REQUIRED

Separate deterministic vs LLM-assisted work:

Deterministic:

- OpenAPI parsing;
- WSDL parsing where possible;
- DTO/client generation;
- manifest/hash generation;
- generation from approved templates/profile;
- fixture-based tests.

LLM-assisted:

- docs-only interpretation;
- endpoint identification from unstructured docs;
- mapping suggestions;
- auth model explanation;
- error model/test scenario suggestions.

Forbidden:

- LLM during live Rate API request;
- LLM during price calculation;
- LLM during runtime response mapping;
- LLM as production dependency.

# BOUNDARIES

Research/report only.

Allowed:

- internet research;
- reading official public docs;
- reading AIKB specification context;
- writing report to gpt-handoff paths listed below.

Forbidden:

- do not edit Azure DevOps source repo;
- do not edit `slavkan777/ai-kb`;
- do not create branches, commits, PRs, or source code;
- do not use or ask for carrier credentials;
- do not sign up for paid accounts;
- do not call paid/real carrier APIs;
- do not claim verified sandbox behavior unless actually verified and documented;
- do not write secrets, tokens, raw credentials, or private data.

# STOP CONDITIONS

Stop and report BLOCKED if:

- internet access is not available;
- most official docs are inaccessible without login;
- task cannot be done without credentials or paid access;
- you cannot safely distinguish official docs from third-party references;
- report paths cannot be written.

# REPORT TARGETS

Write final report to all three paths:

1. TwinCore/rate-api-carrier-docs-research-v0.1/report.md
2. TwinCore/_BRIDGE/LATEST_REPORT.md
3. TwinCore/latest-report.md

After report: STOP. Do not self-accept. GPT will audit.
