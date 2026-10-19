---
inclusion: manual
---

# .NET Backend Standard — BTL

Applies to new BTL .NET backend projects and to changes in existing projects **only where compatible with their approved architecture**. The agent must first follow `ai-development-rules.md`. Existing services must not be migrated, upgraded, or restructured without explicit scope.

## Standard selection

- **Existing projects:** discover actual target framework, API style, database, validation, logging, configuration, test strategy and deployment. Preserve supported existing conventions. Do not blindly copy security defects. Treat conflicts with mandatory security rules as risks to resolve explicitly.
- **New projects:** default to organization-approved .NET 10, C# compatible with project SDK, ASP.NET Core Minimal APIs, secure-by-default authorization and testable code. Use Clean Architecture's four projects when warranted or explicitly required; do not create layers just to satisfy a template.
- SQL Server with EF Core Code-First is the **default for new relational projects**, not the provider for every project. For nonrelational data use an appropriate driver. Do not replace a project's existing database.
- Prefer existing org-approved packages (including BAP) and verify version, license and deployment compatibility before adding dependencies.

## API and application

- Keep HTTP endpoints thin; keep business rules outside transport concerns. Return DTOs, not persistence entities.
- Use `ProblemDetails` for unexpected and HTTP errors; handle expected business outcomes explicitly with typed results or a simple result pattern where useful.
- Use **one** validation approach per endpoint/project: reuse the existing project's implementation; for new .NET 10 APIs consider built-in Minimal API validation with `AddValidation()` if supported and appropriate, or organization-approved FluentValidation. Do not stack competing automatic validation pipelines. Explicitly validate trust boundaries.
- Version public contracts when needed; generate accurate OpenAPI. Expose interactive API documentation only where approved and access controlled (typically development).
- Use policy-based authorization and least privilege; authentication alone is insufficient. Restrict CORS, enforce upload/payload limits, keep secrets out of source and logs.

## Persistence

- For EF Core: scoped `DbContext` per bounded context, separate entity configurations where useful, projections and `AsNoTracking()` for read-only queries, pagination for unbounded collections, concurrency controls for competing writes.
- Add indexes based on access patterns and measured bottlenecks. `DbContext` already provides a unit-of-work abstraction; avoid redundant repositories unless they add value.
- Review migrations as code. Never edit an applied migration. Apply production schema changes through a controlled process; do not automatically run migrations from multiple service instances.
- Keep an ERD synchronized when required by the organizational DoD and relevant to a relational schema change.

## Build quality

- For new projects, configure nullable reference types, analyzers and warnings-as-errors
  in `Directory.Build.props` as appropriate to the approved CI baseline.
- For existing projects, respect current CI quality gates; avoid mass cleanups
  unrelated to the task.

## Security, reliability and observability

- Follow `btl-security` skill where available. Never load/print secrets; preserve `.gitignore` and `.kiroignore` rules for `.env` and `appsettings.DevelopmentDD.json`.
- Log structured events with correlation/trace IDs. Organization-approved Elastic Enterprise logging uses `Btl.Utils.Core` (v4.0.3+ where compatible) with Serilog for environments where that standard applies. Do not force an incompatible logging migration into an existing service.
- Use end-to-end cancellation and timeouts; distinguish transient errors from permanent failures. Retries must be **bounded** with backoff/jitter and safe for the operation; require idempotency or deduplication for side effects. Never use infinite retries as a general error policy.
- Provide appropriate health probes for target runtime: BTL Kubernetes convention `/api/healthcheck` when required by deployment; separate liveness/readiness when dependencies are relevant. A static 200 probe is not proof of dependency readiness.

## BTL deployment and local configuration (only when applicable)

For environment-specific details, load the `btl-backend-environment` skill only
when work affects local configuration, internal packages, logging integration,
RENV, Kubernetes, or deployment.

- Existing BTL workloads may use `RENV`, Vault, ArgoCD/Kubernetes and the `z` script. Determine whether this deployment actually uses them; do not introduce them by default elsewhere.
- `z` merges local `./env.yaml`, Vault/RENV layer, then developer `~/<ProjectCode>/env.yaml` (last wins), generating `appsettings.DevelopmentDD.json` outside Git. Respect the running project's actual `ASPNETCORE_ENVIRONMENT` and configuration order.
- Never inspect, commit, or include generated developer secrets in AI context. Restrict docs and debugging instructions to the relevant environment.

## Verification (GIMLAUT-1477)

- Follow `task-verification.md`. Run relevant build, behavior checks, and risk-based regression tests. Every new feature should have at least one runnable passing check. Report `NOT RUN` accurately when blocked; do not imply passing results.
- Consider accessibility (frontend), docs, data migration/ERD, security, performance, resilience, and observability **when affected**, not as mandatory new components for every change.

## Contextual skills

- `existing-codebase-discovery` for unfamiliar existing code
- `dotnet-clean-architecture` for layer/slice decisions
- `dotnet-webapi` for HTTP endpoint work
- `dotnet-testing` for tests
- `ef-core-codefirst` only for relevant EF/relational work
- `error-handling` for API/application failure design
- `logging-elastic` and `btl-security` when available and applicable
- `btl-backend-environment` for BTL environment/tooling details
