# Spec Delta

## Purpose

Prepare the next owner-reviewed SQL-v1.3 task-set cohort with explicit identity,
scope and foundation evidence while preserving full CI and all live boundaries.

## ADDED Requirements

### Requirement: Preparation never grants live authority

The next-cohort manifest and inspector SHALL remain explicitly disabled and
separate from production admission. Every result SHALL retain full CI, forbid
CI skipping and queue admission, and distinguish a preview from qualification,
review approval, activation, landing, release and assembly acceptance.

#### Scenario: Valid next-cohort preview
- **WHEN** all declared sources and their merge previews validate
- **THEN** report preparation-only evidence with full CI required and no admission

#### Scenario: An activation flag is inserted
- **WHEN** the preparation manifest enables efficiency or requests skipping/admission
- **THEN** refuse the manifest without changing the active P05/P06 behavior

### Requirement: Fresh immutable source and scope evidence

Preparation SHALL name only P07 and P09, their fixed PR identities, complete
task/path/status inventories, bounded exact linear histories and an immutable
main. Each source SHALL declare an exact anchor already landed in that main
and containing the common prerequisite; its complete source chain and inventory
SHALL be checked from that anchor. Missing final owner bindings SHALL remain pending. Changed main, malformed
or duplicate data, unknown paths, invalid modes, nonempty markers, merge commits,
undeclared fixes, dependent members or merge conflicts SHALL refuse preview.
The preparation ceilings SHALL be 16 commits for P07 and 24 for P09, with every
SHA explicitly declared and every cumulative cut checked. These ceilings SHALL
NOT bind unpublished heads, approve reviews or alter live delivery limits.
Preparation SHALL preview P07 before P09, rejecting the reverse order. The
P07 prefix SHALL retain the frozen foundation; P09's declared helper change
SHALL appear only in the second prefix.

#### Scenario: Owner-required source order
- **WHEN** both sources validate and are previewed
- **THEN** preview P07 first without a foundation transition, then P09 with its declared helper change, and refuse a reversed manifest order

#### Scenario: Owner has not finalized a head
- **WHEN** either final source binding is missing
- **THEN** report pending rather than borrowing an older observed head

#### Scenario: A previously observed cut is superseded
- **WHEN** an owner is fixing a rejected cut and no new accepted head exists
- **THEN** retain only its observed hash as context, with null head/review/consent and empty source history, producing no preview or authority

#### Scenario: P07 declares the shared cleanup helper
- **WHEN** the exact P07 preparation inventory includes `tests/sql_register_cleanup.py`
- **THEN** require it as an added regular file, reject a missing or differently typed entry, and preserve final spec ownership and product-review gates

#### Scenario: A source gains an extra file or fix commit
- **WHEN** its actual scope or history differs from the declared immutable cut
- **THEN** refuse rather than widen its projection automatically

#### Scenario: Current main moved
- **WHEN** the observed main differs from the explicit frozen main
- **THEN** refuse the stale preview and require a reviewed refresh

#### Scenario: P09 starts at landed P03
- **WHEN** P09 declares landed P03 as its anchor and P07 declares P01
- **THEN** validate each complete chain independently and reject unlanded anchors or skipped source commits

#### Scenario: A history reaches or exceeds its own ceiling
- **WHEN** P07 has 16 commits or P09 has 24 commits in its exact declared linear chain
- **THEN** validate every prefix without granting live authority, and reject one additional commit or an undeclared fix

### Requirement: Foundation and owner evidence remain separate gates

Preparation SHALL expose the cohort's frozen-foundation changes and link actual
owner consent/review references without representing those links as authenticated
receipts. P09's helper change SHALL NOT weaken the active foundation contract.
P08 SHALL remain held. Existing live verifiers, full coverage, fallback, workflows,
queue settings, lanes and assembly pins SHALL remain unchanged.

#### Scenario: P09 changes the PostgreSQL helper
- **WHEN** the preview includes the declared pgcluster modification
- **THEN** report it for later explicit foundation/landing review while live delivery still falls back to full CI

#### Scenario: A review reference is present
- **WHEN** an operator records an exact-head owner review link
- **THEN** preserve that provenance as context without granting skipping or admission
