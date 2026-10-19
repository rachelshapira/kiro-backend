---
name: dotnet-clean-architecture
description: >
  Apply and extend Clean Architecture in BTL .NET services. Use when an existing solution
  has Domain/Application/Infrastructure/API boundaries, when placing a new use case, or
  when selecting the architecture of a new nontrivial backend.
---

# Clean Architecture — BTL .NET

## First decide what is already there

- Existing project: inspect `.sln`/`.csproj`, dependency references and comparable features. Preserve the existing architecture; do not retrofit four projects to a small change.
- New project: choose the simplest sustainable structure meeting explicit BTL standards. A four-project layout is an option/default for sufficiently complex backends, not a reason to add abstractions with no purpose.

## Four-layer model, when used

- `Project.Domain`: entities, value objects, domain invariants; no infrastructure/framework dependency.
- `Project.Application`: use cases, contracts/ports, validation/business outcomes; depends on Domain.
- `Project.Infrastructure`: database, messaging, external adapters; implements Application contracts.
- `Project.Api`: thin endpoints, composition root and DI; references Application and Infrastructure.
- References point inward; enforce with actual project references.

## Add a feature

1. Locate one nearby use case and its tests.
2. Implement domain behavior only if there are domain rules.
3. Add an application operation using existing conventions, not a new mediator by default.
4. Add persistence/integration only as needed; avoid repositories that merely forward every `DbContext` method.
5. Map endpoint DTOs and outcomes; validate appropriate boundaries and authorization.
6. Add the narrowest behavioral check and run relevant build/tests.

## Avoid premature patterns

CQRS, mediator, specifications, domain events and separate repositories are optional design tools. Adopt only when recurring complexity justifies them or the project already uses them. Do not reorganize unrelated modules. Use real project names rather than copying template identifiers.
