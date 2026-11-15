## Purpose

Observe narrowly bound real v1.3 source cuts after their exact prerequisite
lands, while preserving dependency history and complete CI without authority.

## ADDED Requirements

### Requirement: Known prerequisite topology is immutable
A dependent cut observation MUST bind a main-owned source PR/head/branch,
the exact single-parent prerequisite source and its verified merged contract.
Its base MUST be that prerequisite's exact head or current main. Other stacks,
unmerged prerequisites, duplicate/unknown dependencies and drift MUST fall back.

#### Scenario: P01 is open
- **WHEN** a matching bound child is presented before P01's contract is merged
- **THEN** no proposed cut is reported and full CI remains required

#### Scenario: Owner retargets without rewriting the source
- **WHEN** the bound child retains its exact head after P01 lands and its base is current main
- **THEN** the resolved dependency can be observed without rebasing or flattening it

### Requirement: Intervening cut content cannot be hidden
A known-child observation MUST verify the exact regular-file/task-marker cut
against its prerequisite and require main's cut paths to be unchanged since
that prerequisite. It MUST reject extra paths, missing markers, type/mode or
content drift and arbitrary ancestry, preserving unrelated main changes.

#### Scenario: Main advanced only outside the cut
- **WHEN** main adds unrelated observer files while the bound source remains unchanged
- **THEN** those missing source-side files are not mistaken for cut deletions

#### Scenario: Main changed a cut path
- **WHEN** main changes or lands any named cut file or marker after the prerequisite
- **THEN** observation falls back without overwriting or merging anything

### Requirement: Source push observations bind the live source PR
A known-child push MUST bind repository, ref and SHA to the immutable source
binding and a current open nondraft source PR with explicit matching shadow
intent. Source, prerequisite and main identities MUST be reread. Unsupported
pushes and stale snapshots MUST retain full fallback.

#### Scenario: The source intent changes during a push observation
- **WHEN** the live PR body or source ref changes between reads
- **THEN** the proposed cut is cleared without granting CI omission or admission

### Requirement: Observation never becomes rollout authority
Every result MUST retain disabled efficiency, full required CI, skip false,
admission false and not-qualified. Existing independent-source behavior and all
ordinary/reusable full coverage MUST remain. Workflows and delivery gates
MUST NOT consume a positive observation as permission. PG qualification, activation,
canaries, queues, release acceptance and assembly/session adoption stay separate.

#### Scenario: Every known-child proof succeeds
- **WHEN** a bound P03 or P05 cut is observed after P01's merged contract
- **THEN** only the proposed-cut observation changes, never CI or merge authority
