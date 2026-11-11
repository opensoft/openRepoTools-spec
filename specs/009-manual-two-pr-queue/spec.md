# Feature 009: Manual two-PR queue configuration

Authority: Brett requested opening the config-only PR, retaining full CI and
lanes 1–5, with no canary, bypass, app access or release changes.
Governance: `openspec/changes/manual-two-pr-queue/`.

- FR-001: one manual serial queue, maximum two PRs and one active candidate.
- FR-002: pilot label, head/base restrictions, same-repository open non-draft PRs,
  and only the two pilot Markdown references; mixed/unknown changes refuse.
- FR-003: all four existing full-CI gates from GitHub Actions on original PRs
  and the combined candidate; existing workflow files remain unchanged.
- FR-004: no auto admission, account impersonation, retries, splits or bypass;
  external merges reset candidate validation, external queue edits refuse.
- FR-005: queue preparation does not authorize merge/enqueue/canary/app/release
  operations. Label/review attestation remains explicit and head-specific.

Full CI per PR plus candidate CI is intentional. CI-saving mode is deferred.
Schema and offline policy tests are not live batching acceptance.

## Accepted rollout amendment — 2026-11-11

Brett now approved merging code #44 after CI, adding the general manual code
queue, and updating assembly pins and Claude instructions. The original
preparation-only limits above are historical; this amendment authorizes those
changes, not unrelated merges or live canaries. Reconciliation: spec issue #26.

- FR-006: append `openrepotools-code`: main-only, same-repository, open non-draft
  PRs; at most two members, one candidate, 60-second fill, 90-minute checks,
  zero automatic retries/splitting. Retain earlier queues and all four full gates.
- FR-007: allow known shipped commands, `repos.tsv`, `README.md` and safe paths
  under tests/src/commands/skills/features only. Refuse release-stamp edits,
  workflow/queue/pin/package-control paths and unknown/mixed changes.
- FR-008: repository writers manually request entry after checking merge
  authority, exact-head Codex review, unresolved threads and current head/base/
  paths/full CI. The command is an operator attestation, not automated Codex/SHA
  enforcement. No review label or fabricated success check substitutes for it.
- FR-009: remove only tests.yml's obsolete labeled event. Keep unfiltered PR
  opened/synchronize/reopened, draft candidates, main/release-branch pushes,
  nightly/manual runs, gate names, shard coverage and release workflows.
- FR-010: root Claude/Codex instructions point to one shared code-local workflow.
  Advance only merged leg commits, gitlinks, pin/digests and workflow SHAs in
  lockstep; validate exact assembled pins before authorized local activation.

Do not flatten dependent stacks into this main-only queue. Preserve lanes 1–5,
worktrees, bindings and running benches. No automatic enqueue, bypass, app
access, paid upgrade, release/tag or installed-session change is authorized.

## Accepted coverage-parity prerequisite — 2026-11-11

- FR-011: add only a parameterless reusable entry to the existing full workflow.
  Preserve ordinary PR/main/release-branch/nightly/manual events and all six job
  definitions: guard, five normal shards, four no-submodule shards, their two
  fail-closed aggregators and full package checks (13 expanded jobs).
- FR-012: a separate PR-only qualification caller runs the same local workflow
  at the caller commit and rejects any unsuccessful call. A reviewed inventory
  and mutation regressions prove environment/job/matrix/step parity. No selector
  or conditional skip input is exposed; discovered new tests still run.
- FR-013: this delivery opens source/governance PRs only. No merge, fast-only CI,
  authoritative classifier, queue command, live canary, ruleset/app/release change,
  assembly pin bump or lane/session modification is authorized.
