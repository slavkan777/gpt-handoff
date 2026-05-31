# Gate Report Template & Auditor Rubric

Reusable structure for an **Executor's** per-gate report (`report.md`) and the machine summary
(`summary.json`, validated by [`gate-report.schema.json`](gate-report.schema.json)). Designed so an
**Auditor** can mechanically reject prose-only evidence.

Roles: **Operator** sets goals and authorizes risk · **Auditor** reviews reports and approves
transitions · **Executor** reads/writes/runs/reports strictly inside the current prompt's scope and
never self-accepts a critical verdict.

---

## 1. `report.md` — required sections

```
CURRENT STATE / CURRENT GATE / BOUNDARIES / NEXT SAFE STEP   (4-line header)
VERDICT: <one of the 7 below>

1. What happened           (FACT, numbered)
2. Per DONE-STATE item     (table: item -> status -> evidence tuple)
3. Files changed / not touched
4. Diff summary            (bounded; show only the added/changed lines)
5. Validation              (commands + outputs / file:line)
6. Bypass usage            (only if the prompt authorized one) + unset proof
7. Secrets scan            (CLEAN / FINDINGS)
8. Limitations
9. Next gates              (NOT auto-started)

Final report block          (machine-greppable key: value lines)
```

## 2. `summary.json` — required fields

Validate against `gate-report.schema.json`. Required: `project, gate, type, verdict, verdict_detail,
date_utc, source_priority_used, boundaries_honored, activation_status, files_changed,
files_not_touched, evidence_labels, validation, limitations, next_gates, github_handoff_paths`.

Conditional: `semantic_pass_labels` (any UI flow), `provider_realness_labels` (any provider/DB/cloud
call), `bypass_used` + `bypass_unset_confirmed` (only if a guard bypass was authorized).

## 3. Evidence tuple — mandatory

No `DONE` / `PASSED` / `CLEARED` is acceptable without **one** of:

| Tuple form | Example |
|---|---|
| `path:line` | `server/Foo.cs:142` |
| `command + exit code` | `dotnet test -> exit 0` |
| `N/M passed` | `137/137 tests` |
| persisted-artifact id | `db row id=... fingerprint=...` |

Otherwise the item's status is **`IN_PROGRESS`**, never done.

## 4. Activation classification

Every change a gate makes is tagged so a proposal is never mistaken for an applied change:

`ACTIVE` · `PROPOSED` · `CONDITIONAL` · `QUARANTINE` · `BLOCKED_BY_PROTECTION`

## 5. Auditor verdict rubric

| Verdict | When |
|---|---|
| `ACCEPT` | All DONE-STATE items VERIFIED with tuples; every boundary honored |
| `ACCEPT_WITH_LIMITATIONS` | Verified; minor caveats explicitly listed |
| `PARTIAL_ACCEPT_WITH_LIMITATIONS` | Some subphases applied, others proposed/blocked — all reported honestly |
| `PROPOSED_ONLY` | Nothing mutated; a concrete proposal was produced |
| `REWORK_REQUIRED` | Missing evidence, prose-only claim, or a boundary slip → bounce back |
| `BLOCKED` | Could not proceed (state undeterminable / blocking dirty tree) |
| `BLOCKED_BY_PROTECTION` | Target sacred/protected and the prompt authorized no bypass |

## 6. Auditor rejection triggers (→ `REWORK_REQUIRED`)

- [ ] Any `DONE`/`PASSED`/`CLEARED` without an evidence tuple
- [ ] A numeric claim (`$` / `%` / `×` / `h` / "100%") without a cited source
- [ ] A UI claim missing `SEMANTIC_PASS` or `PERSISTENCE_PASS` (see `_standards/semantic-e2e-standard.md`)
- [ ] A "real provider" claim missing the evidence triple or not excluding the stub fingerprint
- [ ] `files_changed` not matching the shown diff
- [ ] `git add -A`-style staging that could sweep out-of-scope / third-party-named files
- [ ] A bypass reported as used without an explicit per-prompt authorization
