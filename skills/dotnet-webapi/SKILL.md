---
name: dotnet-webapi
description: >
  Design or update ASP.NET Core backend HTTP endpoints, DTOs, validation, OpenAPI,
  authorization, health and resilience. Use for API contract changes in .NET projects.
---

# ASP.NET Core Web API — BTL

## Match the project first

Existing project: preserve its supported controller/Minimal API style, framework, route conventions, validation approach, and API versioning. New projects: BTL default is .NET 10 Minimal APIs; organize resource routes with `MapGroup` and thin endpoint handlers.

## Contract and validation

- Use request/response DTOs, not persistence entities; make JSON casing, enum representation and date/time formats consistent with contract.
- GET is safe; use correct 200/201/204/400/401/403/404/409/422 semantics. For 201 creation include a resource location when available; paginate unbounded collections.
- Reuse existing validators. For new .NET 10 apps choose **one** approach: built-in `AddValidation()` for supported validation attributes or an approved FluentValidation pipeline. Do not silently skip required validators or register competing automatic pipelines.
- Report validation failures consistently, normally with `ValidationProblem`/ProblemDetails. Use global exception handling for unexpected failures, never reveal stack traces or sensitive values.

## Security

Require policies/authorization on the appropriate endpoints or groups. Validate ownership/scope, including object-level access. Constrain CORS, requests/uploads and rate limits according to exposure; use TLS, secure secret handling, and least-privilege outbound credentials.

## Documentation and operations

- Use the API documentation approach the project actually has; for new .NET 10 projects prefer built-in OpenAPI. Add Scalar **or** another approved UI only if needed. Do not register both Swagger UI and Scalar without a deliberate reason.
- Disable/protect interactive docs outside permitted environments. For BTL K8s use required `/api/healthcheck`; optionally separate liveness and readiness probes that check relevant dependencies.
- CancellationToken and bounded timeouts/retries for outbound calls; structured correlation logging and tracing.

## Done

Verify successful behavior, expected errors, validation and authorization changes at the HTTP boundary with appropriate automated tests.
