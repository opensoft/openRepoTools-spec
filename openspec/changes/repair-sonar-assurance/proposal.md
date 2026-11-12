## Why

The assembled project's main analysis has no usable new-code period, so its assigned Sonar way gate returns NONE despite successful scans. Eighteen extensionless commands are not indexed for language analysis, automatic analysis excludes coverage, and twelve existing security findings need evidence-based dispositions.

## What Changes

- Run reproducible CI-based analysis of the exact assembled code/spec/upstream pins, with explicit source/test classification, Python 3.9 compatibility, and explicit language patterns for every shipped extensionless command.
- Generate and import measured Python and shell coverage, validating source paths and preserving the full existing CI and package checks.
- Retain the existing Sonar way thresholds and establish the oldest retained main analysis as the new-code baseline; do not reset the baseline to the repair or hide existing findings.
- Triage all twelve recorded findings. Fix substantiated issues in the owning repository; false-positive dispositions require source-flow evidence and regression tests. Preserve prior results and issue history.
- Use isolated feature worktrees and short-lived CI processes. Preserve active sessions, shared working checkouts, lane data, installed commands and GitHub protection requirements.

## Capabilities

### New Capabilities

- `sonar-assurance`: complete, reproducible static analysis and measured coverage of the assembled project, with auditable quality-gate and finding outcomes.

### Modified Capabilities

None. Existing command, installation, lane, queue and release behavior is unchanged.

## Impact

Canonical governance and feature 010 live in openRepoTools-spec; analysis helpers and tests live in openRepoTools-code; assembly workflow, scanner configuration and coherent leg pins live in openRepoTools. Any necessary changes to pinned openRepoShape files are made upstream and consumed through normal reviewed pin updates. Sonar analysis method and baseline are project-local configuration changes; credentials are scoped to analysis of this project. GitHub required checks and Sonar gate conditions are retained.
