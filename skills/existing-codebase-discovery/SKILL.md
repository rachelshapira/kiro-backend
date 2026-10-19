---
name: existing-codebase-discovery
description: >
  Quickly understand an unfamiliar or existing codebase before implementing a change.
  Discover the relevant architecture, conventions, dependencies, data flow, tests, and
  safest extension point. Use for existing-project features, bug fixes, refactors, and
  integration work when project patterns are not yet clear. Do not run for trivial,
  already-understood edits or when starting a new empty project.
---

# Existing Codebase Discovery

## Goal

Find **just enough evidence to make the smallest safe change**. Do not audit the
whole repository, rewrite documentation, or propose a new architecture by default.

## Workflow

1. **Identify scope** — Restate the requested behavior and locate the relevant
   project, entry point, module, or failing flow. Check `git status` before edits;
   preserve uncommitted work.
2. **Read local rules** — Check applicable `.kiro/steering`, repository docs, and
   project-specific instructions. Do not load unrelated frontend or infrastructure rules.
3. **Inspect reality** — Read the relevant solution/project files, target code,
   configuration *keys* (not secret values), and dependency versions. Identify runtime,
   architecture, persistence, external APIs, and authorization boundaries **only as relevant**.
4. **Find the nearest example** — Locate one or two similar features, tests, or
   integration flows. Prefer actual current implementation over generic templates.
5. **Trace the affected path** — Follow request/event → handler/business rules →
   persistence or external call → response/effect. Note contracts, side effects,
   validation, error handling, and data migration implications where applicable.
6. **Find verification** — Identify existing test commands and the narrowest test
   that demonstrates the requested behavior. For bugs, reproduce or describe the
   failing condition before modifying code when feasible.
7. **Choose the change** — State the smallest affected files, reusable patterns,
   verification plan, and any meaningful risks or unknowns; then implement or hand
   off. Stop discovery once these are known.

## Rules

- **Existing project first:** Preserve its structure, current supported stack,
  naming, dependencies, and established implementation patterns unless the task
  explicitly requires otherwise.
- **Not blind imitation:** Never copy insecure code, known defects, or unsafe
  operational behavior merely because it already exists. Flag material conflicts.
- **Org standards are defaults:** Follow mandatory security and policy requirements;
  do not force new-project patterns, a different database, or a framework upgrade
  onto an established system.
- **No speculative abstractions:** Prefer an existing extension point over a new
  interface, mediator, repository, base class, package, or architectural layer.
- **Evidence over assumptions:** Cite paths, symbols, commands, or tests found in
  the repository. Do not invent components, ports, APIs, or architecture.
- **No secret exposure:** Never open, echo, paste, or summarize secret values.
  Inspect safe config samples/schema only.
- **Ask sparingly:** Ask the developer only for unresolved business, security,
  contract, or architecture decisions that cannot be safely inferred.
- **Keep it bounded:** Start from task-related directories; expand only when a
  dependency or side effect requires it. Do not scan the whole repository routinely.

## Short Output

Before modifying a nontrivial, unfamiliar area, provide at most five concise items:

- **Relevant flow:** entry point → affected components
- **Existing convention:** nearest analogous implementation (paths)
- **Proposed change:** minimal files/behavior
- **Verification:** exact existing or proposed checks
- **Open risks:** only material unknowns (omit if none)

Do not generate a large discovery report unless the user explicitly requests one.
For implementation completion, follow `task-verification.md` and report actual
build/test results—not assumed success.
