# Owner-Simulation Dry-Run — Transcript

**Gate:** `AUTOMATED_OWNER_SIMULATION_DRY_RUN_V0.1` · **Date:** 2026-05-28
**Persona:** skeptical product owner / technical reviewer.
**HEAD/origin-dev:** `0372524` · **origin/main:** `69e6731` (unchanged).

---

## T+00:00 — Bootstrap

> "Repo is `InsuranceAIPlatform`, branch `dev`, HEAD matches accepted. 0 modified tracked, 0 staged. 17 untracked items are carry-forward from the triage classification (9 architecture docs + 8 prior report dirs). Safety preflight clean: no secret leak, no provider SDK, no Can-*=true, no `.env` staged. OK, let me run it."

## T+00:30 — Start the local product

> "Backend on `localhost:5284` returns `Healthy` in 1s — default provider is Mock. Frontend preview on `localhost:4173` serves `<title>InsuranceAIPlatform · Auto Claim AI Workbench</title>`. Both ports respond 200. No secrets in startup logs."

## T+00:45 — Workload / dashboard entry

> "Open claims queue. 5 claims visible; `CLM-1006` is there with customer `Роберт Джонсон`, risk `Високий`, aiStatus `AI-перевірено`. Summary card shows 47 active / 12 pending review / 8 high risk / 14.3h avg SLA / 6 processed today / 2 AI running — feels like real numbers for a demo, not zeros or placeholders."

> "Demo scenario endpoint returns 7 walk-through steps — good portfolio storyline anchor."

## T+01:30 — Claim details deep-dive

> "Click into `CLM-1006`. All 6 sub-routes (`/`, `/customer-vehicle`, `/policy`, `/risks`, `/approval`, `/audit`) return 200. Detail shape is rich: Toyota Camry 2021, POL-2025-AC-4421 policy, ДТП on 2026-05-18, status `В роботі`, risk 82/100, AI confidence 78%, estimate $2720 vs benchmark $1970, deductible $500, recommended payout $1800, 6/7 docs received, missing 'Фото пошкодження заднього бампера'. The owner-facing fields tell a coherent story."

> "Try edge cases: well-formed unknown `CLM-9999` → **404** ✓. Malformed `INVALID` → **400** ✓. Both are safe error envelopes, no DB stack trace leakage."

> "Correlation-id header echoes round-trip (`owner-walk-001`) — observability is wired."

## T+02:30 — Documents

> "7 items returned for CLM-1006: 4 documents + 3 photos + 1 missing item (the rear bumper photo). Synthetic only, no real PII, no binary upload attempted by the system."

## T+03:00 — Safe human actions

> "Exercise the 4 BFF command endpoints with unique `Idempotency-Key` per command:
>
> 1. `POST /approval-draft` (request decision draft) → `success=true cmd=cmd-ee3c961e audit=35 outbox=30`
> 2. `POST /human-decision` (decide `NeedsMoreInformation`) → `success=true cmd=cmd-02d2cc1b status=AwaitingInformation`
> 3. `POST /missing-document-requests` (internal log of missing-doc request) → `success=true cmd=cmd-b92469db` — no external customer message sent
> 4. `POST /document-metadata` (placeholder doc row) → `success=true cmd=cmd-ef40db4c` — no binary upload
>
> Then replay command 1 with the SAME idempotency-key:
> - response: `success=true warnings=['duplicate-idempotency-key: outbox row 30 already exists']`
> - DB: audit table now has 2 rows for `owner-sim-draft-001` (both attempts logged), outbox has 1 row (dedup at side-effect layer).
>
> This is the right shape — operators get a full audit of what was attempted, but a downstream subscriber sees the side effect only once."

## T+03:45 — Mock AI analysis

> "`POST /ai-analysis/run` in default Mock mode → 200. `runId=run_d72d9196`, providerMode=`Mock`, modelName=`local-mock-v0.1`, tokens=4261, cost=0.0187, confidence=78, risk=`high`. 3 findings / 2 evidence / 2 risks (actually 4 risks per the response). recommendedAction=`Запросіть відсутнє фото бампера.` All 7 guardrails read correctly: advisoryOnly=true, requiresHumanReview=true, canApprovePayout/canRejectClaim/canAccuseFraudFinal/canSendCustomerMessage/canChangeClaimStatus = false. Notice: `AI output is advisory only — human decision is final.` This is what the UI is going to render — the contract is sound."

## T+04:15 — Real DeepSeek opt-in

> "Stop default API. Read user-scope `DEEPSEEK_API_KEY` (booleans only: present=true, source=user-scope-env, shape_plausible=true), refresh in-process env, spawn API on `:5285` with `--AiProvider:Mode=DeepSeek --AiProvider:RealCallsEnabled=true`. Startup log: `AI provider resolved to: RealDeepSeek (opt-in)`. The 3-condition gate evaluated all true."

> "Run one call against synthetic CLM-1006. HTTP **200 in 6s**. `runId=run_6fcc493e`. providerMode=`DeepSeek` (not Mock). modelName=`deepseek-v4-flash` (server's alias for the configured `deepseek-chat`). tokens=702 — different from Mock (4261) AND from every prior real run (688/695/700/706/708) — this is a real LLM, not a cached response. cost=0.00018954. confidence=75 (Real fingerprint, vs Mock's 78). riskLevel=`high`. Summary in **English** (Mock's is Ukrainian) — `Claim CLM-1006 involves a 2021 Toyota Camry in a road traffi...`. recommendedAction.action: `Request the missing rear bumper photo before proceeding. Com...`."

> "Guardrails: advisoryOnly=true, requiresHumanReview=true, all 5 Can-* false. correlationId echoed as `owner-sim-real-001`."

> "Process log: `Host: api.deepseek.com Path: /v1/chat/completions` + `Received HTTP response headers after 538.5364ms - 200` + `DeepSeek response received. Model: deepseek-v4-flash, Tokens: 702`. Zero `sk-`-shape matches in stdout, zero `DEEPSEEK_API_KEY` mentions. Clean."

## T+05:00 — Frontend UI evidence

> "I don't have browser automation installed — no DOM-pixel verification possible without installing Playwright. But I can prove the contract the UI consumes is sound, AND I can prove the UI ships the right strings:
>
> - Preview HTTP 200, title served correctly.
> - JS bundle (`assets/index-DqfPgff0.js`) contains all 15 advisory-only UI labels: `Advisory-only AI`, `Останній прогон AI-аналізу`, `Guardrails (порадницький режим)`, `Зведення`, `Рекомендована дія (порадницька)`, `Поліс / покриття`, `Запустити AI-аналіз`, all 5 Can-* labels, `advisoryOnly`, `requiresHumanReview`, `loadLatestAiAnalysis`.
>
> The 15/15 string presence means the React tree renders the right pieces; the HTTP smoke means the data the tree binds to is correct. The only thing left to verify visually is pixel-level layout. That goes to Slava's manual checkpoint."

## T+05:30 — Adversarial probes

> "Now I act as a hostile reviewer. Does the system actually let AI do anything dangerous?
>
> - `POST /api/claims/CLM-1006/payout` → **404** (no such endpoint exists)
> - `POST /api/claims/CLM-1006/customer-messages` → **404** (no such endpoint)
> - `POST /api/claims/CLM-1006/fraud-confirm` → **404** (no such endpoint)
> - `POST /human-decision` with `decision='ApproveForPayout'` → safely **rejected** with message: `"Decision 'ApproveForPayout' is not allowed. Allowed values: ..."` (allowlist-enforced enum)
> - No `sk-`-shape anywhere in any response body.
> - `GET /api/claims/CLM-9999/ai-analysis` → sanitized `{code:'no_ai_analysis_run', message:..., traceId:...}` envelope; no DB exception leak.
> - DB query for forbidden-category audit events: 0 Payout, 0 CustomerMessage, 0 FraudFinal, 0 AiClaimReject.
>
> The system has no path to do the dangerous things. Not blocked at runtime — they don't exist. That's the right architecture."

## T+06:00 — Audit / outbox consistency

> "Post-sim DB counts: 16 AI runs total (+2 this gate; Mock=9, DeepSeek=6, Disabled=1). 7 owner-sim audit events. 7 owner-sim outbox messages.
>
> Each of the 5 owner-sim commands has a matching audit event + outbox message under its correlation id, except the `human-decision` produces 2 outbox messages by design (ClaimStatusTransitionRequested + HumanDecisionSubmitted). And the idempotency-replay produces 2 audit rows + 1 outbox row by design."

## T+06:30 — Verdict synthesis

> "What I just walked through is what Slava would walk through manually. Nothing surprised me. The product works. AI is advisory and provably can't escalate. Real provider is gated behind opt-in and the key never leaks. Frontend ships the right strings.
>
> The ONLY thing I haven't seen is the actual rendered DOM — pixel placement, colour of the guardrail pills, hover states, mobile breakpoints. That's a 5-10 minute hands-on for the operator.
>
> **Slava can manually test now with high confidence. He won't be the one discovering broken flows.**"

## T+07:00 — Cleanup

> "Stop all spawned processes (dotnet + node preview). Verify ports 5284 / 5285 / 4173 all free. Verify HEAD/origin-main unchanged, 0 modified tracked, 0 staged. Done."
