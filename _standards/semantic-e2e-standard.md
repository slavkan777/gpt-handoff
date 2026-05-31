# Semantic E2E Standard

**Purpose:** stop false-green tests. A passing pipeline, an HTTP 2xx, a visible "Saved!" toast, or a
green end-to-end run are **not** evidence that a business outcome happened. This standard defines the
minimum evidence an Executor must produce before claiming a user-facing flow works, and the labels an
Auditor checks.

This operationalizes the governance evidence-gate labels:
- **UI:** `MECHANICAL_PASS` / `SEMANTIC_PASS` / `PERSISTENCE_PASS (reload)` / `NEGATIVE_PASS`
- **Provider/DB/cloud:** `MOCK` / `FALLBACK` / `LOCAL_REAL` / `LIVE_REAL` / `NOT_TESTED`

---

## 1. The four-pass UI matrix

For **any** create / open / update / delete flow, report all four (PASS / FAIL / NOT_TESTED):

| Pass | Question it answers | Example evidence | False-green it kills |
|---|---|---|---|
| **MECHANICAL_PASS** | Can the user click / navigate / submit without a crash? | locator/screenshot; 2xx; no console error | "It rendered" — but submit silently no-ops |
| **SEMANTIC_PASS** | Did the **correct business entity** get created/updated with the **expected values**, and become visible in the expected UI state? | the new row appears in the list with the exact values entered; detail page shows them | toast says "Saved!" but the list is empty / shows defaults |
| **PERSISTENCE_PASS (reload)** | Does the data survive a reload / fresh query / fresh DB context? | hard reload → entity still present; fresh DB context read returns the row | data lived only in client state; gone after refresh |
| **NEGATIVE_PASS** | Does wrong input / wrong filter / invalid data correctly **fail or get rejected**? | invalid form blocked with a message; filter for a non-existent record returns empty, not "all" | every search "succeeds" because the filter is ignored |

**Rule:** `MECHANICAL_PASS` alone is **never** sufficient to claim a flow works. `SEMANTIC_PASS` +
`PERSISTENCE_PASS` are the business-truth gates; `NEGATIVE_PASS` guards against a screen that always
says yes.

## 2. Data-source realness labels

Any surface that calls a provider / DB / cloud / external API must carry a label:

| Label | Meaning |
|---|---|
| **MOCK** | Hard-coded / stub response; no real dependency exercised |
| **FALLBACK** | Real path attempted but degraded to a stub/default (e.g. missing creds) — **counts as NOT real** for acceptance |
| **LOCAL_REAL** | Real dependency, local/dev instance (local DB, local container) |
| **LIVE_REAL** | Real production-grade dependency (live provider host, real cloud DB) |
| **NOT_TESTED** | Path not exercised in this gate |

**Stub-fallback trap:** record the stub's fingerprint (identity string, placeholder values) and assert
its **absence** on any "real" run. A run that "succeeded" but returned the stub fingerprint is a
**FAILED** run for acceptance — not a pass.

**External-call evidence triple:** a real external call must show **(1)** a process log line hitting the
expected host (not a stub endpoint), **(2)** a durable artifact whose identity field carries the real
provider's fingerprint (model/version/request-id) and whose content is **not** the stub string, and
**(3)** shape evidence (token counts / latency / byte size) distinguishing real from the stub default.
A 200 / non-error log is **not** sufficient — a fallback to stub can satisfy all of those.

## 3. Reporting block (paste into the gate summary)

```json
"semantic_pass_labels": {
  "mechanical_pass": "PASS",
  "semantic_pass": "PASS",
  "persistence_pass": "PASS",
  "negative_pass": "PASS"
},
"provider_realness_labels": {
  "claims_db": "LOCAL_REAL",
  "ai_analysis_provider": "MOCK"
}
```

## 4. Auditor checklist

- [ ] All 4 UI passes present for each create/open/update/delete flow (or `NOT_TESTED` + reason)
- [ ] `SEMANTIC_PASS` + `PERSISTENCE_PASS` not substituted by `MECHANICAL_PASS`
- [ ] Every provider/DB/cloud surface carries a realness label
- [ ] No `FALLBACK` / `MOCK` silently counted as real
- [ ] Stub fingerprint checked-for and absent on every "real" run
- [ ] Evidence is tuples (`path:line` / `command + exit` / `N/M`), not prose
