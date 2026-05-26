# InsuranceAIPlatform — .NET Backend Skeleton Implementation V0.1 — Handoff

**Gate:** `DOTNET_BACKEND_SKELETON_IMPLEMENTATION_V0.1` · **Date:** 2026-05-27
**Scope:** create the first minimal .NET backend skeleton under `server/` — builds, runs, health + demo-status endpoints, Swagger UI, CORS for Vite. No DB, no claims API, no AI, no Azure, no commit/push.

CURRENT STATE:
A minimal .NET 9 backend skeleton now exists under `server/` (UNCOMMITTED on `dev`). It builds with 0 errors/0 warnings, its xUnit smoke tests pass (2/2), and a live local run confirms `/health`, `/api/system/demo-status`, Swagger UI, and the Vite CORS header all work. No database, EF Core, migrations, AI provider, or Azure are present. Frontend `src/**`, package files, and the committed planning docs are untouched. Independent Opus QA inspector CLEARED 8/8.

IMPLEMENTATION STATUS: implemented (skeleton; uncommitted).

BRANCH: `dev`
HEAD BEFORE: `a60b889` (`docs: add backend skeleton planning package`) — unchanged; the skeleton is uncommitted working-tree content (commit is the next gate).

TARGET FRAMEWORK: **net9.0** (SDK 9.0.304 installed; .NET 8 LTS remains a one-line fallback). ASP.NET Core **controllers**.

PROJECT STRUCTURE (Option C Hybrid):
```
server/
  InsuranceAIPlatform.sln
  InsuranceAIPlatform.Api/
    Program.cs                 (DI, Swagger, CORS, controllers)
    Controllers/HealthController.cs        (GET /health)
    Controllers/SystemController.cs        (GET /api/system/demo-status)
    Contracts/HealthResponse.cs, DemoStatusResponse.cs
    Domain/ Application/ Infrastructure/   (.gitkeep stubs — reserved for later gates)
    appsettings.json, appsettings.Development.json (non-secret)
    Properties/launchSettings.json         (http :5284)
  InsuranceAIPlatform.Tests/
    SystemControllerSmokeTests.cs          (WebApplicationFactory)
  .gitignore                               (bin/obj + secret patterns excluded)
  README.md
```
Packages: Api → `Swashbuckle.AspNetCore` only; Tests → xunit + `Microsoft.AspNetCore.Mvc.Testing 9.0.0` + Test.Sdk + coverlet. (Template's `Microsoft.AspNetCore.OpenApi` removed — single Swagger path.)

ENDPOINTS:
- `GET /health` → 200 `{ "status":"Healthy", "service":"InsuranceAIPlatform.Api", "environment":"Development", "timestampUtc":"..." }`
- `GET /api/system/demo-status` → 200 `{ "project":"InsuranceAIPlatform", "mode":"LocalDemo", "data":"Synthetic", "backend":"Skeleton", "database":"NotConnected", "aiProvider":"NotConnected", "claimFlow":"Planned", "humanApprovalRequired":true, "message":"Backend skeleton is running. Claims API, database, and AI provider are planned future gates.", "timestampUtc":"..." }`

SWAGGER / OPENAPI: Swashbuckle, Development-only. UI at `/swagger` (HTTP 200), doc at `/swagger/v1/swagger.json` (HTTP 200). Title "InsuranceAIPlatform API" v0.1; description carries the synthetic-data + AI-advisory + human-approval-final disclaimer; `System` tag group.

CORS: policy `ViteDevServer` allows `http://localhost:5173`. Verified header `Access-Control-Allow-Origin: http://localhost:5173` on an API response.

BUILD RESULT: PASS — `dotnet build InsuranceAIPlatform.sln` → Build succeeded, 0 Warning(s), 0 Error(s).

TEST RESULT: PASS — `dotnet test` → Passed: 2, Failed: 0, Skipped: 0 (WebApplicationFactory smoke tests for `/health` and `/api/system/demo-status`).

LOCAL RUN / SMOKE CHECK: PASS — ran the built DLL on `http://localhost:5284` (Development): `/health` 200, `/api/system/demo-status` 200 (synthetic payload), `/swagger/index.html` 200, `/swagger/v1/swagger.json` 200, CORS header present for Origin `:5173`. Server stopped cleanly (no lingering process). Manual run: `dotnet run --project server/InsuranceAIPlatform.Api`.

FILES CREATED/CHANGED: only under `server/**` (solution, Api project + Program/controllers/contracts/folder-stubs/appsettings/launchSettings, Tests project + smoke test, `.gitignore`, `README.md`). `git status` = `?? server/` only; 0 changes outside `server/`; `bin/`/`obj/` gitignored (0 would be committed).

SAFETY SCAN: clean — no API keys, tokens, passwords, real connection strings, or PII in `server/`. The only pattern matches are inside `server/.gitignore` (rules that EXCLUDE secrets). No EF Core / SQL / Azure / AI / auth packages; no `Migrations/`; `DevDept` referenced only as a no-touch note in `README.md`.

FORBIDDEN SCOPE CONFIRMED: no database created/touched · no SQL · no `DevDept` · no EF Core · no migrations · no claims/P0 API · no `src/**` change · no package/lock change · no `.env` · no `appsettings.Production.json` · no secrets · no Azure · no AI provider · no real API calls · no real PII · no commit · no push · no merge to main · no force-push.

NEXT SAFE STEP: a separately-approved **`COMMIT_DOTNET_BACKEND_SKELETON_ONLY`** gate to commit `server/**` on `dev` (then optionally push `dev`). After that: **`BACKEND_READ_ONLY_CLAIMS_API`** (P0 read endpoints over in-memory synthetic CLM-1006 data), per the implementation-gates plan.
