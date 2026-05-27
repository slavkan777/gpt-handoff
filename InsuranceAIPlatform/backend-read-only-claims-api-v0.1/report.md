# InsuranceAIPlatform — Backend Read-Only Claims API V0.1 — Handoff

**Gate:** `BACKEND_READ_ONLY_CLAIMS_API_V0.1` · **Date:** 2026-05-27
**Scope:** implement the first read-only backend business slice — 11 GET endpoints over deterministic in-memory synthetic CLM-1006 data, explicit DTOs, validation/error responses, tests. No DB/EF/migrations, no frontend change, no commit/push.

## CURRENT STATE
P0 read-only claims API is implemented on branch `dev`, building green and passing all tests. The backend now serves 11 read endpoints (claims summary/list/details + documents, AI evidence, risks, policy, customer-vehicle, approval, audit, and demo scenario) plus the preserved `/health` and `/api/system/demo-status`. Data is in-memory synthetic, mirroring the frontend mock golden claim CLM-1006 for the later integration gate. No database, EF Core, migrations, Azure, or AI provider. Working changes are local only — **no commit, no push** (those are the next gate).

## IMPLEMENTATION STATUS: implemented (local, uncommitted).

## BRANCH / HEAD
- Branch: `dev`. HEAD before & after (unchanged — no commit this gate): `d9acde8` (`d9acde85da03bf98bb835ed66ac2623dc90681b3`). `origin/dev` unchanged at `d9acde8`.

## FRONTEND MOCK SEAM INSPECTED (read-only, not modified)
- `src/api/mockInsuranceApi.ts` (13 mock functions), `src/types/index.ts`, `src/data/mock/claim-1006.ts`, `src/data/mock/claims.ts`.
- DTO/seed shapes were aligned to these so a later `FRONTEND_BACKEND_READ_FLOW_INTEGRATION_V0.1` gate can swap mock→real with minimal change.

## FILES CREATED (all under `server/**`)
- `Contracts/Common/ApiErrorResponse.cs`
- `Contracts/Claims/`: `ClaimSummaryDto.cs`, `ClaimListItemDto.cs`, `ClaimDetailsDto.cs`, `ClaimDocumentDto.cs`, `AiEvidenceDto.cs` (+ AiFindingDto, EvidenceSourceDto, ExtractedEntityDto, ConfidenceBreakdownItemDto), `RiskAssessmentDto.cs` (+ RiskFactorDto, PipelineStageDto), `PolicyDto.cs` (+ PolicyCoverageDto, PolicyCheckResultDto), `CustomerVehicleDto.cs` (+ CustomerDto, VehicleDto, CommunicationEntryDto, CustomerVehicleContextDto), `ApprovalDraftDto.cs` (+ HumanDecisionOptionDto), `AuditTraceDto.cs` (+ AuditEventDto, CostDistributionItemDto), `DemoScenarioDto.cs` (+ DemoStepDto)
- `Services/IClaimReadService.cs`, `Services/InMemoryClaimReadService.cs`
- `Controllers/ClaimsControllerBase.cs`, `Controllers/ClaimsController.cs`, `Controllers/DemoController.cs`
- `InsuranceAIPlatform.Tests/ClaimsApiTests.cs`
## FILES MODIFIED
- `InsuranceAIPlatform.Api/Program.cs` — registered `AddSingleton<IClaimReadService, InMemoryClaimReadService>()` (Swagger/CORS/health/demo-status preserved).

## DTOs / CONTRACTS
27 record DTOs across Common/Claims groups (summary, list item, details, documents, AI evidence + findings/sources/entities, risk + factors + pipeline, policy + coverage + checks, customer/vehicle + communications, approval + decision options, audit + events + cost distribution, demo scenario + steps) + `ApiErrorResponse(Code, Message, TraceId)`. Records, nullable-explicit, screen-friendly, no domain/EF entities exposed.

## SERVICES / SEED
- `IClaimReadService` (11 read methods) + `InMemoryClaimReadService` (singleton). Unknown claim → returns null → controller maps to 404.
- Deterministic CLM-1006 seed mirrors the frontend golden claim: riskScore **82**, confidence **78**, tokens **4261**, cost **0.0187**, durationSec **18.9**, traceId **trc_8f3d2a7e**, runId **run_8f3d2a7e**, policyId **POL-2025-AC-4421**, estimate **2720** / benchmark **1970** / deductible **500** / payout **1800**, documents **6/7** (missing rear-bumper photo), governance pipeline ends in **BLOCK**, 5 risk factors summing to 82. List also includes 4 additional synthetic claims (CLM-1007…1010).

## ENDPOINTS IMPLEMENTED (11 GET, read-only) + 2 preserved
1. `GET /api/claims/summary`
2. `GET /api/claims`
3. `GET /api/claims/{claimId}`
4. `GET /api/claims/{claimId}/documents`
5. `GET /api/claims/{claimId}/ai-evidence`
6. `GET /api/claims/{claimId}/risks`
7. `GET /api/claims/{claimId}/policy`
8. `GET /api/claims/{claimId}/customer-vehicle`
9. `GET /api/claims/{claimId}/approval`
10. `GET /api/claims/{claimId}/audit`
11. `GET /api/demo/scenario`
Preserved: `GET /health`, `GET /api/system/demo-status`. No write verbs (no POST/PUT/DELETE/PATCH anywhere). Swagger exposes all via `[Tags]` + `[ProducesResponseType]`.

## VALIDATION / ERRORS
- claimId regex `^CLM-\d{4}$`. Invalid format → **400** `{code:"INVALID_CLAIM_ID"}`. Well-formed unknown → **404** `{code:"CLAIM_NOT_FOUND"}`. `TraceId` from `HttpContext.TraceIdentifier`. No stack traces in responses. Global exception middleware intentionally deferred (documented).

## BUILD RESULT
PASS — `dotnet build InsuranceAIPlatform.sln` → **0 Warning(s), 0 Error(s)** (independently re-verified by coordinator).

## TEST RESULT
PASS — `dotnet test` → **Passed: 14, Failed: 0, Total: 14** (2 pre-existing + 12 new: summary shape, list contains CLM-1006, claim details exact values, ai-evidence advisory, risks score 82, audit BLOCK event, invalid-format→400 [4 cases incl. lowercase], unknown→404, demo scenario). Independently re-verified.

## LOCAL RUN / SMOKE CHECK
Done during implementation (temporary run, process stopped after): `/api/claims/CLM-1006` 200, `/risks` 200 (score 82), `/api/claims/BADID` 400, `/api/claims/CLM-9999` 404. WebApplicationFactory tests re-confirm endpoints in-process.

## SAFETY SCAN
Clean — no API keys/tokens/passwords/real connection strings/SQL credentials/Azure secrets/AI-provider config/PII in `server/**`. Synthetic placeholders only (Роберт Джонсон, VIN ****8842, POL-2025-AC-4421 — mirror the frontend mock). The only "Azure OpenAI" string is a DTO *value* label (`Model: "Azure OpenAI (mock)"`), not wiring. No `.env`, no `appsettings.Production.json`, no new packages (csproj diff empty: Api still Swashbuckle-only).

## CHANGESET (server-only)
`git status --porcelain` → `M Program.cs` + untracked `Contracts/Claims/`, `Contracts/Common/`, `Controllers/ClaimsController.cs`, `ClaimsControllerBase.cs`, `DemoController.cs`, `Services/`, `Tests/ClaimsApiTests.cs`. `src/**`, `docs/**`, `package.json`, `package-lock.json` — zero diffs. `git diff --name-only HEAD` → only `server/InsuranceAIPlatform.Api/Program.cs`.

## QA GATE
Implemented by a Sonnet worker; coordinator independently re-ran build (0/0) + tests (14/14) + changeset + safety; then an independent **Opus** inspector verified against the DONE STATE — **CLEARED 10/10** (build, tests, 11 GET endpoints / no write verbs, existing endpoints preserved, validation INVALID_CLAIM_ID/CLAIM_NOT_FOUND, seed matches frontend, no EF/DB/Azure/AI/auth/new-packages, no secrets/PII, server-only changeset, no commit/push). Verdict: PUBLISH.

## NON-BLOCKING ADVISORY
The gate prompt specified error code `INVALID_CLAIM_ID`; the planning doc `BACKEND_API_CONTRACTS_V0.1.md:74` still says `INVALID_CLAIM_ID_FORMAT`. Code/tests follow the gate prompt (authoritative). Recommend aligning that doc line in a future docs pass (not edited here — this gate forbids doc changes).

## CONFIRMATION — FORBIDDEN SCOPE
No commit · no push · no merge · no DB/SQL/DevDept · no EF Core · no migrations · no DbContext · no SQL provider/auth/Azure/AI packages · no frontend `src/**` change · no `package.json`/lock change · no `.env`/`appsettings.Production.json` · no secrets · no real PII · no real API calls · no write endpoints.

## NEXT SAFE STEP
Separately-approved **`COMMIT_AND_PUSH_DEV_BACKEND_READ_ONLY_CLAIMS_API_ONLY`** — commit the `server/**` changes on `dev` and fast-forward push to `origin/dev` (main untouched). Then `FRONTEND_BACKEND_READ_FLOW_INTEGRATION_V0.1`.
