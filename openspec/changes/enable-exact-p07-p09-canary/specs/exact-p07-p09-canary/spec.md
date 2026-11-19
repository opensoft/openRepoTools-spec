# Spec Delta

## Purpose

Qualify the next two v1.3 task sets as a single ordered canary without
extending the existing efficiency policy to other work.

## ADDED Requirements

### Requirement: Only exact current reviewed sources can use fast-only CI

The route SHALL bind #97 P07 and #103 P09 to their unchanged exact source
heads, bodies, reviewed linear histories and path/marker inventories in
trusted main. Source omission SHALL require fresh exact current-base fast
evidence and live rereads, with review and owner receipt checks for queue
admission. All other new, mixed, drifted or uncertain cases SHALL retain
full CI under their existing policy.

#### Scenario: An old green or unknown source is presented
- **WHEN** an event has a stale base, a changed source, or untrusted proof
- **THEN** it cannot obtain the canary's fast-only route or queue admission

### Requirement: Heavy execution requires the complete ordered pair

The candidate gate SHALL allow only P07 followed by P09 as two separate
ordered merge prefixes on the same verified base. Both recomputed prefix
trees SHALL match. P07 SHALL preserve the reviewed foundation and the
second prefix SHALL make only the reviewed before/after transition. It
SHALL run the unmodified full required suite exactly once for acceptance.

#### Scenario: Only P09 or an invalid pair is available
- **WHEN** the candidate is incomplete, reversed, altered or ambiguous
- **THEN** it cannot start the canary heavy suite or publish qualification

### Requirement: Live acceptance proves both landed prefix trees

The acceptance record SHALL contain both authenticated fast-only source
receipts, one complete authenticated heavy run against the tested combined
tree, and both matching landed prefix trees in order. A valid atomic or
sequential push SHALL preserve the same proof. A matching first-prefix
event may reuse the completed combined heavy receipt, but is never full
canary acceptance by itself. Missing or premature qualification, duplicate
heavy execution, or mismatched history SHALL NOT grant omission or acceptance.

#### Scenario: The reviewed pair lands with both qualified trees
- **WHEN** actual candidate and landed history have the exact same ordered
  prefix trees and all required evidence is present
- **THEN** record acceptance for this pair only; keep P08 held and other
  scopes unchanged
