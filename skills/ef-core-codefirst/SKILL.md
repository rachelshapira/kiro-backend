---
name: ef-core-codefirst
description: >
  Apply EF Core Code-First patterns to existing or new relational data models, queries,
  mappings, schema migrations and provider-backed tests. Do not use for MongoDB-only work.
---

# EF Core Code-First — BTL

## Scope and provider

Confirm the project actually uses EF Core, its version, provider and migration strategy. Preserve existing provider; SQL Server is BTL's default **new relational** provider, not a universal requirement. Use a scoped DbContext per relevant boundary; it already offers unit-of-work semantics.

## Model and queries

- Use `IEntityTypeConfiguration<T>` and explicit constraints/indexes when useful. Pick primary key strategy compatible with the actual database and expected usage; consider concurrency tokens for conflicting updates.
- For read-only work consider `AsNoTracking()`; project only needed data. Paginate unbounded results and assess N+1 and query plans.
- Do not mandate `EF.CompileAsyncQuery`, pooling, split queries, keyset pagination or bulk operations without workload evidence and provider support.
- Pass cancellation tokens through I/O; ensure transaction boundaries and externally visible side effects are safe.

## Migrations

- Create and review migrations with schema/data implications. Never rewrite an applied migration.
- Production deployment uses controlled migration scripts/processes, not unconditional application startup migration. For supported providers consider idempotent scripts; verify generated SQL/provider compatibility.
- Keep ERD documentation in sync when required for schema-changing work. Plan backward-compatible rollouts for multi-version deployments.

## Tests

Exercise important relational behavior against real or disposable matching provider instances, not EF InMemory as a replacement for relational tests. Do not connect tests to production databases.
