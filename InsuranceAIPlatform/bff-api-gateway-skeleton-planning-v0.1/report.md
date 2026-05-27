# BFF / API Gateway Skeleton Planning — V0.1 — Report

**Gate:** `BFF_API_GATEWAY_SKELETON_PLANNING_V0.1` · **Date:** 2026-05-27
**Type:** planning-only — no implementation, no DB, no AI calls, no source commit/push.

## Current state
- Branch `dev` @ `f8df2b6`; `origin/dev` `f8df2b6`; `origin/main` `69e6731` (unchanged).
- Frontend: 11 screens; single API facade (`VITE_INSURANCE_API_MODE` mock|backend, default mock); GET-only backend client with non-silent mock-fallback.
- Backend: single .NET 9 read API — 13 GET endpoints (10 `/api/claims`, 1 `/api/demo/scenario`, `/health`, `/api/system/demo-status`), in-memory read service (singleton), 13 DTOs, 13 tests, `CLM-1006` golden, `^CLM-\d{4}$` validation. (Earlier docs approximated 11 GET / 14 tests; exact counts are 13/13.)

## Why this gate
Accepted backend target = Azure-ready microservice architecture, implemented locally first. The next gate is NOT persistence/EF and NOT a single backend skeleton — it is planning the BFF / API Gateway, the first microservice-aligned boundary, so the frontend keeps one stable surface while internal services are introduced behind it.

## What this gate produced
2 architecture docs + a local report + this sanitized handoff. No code/DB/migrations/AI calls/commit/push.

## Files created (source repo, local, uncommitted)
- `docs/architecture/BFF_API_GATEWAY_SKELETON_PLANNING_V0.1.md` — full plan (purpose/non-goals, current seam, read-API state, responsibilities, migration options + chosen path, endpoint groups, read aggregation, command routing, DTO boundary, anti-corruption layer, service dependency map, error model, correlation/trace, auth placeholder, observability/audit, local topology, test strategy, migration stages, risks, next gate, stop boundaries).
- `docs/architecture/BFF_API_GATEWAY_ROUTE_CONTRACT_MAP_V0.1.md` — 11-screen read map (all 11 required columns) + future-command map + BFF→service matrix.
- `docs/reports/bff-api-gateway-skeleton-planning-v0.1/report.md` — local report.

## Chosen design (summary)
- **BFF = stable frontend surface; owns no business data, no DB, no AI provider.** Frontend → BFF only; the frontend must never call internal services directly.
- **Migration = Option B (staged):** Stage 1 BFF preserves the current `/api/claims/...` + `/api/demo/scenario` routes verbatim and delegates to the current read logic (frontend repoints base URL only — zero code change). Stage 2 service skeletons behind BFF → Stage 3 per-service persistence (schema-per-service; read logic migrates into owning services) → Stage 4 write/events/outbox → Stage 5 AI Analysis.
- **Two surfaces:** Surface 1 = preserved passthrough (contract-stable); Surface 2 = additive composed views (`/api/bff/dashboard`, `/api/bff/claims/{id}/workspace`) adopted later.
- **Prime aggregation:** the claim-detail view spans Claims + Customers & Policies + AI Analysis + Audit & Cost, composed by the BFF with id-only cross-service refs (no shared DbContext, no cross-service DB joins).
- **Commands (0 implemented):** mapped from the deferred safe-buttons to owning services — approval draft/submit → Approval; document request/confirm/add → Documents; run analysis/customer draft → AI Analysis; create case → Claims. AI advisory-only; human-only approval; no real SMS/email; idempotency + audit per command.
- **Error/observability:** reuse the uniform error envelope `{ code, message, traceId }`; correlation/trace headers; `X-Provider-Mode` carries only a mode name (`mock`/`disabled`/`deepseek`), never a secret; partial-aggregation `warnings[]`.

## Service mapping (6 internal services)
Claims · Customers & Policies · Documents · AI Analysis · Approval · Audit & Cost — each mapped to its BFF read/command endpoints. DeepSeek isolated in AI Analysis (mock default); the BFF never calls it.

## Verification
- Only docs/report/handoff changed; no `src/**`, no `server/**`, no package/lock.
- No DB/migrations/EF; no AI provider call; no Azure; no write endpoints.
- `DEEPSEEK_API_KEY` never read/printed/logged; no secret value in any file; synthetic data only (`CLM-1006`); no real PII.
- No source commit/push; `main` untouched (`69e6731`).
- Docs explicitly state: frontend → BFF only; BFF owns no business data; BFF has no DB; BFF does not call DeepSeek; current frontend contract preserved; next gate = `BFF_API_GATEWAY_SKELETON_IMPLEMENTATION_V0.1`.

## Next safe step
`BFF_API_GATEWAY_SKELETON_IMPLEMENTATION_V0.1` (Stage 1) — create the BFF skeleton serving the preserved Surface-1 routes by delegating to the current read logic, add health + correlation-ID middleware, keep the frontend working against the BFF base URL with no code change. Implementation gate; no DB/services/writes/AI/Azure; commit/push only if a separate gate authorizes.
