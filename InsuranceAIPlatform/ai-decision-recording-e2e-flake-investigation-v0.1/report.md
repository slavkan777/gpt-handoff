# Gate Report — AI_DECISION_RECORDING_E2E_FLAKE_INVESTIGATION_V0.1

**Project:** InsuranceAIPlatform
**Date (UTC):** 2026-05-29
**Type:** read-mostly investigation + reproducibility + root-cause evidence
**Executor model/version:** Opus 4.8 (1M context) — `claude-opus-4-8[1m]`. *Prior gates in this project ran on a Sonnet-default / Opus-critical mix. Per the MODEL DRIFT GUARD: the gate was followed exactly as written regardless of model change — evidence and stop-lines govern, not model assumptions.*
**Repo:** `C:/Projects/InsuranceAIPlatform` (`slavkan777/InsuranceAIPlatform`)
**Branch:** `dev`
**HEAD before / after:** `03725241c8dfdbed7ce17db61fb51d9d7f211116` / `03725241…` *(unchanged — investigation only)*
**origin/main:** `69e67312a10cc9bcf28c4e387a126b48c91fb9c5` *(unchanged)*

---

## Verdict

**DIAGNOSED_PASS_ACCUMULATED_STATE** — refined to **TRANSIENT_TIMING / ENVIRONMENT FLAKE**.

The prior gate's `e2e/06-claim-actions.spec.ts:54` failure (88/89 on 2026-05-28) **does not reproduce** on the same working tree today. Three green runs:

| Run | Scope | DB state | Result | Wall |
|---|---|---|---|---|
| A | `e2e/06` isolated | accumulated | **5 / 5 PASS** | 54.4s |
| B | `e2e/06` isolated (re-run) | accumulated | **5 / 5 PASS** | 32.7s |
| C | **full suite (89 tests)** | accumulated | **89 / 89 PASS** | 4.1m |

No source fix is required. **89/89 is restored on this exact working tree** — the original commit/push blocker is cleared.

---

## Root cause (best supported)

The 2026-05-28 failure was a **transient timing flake**, not a product defect and **not DB-row-volume**:

- The AI-decision confirmation assertion uses a **tight `{ timeout: 15000 }`** (`e2e/06-claim-actions.spec.ts:72-74`), while the *analysis-completion* assertion just above it uses `{ timeout: 30000 }` (`:61-64`). The `POST /api/claims/CLM-1006/ai-decision` round-trip does *more* work than a read (latest-run lookup + audit insert + outbox insert), so under machine load / a long serial run it can occasionally exceed 15s, leaving the button stuck in `Збереження…` — exactly the symptom the prior gate captured.
- Today, on a less-loaded machine, the same round-trips were fast (backend logs show EF `DbCommand` 34–40 ms) and every assertion was met well inside its window.

### DB-row-volume is empirically ruled out

| Table | Before runs | After 3 runs |
|---|---|---|
| `ai_analysis.AiAnalysisRuns` | 50 | **57** (CLM-1006: 55) |
| `audit_cost.AuditEvents` | 178 | **207** |
| `audit_cost.OutboxMessages` | 174 | **203** |

Accumulation **grew** across the three runs, yet all three passed 100%. If row count were the cause, today's runs (with *more* rows than yesterday's failure) would have failed harder, not passed. They are also modest sizes where a sort/scan is sub-millisecond.

### Code review (no structural defect found)

- `PersistenceAuditCostService.cs:45-101` and `PersistenceAiAnalysisOrchestrator.cs:99,221` both use `IDbContextFactory<T>.CreateDbContextAsync()` with `await using` — **proper disposal, no captive-dependency, no connection leak**.
- For **CLM-1006** (the failing test's claim) the controller's `ClaimExists()` resolves from the **in-memory** seed (`HybridClaimReadService.cs:61-62`) — it does **not** touch the sync-over-async `.GetAwaiter().GetResult()` bridge. So that known smell is **not** on this failing path.
- The only growth-sensitive queries are `GetLatestAsync` (`PersistenceAiAnalysisOrchestrator.cs:228`, non-sargable `OrderByDescending(r => r.CreatedAtUtc ?? DateTime.MinValue)`) and the outbox idempotency lookup (`PersistenceAuditCostService.cs:82-83`). Both are negligible at 57 / 203 rows. They are latent inefficiencies, not the cause of a 15s hang.

---

## What was actually done

1. **Bootstrap** — confirmed repo/branch/HEAD/origins; 0 staged; 97 working-tree entries preserved.
2. **Source reconciliation** — found `CURRENT_STATE.md` is **stale** (`READY_FOR_COMMIT_GATE` / 89/89) vs `LATEST_CHAT_HANDOFF.md` + the commit-push report (`BLOCKED` / 88/89). Used latest execution evidence per the reconciliation order; **did not edit AIKB** (out of scope).
3. **Safety scan** — clean (no secret-value patterns; no `DEEPSEEK_API_KEY` value; no `Password=` connection strings).
4. **Located reset/seed path** — `server/InsuranceAIPlatform.DbMigrator` + 6 per-service seeders. DB target unambiguous: `InsuranceAIPlatform` on `(localdb)\MSSQLLocalDB` (never DevDept). `Program.cs` does **not** migrate/seed on startup (lazy connection) — schema+seed come only from DbMigrator.
5. **Measured DB accumulation** (sanitized counts only).
6. **Reproduction** — 3 runs (above). Target failure did not reproduce.
7. **Semantic-regression check** — `e2e/21` both cases PASS within the 89/89 (not weakened).
8. **Classification + recommendation** — below.

### DB reset decision: **SKIPPED (justified)**

A drop+reseed was available via `DbMigrator` but is **not needed**: `e2e/06` passes on the *accumulated* DB in isolation, which already rules out row-volume. A destructive drop would have no diagnostic value and would needlessly destroy synthetic state. Skipped per the gate's `SKIPPED_SAFE_REASON` option.

---

## Boundary confirmations

| Boundary | Status |
|---|---|
| Product source edited | NO |
| Test files edited | NO |
| Source `git commit` / `git push` | NO |
| Azure / provider config touched | NO |
| `DEEPSEEK_API_KEY` read/logged/pasted | NO (provider resolved to **Mock** — confirmed in logs) |
| DevDept DB touched | NO |
| AIKB rules changed | NO |
| `e2e/21` weakened | NO (PASS, untouched) |
| Real PII / real payout / external send | NO |

---

## Next recommended gate

**Recommended (small, test-only): `E2E_06_AIDECISION_TIMEOUT_HARDENING_V0.1`** — then re-open `COMMIT_AND_PUSH_DEV_REALISTIC_SANDBOX_BATCH_V0.1`.

89/89 is restored, so the commit gate is unblocked **now**. But because the flake is load-dependent, it *could* recur during the commit gate's own verification run. A tiny **test-infrastructure-only** hardening makes that run reliable:

- `e2e/06-claim-actions.spec.ts:72-74` — raise the `ai-decision-recorded` visibility assertion `{ timeout: 15000 }` → `{ timeout: 30000 }` (match the analysis-completion assertion in the same test).
- `playwright.config.ts:29` — `retries: process.env.CI ? 1 : 0` → `retries: 1` (or `CI ? 2 : 1`) so a transient flake auto-recovers locally **and** `trace: 'on-first-retry'` captures a step-replay for any future occurrence.
- DONE STATE: 89/89 stable across **2 consecutive full-suite runs**; `e2e/21` untouched; no product source change.

These edits do **not** weaken any semantic/negative assertion and do not touch product code. They were **not** implemented in this gate per the STOP LINE.

**Alternative:** the operator may re-open the commit/push gate directly (accepting a small residual flake risk during verification).

**Optional housekeeping:** `CURRENT_STATE.md` is stale (89/89 / `READY_FOR_COMMIT_GATE`); a tiny AIKB refresh could align it with the latest evidence. Out of scope here.

---

## Limitations

1. Timing evidence is from one workstation on one day; a load-dependent flake's non-reproduction today does not prove it can never recur — hence the timeout-hardening recommendation.
2. DB reset deliberately not performed (justified above) — so "fresh-DB" behavior was inferred from the accumulated-DB isolated pass, not directly executed.
3. Single-browser (Chromium) coverage, as per the suite's design.
