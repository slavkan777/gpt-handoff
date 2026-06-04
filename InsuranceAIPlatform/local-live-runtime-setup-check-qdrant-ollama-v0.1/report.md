REQUEST_ID: REQ-2026-06-05-insuranceai-local-live-runtime-setup-check-v0-1
STATUS: PARTIAL
TASK_TYPE: project-runtime-verification
PROJECT: InsuranceAIPlatform
GATE: LOCAL_LIVE_RUNTIME_SETUP_CHECK_QDRANT_OLLAMA_V0.1
COMPLETED_BY: claude
COMPLETED: 2026-06-05

## Headline
Qdrant local runtime is now LIVE_LOCAL (pulled + started + probe-verified). Ollama remains BLOCKED on a missing prerequisite (no installer path without winget + multi-GB model). Honest mixed state; no fake labels.

## Routing Lock Verification
- PROJECT = InsuranceAIPlatform; SOURCE_REPO = C:/Projects/InsuranceAIPlatform (matches ROUTING LOCK).
- branch = `rag/local-foundation-mega-v0.1`; HEAD = `70af774`; working tree = 31 uncommitted RAG files (untouched this gate).
- Report written ONLY to the three locked IAP paths. Global `_BRIDGE` / TwinCore untouched.

## Runtime Setup Check
- Qdrant: local Docker image pulled (`qdrant/qdrant:latest` = 1.18.2) and container `iap-qdrant` started on :6333. Local-only, public image, no account, no cloud.
- Ollama: NOT installed; `winget` NOT available -> no silent automated install path. Bringing Ollama up needs a manual installer download (interactive) + a multi-GB model pull. Per DONE #4 the exact prerequisite is reported and this subpart is STOPPED (not faked, not force-installed).

## Qdrant Result
- LIVE_LOCAL (runtime). `GET http://localhost:6333/` returns `qdrant - vector search engine`, version 1.18.2; TCP :6333 reachable; the API's mechanical probe confirms reachable=true -> vectorRuntime.status=live_local.
- HONESTY CAVEAT: the RAG retrieval pipeline does NOT yet query Qdrant. Gate-1 implemented the status PROBE + a disabled-safe seam only — there is no Qdrant vector-retrieval adapter yet. So Qdrant as a RUNTIME is live_local (verified), but Qdrant as the RETRIEVAL BACKEND is not wired; vectors are still served by the in-process deterministic index. The API field `vectorRuntime.backend="qdrant"` reflects "enabled + reachable", NOT "serving retrieval".

## Ollama Result
- BLOCKED (prerequisite). Exact prerequisite: Ollama is not installed AND `winget` is unavailable, so installation requires a manual installer download (interactive / likely UAC) plus a local model pull (multi-GB). Not performed in this gate (no silent path, heavy download). Status remains `skipped_not_available`. No fake live.

## Product Smoke Result (CLM-1006, runtime flags ENABLED, Qdrant UP)
- Real API; `Rag__LocalLlamaEnabled=true`, `Rag__QdrantEnabled=true`, probe timeout 1500 ms.
- GET `/api/claims/CLM-1006/rag/infrastructure` (fresh correlationId `ae903776...`, real DB):
  - vectorRuntime: status=`live_local`, enabled=true, reachable=true, backend=`qdrant`  <- Qdrant runtime now reachable
  - localReasoningRuntime: status=`skipped_not_available`, enabled=true, reachable=false  <- Ollama still down
  - sqlSourceOfTruth=healthy, evidenceMemoryIndex=healthy
- This proves the mechanical probe across BOTH states: down -> skipped_not_available (prior gate) and up -> live_local (now). Honest, no fake labels.

## Files Changed
- NONE. No source files edited. Qdrant was brought up via Docker (runtime), not via code. Prior gate's RAG status code remains uncommitted on the feature branch.

## Commands Run
- git rev-parse/log/status (route lock).
- `docker rm -f iap-qdrant`; `docker pull qdrant/qdrant:latest`; `docker run -d --name iap-qdrant -p 6333:6333 qdrant/qdrant:latest`; TCP + HTTP probe :6333; `docker ps`.
- `Get-Command ollama` (absent); `Get-Command winget` (absent).
- Started API DLL (seams enabled); GET rag/infrastructure; stopped API.

## Verification Evidence
- Qdrant live: container `iap-qdrant` Up; `/` returns qdrant 1.18.2; API probe reachable=true -> vectorRuntime.status=live_local.
- Ollama: absent + no winget -> blocked prerequisite (reported, not faked).
- Smoke JSON above shows the mixed honest state. Fallback preserved (sql/index healthy). No source code changed this gate, so the prior gate's 165 backend tests + 12 RAG e2e remain valid.

## Boundaries Honored
- No source commit/push; no Azure/cloud; no paid provider; no secrets; no PII; no main; no schema migration; no unrelated project edits; no fake live label.
- Qdrant brought up LOCALLY only (public image, no account). Ollama NOT force-installed (heavy/interactive) -> reported per DONE #4.

## Known Limitations
- Qdrant is live as a RUNTIME but not yet a RETRIEVAL BACKEND (no adapter). `backend="qdrant"` means enabled+reachable, not serving. A Qdrant retrieval adapter is a separate BUILD gate.
- Ollama live_local not achieved: needs manual install (no winget) + multi-GB model pull.
- The `iap-qdrant` container is LEFT RUNNING as the prepared local runtime (stop: `docker stop iap-qdrant`; remove: `docker rm -f iap-qdrant`).

## Not Touched
- Source repo commits; `main`; other projects; global `_BRIDGE`; TwinCore; Azure; secrets; EF schema.

## Next Safe Step
- BUILD gate: implement a real Qdrant vector-retrieval adapter (upsert embeddings + query) so Qdrant actually serves retrieval, then re-verify backend=qdrant end-to-end (now that a live Qdrant is available locally).
- If Ollama live is wanted: owner approves installing Ollama (manual installer, no winget) + pulling a small model (e.g. `llama3.2:1b`) -> then verify live_local for reasoning.
- Or proceed to a commit-only gate for the uncommitted RAG status code.
