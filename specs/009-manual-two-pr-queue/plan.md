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

## Coverage-parity preparation — 2026-11-11

Authority: Brett approved implementing the coverage-parity PR first, keeping
ordinary full CI and lanes 1–5 unchanged. Baselines: merged code d3c55e55aa3516cbaa1d68a77061ddd50191d49a,
spec 66ba22f429caea8da4c664f04f5567bb924064ac. Reuse the existing clean paired
feature worktrees; do not change the pinned mounts or active feature selection.

Add workflow_call without modifying the full workflow's jobs/environment/events.
Add a PR-only same-commit local caller, reviewed baseline, parity/mutation tests,
and bounded code amendment/operator notes. Run focused workflow/shard/queue
regressions through tests/run.sh in a disposable resource-limited py-bench image.
Verify ordinary hosted full CI separately from reusable-call qualification.
Open code and spec PRs with exact-head Codex requests; do not merge or activate.

Local implementation evidence: code bd56ea742233c76e7aefb2465882facde50d86ab.
Red-first parity run: 12 failed / 24 passed before the entry point/caller existed.
Final focused workflow/shard/pilot/parity regression: 419 passed, no skips,
through tests/run.sh in an offline disposable py-bench image (2 CPUs / 2 GiB).
Actionlint and strict OpenSpec validation passed. Hosted ordinary/reusable full
CI, review and eventual landing remain separate pending qualification gates.

## Trusted shadow-classifier preparation — 2026-11-11

Authority: Brett approved the shadow-classifier PR next, ordinary full CI and
lanes 1–5 preserved. Merged baselines: code a16ac8f7a5d14c8dc215a1b82832bc6e9a9e40ca,
spec 476dadc5172be2f88a2a0cd21c744c35b9521d58. Reuse the clean isolated paired
feature worktrees without changing active feature selection or pinned mounts.

Implement a separate ci-shadow workflow, bounded read-only observer, mocked API
and real local-Git tests, plus code-local notes/amendment. Port proof patterns
from OpenBenches stack-ci.py/project-stack-ci.py, not their live skip authority.
Test read failures, stale refs, main moving during observation, third-party edits,
post-merge stale retarget metadata, closed/dequeued/candidate events, unknown
payloads, topology, paths/modes and non-execution of untrusted content. Run focused
workflow/parity/shard/pilot/queue regressions offline through tests/run.sh in the
resource-limited py-bench image. Validate OpenSpec and workflow lint. Open PRs,
request exact-head Codex review and retain full ordinary hosted CI. No merge.

Local qualification: 76 new observer/contract vectors passed; 495 focused tests
passed with no skips across shadow, original pilot/queue, workflow, coverage-parity
and shard-accounting regressions. Native Git fixtures and mocked APIs ran offline
through tests/run.sh in a disposable py-bench image limited to 2 CPUs / 2 GiB.
Workflow lint, strict OpenSpec and whitespace checks passed. The initial red test
failed before shadow_scope.py existed. Bootstrap policy-unavailable is expected
on the preparation PR and is not a live positive-scope/candidate qualification.
