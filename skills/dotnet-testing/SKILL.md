---
name: dotnet-testing
description: >
  Select, write and verify .NET backend tests for new features, bug fixes and refactors.
  Use when touching backend behavior, HTTP contracts, persistence, authorization, or CI tests.
---

# Testing — BTL .NET Backends

## Discover before choosing

Inspect the repository's test framework, commands, fixtures, running dependencies and current CI expectations. Reuse approved libraries and patterns; do not change framework solely for a new test.

## Testing strategy

- For a bug: identify the failing scenario and add a regression test where feasible before fixing it.
- For features: at least one runnable behavioral check; cover success and important failure/authorization paths according to risk.
- For refactoring: run existing behavior checks, adding characterization tests for unprotected behavior when needed.
- Domain rules: small deterministic unit tests. Web API contract/authorization: integration tests, e.g. `WebApplicationFactory<Program>` when applicable.
- EF Core relational behavior: test against the actual provider or compatible disposable instance (such as Testcontainers); do not rely on EF InMemory for relational semantics.

## Conventions

- Use xUnit if already approved/used. Prefer built-in xUnit assertions unless another assertion library is approved and appropriately licensed; do not unconditionally introduce FluentAssertions.
- Prefer descriptive `Operation_Scenario_ExpectedResult` names, Arrange/Act/Assert, isolated data and deterministic time.
- No `Thread.Sleep`; prefer awaited conditions and cancellation-aware methods.
- Run smallest relevant suite first, then expand for changed contracts/cross-cutting risk.

## Report evidence

State exact commands, PASS/FAIL/NOT RUN, key outcomes and environmental blockers. A file being written or a test compiling is not proof it passed. Coordinate with `task-verification.md`.
