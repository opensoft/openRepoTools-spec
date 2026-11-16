# Spec Delta

## ADDED Requirements

### Requirement: Admission caller movement discards the entire observation

An admission artifact reader with authenticated pending observation explicitly
enabled SHALL permit a fresh bounded observation, but SHALL NOT return proof,
when its final caller reread differs and independently authenticates the same
first-attempt caller ID, candidate head, queue branch, repository, workflow,
event and actors with valid pending or successful state. The next observation
SHALL reauthenticate the real archive and all independent source/live evidence.
Only a byte-stable observation SHALL return a record. The existing wait budget
SHALL remain unchanged; exhaustion SHALL retain full CI.

#### Scenario: Caller metadata changes while admission is read
- **WHEN** a valid first-attempt caller changes while its authentic admission artifact is read
- **THEN** discard the raced view with no proof and independently observe again within the existing bound

#### Scenario: Later observation loses evidence
- **WHEN** the following observation has a missing or changed artifact, review or source
- **THEN** the previous observed proof SHALL NOT cover the new evidence and omission SHALL refuse

### Requirement: Invalid or historical reads remain immediate refusals

Changed artifact metadata/digest, malformed/missing/forged evidence, changed
caller identity, failed/cancelled/unknown state or rerun SHALL refuse without
pending authority. Pending states SHALL have null conclusion. Qualified,
historical and non-pending reads SHALL preserve immediate refusal on movement.
All first-attempt heavy, full-job inventory, source/foundation/review, immutable
prefix and actual landing proof requirements SHALL remain unchanged.

#### Scenario: Caller changes to a rerun or failure
- **WHEN** the final caller no longer has the exact authenticated first-attempt identity or acceptable state
- **THEN** refuse immediately rather than wait or accept an older success

#### Scenario: Qualified receipt changes during reading
- **WHEN** a qualified, historical or non-pending artifact/caller changes during the read
- **THEN** retain immediate refusal with no new retry mechanism

### Requirement: Refusal diagnostics expose no private values

Diagnostics SHALL use fixed reason vocabulary and SHALL NOT echo API metadata,
titles, bodies, tokens or arbitrary exception text. Historical generic refusal
SHALL NOT be represented as proof of an unrecorded cause. A passing source/local
check SHALL NOT be represented as accepted candidate/heavy/landing evidence.

#### Scenario: Private title accompanies caller movement
- **WHEN** a caller title changes while a pending-enabled admission read is in progress
- **THEN** log only the fixed admission-reread reason, with no title value
