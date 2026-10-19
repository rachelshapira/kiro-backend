---
inclusion: manual
---

# AI Development Rules

Foundation rules for AI-assisted backend development in this repository. These
sit above the `.NET Backend Standard` (`dotnet-backend.md`) and apply to every backend task when loaded by the backend agent. Where `dotnet-backend.md` gives the concrete .NET baseline, this file
gives the operating discipline: how to discover, how to scope, and how to prove.

## Discover Before Asking

Before asking the developer a technical question:

1. Read project steering.
2. Inspect repository configuration (`*.sln`, `*.csproj`, `global.json`,
   `Directory.Build.props`, `Directory.Packages.props`, and non-secret configuration keys).
3. Inspect relevant source code.
4. Find similar existing functionality and follow the nearest comparable feature.
5. Inspect tests when useful.

Ask only when the answer cannot safely be discovered and the decision materially
affects:

- business behavior
- architecture
- security
- data model
- external API contract
- infrastructure

## Technical Source of Truth

For technical reality, trust in this order (mandatory security and explicit requirements still take precedence over insecure existing code):

1. Current repository
2. Current project configuration
3. Project steering (`tech.md`, `structure.md`, `architecture.md`, `conventions.md`)
4. Organization defaults (`dotnet-backend.md` and the backend skills)
5. Generic best practices

If steering conflicts with the repository:

- trust repository reality
- report the stale steering
- update it when appropriate

## Change Scope

Implement the smallest complete change.

Do not:

- modify unrelated code
- modernize unrelated code
- reorganize the solution without need
- upgrade frameworks automatically
- add unnecessary packages
- introduce a new architecture style without need
- reformat unrelated files

## Existing Projects

First discover relevant patterns using `existing-codebase-discovery` when the repository is unfamiliar or the change spans unfamiliar components. Inspect only what the task needs.

Preserve:

- existing architecture
- project boundaries
- established conventions
- existing approved libraries

Existing patterns are evidence, not permission to copy known defects or insecure practices. Report material conflicts with security or explicit requirements instead of silently following them.

## New Projects

Use the approved, context-appropriate organizational baseline in `dotnet-backend.md`.
Treat mandatory security and deployment requirements as requirements; treat
architecture, persistence, and library choices as defaults only where relevant.
Start with the smallest maintainable design that meets the actual requirements.

Do not ask for a technical choice if a clear organizational default already exists.

## Evidence

Never report completion merely because files were created.

Build/test evidence is required when applicable (see `task-verification.md`).

## Change Safety

- Check `git status` and inspect affected diffs; preserve developer changes.
- Never commit, push, deploy, or apply production migrations without explicit approval.
- Avoid destructive commands and reading generated secrets or private environment files.
- If a current repository pattern is insecure, do not copy it; report and resolve the risk.
