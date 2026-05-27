# Microservice Persistence & Seed (Mega) V0.1 — Report

**Gate:** `MICROSERVICE_PERSISTENCE_AND_SEED_MEGA_V0.1` · **Date:** 2026-05-27
**Type:** bounded implementation — local EF Core persistence + deterministic synthetic seed behind 6 service-owned boundaries. No writes/AI/Azure/secrets, no `src/**` change, **no source commit/push**.
**Status:** implemented + independently verified (Opus inspector **CLEARED**, zero discrepancies).

## Current state
- Source: branch `dev` @ `fed2bc4`; `origin/main` `69e6731` untouched. **No commit this gate** — HEAD still `fed2bc4`; changes uncommitted.
- DB: local SQL Server **LocalDB** `(localdb)\MSSQLLocalDB`, database `InsuranceAIPlatform` created, migrated, seeded (Windows integrated auth — no password).

## What this gate did
EF Core 9 persistence behind the six service boundaries: one DB, six service-owned schemas + DbContexts (**no shared god context** — each schema has its own `__EFMigrationsHistory`), six independent migrations, a dev-only migrator/seeder, deterministic synthetic seed, and 18 new tests. The frontend/BFF read contract is preserved **unchanged** (Option A).

## Persistence shape
| Service | Schema | DbContext | Key entities |
|---|---|---|---|
| Customers & Policies | `customers_policies` | `CustomersPoliciesDbContext` | SyntheticCustomer, Policy, Vehicle |
| Claims | `claims` | `ClaimsDbContext` | Claim, ClaimStatusHistory |
| Documents | `documents` | `DocumentsDbContext` | ClaimDocument |
| Approval | `approval` | `ApprovalDbContext` | ApprovalDraft, ApprovalDecisionOption |
| Audit & Cost | `audit_cost` | `AuditCostDbContext` | AuditEvent, CostTrace, TokenUsageTrace |
| AI Analysis | `ai_analysis` | `AiAnalysisDbContext` | AiAnalysisRun, AiFinding, AiEvidenceReference, AiRiskSignal |

`Api → Services.* → BuildingBlocks`; services never reference each other; cross-service refs id-only; BFF owns no DbContext (composition root unmodified). EF (`EntityFrameworkCore.SqlServer`+`.Design` 9.0.0) added only to the 6 service projects; `EntityFrameworkCore.InMemory` 9.0.0 to Tests.

## Migrations
Six independent initial migrations, one per service project: `Initial{CustomersPolicies,Claims,Documents,Approval,AuditCost,AiAnalysis}Persistence`. No monolithic migration.

## Seed + verification (live, LocalDB)
Dev-only `InsuranceAIPlatform.DbMigrator` applies all six migrations + runs six idempotent seeders.
- **Synthetic test users: EXACTLY 200** — `COUNT(*) WHERE Id LIKE 'CUST-T%'` → **200** (`CUST-T0001..0200`). Table total **201** = 200 + golden customer `CUST-4421` (CLM-1006's Роберт Джонсон).
- **Golden claim:** `CLM-1006` present (status «В роботі», payout 1800.00); 15 claims total across statuses.
- Documents 14 · ApprovalDrafts 1 + options 4 · AuditEvents 6 + CostTraces 4 + TokenUsage 1 · AiAnalysisRuns 1 (`ProviderMode=Disabled`) + Findings 3 + Evidence 2 + RiskSignals 4.
- No real PII: emails `testuserNNN@example.invalid`.

## Verification (independently re-run by an Opus inspector)
| Check | Result |
|---|---|
| Backend build | **0 warnings, 0 errors** |
| Backend tests | **53 passed / 0 failed** (35 prior + 18 new; EF-InMemory, no live-DB dependency) |
| Frontend build | **107 modules**, PASS (no `src/**` change) |
| Live DB apply + seed | PASS (6 migrations applied, seed run, idempotent) |
| 200-count (sqlcmd) | PASS (exact) |
| CLM-1006 | present |
| Live smoke (`:5285`) | 7 routes → **200**; CLM-1006 unchanged; correlation + `X-Bff`; API boots without a DB |
| Write endpoints | **0** |
| Provider/Azure/AI SDK packages | none; AI `ProviderMode=Disabled` only |
| DevDept | not referenced |
| Secrets | integrated-auth (no password); not in prod appsettings; `DEEPSEEK_API_KEY` never read |
| Source commit/push | none; `main` untouched (`69e6731`); HEAD `fed2bc4` |

## Read compatibility (Option A)
Existing 13 read routes + 2 additive BFF endpoints + `/api/bff/health` unchanged (in-memory) → frontend contract byte-identical (live-smoke confirmed). Read migration onto the DB is deferred to a later gate (to avoid contract drift); persistence is proven via the migrator + tests and is ready for that gate.

## Transparency notes (non-blocking)
- The non-secret integrated-auth connection string also appears as a default fallback constant in `BuildingBlocks/SeedConstants.cs` (no credential — within rules).
- `CustomersPoliciesService.CountSyntheticCustomersAsync` returns 0 in-process (BFF not DB-wired by design); the live 200 count comes from the migrator/seeder path.

## Next safe step
`COMMIT_AND_PUSH_DEV_MICROSERVICE_PERSISTENCE_AND_SEED_ONLY` — commit exactly this persistence/seed scope to `dev`, fast-forward push `dev` only; `main` untouched; no force.
