REQUEST_ID: REQ-2026-06-05-insuranceai-local-runtime-live-check-v0-1
STATUS: PARTIAL
TASK_TYPE: project-runtime-verification
PROJECT: InsuranceAIPlatform
GATE: LOCAL_RUNTIME_LIVE_CHECK_QDRANT_OLLAMA_V0.1
COMPLETED_BY: claude
COMPLETED: 2026-06-05

## Routing Lock Verification
- PROJECT = InsuranceAIPlatform; SOURCE_REPO = C:/Projects/InsuranceAIPlatform (matches ROUTING LOCK).
- branch = `rag/local-foundation-mega-v0.1`; HEAD = `70af774`; working tree = 31 uncommitted RAG files (prior gate; untouched this gate).
- Report written ONLY to the three locked project paths. Global `_BRIDGE` / TwinCore not touched.

## Runtime Discovery (mechanical, not guessed)
- Ollama: NOT installed (no `ollama` command on PATH); port 11434 closed; no ollama docker image present.
- Docker: INSTALLED + engine up (v20.10.23). Existing containers: agenthub-mssql, devdept-mssql (up, healthy), devdept-grafana / devdept-prometheus (exited). NO qdrant container.
- Qdrant: port 6333 closed; no qdrant binary on PATH; no qdrant docker image present locally.

## Qdrant Result
- BLOCKED / `skipped_not_available`. Bringing Qdrant up would require `docker pull qdrant/qdrant` (a network image download, ~hundreds of MB). The image is NOT already present, so this is not "already available / safely startable without install/download" per the gate's DONE #3. No pull performed — consistent with the prior gate's explicit no-download-without-approval rule.

## Ollama Result
- BLOCKED / `skipped_not_available`. Ollama is not installed and no ollama image is present; using it would require a system install PLUS a model download. Not performed (out of this gate's scope).

## Product Smoke Result (CLM-1006, runtime flags ENABLED)
- Real API started; `Rag__LocalLlamaEnabled=true`, `Rag__QdrantEnabled=true`, probe timeout 800 ms.
- GET `/api/claims/CLM-1006/rag/infrastructure` (fresh correlationId `7be876c7...`, real DB):
  - vectorRuntime: status=`skipped_not_available`, enabled=true, reachable=false, backend=`in-memory-hash`
  - localReasoningRuntime: status=`skipped_not_available`, enabled=true, reachable=false
  - sqlSourceOfTruth=healthy, evidenceMemoryIndex=healthy
- Endpoint latency ~4.2 s on a COLD first request (.NET JIT + EF first-query warmup + two <=800 ms reachability probes). Bounded by the per-probe timeout — no hang. Honest `skipped_not_available`; NO fake live label.

## Files Changed
- NONE. This is a verification-only gate; no source files were edited. Prior gate's RAG runtime-status code remains uncommitted on the feature branch.

## Commands Run
- `git rev-parse` / `log` / `status` (route lock).
- `Get-Command ollama` (absent); TCP probe 11434 (closed).
- `docker --version`; `docker ps -a`; `docker images` (no qdrant/ollama image); TCP probe 6333 (closed).
- Started API DLL with seams enabled; `GET rag/infrastructure`; stopped API + confirmed port 5099 freed.

## Verification Evidence
- Discovery commands above are mechanical (process / port / image checks), not guessed.
- Live smoke JSON: both runtimes `skipped_not_available`, reachable=false, vector backend `in-memory-hash`, sql/index healthy.
- Fallback preserved: in-process deterministic index serves vectors; mock generation intact. No code changed this gate, so the prior gate's 165 backend tests + 12 RAG e2e remain valid.

## Boundaries Honored
- No source commit/push; no deployment; no cloud; no paid provider; no install; no image/model download; no main; no schema migration; no unrelated project edits; no fake live label.

## Known Limitations
- `live_local` could NOT be exercised on this machine: neither runtime is present and neither is startable without an owner-approved download/install (Qdrant image pull; Ollama install + model pull). This is why the overall STATUS is PARTIAL rather than a full live verification.
- Latency: first cold request ~4.2 s (warmup + probes); warm requests are far faster.

## Not Touched
- Source repo commits; `main`; other projects; global `_BRIDGE`; TwinCore; Azure; secrets; EF schema.

## Next Safe Step
- If owner approves a local download: (a) `docker pull qdrant/qdrant` + `docker run -p 6333:6333` to verify Qdrant `live_local`; (b) install Ollama + pull a small local model to verify Ollama `live_local`. Each is a separate, explicitly-approved gate (network download).
- Alternatively, accept the verified honest fallback state (`skipped_not_available`) and proceed to a commit-only gate for the now-verified uncommitted RAG runtime-status code.
