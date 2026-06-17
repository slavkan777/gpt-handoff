---
title: "TwinCore Phase 0.3 — Draft Generator Algorithm Architecture Research"
type: knowledge
status: delivered
tags: [twincore, phase-0-3, arch-research, connector-generator, universal]
created: 2026-06-17
updated: 2026-06-17
---

# TwinCore Phase 0.3 — Draft Generator Algorithm Architecture Research Report

**Scope:** Universal Integration Connector Generator — pre-G2 architecture research.
**Status:** DELIVERED — READY_FOR_GPT_AUDIT
**Produced by:** bounded-executor (producer) in the Permanent Agent Army conveyor.

---

## 1 Request Metadata

| Field | Value |
|---|---|
| REQUEST_ID | REQ-2026-06-17-twincore-phase-0-3-generator-architecture-research |
| PROJECT | TwinCore |
| GATE | Phase 0.3 / Draft Generator Algorithm Research |
| STATUS | delivered — READY_FOR_GPT_AUDIT |
| ACTIVE_REQUEST_PATH | TwinCore/phase-0.3-universal-connector-generator-architecture-research/ACTIVE_REQUEST.md |
| TARGET_REPORT_PATH | TwinCore/phase-0.3-universal-connector-generator-architecture-research/report.md |
| Blueprint baseline | v0.2 (G1 accepted, G2 next) |
| Owner-proxy | Provisional owner-approved baseline by Slava; reversible when Igor returns |

---

## 2 Agent Army Route Used

**Run ID:** 2026-06-17-twincore-phase03-arch-army

Conveyor order:
1. `project-context-resolver` — loaded AIKB, confirmed G1 accepted, G2 next, canonical model names, gate naming.
2. `commander` — classified HIGH (not CRITICAL): multi-module architecture research, no irreversible action, no real credentials.
3. `aikb-bootstrap-agent` — produced `03-aikb-bootstrap.md`; confirmed consistency requirements A-H.
4. Parallel research workers — produced R1 (documents), R2 (OCR), R3 (OpenAPI/Postman), R4 (SOAP/WSDL/XML), R5 (EDI/legacy), R6 (schema inference).
5. `bounded-executor [producer]` — THIS REPORT. Architecture synthesis from R1-R6 plus inlined G0.1/G0.2 facts.
6. `review-agent` — next stop.
7. `evidence-auditor` — verifies citations, unverified flags, no fabrication.
8. `agent-output-validator` — confirms DONE STATE / verdict fields.
9. `permission-safety-auditor` — confirms no secrets, no forbidden paths, no "Igor approved".
10. `manual-verification-coordinator` — final: READY_FOR_MANUAL_ACCEPTANCE (Slava/GPT).

---

## 3 Executive Summary

Phase 0.1 chose NSwag as the first C# codegen baseline (Kiota deferred behind a client abstraction). Phase 0.2 proved NSwag on a real carrier OpenAPI spec (UPS Rating): YAML-to-JSON pre-convert required (PyYAML literal-block-scalar parse error), produced ~5 271 lines / 147 types, 0 warnings / 0 errors on .NET 9, five recurring mapping-ambiguity categories surfaced.

Phase 0.3 frames what comes before codegen: intake any format a carrier supplies, detect it, extract a machine-readable model, normalize it, score confidence, surface ambiguities for human review, and plan the connector package before a single line of generated C# is produced.

Grand vision: a Universal Integration Connector Generator that ingests any carrier-API artifact (OpenAPI YAML/JSON, Swagger 2, Postman collection, WSDL/SOAP, standalone XSDs, JSON/XML examples, PDFs, HAR captures, curl snippets, EDI specs) and produces a `NormalizedIntegrationContract` covering REST, SOAP, EDI, SFTP-batch, and doc-only scenarios. The canonical domain model (`RateQuoteRequest`, `RateQuoteResult`, `CanonicalAddress`, `CanonicalPackage`, `MoneyValue`, etc.) is the fixed target; the extraction machinery is the variable.

**MVP slice (G2):** OpenAPI YAML/JSON + JSON/XML examples + manual hints. All other format modules are extension points behind the same `ExtractionBackendRegistry` abstraction. AI is design-time only (runtime LLM = ZERO). EF deferred (in-memory through G2; EF at G3+).

Key findings: the safest in-process .NET pipeline uses `Microsoft.OpenApi` (parse), `NSwag` (codegen baseline), `XmlSchemaClassGenerator` (XSD-to-types), and `dotnet-svcutil` (WSDL-to-client). Heavy runtimes (JVM, Python, Node) belong out-of-process as optional sidecars. XXE on untrusted WSDL/XML is the single highest-severity security risk and must be hardened from day one.

---

## 4 Recommended Architecture Modules

Twelve modules. Original 12-module hypothesis verified and refined.

| # | Module | Purpose | Primary Inputs | Primary Outputs | G1/G2 slice |
|---|---|---|---|---|---|
| M1 | Source Intake | Accept any carrier artifact; normalize transport (file, URL, HTTP) | File paths, URLs, raw bytes | Byte stream + detected MIME hint + metadata | G2: file + URL |
| M2 | Source Detector | Identify format family (OpenAPI, WSDL, JSON examples, PDF, EDI...) | Byte stream + MIME hint | FormatTag + confidence score | G2: OpenAPI/JSON/XML |
| M3 | ExtractionBackendRegistry | Registry of primary/fallback/forensic extraction backends per FormatTag | FormatTag | Ordered IExtractionBackend[] chain | G2: OpenAPI entry wired |
| M4 | Extraction Orchestrator | Drive the backend chain; record attempts + evidence; escalate on failure | Backend chain + source bytes | RawExtractionResult per backend | G2: single-backend happy path |
| M5 | Raw Material Normalizer | Flatten heterogeneous extraction results into uniform field list | RawExtractionResult (any format) | NormalizedFieldSet + AmbiguityLog entries | G2: from OpenAPI + examples |
| M6 | Format Intelligence Library | Structured rules: carrier patterns, logistics vocabulary, ambiguity rules | NormalizedFieldSet | SemanticHint[] + ForbiddenAutonomyFlag[] | G2: draft logistics vocabulary |
| M7 | Semantic Model Discovery | AI-assisted field-to-canonical mapping (draft only, never Approved) | NormalizedFieldSet + SemanticHint[] | DraftNormalizedIntegrationContract | G2: design-time, human-reviewed |
| M8 | NormalizedIntegrationContract | Universal superset schema covering all integration styles | Approved mapping | Versioned NormalizedIntegrationContract JSON | G2: shape defined; REST slice |
| M9 | Profile Fit Layer | Score contract against canonical model; flag gaps/overrides | NormalizedIntegrationContract + ICanonicalModelCatalog | FitReport (coverage %, gap list) | G2: coverage gate |
| M10 | Connector Package Planner | Decide which code artifacts to generate (client, DTOs, mapping, tests) | FitReport + approved contract | ConnectorPlan (file list + template bindings) | G3: full; G2: plan only |
| M11 | Validation/Governance Layer | Pre-generation checks: Spectral lint, XXE-safe XML parse, license scan | Contract + raw artifacts | ValidationReport (pass/warn/block) | G2: Spectral + XXE check |
| M12 | Evidence/Ambiguity/Review Gate | Require human sign-off on DraftNIC before generation; enforce coverage % | DraftNIC + AmbiguityLog + FitReport | ApprovedNIC (only path to M10) | G2: mandatory gate |

**Module-to-gate mapping:**
- G2 (Mapping Workbench + Validation): M1-M9 + M11-M12 (plan only for M10; codegen lives in G3).
- G3 (Generator + Sandbox + Quote): M10 code generation driven by ConnectorPlan; sandbox execution; QuoteOption / RateQuoteResult proof.
- G4 (2nd provider proof): all modules reused for MockCarrierB; ExtractionBackendRegistry proves extensibility.

---

## 5 Free/Open-Source Tool Catalog by Category

Items marked **[unverified]** could not be confirmed to a primary source in this research run.

### 5a OpenAPI/REST Parsing and Codegen

| Tool | Purpose | Runtime/deps | License | Maintenance | .NET fit | Role | Source URL |
|---|---|---|---|---|---|---|---|
| Microsoft.OpenApi (OpenAPI.NET) | Parse/write OpenAPI as .NET object model | Native .NET, no Node/Java | MIT | v3.7.0 Jun 2026, very active | In-proc native | parse | https://github.com/microsoft/OpenAPI.NET |
| NSwag | OpenAPI to C#/TS codegen; gen spec from controllers | Native .NET, NuGet+CLI | MIT | v14.7.x 2025-2026, active | In-proc native + CLI | codegen baseline | https://github.com/RicoSuter/NSwag |
| Kiota | Generate strongly-typed HTTP clients from OpenAPI | .NET CLI (dotnet tool) | MIT | v1.32.x Jun 2026, very active (Microsoft) | Native .NET CLI | codegen (deferred) | https://github.com/microsoft/kiota |
| OpenAPI Generator | Codegen clients/stubs, many languages | Java 11+, Docker | Apache-2.0 | v7.23.0 Apr 2026, very active | CLI/Docker out-of-proc | codegen multi-lang | https://github.com/OpenAPITools/openapi-generator |
| Spectral | JSON/YAML linter + style-guide for OpenAPI/AsyncAPI | Node.js | Apache-2.0 | v6.16.x 2025-2026, active | Node CLI in CI | lint/validate | https://github.com/stoplightio/spectral |
| Prism | OpenAPI/Postman mock + validation HTTP server | Node.js >=16 | Apache-2.0 | v5.8.x 2026, active | Node/Docker sidecar | mock | https://github.com/stoplightio/prism |
| Schemathesis | Property-based/fuzz contract testing from OpenAPI | Python | MIT | v3.38.x 2025-2026, active | Python CLI/Docker | contract-test | https://github.com/schemathesis/schemathesis |
| postman-to-openapi | Postman Collection v2 to OpenAPI 3.0 | Node.js | MIT | v3.0.1 ~2022 **dormant** | Node CLI | postman-to-spec | https://github.com/joolfe/postman-to-openapi |
| Newman | CLI runner for Postman Collections | Node.js >=16 | Apache-2.0 | v6.2.x late 2025, active | Node/Docker CI | contract-test | https://github.com/postmanlabs/newman |

### 5b SOAP/WSDL/XML Schema

| Tool | Purpose | Runtime/deps | License | Maintenance | .NET fit | Role | Source URL |
|---|---|---|---|---|---|---|---|
| dotnet-svcutil | WSDL to WCF C# client proxy, headless CLI | Native .NET global tool | MIT | v8.0.0 Mar 2025, active | Native .NET CLI | WSDL-to-client | https://www.nuget.org/packages/dotnet-svcutil |
| XmlSchemaClassGenerator | XSD to C# types (XmlSerializer-compatible) | Native .NET lib+CLI | Apache-2.0 | v3.0.1270 Apr 2026, active | In-proc NuGet | XSD-to-types | https://github.com/mganss/XmlSchemaClassGenerator |
| Trang (jing-trang) | Infer XSD from XML samples; convert DTD/RNG/XSD | Java JRE | BSD **[unverified exact SPDX]** | V20241231 Dec 2024, active | Java CLI out-of-proc | XML-to-XSD inference | https://github.com/relaxng/jing-trang |
| xsd.exe | XSD to C# types (Windows SDK legacy) | .NET Framework, Windows-only | Microsoft proprietary **[unverified]** | Legacy/frozen | Windows-only legacy | XSD-to-types (fallback) | https://learn.microsoft.com/dotnet/standard/serialization/xml-schema-definition-tool-xsd-exe |

### 5c Document Extraction (PDF/Office/HTML)

| Tool | Purpose | Runtime/deps | License | Maintenance | .NET fit | Role | Source URL |
|---|---|---|---|---|---|---|---|
| Apache Tika | Detect + extract text/metadata from 1000+ types | Java JDK 17, tika-server REST | Apache-2.0 | 3.x active, ASF | HTTP sidecar | broad intake | https://github.com/apache/tika |
| pdfplumber | Per-character/table PDF extraction | Python pure, pdfminer.six | MIT | v0.11.10 Jun 2026, active | Python subprocess | PDF tables | https://github.com/jsvine/pdfplumber |
| Camelot (camelot-dev) | PDF table extraction (lattice+stream+OCR) | Python, Ghostscript | MIT | v2.0.0 Jun 2026, active | Python subprocess | PDF tables | https://github.com/camelot-dev/camelot |
| MarkItDown (Microsoft) | Office/PDF/HTML to Markdown for LLMs | Python >=3.10, pre-1.0 | MIT | v0.1.6 May 2026, active | CLI subprocess | Markdown normalization | https://github.com/microsoft/markitdown |
| Docling (IBM/LF AI) | Rich layout-aware doc parsing, optional VLM/GPU | Python, heavy ML | MIT | v2.103.0 Jun 2026, very active | HTTP microservice | heavy extraction fallback | https://github.com/docling-project/docling |
| Unstructured | Complex docs to structured data for LLMs | Python, many OS deps | Apache-2.0 **[per-model licenses unverified]** | v0.23.1 Jun 2026, active | CLI/Docker/SaaS | chunking/partitioning | https://github.com/Unstructured-IO/unstructured |
| PyMuPDF (fitz) | High-performance PDF extract/convert | Python + MuPDF C | **AGPL-3.0** / commercial | v1.27.2.3 Apr 2026, active | Python subprocess | **AVOID (AGPL trap)** | https://github.com/pymupdf/PyMuPDF |
| Tabula (tabula-java) | PDF tables via JVM | Java JVM | MIT | v1.0.5 2021, **effectively abandoned** | JVM CLI | **AVOID (abandoned)** | https://github.com/tabulapdf/tabula-java |

### 5d OCR (Scanned Carrier Docs)

| Tool | Purpose | Runtime/deps | License | Maintenance | .NET fit | Role | Source URL |
|---|---|---|---|---|---|---|---|
| Tesseract (engine) | LSTM OCR, 100+ languages, CPU-only | C/C++ native, Leptonica | Apache-2.0 | v5.5.1 May 2025, active | P/Invoke via wrapper | carrier-extraction | https://github.com/tesseract-ocr/tesseract |
| TesseractOCR (Sicos1977) | C# P/Invoke wrapper, tracks Tesseract 5.5.x | .NET, P/Invoke | Apache-2.0 **[unverified exact SPDX]** | 5.5.2 2025, active | Native .NET best-maintained | in-proc OCR | https://www.nuget.org/packages/TesseractOCR |
| charlesw/tesseract | Older C# P/Invoke wrapper | .NET, P/Invoke | Apache-2.0 | 5.2.0 2022, semi-active | Native .NET fallback | in-proc OCR (fallback) | https://github.com/charlesw/tesseract |
| RapidOCR (ONNX) | PaddleOCR models as ONNX, CPU-capable | ONNX Runtime (.NET binding exists) | Apache-2.0 | v3.5.0 Jan 2026, active | ONNX Runtime native .NET | in-proc OCR alternative | https://github.com/RapidAI/RapidOCR |
| OCRmyPDF | Scanned PDF to searchable PDF (adds text layer) | Python, Ghostscript, Tesseract | MPL-2.0 | v17.6.x active | CLI subprocess | PDF-specific OCR | https://github.com/ocrmypdf/OCRmyPDF |
| Surya OCR | Transformer OCR+layout, 90+ languages | Python, vLLM/GPU, OpenRAIL-M weights | Code Apache-2.0 / **weights OpenRAIL-M ($5M commercial gate)** | v0.20.x 2026, active | HTTP microservice | **license gate for commercial use** | https://github.com/datalab-to/surya |

### 5e Schema Inference from Examples

| Tool | Purpose | Runtime/deps | License | Maintenance | .NET fit | Role | Source URL |
|---|---|---|---|---|---|---|---|
| quicktype | JSON samples to C# types + JSON Schema | Node.js CLI | Apache-2.0 | active **[exact version unverified]** | Node CLI subprocess | JSON-to-C# (only direct path) | https://github.com/glideapps/quicktype |
| GenSON | Infer + merge JSON Schema from many samples | Python pure | MIT | 1.3.0 **[date unverified]**, low cadence | Python subprocess | JSON-to-JSON Schema (best merge) | https://github.com/wolverdude/GenSON |
| schema-infer (@jsonhero) | Infer JSON Schema 2020-12 with format detection | Node.js | MIT | 0.1.6 Jul 2023, **near-dormant** | Node subprocess | JSON-to-JSON Schema (nullable/format hints) | https://github.com/triggerdotdev/schema-infer |
| genson-js | JS port of GenSON, merge/compare | Node.js | Apache-2.0 | **Archived Oct 2024** | Node subprocess | **AVOID (archived)** | https://github.com/aspecto-io/genson-js |

### 5f EDI/Legacy

| Tool | Purpose | Runtime/deps | License | Maintenance | .NET fit | Role | Source URL |
|---|---|---|---|---|---|---|---|
| EDI.Net (indice-co) | EDI serialize/deserialize via POCO attrs; X12+EDIFACT+TRADACOMS | Native .NET 8, no heavy deps | MIT | v2.0.0-beta04 Oct 2025, active beta | Native .NET best fit | EDI parse (future) | https://github.com/indice-co/EDI.Net |
| EdiEngine | X12 read/write/validate; EDI to JSON/XML; 997 acks | .NET Standard 2.0, Newtonsoft.Json | MIT | lightly maintained | Native .NET | X12-only (future fallback) | https://github.com/olmelabs/EdiEngine |
| X12Parser / OopFactory.X12 | X12 to XML, HIPAA support | .NET Framework 4.0 legacy, SQL/XSLT | **CDDL (copyleft)** | appears stale **[unverified]** | **AVOID** (legacy + CDDL) | — | https://github.com/bvanfleet/X12.NET |

---

## 6 Default/Fallback Order per Format

| Format | Primary | Fallback | Forensic |
|---|---|---|---|
| OpenAPI 3.x YAML/JSON | Microsoft.OpenApi in-proc (v3 for 3.2; v2 for 3.1/3.0) | Spectral parse for error detail; @readme/openapi-parser Node for edge $ref | Manual hint injection + AmbiguityLog |
| Swagger 2.0 JSON/YAML | Microsoft.OpenApi (reads 2.0 + converts internally) | NSwag SwaggerDocument reader | Manual hint |
| Postman Collection v2/v2.1 | Prism (native Postman support for mock/validate) | postman-to-openapi Node CLI (dormant; 3.0 output only) | Postman Collection SDK custom mapping |
| WSDL/SOAP | dotnet-svcutil CLI (headless, MIT, .NET) | WCF Web Service Reference (VS GUI; manual fallback) | Manual WSDL parse via hardened XmlReader |
| XSD (standalone) | XmlSchemaClassGenerator NuGet (Apache-2.0, .NET 8) | xsd.exe (Windows legacy) | Manual XSD review |
| JSON examples/samples | quicktype CLI (Node) to C# types in one hop | GenSON (Python) to JSON Schema then NJsonSchema | schema-infer (dormant; last resort for nullable/format hints) |
| XML examples | Trang CLI (Java; xml to xsd) to XmlSchemaClassGenerator | Manual XSD authoring | — |
| PDF (text-based) | pdfplumber (Python, MIT) text/table extraction | Camelot v2 (Python, MIT; dedicated tables) | Apache Tika REST sidecar |
| PDF (scanned) | Tesseract via TesseractOCR .NET wrapper (in-proc) | RapidOCR ONNX via ONNX Runtime (in-proc) | OCRmyPDF CLI (searchable-PDF output) |
| DOCX/XLSX/PPT | Apache Tika REST sidecar (broadest) | MarkItDown CLI (MIT, LLM-Markdown) | Docling sidecar (layout VLM; GPU optional) |
| HTML (carrier portal pages) | Apache Tika REST (text extraction) | MarkItDown CLI | Manual hint + AmbiguityLog |
| CSV | .NET native CsvHelper or built-in System.IO | Tika for embedded CSV | Manual schema annotation |
| EDI X12/EDIFACT | **DEFERRED** — EDI.Net (MIT, native .NET) designated for when this lands | EdiEngine (X12-only fallback) | — |
| HAR captures | Custom JSON parser (HAR is JSON; extract request/response pairs) | — | Manual endpoint review |
| curl examples | Regex/parse curl invocation (URL+method+headers+body) | Manual endpoint entry | — |

**YAML-to-JSON pre-convert note (from Phase 0.2 proof):** OpenAPI YAML with literal-block scalars may fail direct parse. Pre-convert via PyYAML CLI before handing to Microsoft.OpenApi. This is a named pipeline step with its own EvidenceLedger entry.

---

## 7 ExtractionBackendRegistry Design

The ExtractionBackendRegistry maps a FormatTag to an ordered list of IExtractionBackend implementations. It is extensible by DI registration; the orchestrator discovers backends via DI, not a hard-coded switch.

**Interface shape (design-time concept, not an implementation):**

```
IExtractionBackend {
  string BackendId
  FormatTag[] SupportedFormats
  BackendTier Tier           // Primary | Fallback | Forensic
  double BaseConfidence      // 0.0-1.0; adjusted by result quality signals
  ExtractionResult Extract(SourceArtifact source)
}

ExtractionResult {
  bool Success
  NormalizedFieldSet Fields
  AmbiguityLog[] Ambiguities
  EvidenceLedger Evidence
  double ActualConfidence    // may differ from BaseConfidence
  string[] Warnings
}
```

**Conflict detection:** if two backends produce overlapping field names with different types/cardinalities, the orchestrator inserts a TypeConflict AmbiguityLog entry rather than silently picking one. Human resolves at M12.

**Tier semantics:**
- Primary: runs first; result accepted if ActualConfidence >= threshold (default 0.7).
- Fallback: runs if Primary fails or confidence < threshold; may run in parallel.
- Forensic: runs only on explicit escalation (all tiers failed, or ForensicRequired flag on artifact); outputs always Draft confidence.

**G2 wiring:** one Primary backend registered for FormatTag.OpenApiYamlJson; one for FormatTag.JsonExamples; one for FormatTag.XmlExamples. Fallback/forensic entries are stubs (log + return Escalate).

---

## 8 Forensic Mode Design

Forensic mode is a last-resort extraction strategy triggered when all Primary+Fallback backends fail or produce ActualConfidence < forensicThreshold (default 0.3).

**Trigger conditions:**
1. All backends in the chain exhausted without Success = true.
2. ForensicRequired flag explicitly set on the artifact.
3. Manual override via the Review Gate interface.

**Forensic outputs are always DraftStatus = Forensic** — they can never be auto-promoted to Approved. They require explicit human review at M12 with annotator sign-off.

**Forensic strategies (in order):**
1. Raw text dump: extract any readable text; surface as unstructured AmbiguityLog entries with Source = ForensicRawText.
2. Partial match: apply FIL heuristics to raw text (carrier name patterns, logistics vocabulary, endpoint URL shapes) to produce low-confidence SemanticHint[].
3. Human hint injection: structured workbench UI (G2c) where the operator annotates field names, types, endpoint paths. Injected hints enter as Source = ManualHint with Confidence = 1.0 (operator is the authority).
4. AI-assisted draft (design-time only, runtime LLM = ZERO): if design-time AI tooling is invoked, it produces a DraftNormalizedIntegrationContract which enters M12 as Forensic tier.

**Security note:** forensic raw-text extraction parses attacker-controllable content. Apply the same sandboxing rules as primary extraction (out-of-process where Python/Java tools are used; XmlReaderSettings { DtdProcessing = Prohibit } for any XML path).

---

## 9 Format Intelligence Library Design

The Format Intelligence Library (FIL) is a structured, version-controlled set of rules — not ML weights, not runtime LLM calls. Rules are data (JSON/YAML files checked into the repo), loaded at design-time, auditable by humans, versioned with the generator.

### FIL Rule Categories

| Category | What it encodes | Example |
|---|---|---|
| Carrier patterns | Known carrier endpoint URL shapes, API key header names, pagination patterns | `{"carrier":"UPS","apiKeyHeader":"AccessLicenseNumber","baseUrlPattern":"https://*/api/*"}` |
| Logistics vocabulary | Field name synonyms mapping raw names to canonical field semantics | `{"raw":["weight","wt","pkg_weight"],"canonical":"CanonicalPackage.Weight"}` |
| Domain model patterns | Structural patterns that signal a canonical type (address block detection) | `{"pattern":"street1|address_line_1|addr1","signals":"CanonicalAddress"}` |
| Ambiguity rules | Conditions under which extraction must produce an AmbiguityLog entry | `{"condition":"fieldOptionalityUnknown","action":"logAmbiguity","block":false}` |
| Forbidden-autonomy rules | Conditions where the generator must stop and require human review | `{"condition":"currencyFieldWithNoISOCode","action":"BLOCK","reason":"MoneyValue requires ISO 4217 currency"}` |

**Design-now decision (YES):** author the FIL schema definition and at least the logistics vocabulary + forbidden-autonomy rules during Phase 0.3/G2. Vocabulary seeds from Phase 0.2 five mapping-ambiguity categories.

### Draft AmbiguityLog Shape

```json
{
  "ambiguityId": "string (uuid)",
  "artifactId": "string",
  "extractorId": "string (BackendId)",
  "fieldPath": "string (JSONPath or XPath in raw extraction)",
  "ambiguityType": "Optionality | Nullability | CandidateEnum | NumericPrecision | TypeConflict | MissingCurrency | ForensicRawText | ManualHint | Other",
  "description": "string",
  "rawEvidence": "string (raw text/value that triggered this)",
  "candidateMappings": ["string"],
  "resolvedBy": "string | null",
  "resolvedAt": "datetime | null",
  "resolution": "string | null",
  "status": "Open | ResolvedAuto | ResolvedManual | Blocked"
}
```

Five recurring ambiguity types from Phase 0.2 (seed FIL ambiguity rules):
1. Optionality: field absent in some examples; unclear if optional or data-gap.
2. Nullability: field present but null in some samples; type unclear.
3. CandidateEnum: string field with small value set; may be an enum or free text.
4. NumericPrecision: integer vs decimal ambiguity (weight in grams vs kg).
5. TypeConflict: two backends disagree on field type.

---

## 10 Semantic Model Discovery Design

**Core principle: AI is the discovery layer, not the oracle. AI outputs are always Draft; humans are the authority.**

Semantic Model Discovery (M7) takes the normalized field set and semantic hints from the FIL and proposes mappings from raw carrier fields to the canonical model.

**Design-time AI only (runtime LLM = ZERO):** AI is invoked as part of the design/build process, never at connector runtime. The generated connector has no LLM dependency.

**Output contract:**
- Produces a DraftNormalizedIntegrationContract (status = Draft).
- Every mapping carries an InferenceSource tag: FIL_Rule | AI_Inference | ManualAnnotation.
- AI_Inference mappings always enter the AmbiguityLog with status = Open until human-reviewed.
- No AI_Inference mapping may bypass M12 (the Review Gate).

**AI invocation boundary (design-time concept):**

```
Input:  NormalizedFieldSet + SemanticHint[] + ICanonicalModelCatalog (field names + descriptions)
Output: DraftMapping[] where each = { rawField, canonicalField, confidence, inferenceSource, ambiguityRef? }
```

**Canonical model catalog grounding:** ICanonicalModelCatalog contains the locked canonical names (ProviderSchemaDocument, SchemaOperation, SchemaField, QuoteOption, RateQuoteRequest, RateQuoteResult, CanonicalAddress, CanonicalPackage, MoneyValue, RateQuoteError) with field descriptions. This prevents hallucination of new canonical names.

**Hallucination guard:** any canonical field name proposed by AI that is NOT in ICanonicalModelCatalog must be flagged as UnknownCanonicalField and blocked at M12. It cannot auto-create new canonical model fields.

---

## 11 NormalizedIntegrationContract Proposal

### Why Broader than NormalizedApiContract

NormalizedApiContract implies REST/HTTP API. The universal generator must cover REST, SOAP/WSDL, EDI X12/EDIFACT, SFTP-batch, file/example-only, and doc-only scenarios.

NormalizedIntegrationContract (NIC) is the universal superset. It is not a replacement of the canonical domain model — it is the structural envelope that carries the mapping from a specific carrier's protocol to the canonical model. The canonical model defines WHAT data is exchanged. The NIC defines HOW a specific carrier's API/protocol is mapped to that WHAT. A NIC is per-carrier; the canonical model is shared across all carriers.

NormalizedIntegrationContract is the universal SUPERSET of NormalizedApiContract. An API-only contract is a valid NIC with IntegrationStyle = REST.

### Draft NIC Shape (Top-Level)

```json
{
  "nicVersion": "1.0",
  "carrierId": "string",
  "carrierDisplayName": "string",
  "integrationStyle": "REST | SOAP | EDI_X12 | EDI_EDIFACT | SFTP_Batch | DocOnly",
  "specSource": {
    "sourceType": "OpenApiYamlJson | Swagger2 | Postman | WSDL | XSD | JsonExamples | XmlExamples | PDF | HAR | Curl | Manual",
    "sourceUri": "string | null",
    "extractedAt": "datetime",
    "extractorId": "string"
  },
  "operations": [
    {
      "operationId": "string",
      "httpMethod": "string | null",
      "path": "string | null",
      "soapAction": "string | null",
      "ediTransactionSet": "string | null",
      "canonicalOperation": "RateQuote | TrackShipment | CreateLabel | Other",
      "requestMapping": "FieldMapping[]",
      "responseMapping": "FieldMapping[]",
      "ambiguityRefs": ["ambiguityId"]
    }
  ],
  "authScheme": { "type": "ApiKey | OAuth2 | BasicAuth | WS-Security | None", "headerName": "string | null" },
  "status": "Draft | Approved",
  "approvedBy": "string | null",
  "approvedAt": "datetime | null",
  "evidenceLedgerId": "string"
}
```

**G2 scope:** integrationStyle = REST only; status = Draft until M12 gate passes; SOAP | EDI_* styles defined in schema but not wired to extraction backends.

---

## 12 Fact vs Inference Model

Every piece of information in the NIC and AmbiguityLog carries a provenance tag.

| Source type | Tag | Trust level | Requires human review? |
|---|---|---|---|
| Formal spec (OpenAPI/WSDL/XSD) directly parsed | FormalSpec | High | No (structural); Yes (semantic mapping) |
| FIL rule match (carrier pattern / vocabulary) | FIL_Rule | Medium-high | No if confidence >= 0.7 |
| Schema inference (quicktype/GenSON/Trang output) | SchemaInferred | Medium | Yes (optionality/nullable assumptions) |
| AI-assisted mapping (design-time) | AI_Inference | Medium-low | Yes, always |
| Manual annotation (operator in workbench) | ManualAnnotation | High (operator is authority) | No (operator IS the review) |
| Forensic raw text | ForensicRawText | Low | Yes, always |
| OCR output | OCR | Low-medium (depends on scan quality) | Yes |

**Composite trust rule:** a field mapping's overall trust = min(trust[all provenance tags on this mapping]). A ForensicRawText-tagged field drags the entire mapping to Low.

---

## 13 Confidence/Evidence Model + Draft EvidenceLedger Shape

### Confidence Scoring

Confidence is a double in [0.0, 1.0].

| Condition | Delta |
|---|---|
| Formal spec parsed without warnings | base 0.9 |
| Formal spec parsed with warnings | base 0.7 |
| Schema inferred from >=3 examples | base 0.65 |
| Schema inferred from 1 example | base 0.4 |
| AI inference + FIL rule corroboration | +0.15 |
| AI inference only | base 0.45 |
| Manual annotation | 1.0 (operator authority) |
| OCR path involved | -0.15 |
| Forensic raw text | max 0.3 |
| Ambiguity of type TypeConflict | -0.2 |

**Coverage gate (M12 trigger):** overall NIC confidence = weighted mean across all operation mappings. Proposed defaults: 0.65 to proceed to Draft; 0.80 to auto-recommend Approved (human still signs off).

### Draft EvidenceLedger Shape

```json
{
  "ledgerId": "string (uuid)",
  "artifactId": "string",
  "entries": [
    {
      "entryId": "string",
      "step": "SourceDetect | ExtractionPrimary | ExtractionFallback | SchemaInference | AIMapping | FILRuleApplied | ManualAnnotation | ValidationSpectral | XXECheck | ReviewGate",
      "backendId": "string | null",
      "timestamp": "datetime",
      "outcome": "Success | Fallback | Forensic | Failed | Blocked",
      "confidenceBefore": "double | null",
      "confidenceAfter": "double",
      "notes": "string",
      "ambiguityRefs": ["ambiguityId"]
    }
  ],
  "finalConfidence": "double",
  "finalStatus": "Draft | Approved | Blocked"
}
```

---

## 14 Review Gate Rules

The Review Gate (M12) is the only path from DraftNormalizedIntegrationContract to ApprovedNIC. Generation (M10) is gated behind an ApprovedNIC.

### Blocking Conditions

| Condition | Block level |
|---|---|
| Any AmbiguityLog entry with status = Blocked | Hard block — generation forbidden |
| Any ForbiddenAutonomyFlag from FIL unresolved | Hard block |
| Any UnknownCanonicalField proposed by AI | Hard block |
| Overall NIC confidence < 0.40 | Hard block |
| Overall NIC confidence in [0.40, 0.65) | Soft block — requires explicit operator override with written justification |
| Coverage gate: canonical operations mapped < required minimum (default 1 for MVP) | Hard block |
| Any TypeConflict ambiguity unresolved | Soft block |
| ForensicRawText entries without operator annotation | Hard block |

### Gate Pass Conditions

1. All hard blocks cleared.
2. All soft blocks cleared or operator has explicitly overridden each with written justification stored in AmbiguityLog.resolution.
3. MappingProfile approved: operator has reviewed and signed off on the field mapping table.
4. NIC.status set to Approved by a named approver (not the system; not AI).

### Human MappingProfile Approval

The MappingProfile is a human-readable table (generated by M9 Profile Fit Layer) showing: each canonical field and what it maps to in this carrier's contract, confidence per mapping, open ambiguities per mapping, and the gap list (canonical fields with no carrier mapping). The operator must review and annotate each gap before the gate passes.

---

## 15 Validation/Governance Tools Before Generation

These run in M11 (Validation/Governance Layer) before the Review Gate and before any codegen.

| Tool | Purpose | Runtime | When to run |
|---|---|---|---|
| Spectral (Apache-2.0) | OpenAPI style/governance lint; custom rulesets | Node CLI | Always for OpenAPI/Swagger input |
| Microsoft.OpenApi built-in validation | Structural + schema validation in-proc | Native .NET | Always for OpenAPI/Swagger input |
| XmlReaderSettings { DtdProcessing = Prohibit, XmlResolver = null } | XXE/DTD protection on all XML paths (WSDL, XSD, EDI-to-XML) | Built into .NET | Always; mandatory for untrusted input |
| Schemathesis (MIT) | Property/fuzz contract testing; verify generated contract against live API | Python CLI | Optional (G3+; when live endpoint available) |
| Prism (Apache-2.0) | Mock server + request validation against OpenAPI | Node Docker | Optional; validate generated client behavior |
| License scan | Confirm all selected backends are permissive for commercial use | Manual checklist | Before any new backend is registered |
| CA3075 analyzer (Microsoft) | Static analysis: detect insecure DTD processing in C# | .NET Roslyn analyzer | In CI build |

**XXE hardening is mandatory and must be documented as a project-wide non-negotiable.** Reference: OWASP XXE Prevention Cheat Sheet; Microsoft CA3075.

---

## 16 MVP Scope

MVP = Phase 0.3 spec + G2 implementation.

| In scope (G2 MVP) | Out of scope (future extension) |
|---|---|
| OpenAPI 3.x YAML/JSON intake | WSDL/SOAP extraction backend |
| Swagger 2.0 JSON/YAML intake | EDI X12/EDIFACT intake |
| JSON example intake + quicktype inferred types | SFTP/batch transport adapter |
| XML example intake + Trang to XSD + XmlSchemaClassGenerator | PDF/DOC/OCR extraction |
| Manual hint injection via workbench UI (G2c) | HAR/curl automated parsing |
| YAML-to-JSON pre-convert (PyYAML; from Phase 0.2) | Postman-to-OpenAPI conversion (dormant tool) |
| Format Intelligence Library schema + logistics vocabulary draft | Full multi-carrier factory (G4 proves 2nd provider) |
| AmbiguityLog shape (defined here; wired in G2) | EF persistence (G3+) |
| EvidenceLedger shape (defined here; in-memory in G2) | Runtime LLM (forever ZERO) |
| NormalizedIntegrationContract shape (REST slice) | SOAP/EDI NIC integration styles |
| M12 Review Gate (DraftNIC to ApprovedNIC) | Connector code generation (M10, G3) |
| Spectral lint + XXE hardening | Schemathesis fuzz testing (G3+) |
| In-memory persistence through G2 | EF Core (G3+) |

**NSwag remains the first C# codegen baseline (Decision 2, KEEP). Kiota remains deferred behind the client abstraction (Decision 3, DEFER).**

---

## 17 Later Extension Points

All extensions are registered as additional IExtractionBackend implementations in the ExtractionBackendRegistry. No architectural change is needed to the orchestrator or the NIC schema to add them.

| Extension | Target gate | Pre-selected technology | Research status |
|---|---|---|---|
| SOAP/WSDL extraction backend | G3 | dotnet-svcutil (MIT, .NET 8) + XmlSchemaClassGenerator (Apache-2.0) | Researched (R4) |
| EDI X12/EDIFACT intake | G4+ | EDI.Net (MIT, .NET 8, native, X12+EDIFACT+TRADACOMS) | Researched (R5) |
| SFTP transport adapter | G4+ | SSH.NET (.NET) — not in scope of this research | To be researched |
| PDF text-based extraction | G3+ | pdfplumber (MIT, Python subprocess) then Tika REST | Researched (R1) |
| PDF scanned/OCR | G3+ | TesseractOCR .NET wrapper (Apache-2.0, in-proc) | Researched (R2) |
| Office/HTML doc extraction | G3+ | Apache Tika REST sidecar (Apache-2.0) | Researched (R1) |
| HAR/curl automated parsing | G3+ | Custom JSON/regex parser (.NET) | No external tool needed |
| Postman-to-OpenAPI full NIC | G3+ | Postman Collection SDK + custom mapping | Monitor; postman-to-openapi dormant |
| AI-assisted mapping (design-time) | G2b/G3 | Design-time scripting; tool TBD | Open question (#1 in §19) |
| EF Core persistence | G3+ | EF Core .NET | AIKB constraint E |
| 2nd provider proof | G4 | All existing modules reused for MockCarrierB | AIKB consistency A |

---

## 18 Risks and Mitigations

| Risk | Severity | Mitigation |
|---|---|---|
| XXE on untrusted WSDL/XSD/XML | HIGH | XmlReaderSettings { DtdProcessing = Prohibit, XmlResolver = null } on every XML read path; CA3075 in CI; sandbox the WSDL fetch |
| PyMuPDF AGPL-3.0 license trap | HIGH | Do not use; pdfplumber (MIT) is the PDF table alternative |
| Surya model weights OpenRAIL-M commercial gate ($5M threshold) | HIGH | Do not use Surya in commercial product without verifying revenue; use TesseractOCR or RapidOCR/ONNX |
| AI hallucination of canonical field names | HIGH | ICanonicalModelCatalog grounding; UnknownCanonicalField hard block at M12; design-time AI only |
| Single-sample schema inference under-specification | HIGH | Log all optionality/nullability/enum assumptions as AmbiguityLog; require >=3 examples for any field marked required; Coverage gate enforces minimum |
| Java/Node/Python runtime deps bloat | MEDIUM | Primary path is .NET-native; heavy runtimes only for optional sidecars; document minimum runtime set; containerize sidecars |
| postman-to-openapi dormant (2022) | MEDIUM | Use Prism (native Postman support) for mock/validate; replace with Collection SDK custom mapping if needed |
| NSwag partial OpenAPI 3.1 support | MEDIUM | Pin to OpenAPI 3.0 for G2 MVP; test 3.1 before extending; document version contract |
| AGPL / CDDL license traps in EDI libs | MEDIUM | Avoid OopFactory.X12 (CDDL); use EDI.Net (MIT) when EDI lands |
| EDI spec licensing (X12/EDIFACT paywalled) | MEDIUM | Defer EDI to G4+; use carrier-supplied maps where available; do not redistribute paywalled spec data |
| Unstructured hosted API is commercial; per-model licenses unverified | MEDIUM | Use only the OSS Apache-2.0 core; verify per-model license before bundling |
| WS-Security / message-level SOAP on modern .NET | MEDIUM | dotnet-svcutil handles basic SOAP; WS-Security partial; carriers requiring WS-Trust may need CoreWCF; verify per-carrier |
| OCR hallucination on degraded scans | MEDIUM | Require confidence scores from OCR; checksum/validation rules on critical numeric fields; never trust raw OCR blind |
| Trang version unverified in R4 (reconciled by R6: V20241231) | LOW | Trang used only for XML-to-XSD inference (edge case); not on critical G2 path |
| Legal/business mapping mistakes | MEDIUM | MappingProfile human approval at M12 is mandatory; never auto-approve a carrier contract mapping for production use |
| Credentials/secrets in carrier WSDL or spec URLs | HIGH | Never log spec URLs with embedded credentials; SourceArtifact.sourceUri must strip auth tokens before persisting; enforce in EvidenceLedger write path |

---

## 19 Open Questions for Slava/Igor

1. **AI tool for design-time mapping (M7):** Which design-time AI tool/invocation pattern is approved for Semantic Model Discovery? Options: custom script calling an LLM API at generator build time; a local model; a prompt-assisted workbench where the developer reviews AI suggestions live. This is the only unresolved design-time AI integration point.

2. **Coverage gate threshold:** Is 0.65 confidence acceptable as the default gate for DraftNIC to proceed to human review? Or should it be higher (0.75)?

3. **Minimum example count for required-field inference:** Is 3 examples the right floor for promoting a field from optional-assumed to potentially-required? This seeds the AmbiguityLog rule.

4. **YAML-to-JSON pre-convert as a first-class pipeline step:** Should the PyYAML pre-convert (from Phase 0.2 proof) be a named M1 sub-step with its own EvidenceLedger entry, or treated as a transparent codec inside the OpenAPI backend?

5. **NIC versioning strategy:** When a carrier updates their API and a new NIC draft is generated, should the old ApprovedNIC be versioned/archived automatically or require explicit deprecation by a human?

6. **Postman Collection intake priority:** Is Postman Collection a G2 target or defer to G3? The primary conversion tool (postman-to-openapi) is dormant; Prism supports Postman natively for mock/validate but not for full NIC extraction.

7. **SFTP transport adapter target gate:** G4 or G5? EDI.Net is pre-selected for parsing; SFTP transport (SSH.NET or similar) is a separate concern needing a gate assignment for roadmap.

---

## 20 Source Links/Citations

Consolidated from R1-R6 (primary sources; all confirmed against primary source 2026-06-17 unless marked [unverified]):

**OpenAPI/REST:** https://github.com/microsoft/OpenAPI.NET | https://github.com/RicoSuter/NSwag | https://github.com/microsoft/kiota | https://github.com/OpenAPITools/openapi-generator | https://github.com/swagger-api/swagger-codegen | https://github.com/stoplightio/spectral | https://github.com/stoplightio/prism | https://github.com/schemathesis/schemathesis | https://github.com/postmanlabs/postman-collection | https://github.com/postmanlabs/newman | https://github.com/joolfe/postman-to-openapi | https://github.com/scalar/scalar

**SOAP/WSDL/XML:** https://www.nuget.org/packages/dotnet-svcutil | https://learn.microsoft.com/en-us/dotnet/core/additional-tools/dotnet-svcutil-guide | https://learn.microsoft.com/en-us/dotnet/core/additional-tools/wcf-web-service-reference-guide | https://github.com/mganss/XmlSchemaClassGenerator | https://github.com/sissaschool/xmlschema | https://github.com/relaxng/jing-trang | https://relaxng.org/jclark/trang-manual.html | https://learn.microsoft.com/en-us/dotnet/standard/serialization/xml-schema-definition-tool-xsd-exe | https://pypi.org/project/xmltoxsd/ | https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html | https://learn.microsoft.com/en-us/dotnet/fundamentals/code-analysis/quality-rules/ca3075

**Documents/OCR:** https://github.com/apache/tika | https://github.com/microsoft/markitdown | https://github.com/docling-project/docling | https://github.com/Unstructured-IO/unstructured | https://github.com/jsvine/pdfplumber | https://github.com/pymupdf/PyMuPDF | https://github.com/camelot-dev/camelot | https://github.com/tabulapdf/tabula-java | https://github.com/tesseract-ocr/tesseract | https://www.nuget.org/packages/TesseractOCR | https://github.com/charlesw/tesseract | https://github.com/ocrmypdf/OCRmyPDF | https://github.com/PaddlePaddle/PaddleOCR | https://github.com/RapidAI/RapidOCR | https://github.com/JaidedAI/EasyOCR | https://github.com/mindee/doctr | https://github.com/datalab-to/surya

**Schema inference:** https://github.com/glideapps/quicktype | https://github.com/wolverdude/GenSON | https://github.com/aspecto-io/genson-js | https://github.com/triggerdotdev/schema-infer

**EDI/Legacy:** https://github.com/indice-co/EDI.Net | https://github.com/olmelabs/EdiEngine | https://www.nuget.org/packages/xEdi.EdiEngine | https://github.com/bvanfleet/X12.NET | https://github.com/azoner/pyx12 | https://github.com/nerdocs/pydifact | https://github.com/smooks/smooks-edi-cartridge | https://github.com/michaelachrisco/Electronic-Interchange-Github-Resources

---

## 21 Missing Sources / Unverified Flags

| Item | What is unverified | Risk |
|---|---|---|
| TesseractOCR (Sicos1977) exact SPDX | Stated Apache-style; confirm LICENSE file before shipping | LOW |
| charlesw/tesseract release cadence beyond 5.2.0 | Releases page errored on fetch | LOW |
| Trang exact BSD variant | SPDX not surfaced from primary; stated BSD-style (R6 confirms V20241231 date; R4 was less specific — R6 is authoritative) | LOW |
| Unstructured per-model/component licenses | Apache-2.0 for core confirmed; individual ML model weights not verified | MEDIUM — verify before bundling ML pipeline |
| quicktype exact latest version/date | Repo home did not surface version | LOW — pin to specific npm version |
| GenSON exact release date for v1.3.0 | HISTORY.rst present but dates absent | LOW |
| APIDevTools swagger-parser exact current major | v10-v11 observed; @readme fork more active for 3.1 | LOW |
| WCF Web Service Reference / xsd.exe SPDX | Microsoft-proprietary tooling; SPDX not publicly stated | LOW — use-only scenario |
| xmltoxsd exact release date | PyPI page failed to render; confirmed via search result text | LOW — not recommended regardless |
| pyx12 exact Python version floor and BSD variant | Py3 confirmed; 3.11-3.14 range not primary-confirmed | LOW |
| OopFactory.X12 latest commit date / active status | Commit count 588 confirmed; activity date not surfaced | LOW — already flagged AVOID |

All [unverified] items must be independently verified before being used as a basis for a binding architectural or procurement decision.

---

## 22 Not Touched / Boundaries Honored

- No code written. Architecture/research report only. Zero implementation files created or modified.
- No runtime files modified. No changes to AiIntegrator/** or any TwinCore framework file.
- No secrets, credentials, API keys, or local filesystem paths in this report.
- No "Igor approved." The phrase "Provisional owner-approved baseline by Slava; reversible when Igor returns" is used where owner-proxy decisions are referenced.
- No G2 implementation. No generator code, no sandbox execution, no real carrier calls.
- No merge, push, delete, archive, or destructive git.
- No AIKB durable writes without zafixiruy.
- AIKB consistency A-H honored: (A) universal multi-provider framing; UPS Rating referenced only as proof case. (B) G0-G5 gate naming used exclusively. (C) all 11 locked canonical names used without modification. (D) Blueprint v0.2 is the baseline. (E) EF deferred; in-memory through G2 explicitly stated. (F) runtime LLM = ZERO stated in sections 3, 10, 16. (G) FRAMEWORK_NOT_A_BLOCKER_WITH_SPLIT_HOSTING state honored; no old risk repetition. (H) owner-proxy phrasing used.
- file_scope_allowed: single file report.md in the run folder. No other files created or modified.

---

## 23 Next Safe Step

**Slava / GPT audit of this report before any G2 implementation begins.**

Specific next actions (in order):
1. GPT audit — run the standard gpt-handoff review cycle (TARGET_REPORT_PATH above). GPT reviews sections 4-15 for architectural soundness, section 21 unverified flags, and section 19 open questions.
2. Answer open questions from section 19 — particularly #1 (design-time AI tool) and #6 (Postman Collection gate assignment) before G2 planning starts.
3. FIL schema definition — author the Format Intelligence Library JSON/YAML schema and logistics vocabulary seed (5 ambiguity categories from Phase 0.2 plus carrier patterns). This is a design artifact, not code; can be done as a parallel workstream.
4. G2 planning gate — once report is GPT-accepted and section 19 answered, open G2 as the macro-gate (G2a layer split, G2b mapping/validation, G2c workbench UI, G2d freeze profile) per AIKB section 5.
5. Do not open G2 implementation until GPT-ACCEPT on this report.

---

## 24 IMPROVE STEP

This report identified that xmlschema was incorrectly described in the original research brief as an "XSD inference from XML" tool. It is a validator/decoder only. The correction was caught in R6 and propagated throughout this report. Trang (Java, V20241231) is the correct XML-to-XSD inference tool.

Durable lesson candidate: when a brief lists a tool by claimed capability, verify the capability against the primary source before building architecture around it. Claimed capabilities that differ from primary-source documented capabilities should be flagged as CapabilityMismatch in the research artifact equivalent of an AmbiguityLog.

**IMPROVE STEP verdict:** lesson noted; not a pattern requiring a separate skill entry at this time. One-off; already captured in R6 and section 5e/section 6 of this report.