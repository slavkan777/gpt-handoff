REQUEST_ID: REQ-2026-06-05-insuranceai-qdrant-retrieval-adapter-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-implementation
PROJECT: InsuranceAIPlatform
GATE: QDRANT_RETRIEVAL_ADAPTER_V0.1
COMPLETED_BY: claude

# Qdrant Retrieval Adapter — v0.1

Closes the semantic gap flagged in the previous gate: Qdrant is no longer merely probe-reachable, it
now **actually stores and serves** claim evidence vectors when enabled and reachable, with a total,
silent fallback to the in-process deterministic index when it is not. `backend=qdrant` is now reported
only after a real Qdrant retrieval round-trip — never from a reachability flag.

## Routing Lock Verification

| Lock | Expected | Observed | OK |
|---|---|---|---|
| PROJECT | InsuranceAIPlatform | InsuranceAIPlatform | ✅ |
| SOURCE_REPO | C:/Projects/InsuranceAIPlatform | C:/Projects/InsuranceAIPlatform | ✅ |
| SOURCE_REPO_REMOTE | slavkan777/InsuranceAIPlatform | slavkan777/InsuranceAIPlatform | ✅ |
| Branch | rag/local-foundation-mega-v0.1 | rag/local-foundation-mega-v0.1 | ✅ |
| HANDOFF path | gpt-handoff/InsuranceAIPlatform/ | gpt-handoff/InsuranceAIPlatform/ | ✅ |

ROUTING_LOCK_RULE acknowledged; report written only to the three InsuranceAIPlatform project paths.

## Current Repo State

- Branch `rag/local-foundation-mega-v0.1`, HEAD `70af774` **unchanged** (no source commit/push — per BOUNDARIES).
- Working tree carries the uncommitted RAG feature (prior gates) + this gate's adapter files. No tracked file
  was committed; the only tracked file edited is `Program.cs` (+24 lines, DI registration).

## Qdrant Runtime Check

- Container `iap-qdrant` (qdrant/qdrant 1.18.2) reused, `Up`, listening on `0.0.0.0:6333`.
- Wire-format verified against the live container before coding (throwaway collection, then deleted):
  create / upsert / claim-filtered search confirmed. The claim filter proved to be a real leakage guard
  (a foreign-claim point with an identical vector to the query was correctly excluded by the filter).

## Implementation Summary

Minimal seam, no new NuGet (raw REST over `IHttpClientFactory`, same pattern as the runtime probe):

| File | Role |
|---|---|
| `Services/.../Rag/Retrieval/IQdrantVectorClient.cs` (NEW) | Vector-store boundary: `EnsureCollectionAsync` / `UpsertAsync` / `SearchAsync` + `QdrantUpsertPoint` / `QdrantSearchHit`. |
| `Services/.../Rag/Retrieval/IVectorRetrievalRouter.cs` (NEW) | `RankAsync` + `ResolveServingBackendAsync`; `RetrievalOutcome(Hits, Backend)`; `VectorBackends.{Qdrant,InMemoryHash}` constants. |
| `Services/.../Rag/Retrieval/VectorRetrievalRouter.cs` (NEW) | Tries Qdrant (ensure → upsert claim chunks → claim-filtered search → map back); on disabled/no-client/any-error → in-process index. |
| `Api/.../Rag/HttpQdrantVectorClient.cs` (NEW) | REST impl: `PUT /collections/{c}`, `PUT …/points?wait=true`, `POST …/points/search` (claimId filter). Deterministic MD5→GUID point ids (idempotent). Short timeout; never stalls. |
| `Services/.../Rag/RagService.cs` (MOD) | `+IVectorRetrievalRouter? router`; Ask/Search route via the router; `GetInfrastructureStatus` resolves the **honest** backend via a real round-trip. |
| `Services/.../Rag/RagServiceCollectionExtensions.cs` (MOD) | Registers the router; resolves `IQdrantVectorClient` with `GetService` so it stays optional. |
| `Api/.../Program.cs` (MOD) | `AddHttpClient("qdrant")` + `IQdrantVectorClient → HttpQdrantVectorClient`. |

Router and client are **optional** everywhere (defaulted null) — existing constructors and tests are untouched.

## Retrieval Semantics

- **Enabled + reachable + round-trip OK** → retrieval served by Qdrant; `backend=qdrant`.
- **Enabled + reachable but round-trip fails** (e.g. collection error) → in-process index; `backend=in-memory-hash`
  while `status=live_local`, `reachable=true`. This is the honest "up but not serving" state.
- **Enabled + unreachable** → `status=skipped_not_available`, `backend=in-memory-hash`.
- **Disabled** (default) → `status=disabled`, `backend=in-memory-hash`, Qdrant never contacted.
- **Leakage guard (two layers):** every point is tagged with `claimId`; search filters by `claimId`; results are
  then mapped back against the already-claim-scoped candidate set, so a stale/foreign point can never surface.

## Files Changed

NEW (5): `IQdrantVectorClient.cs`, `IVectorRetrievalRouter.cs`, `VectorRetrievalRouter.cs`,
`HttpQdrantVectorClient.cs`, `Tests/VectorRetrievalRouterTests.cs`.
MODIFIED (4): `RagService.cs`, `RagServiceCollectionExtensions.cs`, `Program.cs`, `Tests/RagInfrastructureTests.cs`.

## Commands Run

- `dotnet build InsuranceAIPlatform.sln -c Debug` → **Build succeeded, 0 Warning, 0 Error**.
- `dotnet test InsuranceAIPlatform.sln --no-build` → **Passed! Failed: 0, Passed: 174, Skipped: 0**.
- Live API spawned with `Rag__QdrantEnabled=true` on `:5284` (env at spawn; restarted process — env-snapshot rule).
- `curl` smoke: `/rag/infrastructure`, `/rag/ask`, and direct Qdrant REST queries. Process stopped after smoke.

## Verification Evidence (live CLM-1006 smoke — real, not stub)

| Evidence | Value |
|---|---|
| Qdrant collection BEFORE | `insurance_evidence` **doesn't exist**; `collections: []` |
| `/rag/infrastructure` vectorRuntime | `status=live_local, enabled=true, backend=qdrant, reachable=true` (corr `22edd635…`) |
| Qdrant collection AFTER | `status=green, points_count=13, size=256, distance=Cosine` |
| Direct Qdrant filtered search | returns **only** CLM-1006 payloads (`…coverage-check#0`, `…repair-detail#1`, `…police#0`) |
| `/rag/ask` | HTTP 200; trace `ragtrc_3abb3e39`; 4 citations, retrievedChunkIds all `CLM-1006-*`; `providerMode=Mock`; advisoryOnly=true |
| API process log (named client `qdrant`) | real REST: `GET /`, `GET/PUT /collections/insurance_evidence`, `PUT …/points?wait=true`, `POST …/points/search` → localhost:6333 |
| SQL / index | sql healthy (8/50/21/4); index healthy (13/13 embedded, `local-hash-embed-v0.1`, 256d) |

**Stub-fingerprint check (G5):** the fallback fingerprint is an **empty** Qdrant + `backend=in-memory-hash`.
Observed = 13 live points + `backend=qdrant` + real outbound REST → the stub path is definitively excluded.

## Tests

`dotnet test` = **174 passed / 0 failed** (was 165; +9 net, 1 corrected).

- `VectorRetrievalRouterTests` (NEW, 9 cases): disabled→in-memory (Qdrant untouched); serving client→`qdrant`;
  outage→in-memory fallback; no-client→in-memory; **foreign-chunk hit dropped (leakage guard)**;
  `ResolveServingBackend` qdrant/outage/disabled; infra-status `qdrant` with serving adapter;
  infra-status **outage → in-memory-hash, NOT a fake qdrant label**; `AskAsync` drives upsert+search through Qdrant.
- `RagInfrastructureTests` (MOD): the prior test that asserted `reachable ⇒ backend=qdrant` (the dishonest
  conflation) was corrected to assert reachable-without-serving-adapter → `in-memory-hash`.

## Boundaries Honored

No source commit/push (HEAD `70af774` unchanged) · no Azure/cloud · no paid providers · no secrets (scan clean) ·
no real PII · not on main · no schema/EF migration (zero entity/migration changes) · **no Ollama work** ·
no unrelated project edits · **no fake `backend=qdrant`** (only after a real round-trip).

## Known Limitations

1. The `/rag/ask` response does not carry a per-request backend field; "ask routes through Qdrant" is supported by
   the combination (infra `backend=qdrant` + 13 points upserted live + the `AskAsync` unit test), not a response field.
2. Upsert runs on each retrieval/status call (idempotent via deterministic ids) — no changed-only/incremental upsert yet.
3. Embeddings remain the deterministic local-hash model (256d); Qdrant now stores/serves **these** vectors — embedding
   quality is a separate concern, out of scope here.
4. Cross-claim `similar-claims` still uses the in-process centroid ranker, not Qdrant (claim-scoped retrieval was the target).
5. Qdrant Cosine scores differ slightly from in-process `VectorMath.Cosine` (Qdrant internal normalization); ordering is
   consistent — byte-identical scores are not asserted.
6. Frontend not modified; the existing Vector layer panel already renders `backend`/`reachable`, so it shows `qdrant` live.

## Not Touched

Product source commits/branches; `main`; Azure/cloud; secrets/PII; EF schema & migrations; Ollama/LocalLlama seam;
TwinCore and other projects; global `gpt-handoff/_BRIDGE/`; other project bridges.

## Next Safe Step

Tell GPT `отчёт` to audit this report. Candidate follow-ups (await an explicit prompt; not started here):
(a) incremental/changed-only upsert + a real embedding model behind `IEmbeddingProvider`;
(b) route `similar-claims` through Qdrant too;
(c) owner-approved Ollama install for a genuine `live_local` reasoning runtime;
(d) commit-only gate for the uncommitted RAG code.

STOP after report — no commit, push, deploy, Azure, or unrelated-project changes performed.
