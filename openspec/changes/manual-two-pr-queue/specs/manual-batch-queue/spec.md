## Purpose

Prepare bounded manual batching of pilot documentation changes while retaining
the complete existing CI and explicit human authorization boundaries.

## ADDED Requirements

### Requirement: Queue entry is manual and narrowly scoped
The configuration SHALL define exactly one serial queue with at most two PRs
per batch, one active candidate and no automatic enqueue, retry or split.
Only repository writers SHALL be able to request queue entry, and eligible PRs
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
