# InsuranceAIPlatform — Code-Quality / OOP / SOLID / Architecture Audit (read-only)

**Gate:** CODE_QUALITY_OOP_SOLID_ARCHITECTURE_AUDIT_FOR_IGOR_V0.1
**Type:** read-only senior architecture / maintainability audit — **no code changed, no commit, no push, no cloud/DB changes**
**Date (UTC):** 2026-05-30
**Verdict:** **CODE_QUALITY_AUDIT_COMPLETE**
**Method:** 8 parallel read-only dimension auditors + 1 independent adversarial (anti-flattery) critic; all findings cite `file:line`.

---

## 1. Executive verdict

**Primary classification — hybrid:**
> **Strong portfolio-grade engineering demo / production-shaped demo with limitations.**
> **Decisively NOT junior CRUD. Explicitly NOT production-ready.**

This is the work of someone who has read Clean Architecture / DDD / BFF and applied it with **real discipline** — the bounded-context isolation, the AI-safety model, and the executed Azure IaC are genuinely above the average portfolio demo and are interview-defensible. It is held back from "production-ready" by a cluster of **concrete, non-cosmetic defects** (a live broken navigation, a stale-read seam on the showcase claim, an ID generator that bricks claim creation, a sync-over-async deadlock vector, a write-only "outbox", no global exception handler), several of which are currently **masked by the single-user scripted demo** rather than fixed.

The project's own honesty is a positive signal: the portfolio docs carry an explicit "deployed vs deferred" table and the code comments openly label concessions (`"acceptable for local sandbox"`). This reads as a deliberate demonstrator, not hidden debt.

---

## 2. Repo state

| Field | Value |
|---|---|
| Path | `C:/Projects/InsuranceAIPlatform` |
| Branch | `dev` |
| HEAD | `70af774` (= `origin/dev`, 0 ahead / 0 behind) |
| `origin/main` | `69e67312` (untouched) |
| Working tree | clean except **untracked** `test-results/` (Playwright artifact) — **not a dirty tracked tree** |
| Build | `dotnet build -c Release` → PASS (9 projects, 0 warn / 0 err) |
| Tests | `dotnet test` → **137 / 137 PASS** (Failed 0, Skipped 0, 4 s) |

> Read-only confirmed: nothing in the source tree was edited, staged, committed, or pushed; no Azure/DB mutation.

---

## 3. Scope inspected

- **Backend** (`server/`, 174 `.cs`, 14,214 LOC, 9 projects): all 6 `Services.*`, `Api` (controllers, `Program.cs`, `Services/*ReadService`), `BuildingBlocks`, `DbMigrator`, `Tests`.
- **Frontend** (`src/`, 101 TS/TSX, 12,588 LOC): pages, `features/*` (Redux slices + sagas), `api/*` facade, `i18n/*`, components.
- **Persistence**: 6 DbContexts, 11 migrations, seeders, `DbMigrator`, `SeedConstants`.
- **Infra/DevOps**: `infra/` (Bicep `main` + 9 modules + params), `.github/workflows/azure-deploy-demo.yml`, `staticwebapp.config.json`, `server/Dockerfile`, `docs/architecture/azure/*`, `docs/portfolio/*`.
- **Tests**: `server/InsuranceAIPlatform.Tests/*` (16 files) + `e2e/*` (21 Playwright specs).

---

## 4. OOP / SOLID scorecard

| Principle / Area | Verdict | Evidence | Sev |
|---|---|---|---|
| SRP | PARTIAL | `ClaimCommandsController.cs` 603L repeats the (service-write → `AppendAuditAsync` → `WriteOutboxAsync`) choreography inline across 7 endpoints with no abstraction | P1 |
| OCP | PASS | `IAiProvider` swap at `Program.cs:99-123`; orchestrator/controllers never inspect concrete type | — |
| LSP | **FAIL** | `IClaimReadService` is a **sync** contract; `HybridClaimReadService.cs:40,64` can only satisfy it by blocking → behaviour (deadlock risk) absent from `InMemoryClaimReadService` | P1 |
| ISP | PASS | Narrow interfaces: `IAiProvider` (1+1), `IGuardrailEvaluator` (1), `IClock` (1) |
| DIP | PARTIAL | All prod ctors inject by interface; one leak — orchestrator injects `AppendAuditDelegate`/`WriteOutboxDelegate` (typed `Func<>`) instead of an `IAuditPort` (`Program.cs:137-151`) | P2 |
| Encapsulation | PASS+note | **81 sealed records** (immutable DTOs); EF entities use mutable `{get;set;}` (standard EF, leaky outside infra) |
| Async | **FAIL** | sync-over-async `.GetAwaiter().GetResult()` on the hot read path (`HybridClaimReadService.cs:40,64`) | P1 |
| Error handling | PARTIAL | Provider catches are typed+rethrow; but bare `catch` at `PersistenceAiAnalysisOrchestrator.cs:320` swallows JSON errors with no log | P2 |

**Score: 6.5 / 10** (held by the critic — "the model for how the others should have been framed").

---

## 5. Cohesion / Coupling scorecard

**Backend cohesion is the strong story; the frontend leaks demo data into its logic layer.**

| Area | Cohesion | Coupling | Evidence |
|---|---|---|---|
| Backend service assemblies | High | Very low | All 6 `Services.*.csproj` → **only** `BuildingBlocks`; zero service→service refs (enforced by csproj **and** a reflection test, `ServiceSkeletonTests.cs:126-150`) |
| Cross-service (AiAnalysis↔AuditCost) | — | Low (by delegate) | `Program.cs:137-151` typed delegates instead of a project ref |
| Frontend API seam | High | Low | `insuranceApi.ts:31` `const insuranceApi: typeof mockInsuranceApi = …` forces structural parity of mock vs backend at compile time |
| Frontend slices/sagas | **Low** | **High** | `claimsSaga.ts:14-30` imports the **full mock fixture set** as fallback; `claimsSlice.ts:41-42` uses `claimRows` as `initialState` + `selectedId='CLM-1006'`; `approvalSaga.ts:11` hardcodes `CLAIM_ID='CLM-1006'` |
| FE→BE DTO contract | — | **High** | `backendInsuranceApi.ts:95-317` hand-mirrors ~20 C# record types; **no codegen / no shared spec** → silent drift |
| BFF DTO cohesion | Mixed | — | `ClaimDetailsDto` embeds AI-trace fields (`TraceId/RunId/Tokens/Cost/DurationSec`) next to claim identity → two reasons to change |
| Infra ↔ application | High | None | Bicep carries no secrets/conn-strings; config-driven at runtime |

**Scores: Cohesion 5.5 / 10 (critic cut from 6), Coupling 5.5 / 10.**

---

## 6. Anti-pattern / code-smell register

Severity uses the critic's reconciliation (some auditor "P0" labels are **latent/masked**, marked accordingly).

| # | Area | Smell | Evidence | Sev |
|---|---|---|---|---|
| A1 | Frontend nav | **CLM-1006 hardcoded in Sidebar + 5 page navigations** → any non-golden claim's sub-nav jumps to CLM-1006 | `Sidebar.tsx:27-34` (8 links); `DashboardPage.tsx:341,362,381`; `CustomerVehiclePage.tsx:153`; `RisksChecksPage.tsx:153` | **P0 (LIVE)** |
| A2 | Backend read seam | Hybrid read returns **stale in-memory fixtures** for CLM-1006..1010 — DB writes to the golden claim are invisible on re-read; created claims 404 on sub-resources | `HybridClaimReadService.cs:61-62,106-125` | **P0 (LIVE on showcase claim)** |
| A3 | ID generation | `CLM-{next:0000}` produces `CLM-10000` (8 chars) which `^CLM-\d{4}$` rejects → claim creation bricks once any 5-digit id exists | `PersistenceClaimsService.cs:45` vs `ClaimsControllerBase.cs:14` (PowerShell-verified) | **P0 (latent)** |
| A4 | Async | sync-over-async `.GetAwaiter().GetResult()` on every list/detail read → deadlock + thread-pool starvation under concurrency | `HybridClaimReadService.cs:40,64` | **P0 (masked at demo load)** |
| A5 | Wrong-write (live) | `ImportDocumentMetadataModal` rendered **without** `claimId` in the global queue → falls back to default `'CLM-1006'` and writes there | `ClaimsListPage.tsx:139-142` + `ImportDocumentMetadataModal.tsx:25` | P1 |
| A6 | Dead-code trap | `approvalSaga.ts:11` hardcodes `CLAIM_ID='CLM-1006'`; today the page bypasses the saga (`HumanApprovalPage.tsx:111` direct call) so it's **dead code**, but a latent wrong-write trap | `approvalSaga.ts:11,17,28` | P1 |
| A7 | Reliability | **No global exception handler** — provider/`HttpRequestException` leaks as raw HTTP 500 without the `ApiErrorResponse` envelope | `Program.cs` (no `UseExceptionHandler`/ProblemDetails) | P1 |
| A8 | Architecture honesty | **Write-only "outbox"** — `ProcessedAtUtc` never set, no `BackgroundService`/relay exists → it's audit-only infra labeled "transactional outbox" | `OutboxMessage.cs:17`; zero processor classes | P1 |
| A9 | Query perf | Missing indexes on the 3 hottest columns + `GetAllClaimsAsync` with no projection/`AsNoTracking` (materializes `Description nvarchar(max)`) | `ClaimsDbContext.cs` (no `HasIndex`), `PersistenceClaimsService.cs:102-105` | P1 |
| A10 | Concurrency | ID allocation = read-all-ids→max+1 in app code, 2 DbContexts, no sequence/lock → race + O(n) per create | `PersistenceClaimsService.cs:30-51` | P1/P2 |
| A11 | Error handling | bare `catch` swallows JSON deserialization with no log | `PersistenceAiAnalysisOrchestrator.cs:320` | P2 |
| A12 | UX traps | Date filter is a **no-op**; segment counts `53/32/7/4/5` are **hardcoded** in backend mode; Dashboard filter tabs disabled with no tooltip | `ClaimsListPage.tsx:345-346,200-204`; `DashboardPage.tsx:202-203` | P2 |
| A13 | Log honesty | `DbMigrator` prints `(localdb)\MSSQLLocalDB` **regardless** of the real (possibly Azure) target | `DbMigrator/Program.cs:21` | P2 |
| A14 | Seed bug | `CUST-4421` seeded `IsSynthetic=false` but every read path filters `WHERE IsSynthetic=true` → golden customer **invisible** via API | `CustomersPoliciesSeeder.cs:110` vs `PersistenceCustomersPoliciesService.cs:43,70` | P2 |
| A15 | i18n leak | Cyrillic strings bypass the catalog (`Timeline.tsx:14`, `ClaimHeader.tsx:47-59`, `Modal.tsx:56,77`, modal error fallbacks) | as cited | P2 |

---

## 7. Frontend audit

**Score 6 / 10 — competent demo with visible cracks.**

**Strong:** Redux Toolkit slice/saga separation is clean; the API facade with **explicit `mock-fallback` labelling** (it surfaces an error, never silently swaps — `claimsSaga.ts:71`, warning rendered `ClaimsListPage.tsx:191-194`); i18n catalog enforces **compile-time key parity** (`messages/index.ts:66` `type Messages = (typeof messages)['en']`); the per-route claim-detail binding bug is properly fixed (`ClaimShell.tsx:32-39` guard); create flows carry idempotency keys.

**Weak:** the **A1 Sidebar CLM-1006 hardcoding (P0)**; monolithic pages with no extraction (`AiEvidencePage.tsx` 624L, `DashboardPage.tsx` 434L, 6+ inline sections each); **hardcoded segment counts** that never reflect live data; **zero unit/component tests** (the pure `filterClaimRows` at `ClaimsListPage.tsx:355` is exported and testable but untested); `Modal.tsx` has **no focus trap** (WCAG 2.1.2); `CustomersDirectoryPage` uses local `useState` instead of Redux (inconsistency).

### Special scenario — "search by CUST-T0205 vs by name"
**Root cause (code-traced, verified by the critic): missing server-side search + customerId never projected into the list row.**
- `GET /api/claims` takes **no search parameter at all** (`ClaimsController.cs:33-35`; `IClaimReadService.cs:12`). All "search" is **client-side** Redux filtering.
- `filterClaimRows` builds its haystack from **`id + customer(display name) + vehicle`** (`ClaimsListPage.tsx:362-365`). `ClaimRow` has **no `customerId`** (`types/index.ts:18-30`); `ClaimListItemDto` has **no `Customer_Id`** (`Contracts/Claims/ClaimListItemDto.cs:4-15`); `HybridClaimReadService.cs:43-46` maps only the display name into the row.
- **Therefore:** searching the **customerId `CUST-T0205` cannot match** (it isn't in any list field), while searching the **display name can**. This is the **inverse** of the reported "ID found it, name didn't" — which means the *reported* observation points to a **secondary data-population issue on the created claim's display-name field** (or a reload/timing effect) that needs a 60-second manual reproduction to pin down.
- **Classification: (b) missing search-by-customerId feature + (d) backend projection gap.** Fix = add `customerId` to `ClaimListItemDto` + `ClaimRow`, populate it in the read mapper, add it to the haystack, and add a real `?search=` parameter on the claims list API.

---

## 8. Backend / service-boundary audit

**Score 6.5 / 10 (critic cut from 7).**

**Strong (independently verified):** 6 DbContexts each with its **own SQL schema** (`HasDefaultSchema`: claims / customers_policies / documents / ai_analysis / approval / audit_cost), **zero cross-service assembly refs**, cross-service coupling via **typed delegates at the composition root** (`Program.cs:129-151`), and a **three-layer advisory-only AI model**: `GuardrailFlags.Advisory` is a private-ctor singleton with all `Can*` false and **no public setter** (reflection-tested, `AiGuardrailTests.cs:78-91`) + a 14-forbidden-phrase evaluator + a system-prompt prohibition. Uniform `ApiErrorResponse(Code,Message,TraceId)` on 4xx; `IDbContextFactory<T>` singleton-safety throughout.

**Weak:** the sync-over-async hot path (A4); **AI metadata leaks into the Claims schema** (`Claim.cs:32-36` carries `TraceId/RunId/Tokens/Cost/DurationSec` — a cross-bounded-context leak); **no global exception handler** (A7); business logic (`NetPayout = Math.Max(0, Amount-Deductible)`, currency default) sits in the controller (`ClaimCommandsController.cs:514-516`) rather than the service; command + audit + outbox are **3 separate non-atomic** `SaveChangesAsync` calls (acknowledged as a future gate); anemic entities (acceptable for a demo, but ≠ DDD aggregates).

> **Critic note:** Backend's own register rated the 603L controller "not a fat controller / no change needed" while OOP/SOLID correctly rated the same duplicated-7× choreography a P1 SRP violation. The 7→6.5 cut corrects this self-generous framing.

---

## 9. EF Core / SQL / persistence audit

**Score 6.5 / 10.**

**Strong:** 6 isolated schemas + per-schema migrations-history; **11 coherent additive migrations all with `Down()`**; idempotent seeders (`AnyAsync` guards); `IDbContextFactory<T>` + `AddDbContextFactory(Singleton)`; `CustomersPolicies` reads use `AsNoTracking` + server-side `Select` projection + `EF.Functions.Like` pushed to SQL + pagination; **connection-string hygiene CLEAN** (only a localdb dev string in `appsettings.Development.json` / `SeedConstants`; no cloud credential in any tracked file).

**Weak:** **missing indexes** on `claims.Status`, `claims.CustomerId`, `ai_analysis.AiAnalysisRuns.ClaimId`, `documents.ClaimDocuments.ClaimId` (the hottest query columns); `GetAllClaimsAsync` no projection/`AsNoTracking`; **ID-allocation race** (A10); blocking `.GetAwaiter().GetResult()` (A4); **no `EnableRetryOnFailure`** on any of the 6 SQL connections (Azure SQL serverless cold-starts will throw transients); `CUST-4421` invisible (A14); outbox idempotency key `IsUnique(false)` → app-enforced TOCTOU; `DbMigrator` log-honesty (A13).

**Data-surface map:** SQL-backed = customers/policies/vehicles (201 each), claims (15), documents (14), approval, audit_cost, ai_analysis. In-memory fixtures = CLM-1006..1010 detail + all sub-resources (shadow SQL via `HybridClaimReadService`). Mock = AI output (`RealCallsEnabled=false`). **Gap:** the hybrid split means seed-claim mutations are silently dropped on re-read.

---

## 10. Azure / infra / DevOps audit

**Score 7 / 10 — the strongest axis (and the softest-toned auditor; tone flagged, score upheld).**

**Strong:** **no accidental deploy on push** — `azure-deploy-demo.yml` is `workflow_dispatch`-only, double-gated (`if confirm=='DEPLOY'` **and** deploy steps commented out); genuine cost engineering (`minReplicas:0`, SQL auto-pause 60m, SWA Free, GHCR over ACR, `enableAi/enableSql=false` defaults); **passwordless IaC design** (Entra-only SQL module, `allowSharedKeyAccess:false`, RBAC Key Vault, UAMI); multi-stage non-root Dockerfile (port 8080); App Insights sampling + 1 GB daily cap + 30-day retention; **secret scan CLEAN** across infra/workflow/appsettings/staticwebapp.

**Weak:** the **live SQL uses SQL-auth + password** (stored as a Container App secret) while the **Bicep specifies Entra-only** — honest in the runbook doc but **invisible to IaC-only review**; **Log Analytics shared key via `listKeys().primarySharedKey`** (`container-apps.bicep:36`) surfaces the key in ARM deployment history (use `workspaceResourceId`); `staticwebapp.config.json` missing **CSP/HSTS**; budget is **portal-manual only** (no IaC); cross-region SQL (germanywestcentral) vs ACA (westeurope) leaves a permanent Bicep↔reality mismatch.

> The portfolio docs are notably honest (explicit "list endpoints 500 without SQL", "AI is Mock", "demo auth client-side", a "do-not-say" list) — a real differentiator vs demos that present mock data as production evidence.

---

## 11. Test-quality audit

**Score 6 / 10 (critic cut from 6.5).**

**Strong:** the 137-test xUnit suite is **genuinely semantic** — real EF-InMemory persistence round-trips reading back from a fresh context (`AiOrchestratorPersistenceTests.cs:69-108`), idempotency-key dedup with DB-level proof (`ClaimCommandTests.cs:161-194`), 10+ forbidden-phrase guardrail cases, **architecture boundary enforced as a reflection test** (`ServiceSkeletonTests.cs:126-150`; `PersistenceSeedTests.cs:349-383`), a credible `WebApplicationFactory` harness. Playwright includes a real **regression spec** (`e2e/21-created-claim-detail-binding.spec.ts`) and a **DB-reload persistence** spec (`e2e/18`).

**Weak:** **the claims list has no server-side search**, so "search by name/id/vehicle" is **untested at the HTTP layer** and every e2e search uses claim-id strings only; **zero React unit/component tests** (all 21 specs need a live full stack); `e2e/16` has a **soft-pass escape hatch** (`annotations.push({type:'backend-variation'})` → passes even if approval is broken) = COULD-PASS-WHILE-PRODUCT-WRONG; EF-InMemory **skips constraint/FK enforcement** so the 11 real SQL migrations are never exercised.

> **Critic's decisive point:** the green **137/137 is structurally blind to all the headline P0s** — the ID-format bug needs >9999 rows, the hardcoded saga is undispatched, and the deadlock needs concurrency the serial harness can't produce. A green suite here does **not** protect the actual risk surface.

| Mandatory flow | Coverage |
|---|---|
| Approval write | STRONG (`ClaimCommandTests.cs:40-63` + e2e/16) |
| Payout simulation | STRONG (`SandboxSurfaceTests.cs:175-205`, negative incl.) |
| Document upload/metadata | STRONG (`SandboxSurfaceTests.cs:143-168`) |
| Search by customer **name** | **MISSING** |
| Search by customer **id** | **MISSING** |
| Search by **claim number** | BRITTLE (client-side filter only, no backend contract) |
| Search by **vehicle** | **MISSING** |
| DB-created claim **detail binding** | STRONG (`e2e/21`) |

---

## 12. Metrics / indexes

| Metric | Value | Method |
|---|---|---|
| Backend `.cs` files / LOC | 174 / 14,214 | MEASURED (`find`+`wc`) |
| .NET projects | 9 | MEASURED |
| DbContexts | 6 | MEASURED (`grep : DbContext`) |
| EF migrations | 11 (+6 snapshots) | MEASURED |
| Backend tests | **137/137 PASS** (4 s) | MEASURED (`dotnet test`) |
| `[Fact]`/`[Theory]` attrs | 107 across 15 files → 137 cases | MEASURED |
| Frontend TS/TSX files / LOC | 101 / 12,588 | MEASURED |
| Frontend unit tests | **0** | MEASURED |
| Playwright e2e specs | 21 | MEASURED |
| Largest backend file | `ClaimCommandsController.cs` 603L | MEASURED |
| Largest frontend file | `backendInsuranceApi.ts` 822L | MEASURED |
| Cross-service assembly refs | **0** | MEASURED (csproj) + reflection test |
| Sealed (immutable) records | 81 | INSPECTED |
| Bicep modules | `main` + 9 | MEASURED |
| Missing hot-column indexes | ≥ 4 | INSPECTED |

---

## 13. P0 / P1 / P2 issue list

**P0 — production blockers (4)** *(live vs masked noted):*
1. **CLM-1006 hardcoded navigation** (Sidebar + 5 navs) — **LIVE, demo-visible** broken multi-claim nav. `Sidebar.tsx:27-34`.
2. **Hybrid stale reads** — DB mutations to CLM-1006..1010 invisible on re-read; created claims 404 on sub-resources. **LIVE on the showcase claim.** `HybridClaimReadService.cs:61-125`.
3. **`CLM-{0000}` vs `^CLM-\d{4}$`** — claim creation bricks once a 5-digit sequence exists. **Latent.** `PersistenceClaimsService.cs:45` / `ClaimsControllerBase.cs:14`.
4. **Sync-over-async deadlock vector** on the hot read path — **masked at single-user load.** `HybridClaimReadService.cs:40,64`.

**P1 — serious (11):** A5 live wrong-write modal · A6 dead-code saga trap · A7 no global exception handler · A8 write-only outbox · A9 missing indexes + unprojected list load · A10 ID-allocation race · claims-search missing + customerId not projected (the reported scenario) · AI-metadata cross-BC leak in Claims schema · hand-mirrored FE/BE DTOs (no codegen) · mock fixtures coupled into Redux logic layer + 9 pages · LAW `listKeys()` key exposure · IaC↔reality SQL-auth divergence · fat pages (624L/434L) · zero FE unit tests + e2e soft-pass.

**P2 — polish (14+):** `DbMigrator` log honesty · `CUST-4421` invisible · no-op date filter / hardcoded segment counts / silently-disabled tabs · Cyrillic strings bypassing i18n · no Modal focus trap · no `EnableRetryOnFailure` · outbox key TOCTOU · missing CSP/HSTS · 4 modal `claimId='CLM-1006'` defaults · `CustomersDirectoryPage` non-Redux · `ClaimDetailsDto` mixed concerns · anemic entities · non-atomic command writes · 150s startup probe / portal-only budget / cross-region SQL.

---

## 14. What is actually strong (defensible in an interview)

1. **Bounded-context discipline** — 6 schema-isolated DbContexts, **zero** cross-service refs enforced by csproj **and** a reflection test, delegate-based cross-service wiring. This alone separates it from CRUD.
2. **AI-safety model** — structurally unflippable `GuardrailFlags` (private ctor, no setter, reflection-tested) + 14-phrase evaluator + system-prompt prohibition; AI is advisory-only with **no path** to mutate claim status (grep-verified).
3. **Executed, cost-engineered Azure IaC** — real live deployment, scale-to-zero, RBAC Key Vault, `workflow_dispatch`-only double-gated pipeline, **CLEAN secret scan**.
4. **Genuinely semantic backend tests** — persistence round-trips, idempotency dedup proof, architecture-as-a-test.
5. **Honest engineering posture** — "deployed vs deferred" docs, concessions labeled in code, structural mock/backend parity (`typeof mockInsuranceApi`), compile-time i18n key parity, 81 immutable records.

## 15. What is still demo / unfinished

The four P0s (live broken nav, stale golden-claim reads, ID-format brick, deadlock vector); write-only "outbox"; no global exception handler; missing indexes + unprojected list query; claims search is client-side-only with no backend contract and no customerId projection; demo data (CLM-1006, mock fixtures) wired into the **Redux logic layer and Sidebar**; zero frontend unit tests; AI is Mock; demo auth is client-side; data is synthetic. **The green test suite does not cover any of the four P0s.**

---

## 16. Igor-facing summary (Ukrainian)

**Це не "junior CRUD" і не звичайний прототип.** Архітектура зроблена свідомо і підтверджена кодом, а не на словах: шість окремих bounded-context'ів, кожен зі своєю SQL-схемою та власним DbContext; **нуль перехресних посилань між сервісами** (це закріплено навіть reflection-тестом); крос-сервісна взаємодія — через делегати в composition root, а не прямі залежності; трирівнева "advisory-only" модель безпеки AI (прапорці без сеттера + евалюатор на 14 заборонених фраз + system-prompt), покрита тестами; 81 immutable record; audit/outbox з idempotency-ключем і доказом дедуплікації в тестах; і **реально розгорнута** Azure-інфраструктура (scale-to-zero, RBAC Key Vault, деплой лише вручну з подвійним запобіжником, чистий secret-scan). Backend-тести — змістовні (справжні round-trip'и в БД, а не просто "200 OK"). Це рівень людини, яка читала Clean Architecture / DDD / BFF і застосувала дисципліновано.

**Але це ще НЕ enterprise-production — і це треба говорити прямо.** Є реальні, не косметичні дефекти. Один **видимий одразу**: у Sidebar усі під-посилання захардкоджені на демо-кейс CLM-1006, тож для будь-якого іншого кейса навігація по під-розділах веде на CLM-1006. Інші серйозні: гібридний read-шар повертає закешовані in-memory дані для золотих кейсів (зміни в БД не видно при перечитуванні); генератор ID ламає створення кейсів, щойно з'явиться 5-значний номер; sync-over-async на гарячому шляху читання — потенційний deadlock під навантаженням; "outbox" пишеться, але ніким не споживається; немає глобального обробника помилок; немає індексів на найгарячіших колонках; і **нуль frontend unit-тестів** при зеленому backend-наборі, який структурно **не ловить жоден** із цих P0-дефектів. Підсумок чесний: **сильний, свідомий, захищабельний на співбесіді інженерний демонстратор — але з дефектами, що дискваліфікують його від production.** Більшість компромісів автор сам позначив у коді та документації — це ознака демонстратора, а не прихованих проблем.

---

## 17. Recommended next gates

| Gate | Why | Risk if skipped | Effort | Before Igor/tech-lead demo? |
|---|---|---|---|---|
| `FIX_CLM1006_HARDCODE_NAV` (Sidebar + 5 navs + 4 modal defaults + remove dead approvalSaga) | Kills the one LIVE, demo-visible P0 + wrong-write traps | Demo breaks on first click of a non-golden claim's sub-nav | S (½ day) | **YES** |
| `FIX_HYBRID_READ_STALE_AND_SUBRESOURCES` | Golden-claim writes visible on re-read; created claims show sub-resources from SQL | "Save draft → re-read" shows stale data | M | **YES if demo mutates a claim** |
| `FIX_CLAIMID_FORMAT_AND_RACE` (regex `\d{4,}` + DB sequence/identity) | Removes the creation-brick + concurrency race | Unrecoverable once a 5-digit id lands | S | Recommended (cheap) |
| `MAKE_READ_PATH_ASYNC` (`IClaimReadService` → `Task<>`) | Removes the deadlock/thread-starvation vector | Prod outage under concurrency | M | Before any load/concurrent demo |
| `CLAIMS_SEARCH_BACKEND` (server-side `?search=` + project `customerId` + tests) | Fixes the reported search scenario; makes search real | Search looks broken/partial to a probing reviewer | M | If search is demoed |
| `API_ROBUSTNESS` (global exception handler + `EnableRetryOnFailure` + indexes + list projection) | Stops 500 leakage, cold-start transients, full scans | Ugly errors + slow lists | M | Recommended |
| `OUTBOX_HONESTY` (implement a processor **or** rename to `AuditLog`) | "Outbox" currently misrepresents the architecture | Interview credibility hit if probed | S–M | Before an architecture interview |
| `FRONTEND_UNIT_TESTS` (vitest: `filterClaimRows`, slices, sagas) | Closes the zero-unit-test gap; protects logic | Regressions slip; weak test story | M | Optional |
| `DTO_CODEGEN` (Swashbuckle → openapi-typescript) | Ends hand-mirrored DTO drift | Silent FE/BE contract drift | S–M | Optional |
| `PRODUCT_I18N_QA_POLISH` (Cyrillic→catalog, date filter, segment counts, disabled tabs) | Removes visible polish gaps for the EN-default story | Minor but reviewer-visible | S | Optional |

---

## 18. Forbidden-scope confirmation

| Constraint | Status |
|---|---|
| No source edits | ✅ (read-only; 0 source files changed) |
| No commit | ✅ |
| No push | ✅ (handoff files **created, not pushed** — awaiting authorization) |
| No migrations / seed / deploy | ✅ |
| No Azure / resource / provider changes | ✅ |
| No DB changes | ✅ |
| No real AI enabled | ✅ |
| No secrets printed/committed | ✅ (audit confirmed repo secret-scan CLEAN; no conn-strings / subscription / tenant / object IDs / passwords reproduced) |
| No real PII in this report | ✅ (created test customer referenced as `CUST-T0205`, not by name) |

---

### Agent trace / verification

- **Agent trace:** 1 main (Opus 4.8) orchestrator → 1 Workflow run (`wiv1uz43p`): **8 Sonnet** read-only dimension auditors (parallel) → **1 Opus** adversarial anti-flattery critic (barrier). 9 subagents, ~997K subagent tokens, 608 tool uses.
- **Risk profile used:** READ-ONLY (no mutation).
- **Workflow selected:** scout inline (git state + inventory + `dotnet test`) → parallel dimension audit → Opus adversarial reconciliation → synthesis.
- **Verification evidence:** `git rev-parse`/`status` (HEAD 70af774, clean); `dotnet test` 137/137; `find`/`grep` metrics; every finding carries `file:line`; the critic independently re-verified the P0s against source (incl. PowerShell check of the ID-format/regex mismatch).
- **Files changed:** none in `C:/Projects/InsuranceAIPlatform`. Created (not committed/pushed): the 3 gpt-handoff files below.
- **Not touched:** source tree, Azure, DB, `main`, secrets, git history.
