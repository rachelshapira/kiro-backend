---
name: error-handling
description: >
  Design consistent .NET backend business outcomes, exceptions, HTTP ProblemDetails,
  transient-failure handling and safe retries. Use when implementing error paths or resilience.
---

# Error Handling — BTL .NET

## Distinguish failure types

- Expected validation/business failures are modeled as known outcomes (simple Result type where it adds value), not exceptions for normal flow.
- Unhandled failures are exceptions; central exception handler logs with trace/correlation ID and returns non-sensitive ProblemDetails.
- Cancellation should propagate; do not convert canceled requests to generic HTTP 500 errors.

## Explicit status mapping

| Outcome | HTTP |
| --- | --- |
| Success read/update | 200 or 204 |
| Created | 201 + Location when applicable |
| Validation | 400 |
| Unauthenticated | 401 |
| Forbidden | 403 |
| Not found | 404 |
| Conflict/concurrency | 409 |
| Business precondition/rule | 422 when semantically appropriate |
| Unexpected failure | 500 |

If implementing an `ErrorType` enum (`Validation`, `NotFound`, `Conflict`, `Unauthorized`, `Forbidden`, `BusinessRule`, `Unexpected`), the mapper MUST handle every case explicitly. Never fall through from an expected error to 500. Validate output status and ProblemDetails shape in tests.

## Catching errors

Catch specific exceptions where recovery or meaningful translation is possible. Never swallow errors (`catch { return null; }`). Infrastructure adapters may translate known provider failures to appropriate application outcomes; unknown failures remain exceptions.

## Transient failures and retries

- Retry only clearly transient failures and only when the operation can be retried safely.
- Bound retry attempts and elapsed time; use backoff/jitter and CancellationToken. Avoid hard-coded infinite loops and indiscriminate 10-second retries.
- Side-effecting operations need idempotency/deduplication; surface permanent failure and support dead-letter/reconciliation flows where applicable.
- Prefer approved .NET HTTP resilience mechanisms when already supported; choose timeouts/circuit breakers for the dependency's contract, not a universal default.

## Verification

Test business-to-HTTP mapping, unexpected exception sanitization, cancellation, and retry/idempotency cases when changed.
