# Spec Delta

## Purpose

Prepare conservative, independently disabled v1.3 delivery verification without turning shadow observations into permission or reducing required test coverage.

## ADDED Requirements

### Requirement: Disabled source publication retains full CI

The system SHALL publish v1.3 delivery disabled, independently of claude-current. Disabled, missing, malformed or ambiguous policy SHALL retain full CI and refuse v1.3 candidate proof. Shadow observations SHALL remain non-authoritative. Source publication SHALL NOT change integration workflow commands/jobs, the coverage baseline, Mergify queues, repository settings, releases or running lanes. The existing qualification caller SHALL additionally watch the new control files without changing its jobs.

#### Scenario: Proposed headers while disabled
- **WHEN** a PR declares v1.3 shadow and delivery intent but the trusted v1.3 delivery policy is false
- **THEN** ordinary full CI remains required and v1.3 candidate verification refuses

### Requirement: Immutable source and required-engine prerequisite

After separate activation, eligibility SHALL require the exact reviewed P03/P05 source identity and original parent, a main-targeted open ready PR, unique matching opt-ins, no unresolved dependency, P01's exact merged head, unchanged required-PostgreSQL workflow/baseline and foundation files/markers, and the complete cut's regular-file/empty-marker diff. All identity, fast-run, review and reread requirements of existing delivery SHALL remain applicable.

#### Scenario: Pending P01 or unresolved dependency
- **WHEN** P01 remains open or a proposed source still declares Depends-On
- **THEN** v1.3 omission and candidate verification refuse without changing the source

#### Scenario: Owner resolves a reviewed child onto main
- **WHEN** P01's required-engine contract has landed unchanged, an exact reviewed child targets current main without dependency, and all authenticated source evidence passes
- **THEN** the prepared source verifier can recognize that cut under a separately enabled policy

### Requirement: Scope-bound heavy and historical proof

The system SHALL accept at most two independent members from one delivery scope, authenticate the existing complete full-suite candidate caller and exact-head reviews, bind v1.3 receipts to an explicit scope, reconstruct each clean merge prefix and separately verify actual historical merged records and trees. Mixed scopes, unknown scopes, reruns, stale evidence, changed policy/contracts, missing/skipped heavy jobs or lookalike trees SHALL NOT qualify omission.

#### Scenario: Simulated eligible batch
- **WHEN** disposable local fixtures enable v1.3 and supply two eligible immutable sources with complete authenticated evidence
- **THEN** candidate and historical readers verify ordered tested and landed trees with the existing full job inventory

#### Scenario: Cross-scope or changed foundation
- **WHEN** a candidate mixes claude-current with v1.3 or the required-engine foundation changes after qualification
- **THEN** proof is refused and full CI remains the fallback

### Requirement: Separate live adoption authority

The shared Claude/Codex instructions SHALL distinguish checked disabled source publication from activation, manual queue admission, live efficiency acceptance and assembly/session adoption. No live queue command or owner-PR edit SHALL follow from this change.

#### Scenario: Checked source merges complete
- **WHEN** these source PRs pass latest-head full CI, reusable qualification and exact-head review and merge normally
- **THEN** the v1.3 scope remains disabled and activation/canary/settings/pin/lane actions remain separately gated
