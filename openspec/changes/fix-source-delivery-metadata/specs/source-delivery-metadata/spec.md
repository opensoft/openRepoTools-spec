## Purpose

Allow unrelated source-description prose to evolve before CI without changing
the fixed delivery decision, Git identity, review trust or conservative fallback.

## ADDED Requirements

### Requirement: Both event and live delivery decisions qualify independently
Scoped source routing SHALL permit differing description prose only when both
the triggering event and current PR contain exactly the supported unique shadow
and delivery opt-ins and neither declares a dependency. Editing actor identity
SHALL NOT confer trust or change eligibility.

#### Scenario: An app appends only a summary before routing
- **WHEN** prose differs but both descriptions retain the same valid fixed decision
- **THEN** unrelated prose SHALL NOT by itself force full fallback

#### Scenario: A decision is missing, duplicated or dependent on either side
- **WHEN** either description lacks a unique supported opt-in or declares a dependency
- **THEN** routing SHALL retain full CI even if the other description qualifies

### Requirement: Identity and live consistency remain strict
All other event/live source snapshot fields SHALL match exactly. Existing Git
scope, test-merge parents, branch history, effective protection and authentic
fast evidence SHALL still qualify. Subsequent live rereads SHALL remain strict,
including the full description, and uncertainty SHALL retain full CI.

#### Scenario: Head, base, draft state or other identity differs
- **WHEN** any non-prose snapshot field or existing provenance check fails
- **THEN** routing SHALL retain full CI

#### Scenario: Description changes during live verification
- **WHEN** the live description changes between verification reads
- **THEN** routing SHALL retain full CI rather than ignoring that race

### Requirement: Acceptance remains evidence based and scoped
The repair SHALL preserve all full coverage, workflow definitions, queue and
receipt trust, settings and excluded resources. Checked source qualification
SHALL precede a fresh two-member canary. The failed initial attempt SHALL remain
failed evidence, not be rerun or relabeled as clean efficiency acceptance.

#### Scenario: The fresh canary qualifies
- **WHEN** exact reviewed members complete fast-only source/candidate CI and one
  complete heavy run with matching landed trees and no duplicate full main run
- **THEN** only that supported scope SHALL receive live efficiency acceptance
