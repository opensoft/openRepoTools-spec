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
All other event/live source snapshot fields SHALL match exactly, except the
bounded generated-hint handling below. Existing Git
scope, test-merge parents, branch history, effective protection and authentic
fast evidence SHALL still qualify. Subsequent live rereads SHALL remain strict,
including the full description, and uncertainty SHALL retain full CI.

#### Scenario: Head, base, draft state or other identity differs
- **WHEN** any other identity or existing provenance check fails
- **THEN** routing SHALL retain full CI

#### Scenario: Description changes during live verification
- **WHEN** the live description changes between verification reads
- **THEN** routing SHALL retain full CI rather than ignoring that race

### Requirement: Generated merge hints are nullable metadata, not tested identity
At the initial source and candidate event/live comparison, each generated
merge_commit_sha hint SHALL be missing/null or equal the trusted GITHUB_SHA.
Only when both qualify may the comparison copy align that field. Unknown or
malformed non-null hints SHALL retain full, even if both snapshots agree.
The tested commit and ordered main/head parents SHALL still be independently
verified. All later live rereads SHALL remain strict. Candidate bodies SHALL
remain exact and genuine admission/source proofs SHALL remain mandatory.

#### Scenario: A current hint materializes after a source or candidate event
- **WHEN** one hint is missing/null and the other is the trusted tested SHA
- **THEN** that difference alone SHALL NOT refuse otherwise fully verified work

#### Scenario: A hint is unknown or the actual tested merge is wrong
- **WHEN** a non-null hint differs from the trusted SHA or ordered parents fail
- **THEN** full CI SHALL remain required rather than trusting either hint

#### Scenario: Candidate admission is absent or its body changes
- **WHEN** a valid nullable-hint transition lacks admission or has differing bodies
- **THEN** it SHALL NOT grant omission; existing bounded pending rules remain unchanged

#### Scenario: A hint changes between live verification reads
- **WHEN** the live hint changes after the first current API read
- **THEN** strict rereads SHALL refuse omission rather than normalize that race

### Requirement: Acceptance remains evidence based and scoped
The repair SHALL preserve all full coverage, workflow definitions, queue and
receipt trust, settings and excluded resources. Checked source qualification
SHALL precede a fresh two-member canary. The failed initial attempt SHALL remain
failed evidence, not be rerun or relabeled as clean efficiency acceptance.

#### Scenario: The fresh canary qualifies
- **WHEN** exact reviewed members complete fast-only source/candidate CI and one
  complete heavy run with matching landed trees and no duplicate full main run
- **THEN** only that supported scope SHALL receive live efficiency acceptance

### Requirement: Authenticated pending observations wait without accepting proof
Source routing SHALL handle an authenticated pending check/run/job observation
before completed-proof raw rereads. Every initial identity, source association,
base-history, job and state check SHALL still pass. Waiting SHALL produce no
proof or omission; a fresh poll SHALL revalidate the complete evidence. All-success
observations SHALL retain exact run/check rereads. Admission SHALL remain
success-only. Initial failures or uncertainty SHALL retain full; the existing
retry budget SHALL NOT increase.

#### Scenario: Fast work completes during the first observation
- **WHEN** initially authenticated pending evidence completes between API reads
- **THEN** source routing SHALL wait rather than accept it or treat that observation
  as a completed stable proof, and SHALL qualify only on a fresh successful poll

#### Scenario: Completed proof changes or admission sees pending work
- **WHEN** an all-success proof changes on reread or admission encounters pending work
- **THEN** the existing refusal SHALL remain, without pending-proof acceptance

### Requirement: Refusal diagnostics preserve privacy and decisions
The router SHALL report verification-stage and fixed-vocabulary refusal codes
to stderr. Snapshot mismatch diagnostics SHALL contain field names only, never
values, bodies, tokens, arbitrary exception text or raw API payloads. Diagnostic
emission SHALL NOT alter routing, waiting, proof schema or merge authority.

#### Scenario: Source snapshot or fast evidence refuses
- **WHEN** current strict snapshot or fast provenance checks refuse
- **THEN** full fallback SHALL remain unchanged and diagnostics SHALL identify
  only the fixed refusal category and any mismatched snapshot field names

#### Scenario: An exception contains private text
- **WHEN** an unrecognized exception reaches the conservative fallback
- **THEN** only a generic code and fixed verification stage SHALL be logged
