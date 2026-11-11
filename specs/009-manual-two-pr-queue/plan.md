# Plan

Code baseline `ad740a1e0ba0923859d16fd022bd09c374b007db` (code #37, including #34).
Spec baseline `dd0fd170ecdbeaf77b34add8ca0e405a84f42481` (spec #24).

Add `.mergify.yml`, offline policy/CI-contract tests and a code-local runbook;
link the runbook from the existing shared agent reference. No production code,
workflow, dependency, shape copy or pin changes. Use isolated paired worktrees
under the assembly's ignored worktrees directory, not active lane checkouts.
The isolated assembly still lacks an installed Speckit hook; no global/bootstrap
or active feature selection is changed to create this bounded paired feature.

Validate the real config with Mergify's public schema validator where available;
test parsed queue structure and negative eligibility vectors offline. Use the
existing pinned YAML reader and pytest 8.3.4 through tests/run.sh in a disposable
instance of the existing py-bench image. Retain existing CI/sharding contract
tests, which ensure the required jobs still run on arbitrary PR bases.

## Governed invariants

- `.mergify.yml` — manual admission and full-CI merge policy.
- `.github/workflows/` — existing triggers, gate names and release contracts; unchanged.

Review app/ruleset/plan access before any future live test; no permissions or
feature purchase is inferred from a valid configuration. The current task ends
with open PRs and exact-head Codex review requests, not merge or activation.
