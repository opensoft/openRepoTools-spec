# Verification — 2026-11-10 (America/La_Paz)

## Source and isolated local checks

- Mergify official JSON Schema validation: **passed**, using jsonschema 4.25.1
  and the real .mergify.yml, not a substitute configuration. Source:
  https://docs.mergify.com/mergify-configuration-schema.json. Retrieved schema
  SHA256: f6f924c812112063e81b42f087200935e317e66f64942e001545fea2effba1cb.
  This is the schema URL used by Mergify's official pre-commit hook; validation
  is not service access, condition-engine simulation or live queue acceptance.
- In a disposable instance of the existing py-bench image, pinned upstream
  files, a fresh virtual environment with pytest 8.3.4 and no test-time network:
  `bash tests/run.sh -k 'ci_pilot or tests_workflow or ci_shards'`.
  **182 passed, 4278 deselected in 29.87 seconds**, no selected skips.
- The 65 new queue tests run under the existing ci-pilot selector without a
  workflow edit. Parsed config tests and bounded offline condition vectors
  cover manual-only structure, batch/retry limits, every full gate (including
  missing/pending/skipped/neutral/failed/cancelled), app-qualified checks, mixed
  paths, empty/unknown context, fork/draft/closed PRs and predecessor markers.
  The existing 117 classifier/workflow/shard tests retain their coverage.
- `openspec validate manual-two-pr-queue --strict`: **passed**.
- `git diff --check`: **passed**. All existing workflow files, tests/run.sh,
  the classifier, dependency/shape pins and runtime commands are unchanged.

## Preservation and publication boundaries

The new paired feature worktrees are isolated from lanes 1–5. No lane message,
claim, binding, register, profile or existing worktree was changed. Tests ran
outside the active bench's PID/lock namespace; no running bench was restarted.

Only configuration/tests/reference and canonical governance PRs are prepared.
Hosted exact-head CI and Codex review are separate publication checks. No pilot
label, canary branch, queue request, candidate, merge, app grant, plan purchase,
ruleset operation, release, tag, assembly pin or archive was performed. Verify
service access and obtain separate live-canary approval before queue use.
