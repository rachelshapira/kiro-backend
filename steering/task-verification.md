---
inclusion: manual
---

# Task Verification & Definition of Done

Use for backend tasks through the backend agent. Complements the organizational DoD
(GIMLAUT-1477) in `dotnet-backend.md`.

## Completion gate

A task is DONE only when, where applicable:
1. Requested behavior is implemented and checked.
2. Relevant build passes.
3. Relevant automated checks pass. New features require at least one runnable
   behavioral check; bug fixes should include a regression test when feasible.
4. Changed files and `git diff` are reviewed; existing developer changes preserved.
5. Required validation, authorization, security, configuration and data impacts
   have been addressed.
6. No unresolved blocking failures or introduced secrets remain.

Choose checks proportionate to change impact: endpoint changes need success/error
(and authorization when relevant); DB changes need migration/data checks;
refactors need regression evidence. Start with the narrowest relevant checks.

If required checks cannot run, mark them `NOT RUN` with a reason and report overall
status `PARTIAL` or `BLOCKED`. Never hide failures or claim PASS without evidence.

## Change safety

Do not commit, push, deploy, run destructive commands, or apply production DB
migrations without explicit authorization. Do not read or expose secret values.

## Completion report (compact)

- **Status:** PASS | PARTIAL | BLOCKED
- **Changes:** files/components and behavior changed
- **Verification:** exact build/test commands and PASS | FAIL | NOT RUN results
- **Risks:** blockers or residual risks; `None` if none

Mention configuration, database, security, deployment impacts, assumptions or
skills used only when material. Keep reporting brief for trivial changes.
