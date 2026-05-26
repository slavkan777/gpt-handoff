# InsuranceAIPlatform — .NET Backend Skeleton Planning (V5) — Handoff

**Gate:** `DOTNET_BACKEND_SKELETON_PLANNING_V5_FINAL_STANDALONE` · **Date:** 2026-05-27
**Scope:** DOCS-ONLY. Produce an exhaustive, implementation-ready backend + database technical specification so future backend work is bounded, deterministic, and interview-ready. No backend code, no DB, no migrations, no frontend change, no commit/push to source.

CURRENT STATE:
14 backend planning documents authored under `docs/architecture/` on branch `dev` (left **uncommitted** per gate rules). The plan is grounded in the actual frontend seam (`src/api/mockInsuranceApi.ts`, 13 functions), canonical types, and the golden claim CLM-1006 mock data — so it is concrete, not theoretical. Docs-only verified; independent QA inspection CLEARED 7/7. No code, no `server/`, no DB, no migrations, no commit, no push.

PLANNING STATUS: planned (docs-only).

FILES CREATED (14, under `docs/architecture/`, uncommitted on `dev`):
- `DOTNET_BACKEND_SKELETON_PLAN_V0.1.md` (entry-point: product boundary, platform, structure, NFRs, branch policy, interview narrative, plan acceptance checklist)
- `BACKEND_API_CONTRACTS_V0.1.md` (endpoint plan, concrete CLM-1006 JSON examples, validation/error rules, Swagger portfolio rule)
- `BACKEND_DTO_CONTRACTS_V0.1.md` (27-DTO catalog mapped to screens + source models)
- `BACKEND_PERSISTENCE_AND_DATABASE_PLAN_V0.1.md` (A–E options, Path 1 vs 2, DB reset/safety)
- `BACKEND_SCHEMA_OUTLINE_V0.1.md` (23 tables, PK/FK/index, seed/sensitivity notes)
- `BACKEND_EFCORE_MIGRATION_STRATEGY_V0.1.md` (DbContext, configs, migrations deferred to persistence gate)
- `BACKEND_SEED_DATA_PLAN_V0.1.md` (deterministic CLM-1006 seed + demo-data ownership rule)
- `BACKEND_FRONTEND_INTEGRATION_PLAN_V0.1.md` (route→backend map, mock-to-real, local dev)
- `BACKEND_AI_RISK_AUDIT_PLACEHOLDERS_V0.1.md` (6 service interfaces, governance invariants)
- `BACKEND_SECURITY_AND_DEMO_BOUNDARIES_V0.1.md` (PII/secrets/config strategy, demo honesty)
- `BACKEND_OBSERVABILITY_PLAN_V0.1.md` (structured logs, trace id, health)
- `BACKEND_VERIFICATION_PLAN_V0.1.md` (build/test/smoke + DevDept-not-touched check)
- `BACKEND_IMPLEMENTATION_GATES_V0.1.md` (9-gate decomposition + future Claude file-scope maps)
- `BACKEND_DECISION_MATRIX_AND_NEXT_GATE_V0.1.md` (final decision matrix + exact next-gate spec)

REPO / BRANCH / HEAD INSPECTED: branch `dev`, HEAD `69e67312a10cc9bcf28c4e387a126b48c91fb9c5`, clean before docs; origin = public `slavkan777/InsuranceAIPlatform`.

FRONTEND SEAM INSPECTED: yes — `src/api/mockInsuranceApi.ts` (13 functions), `src/api/insuranceApi.types.ts`, `src/types/index.ts`, `src/data/mock/claim-1006.ts` (full CLM-1006 graph). Plan maps every mock function to a future endpoint + DTO.

RECOMMENDED .NET PLATFORM: **.NET 9** (SDK 9.0.304 installed; current, interview-strong); **.NET 8 LTS** documented as a one-line `TargetFramework` fallback. ASP.NET Core **Controllers** (attribute-routed, tag-grouped), nullable refs + implicit usings on, analyzers deferred.

RECOMMENDED BACKEND STRUCTURE: **Option C — Hybrid** (`server/InsuranceAIPlatform.Api` + `server/InsuranceAIPlatform.Tests`, internal folders Domain/Application/Infrastructure/Contracts), with a documented split into a full modular monolith at the persistence/AI gates. (Option A too flat; Option B overengineered for the first slice.)

RECOMMENDED PERSISTENCE / DATABASE: **Path 1 — in-memory synthetic seed first** (read-only API), SQL Server deferred to an explicit `BACKEND_SQLSERVER_PERSISTENCE_GATE`. Keeps the first implementation gate small/safe.

DEDICATED DB DECISION: **`InsuranceAIPlatform`** (schema `dbo`) on `localhost,19772`. **`DevDept` is NEVER used or touched** — every doc references it only as a forbidden/no-touch guard. No DB created in this gate.

DATABASE SCHEMA SUMMARY: 23 tables outlined (Claims, ClaimDocuments, ClaimPhotos, DocumentChecklistItems, Policies, PolicyCoverages, PolicyExclusions, Customers, Vehicles, AiAnalysisRuns, AiFindings, EvidenceSources, ExtractedEntities, RiskAssessments, RiskFactors, PolicyCheckResults, ApprovalDrafts, HumanDecisionOptions, AuditTraces, AuditEvents, CostTelemetryEvents, DemoScenarios, DemoSteps) with PK/FK/index/seed/sensitivity notes; aligned to UI screens; not over-normalized; no real PII.

EF CORE / MIGRATION STRATEGY: `InsuranceAiDbContext`, `IEntityTypeConfiguration` per entity, migrations under Infrastructure/Persistence/Migrations, design-time factory, runtime idempotent seeder; connection string via user-secrets/env (never committed). **Migrations forbidden until the explicit persistence gate.**

SEED DATA STRATEGY: deterministic CLM-1006 seed reproducing the exact frontend story (7 documents incl. missing rear-bumper photo, 3 photos, risk 82/100 from 5 factors, confidence 78%, 6 audit events incl. Governance BLOCK of auto-approval, cost ≈$0.0187 / 4261 tokens / 18.9s, 7 demo steps); stable IDs; deterministic reset; mock→seed mapping table.

API RESPONSE EXAMPLES: yes — concrete synthetic JSON for `/api/claims/summary`, `/api/claims`, `/api/claims/CLM-1006`, `/documents`, `/ai-evidence`, `/risks`, `/audit`, `/api/system/demo-status`, and `ApiErrorDto`.

VALIDATION / ERROR RULES: yes — `claimId` format `CLM-####`; unknown → 404 `CLAIM_NOT_FOUND`; bad format → 400; no stack traces; every error carries `traceId`; logged with trace id.

DTO STRATEGY: **screen-friendly read DTOs for P0**; never expose EF entities; domain DTOs added as write endpoints arrive. 27 DTOs catalogued.

DEMO DATA OWNERSHIP: mock = source of truth before backend → backend seed becomes source of truth for CLM-1006 after the read API → frontend mock becomes fallback/demo-mode only; any CLM-1006 change updates both deliberately.

CONFIG / SECRETS STRATEGY: `appsettings.json` non-secret defaults only (committable); connection string via user-secrets/env (not committed); `appsettings.Development.json` sanitized or gitignored; no `appsettings.Production.json`; no `.env`; README shows placeholder only; secret scan before any future push.

SWAGGER PORTFOLIO RULE: title "InsuranceAIPlatform API"; description states synthetic demo data + AI advisory + human approval final; tag groups (Claims, Documents, AI Evidence, Risks, Policy, Approval, Audit, Demo/System); P0 example responses; must not imply real production operation.

INTERVIEW NARRATIVE: yes — senior-level bullets (modular monolith vs microservices, DTO-first, mock seam, read-only-first, dedicated DB vs DevDept, AI advisory only, growth to AI/RAG/Azure, audit/cost governance from day one).

NEXT IMPLEMENTATION GATE: **`DOTNET_BACKEND_SKELETON_IMPLEMENTATION_V0.1`** — skeleton only: `server/` hybrid solution, `/health`, Swagger, CORS for Vite `:5173`, `GET /api/system/demo-status`. **No DB, no full API surface, no frontend integration, no AI provider, no Azure.** Completable in one bounded run; full GOAL/DONE-STATE/file-targets/verification/stop-line specified in `BACKEND_DECISION_MATRIX_AND_NEXT_GATE_V0.1.md`.

NEXT IMPLEMENTATION GATES (decomposed, each small): 1 skeleton → 2 read-only claims API (in-memory) → 3 frontend read-flow integration → 4 SQL Server persistence (dedicated DB) → 5 audit/risk placeholders → 6 approval-draft placeholder → 7 mock-AI pipeline planning → 8 mock-AI pipeline impl → 9 real-AI provider planning. Each with file-scope map + commit/push policy (work on `dev`, never direct to `main`).

VERIFICATION (docs-only gate): `git status --short` = 14 untracked `docs/architecture/*.md` only; 0 changes outside `docs/architecture/`; no `server/`/`.sln`/`.csproj`/`.cs`/`appsettings`/`.env`/`Migrations`; all 14 docs non-empty; decision matrix decisive (9 explicit choices); next gate skeleton-only; DevDept only as no-touch; no secrets/real-PII in docs. Independent Opus QA inspector: **CLEARED 7/7**.

ISSUES / LIMITATIONS: docs are uncommitted (a future commit/implementation gate persists them on `dev`); plan is a specification, not validated code; SQL Server interactions are planned only (not exercised); the prior local handoff temp dir from an earlier gate remains pending OS cleanup (token-free).

FORBIDDEN SCOPE CONFIRMED: no backend code · no `dotnet new` · no `.sln`/`.csproj`/`.cs` · no NuGet · no `server/` · no controllers/classes · no database · no SQL exec · no migrations · no SQL Server state touched · no `DevDept` touched · no `appsettings`/`.env` · no `src/` or package change · no Azure · no AI provider · no API keys · no real API calls · no real PII · no commit · no push · no force-push · no unrelated repos.

NEXT SAFE STEP: a separately-approved **`DOTNET_BACKEND_SKELETON_IMPLEMENTATION_V0.1`** gate (skeleton only, on `dev`); optionally a small commit gate first to persist these 14 planning docs onto `dev`.
