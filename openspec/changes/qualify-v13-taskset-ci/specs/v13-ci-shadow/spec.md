## Purpose

Expose conservative v1.3 task-cut and prerequisite observations while preserving
complete ordinary CI and separating observations from integration authority.

## ADDED Requirements

### Requirement: Observations confer no omission or admission
The observer MUST always report full required CI, disabled v1.3 efficiency,
skip false, queue admission false and no live qualification. No gate MAY consume
its output as permission. Existing claude-current behavior MUST remain unchanged.

#### Scenario: A matching cut is observed
- **WHEN** an independent current-main PR matches a disabled P03/P05 projection
- **THEN** the observer reports that proposed cut while full CI remains required

#### Scenario: The disabled flag is changed or unreadable
- **WHEN** configuration is malformed, unavailable or does not explicitly disable efficiency
- **THEN** the observer reports full fallback and grants no authority

### Requirement: Main owns bounded observations
Token-bearing observation MUST execute only trusted-main policy, use bounded
read-only Git/API operations, bind current identities and reread changing inputs.
Bootstrap MUST NOT execute PR-owned policy. Output MUST NOT echo untrusted text.

#### Scenario: A source or main changes during a read
- **WHEN** head, base, body, prerequisite or main evidence drifts
- **THEN** the result is a full fallback with no proposed cut

#### Scenario: A fork or dependent stack is presented
- **WHEN** a PR is foreign, dependent, stale, closed or otherwise unsupported
- **THEN** it retains full CI without flattening or changing the PR

### Requirement: Named paths and exact markers bound a cut
The projection MUST accept only named regular added/modified test paths and the
exact named empty added markers. It MUST refuse deletions, renames/type/mode
changes, marker-only edits, missing/extra/nonempty markers and control/mixed paths.

#### Scenario: A source tries to change the temporary gate
- **WHEN** a diff touches conftest, the gate, a fixture or another unlisted path
- **THEN** it is outside the observed cut and remains full fallback

### Requirement: P01 observation is not PostgreSQL qualification
P01 status MUST distinguish pending, drift/unavailable and observed merged
contract. An observed contract MUST bind PR88's confirmed immutable source,
same-repository main merge ancestry, control blobs and required-engine baseline.
It MUST NOT be described as successful PG execution or full artifact parity.

#### Scenario: P01 has not landed or its anchor changed
- **WHEN** PR88 is open or its immutable source/control contract differs
- **THEN** qualification remains unavailable and full CI is retained

#### Scenario: P01's contract is on main
- **WHEN** the exact merged prerequisite and main contract are observed
- **THEN** the observer reports contract observation only, still requiring separate full PG CI evidence

### Requirement: Complete coverage remains the acceptance boundary
Ordinary full CI and the parameterless reusable full call MUST retain their jobs,
commands, matrices and applicable approved skips. The new control paths MUST
trigger full coverage qualification. Missing PostgreSQL MUST NOT be accepted as
an eligible-engine skip. Existing007 gate behavior and release acceptance MUST
not change in this stage.

#### Scenario: New observer control files change
- **WHEN** a PR changes those controls or their tests/workflow/projection
- **THEN** ordinary full CI and full reusable qualification run, without a selector shortcut

#### Scenario: Future v1.3 qualification is evaluated
- **WHEN** P01 is landed and full PG runs are inspected
- **THEN** complete case identities/outcomes and permitted skip reasons must agree, with no missing or duplicate tests
