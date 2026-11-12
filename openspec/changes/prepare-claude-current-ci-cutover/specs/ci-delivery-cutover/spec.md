## Purpose

Prepare honest, conservative fast/full CI routing and complete batch qualification
for the initial claude-current scope without activating it or reducing current coverage.

## ADDED Requirements

### Requirement: Activation is explicit and verifies effective protection
The prepared policy SHALL be disabled by default. Full work SHALL be omitted
only when trusted-main configuration explicitly enables it, complete effective
main-branch rules require the app-bound delivery gate and none of the four
legacy full-test contexts remains required. Missing, malformed, truncated,
disabled/evaluate-only or unavailable evidence SHALL retain full CI. The policy
SHALL read rules only and SHALL NOT apply settings or use bypasses.

#### Scenario: Flag is enabled before the service migration
- **WHEN** the old four full checks are still required or the new app-bound
  delivery check is not effectively enforced
- **THEN** all existing integration work remains required despite the flag

#### Scenario: Current preparation is tested
- **WHEN** the source PR runs with the committed disabled configuration
- **THEN** every original full job runs and no live canary or setting changes

#### Scenario: Classic protection still requires an old full context
- **WHEN** the ruleset requires delivery but classic branch protection retains
  any legacy context in its contexts/checks arrays, or classic evidence is incomplete
- **THEN** routing remains full and an already-selected omission cannot pass its
  final gate after such a protection change; no administrative setting is modified

### Requirement: Source feedback is scoped and is not merge authority
An enabled fast source route SHALL require the fixed modified regular-file
scope, explicit delivery opt-in, a current independent main-based same-repository
PR, immutable test-merge identity and successful exact-head fast-workflow
provenance. Other sources and uncertain evidence SHALL retain full CI. The
delivery gate SHALL state verification/routing semantics, not fabricate success
under a full-test name or grant direct fast-source merge authority.

#### Scenario: Verified opted-in source
- **WHEN** complete current source and fast-run evidence agree
- **THEN** fast feedback may satisfy the delivery gate while the source still
  requires the named manual queue and complete heavy integration before merge

#### Scenario: Mixed content, dependency or stale fast evidence
- **WHEN** a source edits control/unknown paths, has unsupported topology or
  moved base/head, or lacks unambiguous fresh fast evidence
- **THEN** routing stays full rather than trusting intent, branch or actor alone

#### Scenario: Clean trusted-main checkout lacks the synthetic merge
- **WHEN** a valid source or candidate PR's immutable event commit is absent
- **THEN** each control independently retrieves the validated object from the
  fixed public repository without changing checkout/refs/credentials, validates
  its exact PR ref and parents, and retains full or fails omission if unavailable

#### Scenario: Outcome is permanently full-only
- **WHEN** a source lacks opt-in, is unsupported, or the authenticated caller
  mandates full CI, or evidence is failed/malformed/unknown
- **THEN** full CI begins without a startup retry delay

#### Scenario: Canonical fast evidence is genuinely pending
- **WHEN** otherwise eligible source evidence binds the correct check/run/job,
  head, PR, base history and current attempt but is queued/in_progress
- **THEN** routing may wait at most30 ten-second intervals plus bounded reads,
  never qualifies pending evidence, and retains full at the limit; strict candidate
  admission still requires actual success

### Requirement: Full coverage and reusable-call semantics are preserved
All thirteen original integration jobs SHALL be preserved, including matrices, commands, environments, checkout modes, artifact accounting and failure checks.
Only exact reviewed routing dependencies/guards and additional control jobs
SHALL be allowed outside the existing hashed job projection. Reusable calls,
release branches, nightly and explicit full runs SHALL remain complete.

#### Scenario: Reusable integration call occurs in a scoped context
- **WHEN** another workflow calls the complete parameterless suite
- **THEN** the caller's authenticated run identity forces every integration job,
  not a source fast route inferred from the retained original event

#### Scenario: Selector, command or failure masking changes
- **WHEN** a test command, matrix, checkout, environment, original job inventory,
  result check or unapproved guard differs from the reviewed full baseline
- **THEN** coverage-parity regression fails instead of rehashing reduced coverage

### Requirement: Candidate omission requires authenticated active integration
Duplicate draft-candidate PR work SHALL be omitted only with a genuine current
Mergify push run at the expected workflow, exact branch/SHA, first attempt and
immutable verified-admission artifact. All source/member, scope and ordered
prefix identities SHALL agree. Any absent, failed, cancelled, ambiguous or
changed evidence SHALL retain full work or fail an already-selected fast gate.

#### Scenario: Candidate PR races the push workflow
- **WHEN** a matching live push run has not yet produced trustworthy admission
- **THEN** a bounded wait or full fallback occurs; actor/prefix alone never skips CI

### Requirement: Heavy qualification is top-level and holds the manual queue
The new independent main-based manual queue SHALL require the honest source
verification gate and actual fast check, then a uniquely named top-level heavy
qualification before merge. Qualification SHALL require authentic candidate
proof, every complete integration job and final live evidence rechecks. Existing
queues, writer-only manual commands, zero retries/splits and release behavior
SHALL remain unchanged. Eligibility SHALL never enqueue automatically.

#### Scenario: Additional integration has not completed
- **WHEN** the candidate's comprehensive jobs or final qualification are pending,
  failed, skipped or cancelled
- **THEN** this named queue cannot merge merely because ordinary checks passed

### Requirement: Landed evidence is separate from open admission
Main-push omission SHALL require a first-attempt successful authentic candidate
run, every expected successful job, complete immutable qualified evidence and
unchanged trusted policy/commands. The actual source heads, ordered landed
parents/prefix trees and GitHub merge records after qualification SHALL match.
Closed source records SHALL never be treated as open admission requests.

#### Scenario: Exact tested prefix lands
- **WHEN** the Mergify main event and live GitHub/Git records prove an exact
  qualified prefix or final tree
- **THEN** duplicate ordinary integration may be omitted, without declaring
  direct full-run release acceptance or success of tests not run in that event

#### Scenario: Lookalike tree, wrong actor, attempt, job or historical policy
- **WHEN** any required immutable identity or complete provenance is missing,
  mismatched, stale, incomplete, unsuccessful or ambiguous
- **THEN** ordinary full integration remains required

### Requirement: Preparation and activation have separate authority
This delivery SHALL open reviewed source/configuration and governance PRs only.
It SHALL document, not apply, the service migration and its changed GitHub merge
floor: source fast verification is not heavy integration, and manual-queue-only
usage is an operator policy unless separately approved controls enforce it.
Managed review requirements SHALL remain unchanged; a local source-preparation
review substitution SHALL NOT become a forged future candidate receipt.

#### Scenario: Prepared implementation exists
- **WHEN** local tests or source CI pass
- **THEN** no merge, enablement, live canary, required-check application, bypass,
  app/paid access, release/tag, assembly refresh or lane mutation is inferred
