# BTL Kiro backend — final reviewed package

Copy `steering/` to `.kiro/steering/` and `skills/` to `.kiro/skills/` in the target
workspace (or your approved Kiro skill path).

## Renames
- `00-ai-operating-rules.md` → `ai-development-rules.md`
- `01-definition-of-done.md` → `task-verification.md`
Remove the old named files and update any agent references.

## Scope
All three backend steering files use `inclusion: manual` so the backend agent
must explicitly include them in its `resources` (or load them on demand).
This avoids injecting .NET-specific rules into frontend, Jira and other agents.
If this is a dedicated backend-only workspace you may instead deliberately set
selected steering files to `inclusion: always`.

The newly added `btl-backend-environment` skill preserves environment specifics
from the original BTL standards without loading them on every coding task.
The new `existing-codebase-discovery` skill is for unfamiliar legacy codebases.

See `backend-agent-resources.example.json` for the intended resource paths; merge
its `resources` into your real agent configuration rather than replacing the agent.
Check that your installed Kiro version accepts the resource URI patterns; verify
by asking the agent to identify loaded instructions before first use.

Existing `btl-security` and `logging-elastic` were not provided and remain unchanged.
No runtime builds or application tests were performed; these are guidance files.
