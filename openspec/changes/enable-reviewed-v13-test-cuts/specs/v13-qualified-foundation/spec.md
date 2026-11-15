# Spec Delta

## Purpose

Enable the first owner-selected test-only v1.3 efficiency pair against an
explicitly reviewed and qualified foundation without following mutable main.

## ADDED Requirements

### Requirement: Foundation revisions are explicit immutable epochs

The system SHALL accept a revised PostgreSQL foundation only when trusted policy
explicitly binds a reviewed immutable actual-main ancestor descended from P01,
the complete four-file foundation blob inventory and unchanged P01 markers.
The required-engine workflow, baseline, original thirteen integration jobs and
P01 merged identity SHALL remain frozen. The epoch SHALL be an ancestor of the
current base and its exact foundation files/modes/markers SHALL remain unchanged
through that base. Absent an epoch, the original P01-only freeze SHALL remain.
Malformed, incomplete, unlanded, unrelated or drifted epochs SHALL retain full
CI and refuse candidate qualification. A matching blob alone SHALL NOT prove
real-engine execution or authorize an epoch change.

#### Scenario: Reviewed helper revision from the normal owner batch
- **WHEN** the actual P02/P03 landing and its foundation revision are independently qualified, explicitly bound and preserved through current main
- **THEN** eligible test-only cuts may use that reviewed foundation without pretending the original P01 helper is unchanged

#### Scenario: New unreviewed helper or weaker required-engine contract
- **WHEN** a helper changes after the pinned epoch, the epoch is not landed on main, or a frozen workflow/baseline changes
- **THEN** efficiency proof refuses and all full checks remain required

### Requirement: Initial pair replacement stays test-only

The system SHALL bind exactly the owner-selected P05/P06 heads, branches, task
sets and bounded reviewed linear corrections for the initial canary. P05 SHALL
map to S007-07 and T217/T235/T259; P06 SHALL map to S007-09 and
T212/T256/T225/T242. Each source SHALL retain only its registered named SQL
test modules and exact empty added markers, with the existing six-file maximum
and two independent main-targeted members. No foundation, product, legacy
fixture, control, unresolved dependency or unregistered source SHALL qualify.
All immutable source, exact-head review, fast/base-history, candidate prefix,
complete heavy artifact, live reread and historical landing requirements SHALL
remain. The observer SHALL stay non-authoritative and disabled as an efficiency
grant even after the separate delivery policy is enabled.

#### Scenario: Owner's exact corrected test-only pair
- **WHEN** both registered heads have their complete exact linear ancestry, fresh owner confirmation/review and all authenticated source evidence
- **THEN** the same narrow test-only path may qualify under enabled delivery policy

#### Scenario: Old P03 or old P05 head
- **WHEN** a previously projected source is merged or superseded, or a source claims another task set
- **THEN** it cannot borrow the new pair's opt-in or qualification

### Requirement: Enablement and live acceptance have separate gates

Publication with delivery enabled SHALL require checked prerequisite source
merges, passing current main/integrated-tree qualification, actual real-engine
fixture start/clone/stop/restart evidence at identical foundation blobs, exact
published owner heads with required reviews/full CI, and latest-head checked
source/config review and normal merge. Unverified facts SHALL NOT be filled with
guesses. Canary admission SHALL additionally require fresh exact-head consent
from affected owners and a stable coordinated main/queue window. The canary
SHALL prove fast-only source and candidate PR CI, one complete accepted heavy
caller, no duplicate post-merge full CI, and matching actual tested/landed prefix
and final trees. Source or local success SHALL NOT claim live acceptance.

#### Scenario: Preparation while owner work and CI are running
- **WHEN** a required owner head, epoch, review or consent is still pending
- **THEN** preparation may continue but committed enablement/admission does not follow from the draft

#### Scenario: Successful first canary
- **WHEN** the complete actual service, heavy, prefix and landing evidence passes for the consented pair
- **THEN** record only initial test-only acceptance and prepare any wider task-set profile through a new reviewed stage; do not claim full all-work rollout or assembly/session adoption

### Requirement: Regenerated merge hints require immutable content equivalence

For the initial source and genuine candidate event/live comparison, a generated
merge_commit_sha hint MAY differ from the tested GITHUB_SHA only when an
authenticated read of that exact full-SHA Git commit in the configured repository
proves the exact same two ordered parents and exact tree as the tested merge.
Missing/null/current hints SHALL preserve their prior behavior without a new
lookup. At most two distinct immutable commit lookups SHALL be made per initial
comparison, without added permission, ref writes, retries or checkout changes.
This explicitly amends the earlier rule rejecting every different non-null hint;
it SHALL NOT discard arbitrary hint differences or change tested-commit authority.

Unknown, unavailable, malformed, wrong-identity, wrong-parent-count/order/base/head
or wrong-tree hints SHALL retain full CI. The actual GITHUB_SHA and ordered
current-main/head parents SHALL remain independently checked. Every other
event/live identity field, opt-in decision, authenticated fast/base-history and
review proof, foundation and candidate admission/heavy/landing requirement SHALL
remain enforced. Subsequent original live PR rereads SHALL remain byte-strict,
including the generated hint. A hint lookup SHALL NOT confer omission by itself.

#### Scenario: Reopen regenerates only the test-merge commit identity
- **WHEN** the hint names another authenticated commit with the exact tested two ordered parents and tree, and all independent source or candidate proofs pass
- **THEN** the initial generated-hint difference alone does not force full CI

#### Scenario: A hint object is uncertain or differs semantically
- **WHEN** immutable commit lookup fails or the returned identity, tree or ordered parents differ
- **THEN** full CI remains required without executing the hint or inventing proof

#### Scenario: A real base change or later live hint change occurs
- **WHEN** the event base differs from live main or the original live PR changes after the first read, even to an equivalent hint
- **THEN** omission proof refuses and full fallback remains

### Requirement: Same-head fast history cannot substitute older success

Fast provenance SHALL select only the unique newest canonical fast workflow for
the exact source head. The configured workflow catalogue SHALL be complete and
bounded to100 rows, with exact PR-event/path/repository/head/branch identities,
positive unique IDs and per-workflow run numbers, and one workflow identity.
Selection SHALL use the greatest run_number, not check order, IDs or success.
Every named check SHALL authenticate its Actions app, exact head/name, URL/job
identity and unique matching catalogue run/check suite. The selected run SHALL
have exactly one matching current check plus all existing independent run/job,
attempt, current source/base, retarget history, success and reread proof.
Catalogue selection SHALL be rechecked before proof; newer events SHALL refuse.

Historical authentic checks MAY coexist, but SHALL confer no omission authority.
The newest failure or cancellation SHALL NOT borrow an older green. A missing
newest check/job, even with a pending run, SHALL retain immediate full refusal.
Only complete authenticated current pending evidence MAY use the existing
bounded observation behavior. Foreign, malformed, duplicate, unrecognized,
truncated, incomplete or raced evidence SHALL retain full. This amendment SHALL
add no retry mechanism, permission, credential, queue/profile scope, workflow,
baseline, installed-tool, lane/session or release change.

#### Scenario: An unchanged head has several authentic canonical events
- **WHEN** the complete catalogue and named checks agree, the unique newest run is successful, and every independent current provenance check passes
- **THEN** historical authentic checks do not make that selected current proof ambiguous

#### Scenario: An older green exists but the newest event is not proven
- **WHEN** the newest run failed, was cancelled, is pending or lacks its current job
- **THEN** no older result supplies proof; existing authenticated-pending rules or immediate full refusal apply without omission authority

#### Scenario: Catalogue identity or latest selection changes
- **WHEN** catalogue/check coverage is incomplete or forged, workflow/sequence identity is ambiguous, or a newer event appears during reads
- **THEN** full CI remains required rather than selecting a convenient green
