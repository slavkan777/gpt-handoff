# Microservice Write Actions & Audit (Mega) V0.1 — Report

**Gate:** `MICROSERVICE_WRITE_ACTIONS_AND_AUDIT_MEGA_V0.1` · **Date:** 2026-05-27
**Type:** bounded implementation — first human-controlled write commands + audit append + transactional outbox, behind BFF/service boundaries. No AI/Azure/payout/messaging/upload/broker/secrets, **no source commit/push**.
**Status:** implemented + independently verified (Opus inspector **CLEARED**, zero discrepancies).

## Current state
- Source: branch `dev` @ `1fc1774`; `origin/main` `69e6731` untouched. **No commit this gate** — HEAD still `1fc1774`; changes uncommitted.
- DB `InsuranceAIPlatform` (LocalDB): 2 new migrations applied; 200 synthetic users + `CLM-1006` intact.

## Commands (4, human-controlled, BFF-only)
| Command | POST route | Service | Audit | Outbox |
|---|---|---|---|---|
| SaveApprovalDraft | `/api/claims/{id}/approval-draft` | Approval | `ApprovalDraftSaved` | `ApprovalDraftSaved` |
| SubmitHumanDecision | `/api/claims/{id}/human-decision` | Approval | `HumanDecisionSubmitted` | `HumanDecisionSubmitted` + `ClaimStatusTransitionRequested` |
| RequestMissingDocument | `/api/claims/{id}/missing-document-requests` | Documents | `MissingDocumentRequested` | `MissingDocumentRequested` |
| CreateDocumentMetadataPlaceholder | `/api/claims/{id}/document-metadata` | Documents | `DocumentMetadataCreated` | `DocumentMetadataCreated` |

Exactly four `[HttpPost]` endpoints — the only write verbs in the codebase. Frontend never calls a service directly.

## BFF orchestration / boundaries
`ClaimCommandsController` injects only service interfaces (`IApprovalService`/`IDocumentsService`/`IAuditCostService`) — **never a DbContext**. Per command: correlation id → synthetic actor (`demo.adjuster@insuranceai.local` / "Synthetic Adjuster") → owning-service write (its own DbContext) → `AppendAuditAsync` + `WriteOutboxAsync` → `CommandResult { Success, CommandId, ClaimId, Status, AuditEventId, OutboxMessageId, CorrelationId, Message, Warnings }`. Services never reference each other; DB-backed impls are singletons via `IDbContextFactory`; the 6 skeleton health contributors are preserved.

## Human-control / safety rules
Allowed decisions: `ApproveForReview`, `RejectForReview`, `NeedsMoreInformation`, `RequestDocuments` (validated; invalid → `400 INVALID_DECISION`, re-validated defense-in-depth). AI cannot decide. **No payout execution** — `RecommendedPayout` is a synthetic display amount only. No customer message sent. No binary upload. Claims row is not mutated (a `ClaimStatusTransitionRequested` outbox event is emitted instead).

## Audit + outbox
`AuditEvent` extended (nullable `CorrelationId`/`Actor`/`ActionType`/`OccurredAtUtc`/`MetadataJson`); new `OutboxMessage` in `audit_cost` (`EventType`, `ClaimId`, `OccurredAtUtc`, `CorrelationId`, `PayloadJson` sanitized, `Processed=false`, `ProcessedAtUtc?`, `Error?`, `IdempotencyKey?`). Every command appends audit + writes outbox in one `AuditCostDbContext` SaveChanges. Idempotency-Key header dedups outbox (duplicate → existing id + warning). No broker.

## DB / migrations
`AddOutboxAndCommandAudit` (AuditCost), `AddMissingDocumentRequests` (Documents) — applied live. No Approval/Claims migration; no monolithic migration; DevDept untouched.

## Verification (independently re-run by an Opus inspector)
| Check | Result |
|---|---|
| Backend build | **0 warnings, 0 errors** |
| Backend tests | **75 passed / 0 failed** (53 prior + 22 new; InMemory factory, no live-DB dependency) |
| Frontend build | **107 modules**, PASS (additive `src/api/` only) |
| Live command smoke | 4 POSTs → safe `CommandResult` (audit/outbox ids populated, correlation echoed); invalid decision → 400 |
| DB | 200 synthetic users exact; `CLM-1006` present; `OutboxMessages` + `MissingDocumentRequests` exist; AuditEvent command columns present; outbox = 6 rows |
| Write endpoints | exactly **4** (`[HttpPost]`); none for Put/Patch/Delete/upload |
| Provider/Azure/messaging SDK | none; no AI provider call; `DEEPSEEK_API_KEY` never read |
| Boundaries | BFF injects no DbContext; no service-to-service refs |
| Source commit/push | none; `main` untouched (`69e6731`); HEAD `1fc1774` |

## Frontend
Additive only: typed command client functions + types under `src/api/`. No component/page rewrite; no payout/upload/message buttons enabled; UI button wiring deferred (honest). Build PASS.

## Deferred / limitations
True aggregate-atomic outbox (owning-service write and audit/outbox write are two separate transactions); broker/Azure Service Bus mapping; DB-backed read migration (Option A still in force); claim-status mutation; AI Analysis provider.

## Next safe step
`COMMIT_AND_PUSH_DEV_MICROSERVICE_WRITE_ACTIONS_AND_AUDIT_ONLY` — commit exactly this write/audit/outbox scope to `dev`, fast-forward push `dev` only; `main` untouched; no force.
