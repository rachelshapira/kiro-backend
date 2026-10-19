---
name: btl-backend-environment
description: >
  Organization-specific BTL .NET backend environment reference for RENV, Vault,
  ArgoCD/Kubernetes, local z configuration, shared NuGet packages, Elastic logging,
  debugger setup, and deployment conventions. Load only for tasks that interact
  with these BTL facilities; not for generic backend coding.
---

# BTL Backend Environment Reference

Use this only when the target project actually uses these BTL facilities.
Do not automatically migrate existing projects. Verify current project scripts
and DevOps documentation before changing environment behavior.

## Build and quality baseline

For new projects, define `Nullable`, analyzers, and warnings-as-errors via
`Directory.Build.props` according to the approved CI setup. Do not retrofit
project-wide warnings-as-errors in the middle of an unrelated bug fix.

## Logging and shared packages

- The BTL Elastic Enterprise integration uses Serilog and `Btl.Utils.Core`
  v4.0.3+ where approved and compatible. Existing old logger is not suitable
  for the newer Elastic cluster. Consult `logging-elastic` if available.
- `appName` is the actual project key (for example `gva` or `rps`).
- Prefer approved BAP packages when suitable instead of reimplementing shared concerns.
- Internal NuGet feed:
  `https://artifactory.snifim.blroot/artifactory/api/nuget/nuget-virtual`
- BAP source:
  `https://bitbucketp.snifim.blroot/projects/BAP/repos/btl.utils.core`
- Verify approved versions, license, and runtime compatibility before adding packages.

## RENV / Kubernetes (existing BTL deployments)

RENV resources may come from ArgoCD/Kubernetes (nonsecret values), Vault
(secrets and connection strings), and mounted persistent filesystems:
`\\blappdata.snifim.blroot\BtlaD\RENV`.

The `z` script combines YAML in precedence order (last wins):

1. `./env.yaml`: checked-in defaults and placeholders only.
2. `/mnt/shared/vault/<ProjectCode>2<RENV>/env.yaml`: secret RENV layer; do not read or expose it.
3. `~/<ProjectCode>/env.yaml`: developer overrides; do not read or expose it.

The generated `appsettings.DevelopmentDD.json` is untracked and secret-bearing.
Existing projects may also use `appsettings.Development.json`, `appsettings.k8.json`,
and `appsettings.json`. Verify the effective configuration and actual app bootstrap
logic: `ASPNETCORE_ENVIRONMENT` controls environment-specific configuration, but
custom code or deployment can change file selection/order.

**Never run `z_cat_env` or print merged settings in the agent context:** output can
contain secrets. Ask for sanitized keys or safe status checks when required.
`z_edit_env` opens private developer overrides: never read them into AI context.

## Secrets and excludes

Ensure `.gitignore` and `.kiroignore` exclude at least:

```gitignore
.env
appsettings.DevelopmentDD.json
```

Also exclude other locally generated or secret-bearing files used by the project.
Do not inspect contents of Vault, developer environment overrides, or live secret stores.
Keep checked-in `appsettings.json` free of secret values.

## Probes, docs, retries

- BTL Kubernetes integrations may require `/api/healthcheck` returning 200 and
  `"OK"` for an existing probe contract; preserve it where required. Add dependency
  readiness checks only when infrastructure expects them.
- Restrict Swagger or Scalar to approved environments. Some BTL deployments use
  `RUNMODE=Development`; do not assume this replaces security/access controls.
- Replace legacy blanket 10-second/infinite retry loops with operation-specific
  bounded policies, unless an explicitly required process-supervision design
  establishes restart/retry behavior.

## Local debugging (only if relevant)

- Organization-supplied C# extensions may exist at `/mnt/shared/kiro_vsix`.
- Legacy guidance: Free/Libre C# Support and C# Dev Tools; restart OmniSharp via
  command palette. Verify installed versions and current tooling before advising.
- For attach debugging, stop conflicting `z` processes and attach to the running
  .NET process using the project's approved debugger configuration.
- Missing `ProjectReference` can cause CS0012, but diagnose first; don't add a
  dependency solely to hide an analyzer symptom.

## Organizational DoD

GIMLAUT-1477 includes security, tests, business/technical documentation,
Code-First data and ERD alignment where applicable, performance, resilience,
observability, and accessibility for frontend work. Apply requirements to the
actual affected scope and track policy exceptions explicitly.
