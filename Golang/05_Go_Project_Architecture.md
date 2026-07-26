# Go project architecture

## Repository shape

```text
cmd/api/main.go
internal/config
internal/transport/http
internal/transport/grpc
internal/handler
internal/service
internal/repository
internal/integration/financial
internal/models
internal/workers
internal/observability
proto
migrations
tests
```

## Startup flow

```text
main.go
  -> root context and signal handling
  -> configuration and logger
  -> PostgreSQL/GORM
  -> repositories and integration clients
  -> services/use cases
  -> HTTP and gRPC handlers
  -> workers and scheduled jobs
  -> server start
  -> cancellation and graceful shutdown
```

## Request flow

```text
Router
  -> middleware
  -> authentication/RBAC
  -> handler
  -> service/use case
  -> repository/GORM/SQL
  -> PostgreSQL
  -> integration client
  -> DTO/response
```

## Core rules

- `context.Context` is passed from transport through service, DB, and external calls.
- Business invariants belong in service/repository/DB, not only middleware.
- Transactions are owned by the use case that defines the atomic business operation.
- Slow network calls should normally stay outside DB transactions.
- Critical state changes should use conditional updates or locking/versioning.
- Every long-lived goroutine needs an owner, cancellation path, error strategy, and join mechanism.
- Retries must be bounded and duplicate-safe.
- Financial callbacks must be authenticated, deduplicated, and order-tolerant.

## Reliability patterns

### Idempotency

Persist idempotency key, request fingerprint, status, and result under a unique constraint. Repeated identical requests return the existing result; conflicting reuse is rejected.

### Status model

`Pending -> Processing -> Succeeded | Failed | RequiresReview`

### Atomic update

Avoid `SELECT active` followed later by `UPDATE`. Prefer an atomic conditional update and verify `RowsAffected`, or use row locking/optimistic versioning when required.

### Graceful shutdown

Receive SIGTERM/SIGINT, stop new requests, cancel root context, shut down servers with timeout, stop workers, wait for goroutines, flush telemetry, and close resources.

## .NET mapping

- ASP.NET Controller -> Go handler
- Application service -> service/use case
- EF Core -> GORM/SQL
- CancellationToken -> context.Context
- BackgroundService -> managed goroutine worker
- DI container -> explicit constructor wiring
- Task.WhenAll with cancellation -> errgroup.WithContext
