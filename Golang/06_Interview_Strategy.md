# Interview strategy

## Goal

Pass the Go/fintech technical interview using a consistent CV, a compact Phantom candidate context, and a technically correct target-role context.

## Answer format

1. Direct answer.
2. Mechanism.
3. One practical example.
4. One risk or trade-off.
5. Stop and wait for follow-up.

Normal first answer: 15–30 seconds.

## Question routing

- Personal/history question -> Candidate Brain only.
- Project question -> canonical legend + personal responsibility.
- Go technical question -> Vacancy & Technical Brain.
- Architecture question -> design, reason, failure mode, mitigation.
- Comparison -> main difference, when to choose each, one trade-off.
- Debugging -> reproduce, collect evidence, isolate layer, fix, verify.
- Unknown personal experience -> do not invent; connect to confirmed .NET/backend experience and explain the correct Go approach.

## Main interview blocks

- introduction and career transition;
- Go application lifecycle;
- context, goroutines, channels, WaitGroup/errgroup;
- REST and gRPC/protobuf;
- GORM and PostgreSQL;
- transactions, locking, idempotency, retries;
- workers and graceful shutdown;
- insurance/financial integration;
- AI/LLM and controlled vibe coding;
- React/Angular product delivery;
- testing, observability, cloud, and incident reasoning.

## High-risk mistakes

- long encyclopedic answers;
- repeating the same point;
- claiming the entire platform was rewritten in Go;
- confusing the insurance platform, the external financial application, and the target company project;
- saying gRPC is always faster or REST only uses HTTP/1.1;
- describing context as DI or as a general parameter bag;
- claiming WaitGroup propagates errors or cancellation;
- putting critical financial invariants only in middleware;
- allowing LLM output to authorize or execute a financial action;
- presenting demo/mock data as a production customer system.

## Smoke-test questions

1. Tell me about yourself.
2. Why did you move from .NET to Go?
3. What exactly did you build in Go?
4. Explain the system boundaries.
5. How did context propagate through the service?
6. How did you implement graceful shutdown?
7. Why gRPC and how do you evolve protobuf safely?
8. How did you use GORM and PostgreSQL transactions?
9. How did you prevent duplicate financial operations?
10. What did you build on React and Angular?
11. How did the RAG/LLM flow work?
12. How did you use Claude Code and Codex safely?
