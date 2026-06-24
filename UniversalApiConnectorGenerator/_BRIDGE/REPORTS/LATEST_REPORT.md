REQUEST_ID=REQ-2026-06-24-uacg-prompt-review-v0-1
STATUS=READY_FOR_GPT_AUDIT
PROJECT=UniversalApiConnectorGenerator
GATE=SOURCE_SCAN_PROMPT_REVIEW_V0_1
MODE=READ_ONLY_REVIEW
TARGET_PROMPT=UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/REQ-2026-06-24-uacg-next-container-v0-1.md
PROMPT_PATH=UniversalApiConnectorGenerator/_BRIDGE/PROMPTS/REQ-2026-06-24-uacg-prompt-review-v0-1.md
REPORT_PATH=UniversalApiConnectorGenerator/_BRIDGE/REPORTS/LATEST_REPORT.md
COMPLETED=2026-06-24
COMPLETED_BY=claude

# Prompt Review - SOURCE_SCAN_CONTAINER_V0_1 (target: REQ-2026-06-24-uacg-next-container-v0-1)

Read-only review of the target container macro prompt. The target was NOT executed.
Reviewed against the actual shipped skeleton at C:\Projects\UniversalApiConnectorGenerator
(6 projects net9.0; build 0/0; 12/12 tests; inspect/generate/verify stubs).

## Bridge process note (non-blocking)

STATUS.json advanced to `REQ-2026-06-24-uacg-prompt-review-v0-1` and its `promptPath`
points to `PROMPTS/REQ-2026-06-24-uacg-prompt-review-v0-1.md`, but the canonical
`PROMPTS/ACTIVE_REQUEST.md` still contains the already-completed
`...local-foundation-skeleton-net9-v0-2` request. I did not re-run the finished
foundation gate. I executed the request designated by STATUS.json (self-consistent
header, READY_FOR_CLAUDE, read-only). Recommend GPT re-sync `ACTIVE_REQUEST.md` (and the
`_BRIDGE/ACTIVE_REQUEST.md` compatibility pointer) to the current request so the
`промт` -> ACTIVE_REQUEST.md path stays authoritative.

## Prompt Review Verdict

VERDICT: ACCEPT_WITH_REQUIRED_CHANGES.

The prompt is well-scoped (scan only, explicitly "not connector generation"), the 9
containers are ordered safely (preflight -> contracts -> inventory -> parser -> artifacts
-> CLI -> verify -> docs -> report), and it correctly matches the architecture's smallest
safe first slice (inspect a local spec, no generation). It is NOT safe to execute as-is:
five gaps would force the implementer to silently choose architecture, a parser library,
and safety behavior. Patch them, then execute.

## Critical Issues

1. LAYER PLACEMENT UNDEFINED (highest). Container 1 says "Contracts: add SourceInventory,
   SourceFileEntry, ProviderSchemaDocument, ProviderOperation, Diagnostic, service
   interfaces, options and result types." The solution has NO "Contracts" project; it has
   Domain / Application / Infrastructure. Without explicit mapping the implementer may
   create a stray project or put service interfaces in Domain, breaking the dependency
   graph (Domain must have zero project refs). Also `Diagnostic` must reuse the existing
   `ConnectorGenerator.Domain.DiagnosticSeverity` enum, not duplicate it.

2. PARSER PACKAGE UNSPECIFIED + INTAKE-GATE RISK. Container 3 says "add approved API spec
   reader package if needed." This leaves library choice open (silent-selection) and does
   not route through the External Tool Intake Gate. The package must be NAMED, version-
   PINNED, restored offline, and owned only by Infrastructure so Domain stays library-free.

3. SOURCE-PATH SAFETY MISSING. Container 2 reads local files/folders but mandates no
   path-traversal / containment protection. A `--source` escaping the workspace (e.g. `..`,
   absolute path, symlink) must be rejected, with an extension allowlist and a size cap.
   This is a hard safety gate, not optional.

4. DETERMINISM NOT REQUIRED. Containers 3-4 produce SchemaHash and JSON artifacts but no
   determinism guarantee. Re-scanning the same source must yield identical content hashes;
   `scan-manifest.json` should pin parser package+version, tool version, and TFM; volatile
   fields (run-id, timestamps, absolute paths) must be excluded from determinism checks.

5. EXIT-CODE CATALOG VAGUE. Container 5 says "missing source exits non-zero" without a
   code, and does not define `inspect` with no `--source`. Define explicit codes and the
   no-source behavior so `verify` (container 6) can assert them.

## Recommended Patches

- Add a layer-placement table to container 1:
  - Domain: `ProviderSchemaDocument`, `ProviderOperation`, `SourceFileEntry`,
    `SourceInventory`, `Diagnostic` (reusing `DiagnosticSeverity`) - pure POCOs, no I/O.
  - Application: `ISourceInventoryService`, `ISpecParser`/`ISchemaExtractor`, scan options,
    scan result types (ports + DTOs only).
  - Infrastructure: inventory scanner, SHA-256 hasher, parser implementation, artifact
    writer. Domain references nothing; Application -> Domain; Infrastructure -> Application+Domain.
- Container 3: name and pin the parser, e.g. `Microsoft.OpenApi.Readers` (pin an exact
  version), restored from the approved offline source; explicitly route via the External
  Tool Intake Gate; forbid the parser type from leaking past Infrastructure.
- Container 2: require extension allowlist {.yaml,.yml,.json}, reject any resolved path
  outside the allowed root (traversal/symlink/absolute-escape), cap file size, and use
  deterministic sorted enumeration.
- Container 4: define `run-id` derivation; record parser pkg+version, tool version, TFM,
  per-file SHA-256, and diagnostics summary in `scan-manifest.json`.
- Container 5: define exit codes - 0 ok; 4 source-invalid/missing; 1 unexpected; keep
  generate=2; `inspect` with no `--source` prints usage and exits 0 (back-compat). Define
  whether `--operation`/`--profile` are recorded-in-manifest (this gate does not generate)
  rather than silent no-ops.
- Container 6: add three checks - (a) re-run scan on the fixture and assert identical
  artifact content hashes; (b) negative test: `--source` escaping the workspace is rejected
  with the source-invalid code; (c) assert the exact missing-source exit code.

## Container-by-Container Notes

- 0 Preflight: good. Add: STOP if the named parser package is not on the approved/offline
  allowlist; confirm baseline still 12/12.
- 1 Contracts: see Critical #1. Map every type to Domain/Application; reuse DiagnosticSeverity.
- 2 Inventory: see Critical #3. Add traversal safety + determinism (sorted) + size cap.
- 3 Parser: see Critical #2. Name+pin package; YAML+JSON via the same reader; add fixture
  `tests/fixtures/api/rate-minimal.yaml` (confirmed not present yet) + a determinism test.
- 4 Artifacts: paths `output/<run-id>/scan/` fine (output/ already exists). Add manifest
  provenance fields; define run-id; ensure artifact cleanup note (no repo exists yet, so no
  .gitignore needed now - flag for the future-repo gate).
- 5 CLI: `inspect` currently has no flag parsing (only args[0]). Adding `--source` is new;
  minimal manual flag parsing is sufficient (no new dependency). Keep generate/verify/menu
  intact. Define exit codes + no-source behavior (Critical #5).
- 6 Verify: solid baseline; add the 3 checks above. Use redirected stdin for the no-args run
  to avoid hanging (already proven in the foundation gate).
- 7 Docs: README.md + docs/IGOR_DEMO_GUIDE_UA.md + docs/ARCHITECTURE_UA.md (none exist yet -
  clean create). Require sanitization: no secrets, no personal data, honest limits ("scan
  only, fixture-only, no real UPS, no generation"), and the net9-temporary / net10-future
  note consistent with TARGET=net9.0.
- 8 Report: standard; READY_FOR_GPT_AUDIT or BLOCKED.

## Risks

| Risk | Severity | Mitigation |
|---|---|---|
| Service interfaces land in Domain -> dependency-graph break | High | Layer-placement table (patch) |
| Silent/un-pinned parser library; intake-gate bypass | High | Name+pin+offline+gate (patch) |
| Unsafe file read outside workspace | High | Traversal allowlist + negative test (patch) |
| Non-deterministic artifacts/hashes | Medium | Determinism test + manifest provenance |
| Scope breadth (contracts+parser+docs in one gate) | Medium | Acceptable as macro-gate once patches land; could split docs to a sub-step if it overruns |
| net9/net10 drift in docs | Low | Keep temporary-net9 + net10-future note |

## Suggested Final Prompt Changes

1. Insert the layer-placement table (container 1).
2. Replace "approved API spec reader package if needed" with the exact pinned package,
   offline-restore, and intake-gate routing (container 3).
3. Add a SOURCE SAFETY block: extension allowlist, traversal/symlink/absolute-escape
   rejection, size cap, deterministic enumeration (container 2).
4. Add a DETERMINISM block + manifest provenance fields; define run-id (containers 3-4).
5. Add an EXIT CODES block and define inspect-without-source; expand container 6 with the
   determinism, traversal-negative, and missing-source-code assertions.
6. Add to STOP: parser package not approved/offline; any unsafe source path; baseline not
   12/12.

## Execute / Do Not Execute Recommendation

DO NOT EXECUTE AS-IS. Apply the six suggested prompt changes (they encode the five critical
issues), then the gate is safe to open. The structure and ordering are sound; the patches
are additive and small. After GPT patches the target prompt and re-issues it with a fresh
READY_FOR_CLAUDE request (and re-synced ACTIVE_REQUEST.md), implementation can proceed.

This is a read-only review; acceptance remains GPT + Slava. No self-acceptance.
