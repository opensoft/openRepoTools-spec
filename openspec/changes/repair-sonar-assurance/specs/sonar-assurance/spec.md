## Purpose

Provide complete, reproducible analysis and measured test coverage of the assembled project, with meaningful quality-gate results and auditable security finding dispositions.

## ADDED Requirements

### Requirement: Complete command and test classification
The analysis SHALL include every shipped command in its actual language, including extensionless Python and shell commands, and SHALL classify tests explicitly. It MUST use the project's supported Python version and exact published assembly pins.

#### Scenario: Extensionless command
- **WHEN** a shipped extensionless command appears in the source inventory
- **THEN** analysis SHALL index it in its declared language and report its path in the inventory receipt

#### Scenario: Missing source or provider
- **WHEN** an expected command or pinned provider is missing
- **THEN** analysis SHALL fail rather than silently excluding that source

### Requirement: Measured coverage
The analysis SHALL import coverage produced by executed tests. Coverage from temporary copies SHALL map only to byte-identical committed sources with validated line bounds. Failed test execution, missing reports and inconsistent provenance MUST fail verification.

#### Scenario: Coverage artifact
- **WHEN** analysis is submitted
- **THEN** its coverage metrics SHALL derive from actual test reports associated with the exact source tree

### Requirement: Meaningful quality gate
The assigned Sonar way conditions and thresholds SHALL remain unchanged. Main SHALL use the oldest retained main analysis as its initial baseline, and the submitted analysis SHALL finish with an evaluated quality-gate result rather than NONE.

#### Scenario: Gate failure
- **WHEN** a condition is violated
- **THEN** the workflow SHALL report failure and retain the failed analysis evidence

### Requirement: Auditable security triage
Each of the twelve recorded findings SHALL have a source-owner, evidence-backed disposition. Substantiated issues MUST be fixed in the owning source and consumed through ordinary pins. False positives MUST include source-flow and regression evidence; acceptance or scope exclusion MUST NOT hide a real finding.

#### Scenario: Pinned upstream finding
- **WHEN** a finding belongs to the pinned upstream standard
- **THEN** any code repair SHALL land upstream before a coherent consumer pin advance

### Requirement: Preserve operating sessions and checks
The repair SHALL preserve active checkout content, sessions, lane bindings, installed tools and all existing required checks.

#### Scenario: Verification workspace
- **WHEN** tests or scanners execute
- **THEN** they SHALL use isolated workspaces without replacing a shared running checkout or restarting an active session
