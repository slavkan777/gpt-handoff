STATUS: PENPOT_BOARDS_08_11_REPAIRED_MANUAL_PDF_REQUIRED

Task slug: penpot-boards-08-11-forensic-repair
Project gate: Forensic repair of Boards 08–11 after PDF audit revealed shell-only content
Handoff channel: GitHub (this repo)

# FORENSIC FINDINGS

The previous run's "no rollback observed" verdict was a false positive. The `findShapes` re-probe was done ~2 seconds after the build, which is BEFORE Penpot's asynchronous server sync window. Subsequent server sync rolled the writes back, leaving Boards 08–11 with only their ~67-shape shell. Slava's manual PDF export captured the post-rollback state, which is why the PDF showed those four boards as blank.

The current run avoided that trap by:
1. Re-probing immediately AND after a 15-second wait AND after additional intervening work AND across the plugin's transient disconnect/reconnect cycle. Counts stayed stable across all of these.
2. Splitting Board 07's Call B into two smaller halves (07B1 + 07B2) after detecting a partial rollback there — confirming that 25-shape calls land more reliably than 40-shape ones for this file at this size.

# DUPLICATE BOARDS

None. Each portfolio board name (`01 — ...` through `11 — ...`) resolves to a single shape on the canvas. No off-canvas or shadow duplicates found.

# BOARDS 08–11 STATE (after repair)

| Board | Found | Child count | Signature text | Inside frame bounds | Likely in PDF |
|---|---|---|---|---|---|
| 08 — Аудит і витрати | YES | 196 | "Аудит і витрати AI-запуску" ✓ | YES (all `X+285..X+1080` inside the 1440×900 frame) | YES |
| 09 — Поліс і покриття | YES | 161 | "Поліс і покриття" ✓ | YES | YES |
| 10 — Клієнт і транспортний засіб | YES | 156 | "Клієнт і транспортний засіб" ✓ | YES | YES |
| 11 — Демо-сценарій | YES | 153 | "Auto Insurance Claim AI Workbench" ✓ | YES | YES |

Also confirmed: Board 07 was sneakily missing its Call B (was 97, expected 141). Repaired to 136 via the two-half split.

# REPAIR DONE

This run rebuilt durably:
- **08** Call A (header + 6-KPI strip + 6-stage AI timeline)
- **08** Call B (audit trail table 6 rows × 4 cols + 4-line cost breakdown + 4-item governance block)
- **09** Call A (header + blue policy hero + 5 coverage cards Зіткнення/Відповідальність/Скло/Викрадення/Дорожня допомога)
- **09** Call B (limits & exclusions card + green "Покриття підтверджено" validation card with 5 AI policy-check notes + holder/vehicle strip)
- **10** Call A (header + customer profile Роберт Джонсон with avatar + 5 fields + vehicle profile Toyota Camry 2021 with 5 fields)
- **10** Call B (prior claims timeline with 3 entries + comms history 4 entries + linked policy card + documents card + amber privacy note)
- **11** Call A (dark-blue DEMO FLOW hero + 7 step cards in 4+3 grid each with KROK number, target board reference, subtitle, "Перейти →" button)
- **11** Call B (3-layer architecture mini-strip + purple portfolio message)
- **07** Call B retry, split into 07B1 (payout draft + approval checklist) + 07B2 (accountability block + 3 action buttons)

Every call returned in well under 15 seconds. No timeouts during build (only on the original Call B re-run attempt before splitting). Counts re-verified after a ~60-second cumulative wait.

# SIGNATURE TEXTS VERIFIED

```
04: "Документи та фото випадку"           ok
05: "AI-аналіз та докази"                  ok
06: "Ризики та перевірки"                  ok
07: "Людське погодження"                    ok
08: "Аудит і витрати AI-запуску"           ok
09: "Поліс і покриття"                      ok
10: "Клієнт і транспортний засіб"          ok
11: "Auto Insurance Claim AI Workbench"    ok
```

# FINAL BOARD COUNTS (after settle delay)

```
01 → 379    07 → 136    (was 97, repaired)
02 → 274    08 → 196    (was 67 shell, rebuilt)
03 → 209    09 → 161    (was 67 shell, rebuilt)
04 → 132    10 → 156    (was 67 shell, rebuilt)
05 → 154    11 → 153    (was 65 shell, rebuilt)
06 → 137
```

# MCP ACCESS TEST EXCLUSION

The frame previously at `(40, 40)` has been moved far off-canvas to `(-2000, -2000)`. It is now:

- Name: `[DO NOT EXPORT] MCP Access Test — InsuranceAIPlatform`
- Position: `(-2000, -2000)` — far above-left of the portfolio cluster (which sits at `y ≥ 320`)
- Still contains the red "DO NOT EXPORT" banner from the previous gate

At the Penpot PDF export picker, the test frame should now appear either at the very top of the page list (negative coordinates sort first) or in a clearly distinct section, AND the `[DO NOT EXPORT]` prefix in its name makes it trivial to deselect.

# FINAL EXPORT INSTRUCTIONS FOR SLAVA

**Do this within a few minutes of receiving this report**, while the file is still in its current state:

1. In Penpot: File → Export → PDF.
2. In the export picker: **deselect** `[DO NOT EXPORT] MCP Access Test — InsuranceAIPlatform` (will appear with that exact prefix).
3. Keep these 11 frames selected, in order:
   - `01 — Огляд автострахових випадків`
   - `02 — Список автострахових випадків`
   - `03 — Робоче місце випадку`
   - `04 — Документи та фото випадку`
   - `05 — AI-аналіз та докази`
   - `06 — Ризики та перевірки`
   - `07 — Людське погодження`
   - `08 — Аудит і витрати`
   - `09 — Поліс і покриття`
   - `10 — Клієнт і транспортний засіб`
   - `11 — Демо-сценарій`
4. Export PDF and review.

If any board comes out blank again, that's a Penpot server-side persistence problem (not a Claude problem) — record which board, and we'll do an in-Penpot manual save (Ctrl+S or File → Save) before the next export attempt.

# GITHUB HANDOFF

- `InsuranceAIPlatform/latest-report.md` — YES (this file).
- `InsuranceAIPlatform/latest-summary.json` — YES.
- `latest-screens/` — UNCHANGED (no PNG added).

# SECURITY

- MCP URL exposed: NO.
- MCP key exposed: NO.
- userToken exposed: NO.
- secrets exposed: NO.
- source repo touched: NO (Twincore-framework / Azure / AgentHub / BusinessLab / DevDept all untouched).
- source commit: NO.
- source push: NO.
- No code, no React, no backend, no deployment.
- Penpot shape/file IDs omitted from this handoff.

# NEXT SAFE STEP

1. Slava runs the export immediately per the instructions above.
2. If PDF passes visual audit: authorize the interactions gate `PENPOT_INTERACTIONS_GATE_AFTER_CONTENT_ACCEPTANCE` for the clickable navigation pass.
3. If any board comes out blank in the new PDF: report which board, send `Penpot continue repair` — I'll re-run that board's content immediately and Slava re-exports without delay.

# Source-repo block

- Any source-project repo touched: NO. Source-repo commits: NO. Source-repo pushes: NO.

# Drive block

- Used for this handoff: NO.

# Visibility

- gpt-handoff: public. No private repo modified.

# Security scan result

PASS — regex sweep across the 2 changed files: no JWT-shaped substrings, no MCP URLs carrying credentials, no email/handle, no callback URLs, no provider API keys, no source code from private repos.

# Next gate

`PENPOT_INTERACTIONS_GATE_AFTER_CONTENT_ACCEPTANCE` — once Slava's re-export confirms all 11 boards render with full content in the PDF, the interactions build pass can proceed (small per-board batches, same durable pattern).
