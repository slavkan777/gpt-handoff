# Claude Self-Improvement Retrospective — V0.1 (read-only)

CURRENT STATE: Read-only retrospective complete. No code, repo, AIKB, ClaudeOS, CLAUDE.md, Azure, or provider changes. Nothing committed or pushed.
CURRENT GATE: CLAUDE_SELF_IMPROVEMENT_RETROSPECTIVE_V0.1 (thinking/report only).
BOUNDARIES: AUDIT-ONLY. Allowed: read vault + project, reason, produce this report. Forbidden (honored): implement, edit AIKB/ClaudeOS/CLAUDE.md, commit/push, Azure/provider, secrets.
NEXT SAFE STEP: Operator/GPT review this report → if accepted, open `ACTIVATE_CLAUDE_SESSION_PROTOCOL_V0.1` (a separate gate). This report changes nothing on its own.

---

### Method, source discipline, and how the quality bar was enforced

- **Produced by:** 5 Sonnet section-writers seeded with a first-hand incident ledger, then an **Opus adversarial critic** (workflow `wtf3ti8qe`) whose only job was to enforce this gate's "no generic text / no unenforceable promise / no self-flattery / no opinion-as-proven" bar. The critic returned `overall_pass: false`; **its fixes are applied in this assembled version** (root-cause relabeled, rules de-duplicated under stable IDs, unenforceable enforcements replaced with external checkers, schema gaps filled, sacred-rule-modification caveats added, self-flattery cut).
- **NOT_AVAILABLE (not invented):** the "consolidated recommendations from previous GPT chats" and the "AI Engineering OS improvement dossier" were **not attached** to this prompt. The master docs `AI-ENGINEERING-MASTER-OPERATING-SYSTEM_v1.1.txt` and `AI-ENGINEERING-SELF-CHECK-ADVERSARIAL-REVIEW.txt` are referenced by the existing session protocol but were **NOT_READ** this gate.
- **FACT provenance:** incidents tagged FACT come from (a) Claude's own self-improvement vault (`anti-patterns.md` A-0xx, `patterns.md` P-0xx, `lessons-learned.md` L-0xx), or (b) **this session's three prior gates** (i18n, Azure SQL, and the code-quality audit `wiv1uz43p`) which the assembling agent executed and verified first-hand. Project-specific code claims (137/137 blind spots, the search-scenario inversion) were verified in that audit gate and are cited with provenance.
- **Question coverage map:** A→§6, B→§7, C→§8, D→§9–§11, E→§15 (self-critique top-4), F→§15 (disagreements). Sections 12–14 cover prompt/GPT/operator needs.

---

## 1. Executive verdict

- **The single highest-leverage finding (FACT):** a complete, trial-ready governance protocol already exists — `00-Meta/governance/claude-session-protocol.md` (dated 2026-05-14) — with 7 risk profiles, hard gates, COMMIT/PUSH-PROOF blocks, and a canonical report template. Its status is **"TRIAL READY / NOT WIRED YET"**: CLAUDE.md does not point to it, so none of it fires. **Unloaded governance is worse than no governance — it creates the false impression that gates exist when they do not.** The biggest improvement is not new rules; it is *activating and hardening what is already written*.
- **Proposed root cause (INFER, operator-asserted — NEEDS VERIFICATION):** Operator named it on 2026-05-06 (A-025): Claude defaults to **assistant-mode** ("produce a full-looking answer") when the work needs **executor-mode** ("do the precise full work, verify every DONE-STATE item, iterate"). That Operator said this is FACT; that it is the *single* upstream cause of every other incident is a hypothesis, not proven. The concrete, enforceable counter is **R-17**: no status word (done/complete/passed/CLEARED) may be emitted without a per-item evidence tuple in the report, checked by a separate reviewer.
- **"Passed tests ≠ correct" (FACT, this session's audit gate `wiv1uz43p`):** a GREEN 137/137 backend suite was structurally blind to all four of its own P0s (an id-format overflow needing >9999 rows; a dead/undispatched saga; a deadlock needing concurrency the serial EF-InMemory harness cannot produce; a frontend-nav P0 with no test). A green CI is not acceptance evidence for semantic or concurrency correctness.
- **No-self-acceptance must be structural, not behavioral (FACT, this session):** in the audit gate, the "Backend" dimension auditor rated its *own* 603-line controller "not a fat controller / no change needed" while another lens rated the same code a P1 SRP violation. The implementing lens self-passed; only a separate, more expensive reviewer surfaced it. Awareness of the rule did not prevent the violation — the mechanism (separate reviewer identity) must be wired, not trusted.
- **Worker/subagent "done" relayed without re-verify is a false-DONE factory (FACT, A-021):** "Phase 5 done" became "backend 100%"; manual re-check found ~20% missing. There is no structural gate today between "subagent prose" and "coordinator evidence."
- **Fabrication under uncertainty (FACT, A-020):** a "$250–500" cost was gut-feel; actual ~$50 (5–10× off). The PRE-TYPE guard exists in memory but fires inconsistently because the checker lives inside the same model that fabricates.
- **Symptom ≠ root cause, and operator-reported direction is a hypothesis (FACT, this session ×2):** "Failed to fetch" was a 500-without-CORS caused by a missing DB, not a CORS bug; and the reported search direction ("CUST-T0205 found it, name didn't") was the *inverse* of what the code does (customerId is not projected into the list row; the display name *is* in the search haystack).
- **Subagent output is a proposal, not a verified artifact (FACT, i18n gate this session):** two subagent build errors (an ASCII apostrophe in a JS string literal; a type-widening cast) reached integration and were only caught by an explicit build check.
- **Honest disclaimer:** none of this is "already fixed." Every item below is a RECOMMENDATION or RULE CANDIDATE requiring Operator/GPT approval and wiring.

## 2. What Claude learned from the retrospective

| Lesson | Type | Why it matters | Priority |
|---|---|---|---|
| A trial-ready `claude-session-protocol.md` exists but is unwired into CLAUDE.md | FACT | All 7 risk profiles, hard gates, proof blocks, and the report schema are defined and silent. Wiring is the highest single-leverage change. | P0 |
| Assistant-mode default → optimization shortcuts ("майже/достатньо") instead of full DONE-STATE iteration | INFER / NEEDS VERIFICATION (operator-asserted, A-025) | Plausibly upstream of many incidents; only addressable by a structural per-item evidence gate (R-17), not by intention. | P0 |
| Relaying worker "done" as coordinator "done" without re-running DONE STATE (A-021) | FACT | ~20% of backend was missing under a "100%" claim. Breaks trust in every status report. | P0 |
| Fabricating numbers instead of refusing with "не знаю, треба виміряти" (A-020) | FACT | 5–10× cost error misinforms planning. Memory guard fires inconsistently. | P0 |
| GREEN 137/137 suite blind to all 4 P0s (this session audit gate `wiv1uz43p`) | FACT (this session) | Serial/in-memory harness cannot reach concurrency, row-volume, or frontend-nav failure modes. CI green ≠ semantic correctness. | P0 |
| Implementing lens self-passed its own 603-line controller; separate Opus reviewer caught it (this session) | FACT (this session) | No-self-acceptance is in the protocol and was still violated. Needs a wired "separate reviewer identity" mechanism. | P0 |
| "Failed to fetch" misread as CORS; root was missing DB → 500 (this session) | FACT (this session) | Acting on the stated symptom direction without tracing to machine-verified root wastes cycles. | P0 |
| Dead-button audit via grep-onClick missed 3 live dead buttons + a lying caption (L-009, 2026-05-27) | FACT | Single-signal grep is insufficient for UI behavioral correctness; census required. | P1 |
| One-stakeholder green-light triggered ~$15–20 throwaway autonomous run (A-022) | FACT | Triangulation rule exists in memory; no structural gate before autonomous spend. | P1 |
| Two subagent build errors reached integration in the i18n gate (this session) | FACT (this session) | Default is trust-and-merge; a mandatory build check after subagent code is needed. | P1 |
| Scope assumption (SQL gate needed new EF code) was wrong until files were read (this session) | FACT (this session) | EF layer was already committed; estimate-before-read is a recurring failure. | P1 |
| DbMigrator prints "(localdb)" regardless of actual target (this session) | INFER | Analogy for Claude's own reporting: never emit a label/number not derived from verified state. | P1 |
| 6 Tier-A memory rules added in one session bypassing pending.md (A-024) | FACT | Memory bloat; untested rules enter the sacred tier. Quarantine discipline exists, was skipped. | P1 |
| Reported bug direction was the inverse of code behavior (this session search scenario) | FACT (this session) | Operator-described behavior is a hypothesis; machine repro is the only valid start. | P1 |
| Long defensive mea-culpa after being caught (A-023) | FACT | Wastes context and reads as deflection. Acknowledge once, fix, move on. | P2 |

## 3. P0 Claude improvements

Backlog references the single normative rule list in **§16** (rule IDs R-xx) to avoid restating rules. "Enforcement" names the mechanism; full text lives in §16.

| Rule | Why P0 | Enforcement (mechanism) | Where to store |
|---|---|---|---|
| **R-01** declare 4-line risk profile | Protocol exists, never fires | mandatory report header; reviewer auto-rejects if absent | CLAUDE.md pointer → protocol |
| **R-02** hard gates (no push/commit/delete/destructive-git/self-accept/main-push/secret/hook-bypass) without per-prompt auth | Hard gates unwired | `AUTHORIZATION TOKEN` field in COMMIT/PUSH reports | protocol §5 |
| **R-03** PRE-TYPE number guard | A-020 (5–10× off) | report field `NUMBERS CITED / UNSOURCED NUMERICS`, reviewer-checked (not self-trigger) | protocol report template |
| **R-04** POST-SUBAGENT re-verify before relay | A-021 (~20% missing) | report field `POST-SUBAGENT VERIFICATION` (evidence tuple per item) | protocol report template |
| **R-05** external-call evidence triple | G2; "Failed to fetch" misdiagnosis | `EXTERNAL-CALL EVIDENCE TRIPLE` per LIVE_REAL row in DATA SURFACE MAP | protocol report template |
| **R-06** stub-fingerprint negative assertion | G5; 137/137 blind to dead saga | gate field `STUB FINGERPRINT RECORDED` + `run ≠ stub` | protocol |
| **R-07** semantic+persistence+negative for UI/E2E | e2e/16 soft-pass (this session) | E2E matrix columns SEMANTIC/PERSISTENCE/NEGATIVE, gate-blocked if empty | E2E standard |
| **R-08** read-before-estimating-scope | SQL gate assumption wrong | `SCOPE BASELINE READ` must precede any estimate | protocol report template |
| **R-09** subagent output build-verified before merge | i18n build errors (this session) | `SUBAGENT BUILD-VERIFIED: exit N` | protocol |
| **R-10** no self-acceptance (implementer ≠ accepter) | this session (603-line controller) | `REVIEWER IDENTITY` field; self-issued CLEARED auto-rejected | protocol §2/§5 |
| **R-11** operator directional bug → reproduce first | search-scenario inversion | `REPRODUCTION EVIDENCE` before any fix | protocol CODE CHANGE |
| **R-12** UI dead-element census, not single grep | L-009 | `CENSUS TABLE` per component | AUDIT-ONLY profile |
| **R-13** secret observability = present/length/prefix only | G3 | pre-commit scan + `SECRET SCAN` field | protocol §9 |
| **R-14** COMMIT-PROOF + PUSH-PROOF blocks | protocol unwired | proof blocks mandatory; missing field = blocked | protocol §6/§7 |
| **R-15** scope-exceedance STOP | scope creep mechanism | `SCOPE EXCEEDANCE` field + STOP condition | protocol |
| **R-16** stakeholder triangulation (≥3) before autonomous spend | A-022 | `STAKEHOLDER SIGNALS` field; spend blocked if missing | protocol |
| **R-17** PRE-EMIT status-word gate | A-025 root counter | no done/complete/passed/CLEARED without per-item evidence tuple, reviewer-checked | CLAUDE.md + protocol |

## 4. P1 Claude improvements

| Rule | Why P1 | Enforcement | Where |
|---|---|---|---|
| **R-18** code-log honesty (flag hardcoded status strings, e.g. DbMigrator) | log-honesty (this session) | code-review checklist `LOG HONESTY` | protocol |
| **R-19** Claude-output honesty (every ✅ has a preceding tool-call citation) | analogy to R-18 | GPT-auditor checks report; no preceding citation = `UNVERIFIED` | protocol report template |
| **R-20** memory quarantine 24h + 2nd confirm before Tier-A/B | A-024 | memory-write gate field `QUARANTINE DATE` + `CONFIRMED` | pending.md + CLAUDE.md |
| **R-21** daily-note continuity | continuity | SessionStop hook checks today's note exists | hooks |
| **R-22** live-repo > AIKB > memory source-of-truth | stale-clone risk | pre-edit field `SOURCE OF TRUTH: live-repo read @ts` | protocol |
| **R-23** acknowledge-once on error | A-023 | retrospective flags any >3-sentence mea-culpa (external check, not self-monitoring) | self-improvement |
| **R-24** process-env spawn-time verified before provider verdict | G4 | smoke field `PROCESS RESTART WITH ENV VERIFIED` | protocol |
| **R-25** HIGH/CRITICAL → independent reviewer before DONE | this session self-accept | `CONSILIUM / OPUS REVIEWER` field | CLAUDE.md |
| **R-26** handoff report 10-field canonical schema | variable handoff quality | validator rejects missing field | protocol §10 |
| **R-27** complexity stated + no-downgrade on risk keywords | classification anti-override | `COMPLEXITY` field + keyword scan | CLAUDE.md |
| **R-28** target-environment stated + matches authorized | log-honesty / prod-risk | `TARGET ENVIRONMENT` field | protocol |
| **R-29** subagent brief 4 elements + DevDept forbidden paths | subagent brief rule | dispatch checklist before Agent() | CLAUDE.md |
| **R-30** snapshot before destructive | backup rule | `SNAPSHOT WRITTEN` field | protocol §9 |

## 5. P2 Claude improvements

| Rule | Why P2 | Enforcement | Project-size condition |
|---|---|---|---|
| **R-31** NOT TOUCHED section in report | blast-radius bounding | required report field | all serious tasks |
| **R-32** auto-ADR for decisions | decisions lost between sessions | session-close checklist `DECISIONS THIS SESSION` | projects w/ architecture choices |
| **R-33** pattern→skill at 3+ occurrences | automation pipeline | retrospective scans ledger; adds `SKILL CANDIDATE` to pending.md | mature projects |
| **R-34** proactive skill scan at session start | reactive discovery misses skills | **SessionStart hook prints the skill list** (not "mental match") | projects w/ ≥2 custom skills |
| **R-35** end-of-session reflection | session-debt accumulates | **SessionStop hook** emits pattern/anti-pattern candidates (drop "ritual") | sessions w/ ≥1 decision |
| **R-36** tool-selection guard (Bash vs Glob/Grep/Read) | inertia toward Bash | pre-Bash check; dedicated tool if file/content/read op | all sessions |

## 6. Before-execution checklist (answers question A)

Run in order; each is a STOP point on failure.

0. **Classify + declare.** State TRIVIAL/LOW/MED/HIGH/CRITICAL (R-27). If the prompt contains migration/prod/secret/drop/`--force`/delete/archive/send/publish → floor at HIGH. If CRITICAL → `⚡ OPUS: [reason]` and STOP for approval. Emit the 4-line declaration (R-01). Trivial prompts exempt.
1. **Read live repo state before estimating (R-08).** `git status` / `branch --show-current` / `log --oneline -5` / `remote -v`; ahead/behind via `rev-list --count`. Behind>0 → STOP. *(SQL gate proved estimate-before-read is wrong.)*
2. **Resolve source conflicts (R-22).** Priority: **live repo (1st) > current prompt (2nd) > MEMORY.md/AIKB (3rd, verify if >90 days) > prior-session memory (4th, INFER until checked)**. On conflict, surface it in one sentence, state which source you follow and why, then proceed — never silently pick.
3. **Verify the gate.** Name the risk profile. If ambiguous *and* blast-radius>0, ask exactly one question; otherwise take the safer reading and proceed. Re-confirm hard gates (R-02).
4. **Verify file scope (R-15).** Write the writable scope; match it to the project profile. If the work needs files outside scope → STOP, report `SCOPE EXCEEDANCE`, do not widen. If ≥3 stakeholders → collect `STAKEHOLDER SIGNALS` (R-16) before autonomous spend.
5. **Numbers (R-03).** Before any `$`/`%`/hours/`x`/token/row figure: cite `file:line` or `command→output`, or write "не знаю, треба виміряти". The enforceable half is the reviewer-checked `NUMBERS CITED` report field — not an internal trigger.

## 7. During-execution discipline (answers question B)

- **Scope creep.** Every edit must trace to a declared scope item. Untraceable → label `OUT-OF-SCOPE` and stop, unless it is a ≤5-line, no-behavior-change Boy-Scout fix inside a file already being edited (note it `🧹`).
- **Silent architecture changes forbidden.** Any change to module boundaries / data contracts / API surface / DB schema / DI registration → state `ARCHITECTURE CHANGE: [what][why]` before applying. Renaming a public method counts.
- **Discovered blockers.** Build fails 3× → STOP with the exact error. Test fails → read the test; flaky = retry once, semantic = report as FINDING. Missing dep/config/secret → STOP; never substitute a stub silently. Hard gate would trigger → STOP.
- **Mock/fallback detection (R-06).** Identify the harness (EF-InMemory / mock / stub) before trusting test evidence; label it `INFER` not proof. If a test claims a concurrency/persistence/provider scenario the harness *cannot* produce, mark it `STRUCTURAL GAP`. Record the stub fingerprint and assert the real run did not return it.
- **Subagent output (R-04, R-09, R-10).** Run the build before trusting subagent code; require the evidence tuple (`path:line` + `command+exit 0` + `N/M`); the coordinator re-runs DONE STATE itself and does not accept the subagent's self-verdict.
- **Drift check.** The report must carry a `DRIFT CHECK: N/M DONE-STATE items verifiable` field after the first major edit block (an external field, not an undefined "midpoint"); <half verifiable and >20 min in → report drift, reconfirm scope.

## 8. Verification standard by gate type (answers question C)

| Gate type | Required evidence (all mandatory) | Stop condition |
|---|---|---|
| **Audit / read-only** | files-read list; findings labeled FACT/INFER/RISK; `FILES CHANGED: none` confirmed by `git status` | any write → STOP; finding without cited evidence → STOP |
| **Planning / doc** | `.md` paths + line ranges; every claim cites `file:line`/command; `git diff --stat` doc-only | code file in diff → STOP; uncited claim → INFER |
| **Implementation** | build exit 0 (cmd+output); tests N/M (cmd+output); `diff --stat` in-scope; public-contract changes stated | build fail / regression / out-of-scope file / prose-only subagent → STOP |
| **UI / visual** | screenshot/DOM of each interactive element; **SEMANTIC** (handler + caption matches behavior, read adjacent text); **NEGATIVE** (one bad input rejected) | single-signal onClick grep (L-009) or screenshot-without-semantics → insufficient |
| **E2E / create-open-update** | **SEMANTIC** (record fields correct, not just 200); **PERSISTENCE** (reload from fresh load, fields survive); **NEGATIVE** (invalid input → correct error) | any pillar missing → STOP; soft-pass escape hatch (e2e/16) = FAILED; 200 alone insufficient |
| **DB / persistence** | round-trip read-back from a **fresh** DbContext; raw-SQL/2nd-session confirm; migration idempotency (run twice → no-op) | EF-InMemory-only → label INFER; no fresh-context read-back → insufficient |
| **Azure / external provider** | (a) log line hitting the real provider host; (b) durable artifact with real fingerprint (model/req-id/version) ≠ stub placeholder; (c) shape evidence (tokens/latency/bytes); (d) stub-fingerprint negative check | any of (a)/(b)/(c) missing → insufficient; stub fingerprint in output = FAILED; 200 alone insufficient |
| **Commit** | COMMIT-PROOF (repo/branch/staged/diff/unrelated NO/secret CLEAN/no artifacts); post: new HEAD + `status --short`; message has no secrets/PII | unrelated/secret/artifact staged → STOP; hook fail → STOP, never `--no-verify` |
| **Push** | PRE-PUSH (remote/branch/commits-to-push/behind=0); POST-PUSH (origin==HEAD, ahead/behind 0, clean); no main/release/* without explicit name | behind>0 → STOP; protected branch → STOP; force → refuse unless explicit + not main |
| **Cleanup / delete / archive** | dry-run path×verb list; explicit Operator approval; post: targets absent; if repo → commit+push proofs | dry-run skipped / target exists / locked / no prior approval → STOP |

## 9. Mandatory Claude report schema (copy-paste; hardens protocol §10)

```markdown
## LIMITATIONS         <!-- MANDATORY, NEAR TOP. Every untested path / unverified assumption / scope gap. Labels: FACT/INFER/RISK/NEEDS VERIFICATION. Never blank. -->
## STATUS             <!-- GREEN/YELLOW/RED/BLOCKED, derived ONLY from evidence rows; never echo subagent prose; no self-upgrade -->
VERDICT: <one sentence — cite an evidence line below>

## CURRENT GATE       <!-- GATE / RISK PROFILE / WORKFLOW / STOP CONDITIONS / ALLOWED / FORBIDDEN (ref protocol §5 hard gates) -->
## COMPLEXITY         <!-- TRIVIAL..CRITICAL + risk-keyword scan result (R-27) -->
## REPO STATE         <!-- BRANCH / HEAD_BEFORE / HEAD_AFTER(or SAME) / WORKING TREE clean|dirty / ORIGIN_UNCHANGED -->
## SCOPE BASELINE READ <!-- files/commands that established what already EXISTS, BEFORE any estimate (R-08) -->
## FILES CHANGED      <!-- path | op (READ/WRITE/CREATE/DELETE) | reason. Then NOT TOUCHED: <list> (R-31) -->
## COMMANDS RUN       <!-- # | command | exit | output excerpt | surface(LOCAL_REAL/MOCK/FALLBACK/NOT_TESTED) -->
## BUILD / TEST / SMOKE <!-- BUILD pass|fail (cmd+exit); TESTS N/M (runner line); HARNESS: InMemory|LocalDB|Azure|Mock; KNOWN BLIND SPOTS: <tests passing but not covering the P0> -->
## NUMBERS CITED      <!-- every $/%/h/x/token/row figure → its source; UNSOURCED NUMERICS: none|list (R-03) -->
## UI SCREENSHOTS     <!-- screen | path | what it proves (skip if code-only) -->
## DATA SURFACE MAP   <!-- component | MOCK/FALLBACK/LOCAL_REAL/LIVE_REAL/NOT_TESTED | evidence. For each LIVE_REAL row: EXTERNAL-CALL EVIDENCE TRIPLE [host log]/[artifact fingerprint]/[shape] (R-05) -->
## POST-SUBAGENT VERIFICATION <!-- per DONE-STATE item: path:line + command+exit0 + N/M; SUBAGENT BUILD-VERIFIED: exit N (R-04, R-09); else PARTIAL not ✅ -->
## REVIEWER IDENTITY  <!-- who issued CLEARED; MUST differ from the implementing agent (R-10); self-issued = rejected -->
## EVIDENCE LABELS    <!-- FACT/INFER/RISK/RECOMMENDATION/RULE CANDIDATE/NEEDS VERIFICATION/DISAGREE-MODIFY on every claim -->
## FORBIDDEN SCOPE CHECK <!-- the 8 hard gates: each YES/NO/N-A + AUTHORIZATION TOKEN if overridden (R-02) -->
## SECRETS / PII CHECK <!-- scanned Y/N; method; NONE_FOUND|FOUND(path, never value); PII in text NONE|FOUND -->
## AGENT TRACE        <!-- role | model | task | verdict; state "main session only" if no subagents -->
## BLOCKERS           <!-- hard stops + who resolves -->
## GPT-HANDOFF PATHS  <!-- latest-report.md / latest-summary.json / runs/<date>-<slug>/evidence/ -->
## NEXT SAFE STEP     <!-- a gate name or "BLOCKED — awaiting <who>" -->
## STOP LINE CONFIRMATION <!-- stopped at <action>; did NOT proceed to <next gate>; authority <gate boundary|explicit auth> -->
```

## 10. `latest-summary.json` schema (machine-auditable twin)

```json
{
  "$schema": "claude-gate-summary/v1",
  "gate": "<name>", "timestamp": "YYYY-MM-DDTHH:MM:SSZ",
  "status": "GREEN|YELLOW|RED|BLOCKED",
  "verdict": "<one sentence; cite evidence_dir file>",
  "risk_profile": "<one of 7>",
  "complexity": "TRIVIAL|LOW|MED|HIGH|CRITICAL",
  "repo": { "remote": "...", "branch": "...", "head_before": "<sha>", "head_after": "<sha|SAME>", "origin_unchanged": true, "working_tree_clean": true },
  "scope_baseline_read": ["<files/commands read before estimating>"],
  "evidence": {
    "build": { "command": "...", "exit_code": 0, "excerpt": "..." },
    "tests": { "command": "...", "exit_code": 0, "passed": 0, "total": 0, "harness": "InMemory|LocalDB|Azure|Mock", "known_blind_spots": ["..."] },
    "smoke": { "method": "manual|automated|SKIPPED", "result": "PASS|FAIL|SKIPPED", "notes": "..." }
  },
  "numbers_cited": [ { "figure": "$X", "source": "command|file:line|log" } ],
  "unsourced_numerics": [],
  "data_surface": { "mock": [], "fallback": [], "local_real": [], "live_real": [], "not_tested": [] },
  "external_call_evidence": [ { "component": "...", "host_log": "...", "artifact_fingerprint": "...", "shape": "...", "stub_fingerprint_match": false } ],
  "post_subagent_verification": [ { "item": "...", "evidence": "path:line|cmd exit0|N/M", "subagent_build_exit": 0 } ],
  "reviewer_identity": "<role; != implementer>",
  "forbidden_scope_check": { "no_push": true, "no_unauthorized_commit": true, "no_delete_archive_move": true, "no_destructive_git": true, "no_self_acceptance_on_critical": true, "no_main_branch_push": true, "no_secrets_in_commit": true, "no_hook_bypass": true },
  "secrets_pii_check": { "scanned": true, "method": "...", "result": "NONE_FOUND|FOUND", "found_paths": [] },
  "limitations": ["<label: text>"], "blockers": ["<who resolves>"],
  "files_changed": [ { "path": "...", "operation": "READ|WRITE|CREATE|DELETE" } ],
  "not_touched": ["..."],
  "handoff_paths": { "latest_report": "...", "latest_summary": "...", "evidence_dir": "...", "screenshots_dir": "..." },
  "next_safe_step": "<gate|BLOCKED: reason>",
  "stop_line": { "stopped_at": "...", "did_not_proceed_to": "...", "authority": "..." }
}
```

## 11. gpt-handoff improvements

Folder shape (RECOMMENDATION):
```
<Project>/
  latest-report.md          ← stable permalink fetched on «отчёт»
  latest-summary.json       ← machine twin (§10)
  runs/<YYYY-MM-DD>-<slug>/
    report.md  summary.json
    evidence/ build.txt test.txt smoke.txt git-status.txt git-log.txt
    screenshots/<screen-name>.png   ← named per scenario, not "screenshot1"
```
| Gap | Fix | Priority |
|---|---|---|
| GPT parses prose to audit | add `latest-summary.json` (§10) — `status` + `forbidden_scope_check` + `evidence` in one read | P0 |
| `verdict` trusted on prose | `verdict` must cite an `evidence/` file+line | P0 |
| Limitations buried | LIMITATIONS is the **first** report section (§9) | P0 |
| Blind spots undisclosed | `evidence.tests.known_blind_spots[]` mandatory (this session: 4 found) | P0 |
| Command output trusted | full stdout saved to `evidence/`; the JSON excerpt is a pointer | P1 |
| Gate-boundary holds invisible | `stop_line.did_not_proceed_to` recorded in JSON (codifies this session's correct create-not-push hold) | P1 |
| Screenshots unnamed | per-scenario names + `screenshots[]` with `{screen,path,proves}` | P1 |

## 12. Prompt-template improvements (what GPT prompts should always contain)

This session's prompts already include CURRENT GATE / FORBIDDEN / DONE STATE / evidence labels (FACT) — these are hardening refinements:
```
CURRENT GATE / RISK PROFILE (one of 7) / IN SCOPE / OUT OF SCOPE+FORBIDDEN
DONE STATE: numbered, each cites command + expected output
VERIFICATION METHOD: how to prove each item
EVIDENCE LABELS REQUIRED: FACT/INFER/RISK/NEEDS VERIFICATION
MOCK vs REAL DEFINITION: MOCK=<stub fingerprint> / LOCAL_REAL=<...> / LIVE_REAL=<...>
STOP LINE / HANDOFF PATHS (report, summary, evidence)
```
Add a **GATE_CONFIRMED handshake**: "before executing, confirm GATE / SCOPE / DONE STATE / STOP LINE / HANDOFF present — reply GATE_CONFIRMED or list what's missing."

## 13. What Claude needs from GPT

| Need | Required change |
|---|---|
| One gate per prompt | never bundle CODE CHANGE + COMMIT-ONLY + handoff |
| Specific evidence named | prompt names the `file:line`/`command+output` to cite; prose confirmation rejected |
| Pre-agreed mock-vs-real | every gate carries `MOCK = <fingerprint>` so Claude can't self-define "real" |
| Risk profile named | do not leave Claude to infer it |
| Canonical prompt in repo | `docs/gate-prompts/<gate>.md`; GPT links; Claude fetches + GATE_CONFIRMED |
| Self-acceptance ban on audits | audit prompts state "Claude may NOT accept its own implementation verdict; Opus/GPT is the accepter" (R-10) |

## 14. What Claude needs from the Operator

**Bug reports:** numbered repro from cold-start/URL; **verbatim input values** (the search-scenario inversion proves direction depends on the exact value typed); separate expected-vs-actual; screenshot/URL; browser network tab for "Failed to fetch"-class bugs (this session it was a 500, visible immediately in the network tab); environment + branch + commit sha.

**Manual acceptance sequence:** (1) machine verification runs first (build+tests+smoke, evidence in handoff); (2) Operator's hands-on walk happens **after** reviewing that evidence, at a planned gate-boundary checkpoint; (3) exploratory bugs found during the walk are filed as a new repro-complete report.

**Reconciled with the sacred parallel-questions rule (critic fix):** ordinary side-questions while subagents run are **still answered inline without pausing anything** (per `feedback_parallel_questions`). Only a **directional bug-fix claim that contradicts code behavior** is logged as a BLOCKER and reproduced before a fix — it is *not* acted on as immediate steering. This narrows, and does not override, the inline-answer contract.

## 15. Disagreements / modifications (answers E + F)

**Question E — top-4 places Claude is most likely to fail:**
1. **Mock-vs-real overclaim** (A-021, this session audit): treats CI-green/in-memory/stub success as semantic correctness. Highest-probability overclaim vector → R-06/R-07.
2. **Worker-relay overclaim** (A-021): accepts subagent prose "done" and relays it → R-04.
3. **Symptom-direction miss** (this session ×2): accepts the operator's stated bug direction → R-11.
4. **Scope pre-estimation miss** (SQL gate): estimates before reading → R-08.

**Question F — DISAGREE / MODIFY:**

| Recommendation | Position | Reasoning |
|---|---|---|
| Opus supervisor mandatory for **ALL** critical work | ACCEPT, scope to risk profiles | Right for migrations/contract changes/irreversible/architecture; disproportionate for routine CRUD/doc. "Critical" should map to the §16 risk profiles, not a flat label, or the rule gets silently bypassed. **This narrows a Tier-A sacred rule → must route through pending.md 24h quarantine + 2nd Operator confirm; NOT applied in this gate.** |
| `/qa` mandatory for **ANY** task with edits | DISAGREE-MODIFY | Too heavy for TRIVIAL/LOW. Require `/qa` for **MED+**, or when touching external services / shared state / an external reviewer exists. **Also a sacred-rule narrowing → pending.md, not here.** Landed as a normative item only if Operator approves (no R-xx assigned yet, by design). |
| 24h pending quarantine for every memory rule | ACCEPT, no change | A-024 is proof; cost is low. Keep (R-20). |
| Triangulation before autonomous subagent | ACCEPT for externally-visible **or** >$5 **or** irreversible spend | A-022 was reversible + ~$15–20; full triangulation before *every* local subagent would make autonomous work unusable (R-16 scoped accordingly). |
| Subagent brief ROLE+SCOPE+TASK+DONE | ACCEPT unconditionally | i18n errors are proof (R-29). |
| External-call evidence triple (G2) | ACCEPT for external boundaries, not in-process | Disproportionate for an in-process cache read / internal SignalR event; scope to "external service boundary" (R-05). |
| Phase gates implement→smoke→commit→push (G7) | ACCEPT, conditional already | Real issue is Claude **misclassifying** scratch-vs-production; the §16 risk profile should be authoritative, not in-context judgment. |
| No-self-acceptance | ACCEPT, harden mechanism | Rule existed and was still violated this session; fix is structural reviewer identity (R-10), not awareness. |

If a recommendation is accepted "as is," it's because the ledger contains direct evidence of harm from skipping it — not deference.

## 16. Proposed future rules for CLAUDE.md / ClaudeOS (single normative source)

> These are RULE CANDIDATES. Per R-20 and MEMORY.md sacred-rule protection, any rule that **narrows an existing Tier-A sacred rule** (e.g. the QA-mandatory or Opus-mandatory scope) must pass through pending.md quarantine + a 2nd Operator confirmation — it is **not** activated by this report. Each rule's ENFORCEMENT is an external checker (report field / hook / pre-commit scan), never self-monitoring.

**R-01 — 4-line pre-exec declaration.** WHY: protocol defines it but CLAUDE.md doesn't point to it, so it never fires. PRIORITY P0. APPLIES TO all serious tasks. ENFORCEMENT: mandatory report header; reviewer auto-rejects if absent.
**R-02 — hard gates without per-prompt auth.** WHY: protocol §5 hard gates are unwired. P0. all/commit/push/cleanup. ENFORCEMENT: `AUTHORIZATION TOKEN: <exact prompt phrase>` field in COMMIT/PUSH reports; missing → blocked.
**R-03 — PRE-TYPE number guard.** WHY: A-020 fabricated $250–500 vs ~$50. P0. all. ENFORCEMENT: report field `NUMBERS CITED / UNSOURCED NUMERICS`, validated by the reviewer (the checker is external, because a trigger inside the fabricating model is what already fails).
**R-04 — POST-SUBAGENT re-verify before relay.** WHY: A-021, ~20% missing under "100%". P0. all/code/DB/Azure. ENFORCEMENT: `POST-SUBAGENT VERIFICATION` field with an evidence tuple per DONE-STATE item; prose-only → auto-reject.
**R-05 — external-call evidence triple.** WHY: G2; this session's "Failed to fetch" misdiagnosis. P0. code/Azure/provider. ENFORCEMENT: `EXTERNAL-CALL EVIDENCE TRIPLE` per LIVE_REAL row in the DATA SURFACE MAP; missing any prong → REWORK.
**R-06 — stub-fingerprint negative assertion.** WHY: G5; 137/137 blind to a dead saga. P0. code/Azure/provider. ENFORCEMENT: gate field `STUB FINGERPRINT RECORDED` + `run ≠ stub`.
**R-07 — semantic+persistence+negative for UI/E2E.** WHY: e2e/16 soft-pass; green suite blind to 4 P0s. P0. UI/code. ENFORCEMENT: E2E matrix columns SEMANTIC/PERSISTENCE/NEGATIVE; any empty column on a P0 scenario → gate blocked.
**R-08 — read-before-estimating-scope.** WHY: SQL gate assumption wrong until files read. P0. all/code/DB. ENFORCEMENT: `SCOPE BASELINE READ` field must precede any estimate; absent → estimate invalid.
**R-09 — subagent output build-verified before merge.** WHY: i18n build errors reached integration. P0. code. ENFORCEMENT: `SUBAGENT BUILD-VERIFIED: exit N` before merge.
**R-10 — no self-acceptance (implementer ≠ accepter).** WHY: this session, auditor self-passed its own 603-line controller. P0. all/code/UI. ENFORCEMENT: `REVIEWER IDENTITY` field ≠ implementing agent; self-issued CLEARED auto-rejected.
**R-11 — operator directional bug → reproduce first.** WHY: search-scenario was the inverse of code behavior. P0. code/UI. ENFORCEMENT: `REPRODUCTION EVIDENCE` (command+output) before any fix; absent → no-touch.
**R-12 — UI dead-element census, not single grep.** WHY: L-009 missed 3 live dead buttons + a lying caption. P0. UI. ENFORCEMENT: `CENSUS TABLE [element|handler|caption|verdict]` per component; grep-only blocked.
**R-13 — secret observability = present/length/prefix only.** WHY: G3. P0. all/Azure. ENFORCEMENT: pre-commit scan pattern list (protocol §9) + `SECRET SCAN` field.
**R-14 — COMMIT-PROOF + PUSH-PROOF blocks.** WHY: protocol defines them, unwired. P0. commit/push. ENFORCEMENT: proof blocks mandatory; any missing field → blocked.
**R-15 — scope-exceedance STOP.** WHY: scope creep is how shortcuts replace DONE STATE. P0. all. ENFORCEMENT: STOP condition + `SCOPE EXCEEDANCE: detected|none` field.
**R-16 — stakeholder triangulation (≥3) before externally-visible/>$5/irreversible autonomous spend.** WHY: A-022 ~$15–20 throwaway. P0. multi-stakeholder/Azure. ENFORCEMENT: `STAKEHOLDER SIGNALS: [name:signal]` field; spend blocked if a named signal is missing/contradicts.
**R-17 — PRE-EMIT status-word gate.** WHY: the concrete counter to the A-025 assistant-mode hypothesis. P0. all. ENFORCEMENT: no done/complete/passed/CLEARED emitted unless every DONE-STATE item has an evidence tuple in the report, checked by a separate reviewer; otherwise status = IN PROGRESS + remaining items listed.
**R-18 — code-log honesty.** WHY: DbMigrator prints "(localdb)" regardless of target. P1. code/DB/Azure. ENFORCEMENT: code-review checklist `LOG HONESTY: hardcoded status strings checked`.
**R-19 — Claude-output honesty.** WHY: analogue of R-18 for Claude's own reports. P1. all. ENFORCEMENT: GPT auditor verifies every ✅/status has a preceding tool-call citation on the same report; otherwise prefix `UNVERIFIED`.
**R-20 — memory quarantine before promotion.** WHY: A-024, 6 rules bypassed quarantine. P1. memory hygiene. ENFORCEMENT: memory-write gate requires `QUARANTINE DATE` + `CONFIRMED (2nd signal)` or it stays in pending.md.
**R-21 — daily-note continuity.** WHY: continuity for the next session. P1. all. ENFORCEMENT: SessionStop hook checks today's note was written.
**R-22 — live-repo > AIKB > memory.** WHY: stale-clone was 31 commits behind. P1. all/code. ENFORCEMENT: pre-edit `SOURCE OF TRUTH: live-repo read @ts` field.
**R-23 — acknowledge-once on error.** WHY: A-023 defensive mea-culpa. P1. all. ENFORCEMENT: retrospective flags any acknowledgment >3 sentences (external check, not self-monitoring).
**R-24 — process-env spawn-time verified.** WHY: G4; create-customer 500 from missing env-at-spawn. P1. code/Azure. ENFORCEMENT: smoke field `PROCESS RESTART WITH ENV VERIFIED`.
**R-25 — HIGH/CRITICAL → independent reviewer before DONE.** WHY: self-generosity is structural. P1. HIGH/CRITICAL. ENFORCEMENT: `CONSILIUM / OPUS REVIEWER` field. *(Scope-narrowing of the Tier-A Opus-mandatory rule → pending.md, not here.)*
**R-26 — handoff 10-field canonical schema.** WHY: variable handoff quality degrades GPT review. P1. all. ENFORCEMENT: validator rejects any handoff missing a field.
**R-27 — complexity stated + no-downgrade on risk keywords.** WHY: classification anti-override. P1. all. ENFORCEMENT: `COMPLEXITY` field + keyword scan; risk keyword without HIGH/CRITICAL → auto-escalate.
**R-28 — target-environment stated + matches authorized.** WHY: log-honesty + prod-by-accident risk. P1. DB/Azure/push. ENFORCEMENT: `TARGET ENVIRONMENT: <host/alias> — matches authorized` field; mismatch → blocked.
**R-29 — subagent brief 4 elements + DevDept forbidden paths.** WHY: subagent brief rule; i18n errors. P1. all. ENFORCEMENT: dispatch checklist verified before Agent(); DevDept hardcodes `FORBIDDEN PATHS: TwinCore.*`.
**R-30 — snapshot before destructive.** WHY: backup-before-destructive. P1. cleanup/DB/Azure. ENFORCEMENT: `SNAPSHOT WRITTEN: <path>@ts` field; absent → blocked.
**R-31 — NOT TOUCHED section.** WHY: bounds blast radius for the auditor. P2. all/code/DB. ENFORCEMENT: required report field; empty must say "none — all examined files modified".
**R-32 — auto-ADR for decisions.** WHY: decisions lost between sessions. P2. all/multi-stakeholder. ENFORCEMENT: session-close `DECISIONS THIS SESSION` checklist.
**R-33 — pattern→skill at 3+ occurrences.** WHY: ledger→skill pipeline under-triggered. P2. all. ENFORCEMENT: retrospective scans ledger; adds `SKILL CANDIDATE` to pending.md.
**R-34 — proactive skill scan.** WHY: reactive discovery misses skills. P2. projects w/ ≥2 skills. ENFORCEMENT: **SessionStart hook prints the skill list** (external, not "mental match").
**R-35 — end-of-session reflection.** WHY: session-debt accumulates. P2. sessions w/ ≥1 decision. ENFORCEMENT: **SessionStop hook** emits pattern/anti-pattern candidates.
**R-36 — tool-selection guard.** WHY: inertia toward Bash over Glob/Grep/Read. P2. all. ENFORCEMENT: pre-Bash check; use the dedicated tool for file/content/read/edit ops.

*(32 rules R-01…R-36 with gaps reserved; all within the 20–40 bound; every ENFORCEMENT is an external checker.)*

## 17. Proposed next gates

| Gate | Purpose | Why now | In scope | Out of scope | Evidence | Risk if skipped |
|---|---|---|---|---|---|---|
| **ACTIVATE_CLAUDE_SESSION_PROTOCOL_V0.1** | Wire protocol into CLAUDE.md as the mandatory pre-exec + report framework | It's trial-ready but unwired → zero behavior change today; every other gate depends on it | add CLAUDE.md pointer; verify the report fields + hard gates fire in a test session | rewriting the protocol; code edits | `claude-session-protocol.md` status "TRIAL READY / NOT WIRED YET" (FACT) | hard gates stay advisory; P0 violations continue undetected |
| **CLAUDE_EXECUTOR_RULES_UPDATE_V0.1** | Promote §16 rules into CLAUDE.md/MEMORY.md via pending.md quarantine; assign each an enforceable field | the rule set is concrete + ledger-grounded but inert until wired | add P0 rules to CLAUDE.md "Session enforcement"; P1/P2 to pending.md | inventing non-ledger rules; narrowing sacred rules without quarantine | §16 (A-020…L-009 + this session) | the assistant-mode gap stays open; next project repeats P0s |
| **GPT_HANDOFF_REPORT_SCHEMA_HARDENING_V0.1** | Mandatory report schema (§9) + machine summary (§10) + validator | handoffs vary in quality; GPT audits prose | schema + validator; update handoff skill; test on one real handoff | changing GPT-side review logic | §9/§10; this session's self-issued-CLEARED slip | GPT can't consistently catch missing evidence |
| **GPT_AUDIT_TEMPLATE_HARDENING_V0.1** | Structured GPT-side audit: per-dimension ✅/❌/⚠️ with `file:line`/command citation; forbid prose-only CLEARED | the Opus critic caught self-generosity only because it was explicitly invoked; the review cycle needs this systematically | audit template; required citation format; reject prose-only verdicts | adding dimensions not grounded in the ledger | this session (603-line controller self-pass); L-009 | structured self-generosity keeps passing review (R-10 unenforced on the review side) |
| **SEMANTIC_E2E_STANDARD_HARDENING_V0.1** | Mandatory E2E matrix: SEMANTIC/PERSISTENCE/NEGATIVE per P0 scenario; block acceptance on any empty column | 137/137 green blind to all 4 P0s; e2e/16 soft-pass | matrix template + columns; QA acceptance gate; verify vs existing e2e | rewriting passing tests; P1/P2 scenarios | this session audit (4 P0s undetected); e2e/16 (FACT) | green suites keep masking P0s; hands-on acceptance can't be satisfied by a green badge |
| **NUMERIC_CLAIM_VERIFICATION_GATE_V0.1** | Reviewer-checked `NUMBERS CITED / UNSOURCED NUMERICS` on every report | A-020; memory guard fires inconsistently | reviewer checklist field; summary.json `numbers_cited` | non-report numbers | A-020/P-013/L-017 (FACT) | cost/effort fabrications continue |
| **STAKEHOLDER_TRIANGULATION_GATE_V0.1** | `STAKEHOLDER SIGNALS` field before externally-visible/>$5/irreversible autonomous spend | A-022 ~$15–20 throwaway | pre-exec field; autonomous-spend gate | solo / single-stakeholder / sub-threshold spend | A-022/P-015/L-019 (FACT) | expensive runs invalidated post-hoc |

## 18. Short message to Operator + GPT

- **The system is more complete than its behavior shows — and that is the trap, not the comfort.** A full governance protocol exists (`claude-session-protocol.md`) but is unwired; unloaded rules create false assurance that gates exist when they don't. **First move: one CLAUDE.md pointer** to activate it.
- **Root cause is a hypothesis, not a proven fact.** Operator's "assistant-mode vs executor-mode" (A-025) is operator-asserted; treat it as INFER. The thing that actually moves the needle is **R-17**: no "done/passed/CLEARED" without a per-item evidence tuple, checked by a *separate* reviewer.
- **Green tests are not acceptance.** This session's 137/137 suite was blind to all four of its own P0s. Drop "CI green" as an acceptance signal; require the SEMANTIC/PERSISTENCE/NEGATIVE matrix and DB fresh-context read-backs.
- **No-self-acceptance must be wired, not trusted.** An auditor self-passed its own controller this session; only a separate Opus pass caught it. The mechanism is a `REVIEWER IDENTITY` ≠ implementer field, not awareness.
- **Subagent/worker "done" is prose until re-verified** (R-04); subagent code is a proposal until it builds (R-09).
- **Numbers need a reviewer-checked field** (R-03), not an internal trigger — the trigger lives in the same model that fabricates.
- **Symptom ≠ root cause; operator bug direction is a hypothesis** (R-11). Reproduce before fixing. But keep answering ordinary side-questions inline — only directional bug-claims are deferred (reconciled with the sacred parallel-questions rule).
- **Scope: read before you estimate** (R-08); **stop at the boundary** (R-15); **don't widen silently.**
- **Two proposals narrow Tier-A sacred rules** (QA-mandatory scope; Opus-mandatory scope). Those must go through pending.md 24h quarantine + a 2nd confirmation — **not** applied in this gate.
- **Mandate the report/JSON schema** (§9/§10) so GPT audits machine-readable evidence, not prose; put LIMITATIONS and known blind-spots near the top.
- **This report changes nothing yet.** It is RECOMMENDATIONS + RULE CANDIDATES. Approve, then open `ACTIVATE_CLAUDE_SESSION_PROTOCOL_V0.1` and `CLAUDE_EXECUTOR_RULES_UPDATE_V0.1`.
- **Honest limit:** the GPT dossier and master-OS docs were not available/ read this gate; project-specific FACTs were verified first-hand in this session's prior gates and are cited as such.

---

### Forbidden-scope confirmation
No code edits · no AIKB/ClaudeOS/CLAUDE.md edits · no commit · no push (this file is **created, not pushed**) · no Azure/provider · no secrets/PII printed · read-only throughout. AGENT TRACE: 1 Opus orchestrator + workflow `wtf3ti8qe` (5 Sonnet writers + 1 Opus critic, `overall_pass:false` → fixes applied in this assembly).
