## Purpose

Qualify narrow PR scope observations while preserving full integration acceptance
and the existing independent lane, review and release contracts.

## ADDED Requirements

### Requirement: Shadow classification never relaxes acceptance
The pilot SHALL preserve existing full CI and release workflows and SHALL emit
full-CI-required and no-queue-admission on every classification result.

#### Scenario: A narrow component change is observed
- **WHEN** an open non-draft same-repository PR changes only regular added or
  modified `claude-current` or `tests/test_claude_current.py` files
- **THEN** it is a shadow candidate with exact head/base identities, full CI
  required, and no queue admission

#### Scenario: An uncertain change is observed
- **WHEN** context is missing, malformed, foreign, closed, dequeued or draft,
  commits cannot be read, ancestry is unrelated, or paths are mixed/unknown,
  deleted, renamed or nonregular
- **THEN** the pilot reports full scope and grants no admission or skip

### Requirement: Event classification uses the actual immediate base
The pilot SHALL compare the PR head against its immediate base's merge base.
Pushes, unsupported events and unverified metadata SHALL retain full scope.

#### Scenario: An upper PR changes one allowed file
- **WHEN** a predecessor adds unrelated files and the upper PR changes only an
  allowed file relative to that predecessor
- **THEN** only the upper PR's own changes are considered for shadow scope

#### Scenario: Metadata changes without a code change
- **WHEN** labels or body are edited on an open PR, including by another actor
- **THEN** classification is recomputed from git and immutable identities, never
  granted by labels, actor identity or body text

### Requirement: Both agents retain the same bounded delivery contract
Claude and Codex SHALL read one code-local task-set instruction source while
preserving assembly/spec ownership, exact-head review, safe intermediate states,
existing lane claims, and ordinary full acceptance.

#### Scenario: A lane continues current work
- **WHEN** it reaches a safe checkpoint and reads the new instructions
- **THEN** its session, worktree, ownership and active task IDs are preserved;
  targeted verification from PR #34 is not represented as full acceptance
