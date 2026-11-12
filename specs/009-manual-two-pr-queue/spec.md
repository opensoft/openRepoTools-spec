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

## Accepted trusted shadow-classifier prerequisite — 2026-11-11

- FR-014: add only an independent trusted-main observer with read-only contents/PR
  access. Missing policy reports full fallback; no PR-owned policy execution with
  its API token. Source tests run separately and offline without that token.
- FR-015: validate live main/PR/branch identities and local immutable Git diffs for
  the fixed modified regular-file claude-current/test pair. Intent is observational,
  never a review/admission receipt. Recheck main and source identities; uncertainty
  retains full CI. Unsupported dependent topology remains full.
- FR-016: observe already-merged edited metadata only with same-head/live merge,
  parent/ancestry and landed-scope proof. Candidate and main-push observations
  remain explicitly unproved pending the later heavy/candidate identity stage.
- FR-017: all results are shadow/full/no-skip/no-admission. Preserve original pilot,
  all existing full/reusable/verify/release workflows, job baseline and queues.
  Open source/spec PRs only; no merge, canary, ruleset/app change, assembly pins,
  installed tooling or lane/session mutation.

## Full-rollout goal: fast/candidate preparation — 2026-11-11

- FR-018: add exact-checkout fast source feedback and the scoped component/guard/
  hygiene/policy tests, without replacing required full CI or authorizing admission.
- FR-019: prepare a parameterless workflow_call-only candidate verifier, no queue/
  dispatch trigger. Verify genuine Mergify context, live main/ref, one or two unique
  ordered independent main-based PRs, scoped modified regular content, exact fast
  Actions workflow, fresh clean Codex and resolved threads. Missing/ambiguous or
  stale evidence fails; all reads are bounded and metadata permissions read-only.
- FR-020: after verification reuse the same complete thirteen-job tests.yml on the
  exact candidate and require both verifier and full call success in its aggregate.
  No workflow/policy/control changes can be present in a qualifying scoped candidate.
- FR-021: existing full/reusable/verify/release/shadow workflows, coverage baseline
  and queues remain unchanged. No live canary/queue, ruleset/app/release change,
  source success-status writer, dependent-stack/wider-scope activation or lane
  modification follows from this additive preparation.

FR-019 clarification after local review: establish member independence from
Git history, not absence of dependency text. Bind a fast run to its unique source
PR and current main base with complete base-change history: its immutable creation
time must be strictly after every retarget event. Current run PR associations alone
are mutable and insufficient. Reopening after a retarget may produce fresh fast
evidence; rerunning a historical run does not change its original creation time.
Recheck successful fast run/job/check, base history, Codex and threads at the final
source consistency boundary and refuse evidence drift, not just head/ref drift.
