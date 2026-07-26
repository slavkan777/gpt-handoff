# Canonical project legend

## One-sentence version

An existing .NET insurance/document platform was extended with a separate Go integration and orchestration service that exchanged validated data and operation statuses with an external financial application; AI document workflows and React/Angular product interfaces were developed around the same business process.

## System boundaries

```text
Insurance/document platform (.NET + web UI)
        |
        v
Go integration and orchestration service
        |
        v
External financial application owned by another team
        |
        v
Banking APIs
```

The Go service was not a bank, did not own banking APIs, and did not replace the external financial application.

## Why Go was introduced

The existing .NET platform was not rewritten. Go was selected for a new isolated integration boundary requiring explicit dependencies, REST/gRPC contracts, concurrent external calls, background processing, reliable lifecycle management, and simple deployment.

## Responsibilities of the Go service

- accept requests from the insurance platform;
- validate and normalize data;
- map insurance DTOs to REST/protobuf contracts;
- call the external financial application;
- persist correlation IDs, request fingerprints, statuses, and audit data;
- apply timeouts, bounded retries, cancellation, and idempotency;
- process callbacks or asynchronous results;
- expose stable operation state to the insurance workflow;
- provide structured logs, tracing, tests, and production diagnostics.

## AI document-processing boundary

```text
Insurance document
  -> ingestion and normalization
  -> chunking and embeddings
  -> authorized claim-scoped retrieval
  -> LLM structured output with citations
  -> deterministic validation and evaluator checks
  -> human review
  -> ordinary backend workflow
```

Python/FastAPI/LangChain handled probabilistic AI processing. Go/.NET services retained authorization, persistence, business rules, and final actions. LLM output was advisory only.

## Frontend boundary

The live portfolio demonstrates an operator dashboard with:

- claims overview and queue;
- claim workspace;
- documents and photos;
- AI checks and recommendations;
- risk level and confidence;
- human approval;
- audit and AI cost/latency visibility.

React/TypeScript was used for the live dashboard. Angular/TypeScript represents broader enterprise workflow experience and supporting modules.

## Controlled vibe-coding model

```text
Requirement
  -> decomposition
  -> bounded specification
  -> Claude Code implementation
  -> Codex/ChatGPT independent review
  -> tests, lint, race/security checks
  -> human acceptance
```

AI accelerates implementation but does not own architecture, verification, debugging, or production responsibility.

## Claims that must not be made

Do not say that Vyacheslav:

- built core banking;
- owned banking APIs;
- created the external financial application;
- rewrote the entire insurance platform in Go;
- allowed an LLM to execute payments;
- has many years of commercial Go experience;
- was the Senior Go Architect of the target company's platform.
