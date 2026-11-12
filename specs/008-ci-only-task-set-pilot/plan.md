# Implementation plan

Code baseline: `opensoft/openRepoTools-code@749e969ff92cc8b25728a6933d576a0762852622`.
Spec baseline: `opensoft/openRepoTools-spec@77b6815`.
Assembly context: `opensoft/openRepoTools@822e2bff7caa367b6422c1b7308a2f8b93509538`.

One task set and one code PR: a standard-library shadow classifier, offline git
fixtures and workflow tests, an additive read-only workflow, and shared code-local
instructions linked through AGENTS.md (CLAUDE.md already points there).
No policy-owned file is copied from or edited in the pinned standard.

Use isolated paired worktrees, not lane worktrees. The fresh assembly has no
installed `.specify` hook; no bootstrap is run against active work. Keep canonical
feature artifacts in this spec worktree; no alternate task checklist in code.

Verify in a disposable instance of the existing py-bench image, without network,
using the canonical tests/run.sh wrapper. Preserve the running bench's PID
namespace and workstation test lock. CI remains the full-suite record.

## Governed invariants

- `.github/workflows/` — changes CI event execution; existing gates stay intact.
- `.github/ci/pilot_scope.py` — advisory only; no future admission authority implied.

## PR 34 coordination

Code #34 at `13600fd1dda267efd84b3f894ba0c7be373ddd80` adds only verify.yml.
No overlapping file is changed. Cross-link its targeted-feedback contract in
instructions and PR bodies; do not copy, cherry-pick, merge or dispatch its branch.
Coordination: code PR #34 comment `6783470163`.
