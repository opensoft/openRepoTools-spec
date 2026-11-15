## Purpose
Allow normal project commands to discover development repositories in grouped project folders without depending on root compatibility aliases.

## ADDED Requirements

### Requirement: Bounded grouped discovery
Commands SHALL discover direct projects and projects below one or two grouping directories, respect configured projects roots, and stop at Git, project and family boundaries. Worktree and lane collections SHALL be excluded.

#### Scenario: Grouped project without alias
- **WHEN** a project exists at `$PROJECTS_ROOT/developerTools/Example`
- **THEN** normal name lookup resolves that project without a root alias.

#### Scenario: Supporting checkouts
- **WHEN** matching checkouts exist only below workTrees, laneWorkspaces or brett-wip-lanes
- **THEN** they are not selected as default projects.

### Requirement: Unique physical target
Commands SHALL deduplicate aliases reaching one physical target and refuse ambiguous distinct matching names within a projects root or duplicate matching origins before performing writes. Estate name lookup SHALL preserve the existing precedence of home projects-directory spellings.

#### Scenario: Same target alias
- **WHEN** a root alias and a grouped directory reach the same project
- **THEN** enumeration and default lookup select it once.

#### Scenario: Distinct projects
- **WHEN** distinct grouped projects match the requested name
- **THEN** lookup refuses and requires an explicit path.

### Requirement: Existing path authority
Explicit and recorded lane directories SHALL retain precedence, including refusal for a missing recorded directory. Discovery SHALL preserve repository identity validation, independent register clones, pins and lane-state.

#### Scenario: Explicit lane directory
- **WHEN** a lane supplies an explicit directory
- **THEN** grouped discovery does not replace that directory.
