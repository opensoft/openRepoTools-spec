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

## Accepted rollout — 2026-11-11

The preceding preparation-only scope is the original delivery. Brett now
approved normal checked merges of #44, the general manual code queue and
matching spec/assembly pins/instructions. Baselines: code #44 head
`1a49e7bf3c4609791011e13ecf40e67571413848`, spec main
`dbfed9ebfe57484c868b8be1a2a79e1f012f4def`, assembly main
`a29ba3f519a1db386240f7341de479c6a6de63dc`.

Use the existing isolated paired worktrees and a safe-character path allowlist.
Queue commands attest fresh exact-head review; eligibility never auto-enqueues.
Do not change the classifier, full jobs, releases or active lane branches.
Reconcile batches #41/#43, code-local amendments and #44 under spec issue #26.
Keep historical approval records; record current as-built behavior separately.
Use openRepoShape's bump-leg.py to advance merged legs, gitlinks, recomputed
digests and workflow SHA references together. Run make validate and composed
exact-pin CI before refreshing authorized clean local mounts. Do not archive
until assembly landing, or replace installed lane tools/sessions.
