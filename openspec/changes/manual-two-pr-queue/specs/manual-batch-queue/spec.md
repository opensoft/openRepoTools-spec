## Purpose

Prepare bounded manual batching of pilot documentation changes while retaining
the complete existing CI and explicit human authorization boundaries.

## ADDED Requirements

### Requirement: Queue entry is manual and narrowly scoped
The initial documentation pilot SHALL define one serial queue with at most two PRs
per batch, one active candidate and no automatic enqueue, retry or split.
Only repository writers SHALL be able to request queue entry, and pilot PRs
SHALL be same-repository, open, non-draft, explicitly labeled pilot branches
changing only the two code-local pilot references. A non-main base SHALL use
the pilot prefix and carry an explicit predecessor marker.

#### Scenario: An ordinary or mixed PR matches no queue
- **WHEN** a PR lacks the pilot label/prefix, has a foreign or unsupported base,
  or changes runtime, test, workflow, configuration or unrelated files
- **THEN** it is not admitted to the pilot queue

#### Scenario: Eligibility is not an enqueue command
- **WHEN** a scoped PR has its label and green checks but no explicit queue request
- **THEN** no automatic rule enqueues or merges it

### Requirement: Full acceptance is retained on original and combined changes
The queue SHALL require all four existing GitHub Actions gates before admission
and on the combined candidate, retaining the current PR, main, nightly and
release-branch workflows unchanged. Feature 008's classifier SHALL remain advisory.

#### Scenario: One full check is missing or unsuccessful
- **WHEN** guard-launch-mode, tests, tests-no-submodule or package is missing,
  pending, skipped, failed or cancelled
- **THEN** queue admission or candidate landing waits or refuses, never substitutes
  targeted feedback, shadow results or a manually posted success status

### Requirement: Configuration preparation is distinct from live qualification
The current delivery SHALL open configuration and governance PRs only. It SHALL
preserve lanes 1–5 and exclude live canaries, queue operations, app/plan/ruleset
changes, bypasses, releases, tags, assembly pins and installed-session updates.

#### Scenario: Later live preparation encounters an access blocker
- **WHEN** required Mergify access, plan capability or repository permission is absent
- **THEN** the operator stops for separate approval rather than widening authority

### Requirement: Approved rollout adds a conservative manual code queue
Under Brett's 2026-11-11 rollout approval, the configuration SHALL additionally
provide `openrepotools-code` for same-repository open non-draft PRs targeting main
and changing known normal code paths only. It SHALL retain full app-qualified
CI on members/candidate, writer-only manual commands, serial operation, at most
two members and zero retries/splitting. Control-plane, release-stamp and unknown
or mixed paths SHALL refuse. Earlier queues SHALL remain unchanged.

#### Scenario: Reviewed normal code is manually requested
- **WHEN** a repository writer verifies merge authority, current head/base/paths,
  exact-head Codex review, unresolved threads and all full checks, then requests
  the named queue
- **THEN** eligible code can enter combined validation without renaming lane
  branches, adding a review label or replacing active sessions

#### Scenario: Eligibility alone or a changed source head
- **WHEN** no command exists, or a queued source head changes
- **THEN** no automatic admission occurs and changed code requires fresh review,
  CI and a fresh command; operator attestation is not automated Codex enforcement

### Requirement: Label metadata does not duplicate full CI
The rollout SHALL remove tests.yml's obsolete labeled trigger while retaining
unfiltered opened/synchronize/reopened, draft candidates, main/release-branch
pushes and nightly/manual runs. Required gate names, shards and release workflows
SHALL remain unchanged. This dated amendment supersedes only the earlier clause
preserving every trigger, not full acceptance requirements.

#### Scenario: Queued code changes
- **WHEN** another commit reaches a queued PR
- **THEN** synchronize still runs full CI regardless of labels

#### Scenario: Only queued label is added
- **WHEN** Mergify changes a label without changing code
- **THEN** no full tests.yml run or skipped replacement gates are created

### Requirement: Assembly activation is coherent and preserves lanes
The approved rollout SHALL pin merged legs with matching gitlinks, recomputed
digests and workflow SHA references, and point root Claude/Codex sessions to the
same code-local instructions. It SHALL qualify exact assembled pins before
local activation and preserve lanes 1–5, worktrees, bindings and running benches.

#### Scenario: Current lane work still exists
- **WHEN** the authorized clean assembly mounts are refreshed
- **THEN** no active lane branch, worktree, binding or installed session changes
  and no stash, force, cleanup, app access, bypass or release act is inferred

### Requirement: Coverage parity precedes CI efficiency
The coverage-parity preparation SHALL retain all existing full-CI events, job
IDs, commands, shard matrices, dependency modes, environments and aggregation
checks. It MAY add a parameterless workflow_call entry to tests.yml and a
PR-only qualification caller executing that exact same local workflow. No fast
mode, queue trigger, admission or release eligibility SHALL be introduced.

#### Scenario: A reused full job is removed or changed
- **WHEN** a job, shard, checkout mode, command, environment or aggregation
  differs from the reviewed baseline
- **THEN** the parity regression fails rather than accepting reduced coverage

#### Scenario: A full call fails, skips or is cancelled
- **WHEN** the reusable call does not succeed in its entirety
- **THEN** its qualification aggregate fails; ordinary full CI still runs and
  no manually posted success status substitutes for it

### Requirement: Trusted shadow observations grant no authority
The preparation SHALL add a separate trusted-main read-only observer without
changing ordinary full CI, queues, release checks or feature 008's pilot.
Every result SHALL require full CI, deny skips and deny queue admission. Policy
absence and uncertainty SHALL report full fallback, not run head-owned policy.

#### Scenario: A current independent scoped PR or source push
- **WHEN** live API identities, declared intent, current policy/main and regular
  allowlisted Git content agree
- **THEN** the observer may report claude-current scope without changing any gate

#### Scenario: Delayed edited metadata after a merge
- **WHEN** a live merged PR has the same source head and an exact merge/ancestry
  and scoped landed-diff proof despite stale event base or test-merge metadata
- **THEN** the observer may report merged scoped metadata but still requires full CI

#### Scenario: Missing evidence or unsupported topology
- **WHEN** identities move, reads fail, scope is mixed, a PR is closed/dequeued
  without the narrow metadata proof, or candidate/landing integration is unproved
- **THEN** the result stays full and grants no exemption based on actor or prefix

### Requirement: Additive fast and candidate preparation preserves full acceptance
The next preparation SHALL retain all existing full CI, queues, release behavior
and shadow authority. It MAY add fast PR feedback and a workflow_call-only entry
that verifies a bounded exact candidate before reusing the complete full suite.
No queue trigger, admission command, source status writer or ruleset migration
SHALL be introduced by this preparation.

#### Scenario: An exact reviewed candidate is supplied by a future approved caller
- **WHEN** current main, queue ref, ordered independent members, scoped Git content,
  exact-head fast workflow, Codex review and thread evidence all agree
- **THEN** the reusable entry runs every full-CI job on that exact candidate and
  qualifies only after verifier and complete integration both succeed

#### Scenario: Evidence is stale, missing, ambiguous or unsuccessful
- **WHEN** a source/base/ref moves, an API response is incomplete, review/fast
  evidence is absent, scope/topology is unsupported or any integration job fails
- **THEN** verification/qualification fails without a bypass or fabricated success

#### Scenario: Preparation is merged but no activation is approved
- **WHEN** these source files land without a separately approved queue/ruleset change
- **THEN** ordinary full CI remains required and no live batch or canary starts

#### Scenario: Hidden dependency or evidence changed while verification runs
- **WHEN** source heads share non-main history, a fast run predates a base retarget,
  run/base-history reads are incomplete, or fast/review/thread evidence changes
- **THEN** qualification refuses without trusting missing dependency text or a
  mutable current run-to-PR association as historical execution proof
