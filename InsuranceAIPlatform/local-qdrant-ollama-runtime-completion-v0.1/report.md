REQUEST_ID: REQ-2026-06-04-insuranceai-local-qdrant-ollama-runtime-completion-v0-1
STATUS: READY_FOR_AUDIT
TASK_TYPE: project-implementation
PROJECT: InsuranceAIPlatform
GATE: LOCAL_QDRANT_OLLAMA_RUNTIME_COMPLETION_AND_PRE_FINAL_AUTOMATION_V0.1
COMPLETED_BY: claude
COMPLETED: 2026-06-05

## Current State
Repo `C:\Projects\InsuranceAIPlatform`, branch `rag/local-foundation-mega-v0.1`, HEAD `70af774`.
All work is UNCOMMITTED on the feature branch (per request OUT OF SCOPE: no source commit/push).
The RAG infrastructure-status path now reports Qdrant (vector) and Ollama (local reasoning) runtime
state from a MECHANICAL HTTP probe instead of guessing from a config flag. Both local runtimes are
absent on this machine, so the honest status is `skipped_not_available` with the in-process
deterministic index as the safe vector fallback. Existing RAG behaviour (SQL source of truth,
hash-embedding index, mock generation, audit, citations, similar-claims) is preserved and green.

## Runtime Discovery (mechanically checked, not guessed)
- Qdrant `http://localhost:6333` -> no response (curl HTTP `000`). NOT running.
- Ollama `http://localhost:11434` -> no response. NOT running.
- `docker ps` -> no qdrant/ollama container (docker not active).
- Conclusion: both runtimes UNAVAILABLE locally -> labels `VECTOR_RUNTIME_SKIPPED_NOT_AVAILABLE`
  and `LLAMA_RUNTIME_SKIPPED_NOT_AVAILABLE`. No model was downloaded (per request constraint).

## DONE Criteria vs Evidence
1. Project bridge honored — this report echoes the exact REQUEST_ID + GATE; written to the three
   project-specific paths. Does NOT claim the stale `LOCAL_RAG_INFRASTRUCTURE_UI_DEMO_V0.1` as current. OK
2. Runtime availability MECHANICALLY checked — new `IRagRuntimeProbe` / `HttpRagRuntimeProbe` issues a
   short-timeout (1500 ms default; 800 ms in smoke) GET; connection-refused/timeout => not reachable.
   Verified live (see Verification) that the real probe vs DOWN runtimes returns `skipped_not_available`. OK
3. Existing RAG infrastructure preserved — Evidence Intelligence Stack still renders; SQL source of
   truth healthy; in-process hash embedding cache intact as fallback; 165 backend tests + 12 RAG e2e
   green. OK
4. Local runtime integration added disabled/fallback-safe — `RagOptions.QdrantEnabled` and
   `LocalLlamaEnabled` default FALSE; when enabled-but-unreachable the status is honest and the
   in-process index serves vectors (`backend = in-memory-hash`). No paid provider, no Azure, no key,
   no secret, no PII. OK
5. UI/API honest — status labels distinguish `disabled` / `live_local` / `skipped_not_available`;
   vector backend shows `in-memory-hash` vs `qdrant`; a `reachable` boolean is surfaced per runtime;
   no fake "live" label when a runtime is not reachable (e2e NEGATIVE_PASS enforces this). OK
6. Verification executed and reported — see below. OK
7. Fresh sanitized report published to all three project paths. OK

## Files Changed (writable scope only; all uncommitted on feature branch)
Backend (server/InsuranceAIPlatform.*):
- `Services.AiAnalysis/Rag/RagOptions.cs` — +QdrantEnabled (false), +QdrantEndpoint, +QdrantCollection, +RuntimeProbeTimeoutMs.
- `Services.AiAnalysis/Rag/Runtime/IRagRuntimeProbe.cs` — NEW interface (mockable reachability probe).
- `Api/Rag/HttpRagRuntimeProbe.cs` — NEW HTTP impl (any response => up; refused/timeout => down; never throws).
- `Services.AiAnalysis/Rag/Contracts/RagContracts.cs` — RagRuntimeStatus +Reachable; NEW RagVectorRuntimeStatus; aggregate +VectorRuntime.
- `Services.AiAnalysis/Rag/RagService.cs` — ctor +optional IRagRuntimeProbe; GetInfrastructureStatusAsync now probes Ollama+Qdrant and computes honest statuses.
- `Api/Contracts/Rag/RagDtos.cs` — RagRuntimeStatusDto +Reachable; NEW RagVectorRuntimeStatusDto; aggregate +VectorRuntime.
- `Api/Controllers/RagController.cs` — MapInfrastructureStatus maps VectorRuntime + Reachable.
- `Api/Program.cs` — AddHttpClient("rag-runtime-probe") + register IRagRuntimeProbe -> HttpRagRuntimeProbe.
- `Tests/RagInfrastructureTests.cs` — test1 +vector/reachable asserts; +StubRuntimeProbe; +5 probe-status tests.
Frontend (src/, e2e/):
- `api/insuranceApi.types.ts` — RagInfrastructureStatus +vectorRuntime +reachable.
- `api/mockInsuranceApi.ts` — ragInfrastructure + ragReindex return honest vectorRuntime (disabled / in-memory-hash) + reachable=false.
- `i18n/messages/rag.ts` — +infraLayerVector/infraFieldBackend/infraFieldReachable/infraVectorDisabledNote (EN + UK).
- `components/claim/RagInfrastructureStackPanel.tsx` — statusColor +live_local/+skipped_not_available; +reachable field; NEW Vector Runtime layer card (testid rag-infra-vector).
- `e2e/22-rag-evidence.spec.ts` — assert rag-infra-vector row visible.

## Commands Run
- Runtime discovery: `curl localhost:6333` -> `000`; `curl localhost:11434` -> no response; `docker ps` -> none.
- `dotnet build server/InsuranceAIPlatform.sln` -> Build succeeded, 0 Warning(s), 0 Error(s).
- `dotnet test ...Tests.csproj` -> Passed! Failed: 0, Passed: 165, Skipped: 0.
- `npm run build` (tsc -b && vite build) -> built, 154 modules, 0 type errors.
- `npx playwright test 22-rag-evidence --config playwright.mock.config.ts` -> 12 passed.
- Live HTTP smoke (real API, seams ENABLED, probe timeout 800 ms): see Verification.
- `git diff` secret/leak scan; `git status` forbidden-scope check.

## Verification Evidence
- Backend unit: 165 passed / 0 failed. New tests prove: enabled+reachable->`live_local`; enabled+unreachable->`skipped_not_available`; disabled->`disabled` (probe not called even if it would succeed); Qdrant fallback backend `in-memory-hash`.
- Frontend build: `tsc -b` clean (EN/UK i18n key parity holds via `type T = typeof en`); `vite build` ok.
- E2E spec 22: 12/12 passed incl. MECHANICAL (4 layer rows incl. vector), SEMANTIC (runtime=disabled in mock), NEGATIVE (runtime row never claims live), cross-claim isolation.
- LIVE HTTP smoke (the path unit tests stub — now real): started the real API with `Rag__LocalLlamaEnabled=true Rag__QdrantEnabled=true Rag__RuntimeProbeTimeoutMs=800` while Qdrant:6333 and Ollama:11434 were DOWN. `GET /api/claims/CLM-1006/rag/infrastructure` (fresh correlationId, real DB: sql=healthy, index=healthy) returned:
  - `vectorRuntime: status=skipped_not_available, enabled=true, reachable=false, backend=in-memory-hash`
  - `localReasoningRuntime: status=skipped_not_available, enabled=true, reachable=false`
  The real `HttpRagRuntimeProbe` handled connection-refused gracefully (no hang) and produced honest status.
- Secret/leak scan over the diff + new files: no API keys / secrets / connection-string values / Azure URLs (only the pre-existing `connectionString` variable reference). No raw runtime endpoint is exposed in the API response (only `endpointConfigured` boolean + `backend` label).

## Runtime Status Labels (final, honest)
- Vector runtime (Qdrant): default `disabled`; enabled+unreachable `skipped_not_available`; enabled+reachable `live_local`. Backend serving vectors: `in-memory-hash` (fallback) or `qdrant` (only when live).
- Local reasoning runtime (Ollama): default `disabled`; enabled+unreachable `skipped_not_available`; enabled+reachable `live_local`. Generator remains the deterministic mock until a real local call lands.

## Boundaries Honored
- No source commit / push (all changes uncommitted on feature branch).
- No Azure, no cloud resource, no paid provider, no API key, no secret, no real PII.
- No new EF migration (changes are options/records/DTO/UI/tests only — no schema change).
- Did not touch other projects, TwinCore, AgentHub, global `_BRIDGE`, or `main`. No force-push.
- Did not download any model.

## Known Limitations
- Qdrant and Ollama are not installed/running on this machine, so a real `live_local` path could not be
  exercised end-to-end; it is covered by a unit test using a stub probe (enabled+reachable->live_local).
  Bringing them up (container / model pull) is a separate, owner-approved step (software install /
  download) and was intentionally NOT performed.
- No full Qdrant vector-store retrieval adapter was built: while Qdrant is absent it cannot be verified,
  so this gate delivers honest status + a disabled/fallback-safe seam (mirroring the existing
  LocalLlama seam), not an unverifiable live retrieval path.

## Not Touched
- Source repo commits/pushes; `main`; other projects; global bridge; Azure; secrets; EF schema/migrations.

## Next Safe Step
- Architect GPT audit of this report (`отчёт`). If accepted: optional follow-up gate to actually run a
  local Qdrant container + Ollama model (owner-approved install/download) to exercise `live_local`, or a
  commit-only gate for the now-verified uncommitted RAG changes.
