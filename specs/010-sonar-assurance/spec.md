# Feature 010: Sonar assurance

Authorized by Brett Heap on 2026-11-12: repair the Sonar gaps for openRepoTools, include missing commands and coverage, triage all twelve findings, preserve active sessions, retain checks, and prove gate computation.

## User Scenarios & Testing

### US1 — Complete analysis (P1)
An engineer sees every shipped command analyzed in its actual language, including extensionless commands, and explicitly classified tests at the project's supported Python version.
Acceptance: exact source/pin inventory; no missing expected commands; valid source and test classification receipts.

### US2 — Measured coverage and meaningful gate (P1)
An engineer sees coverage from executed tests and an evaluated Sonar way result for main.
Acceptance: provenance-checked Python/shell coverage; unchanged gate thresholds; oldest retained baseline; real analysis conditions and metrics; missing/invalid reports and NONE fail.

### US3 — Security findings resolved honestly (P1)
A maintainer can inspect each of the twelve original issue keys, its owner, source flow, tests and disposition.
Acceptance: substantiated issues fixed at their owner and consumed by ordinary pins; false positives supported by tests and caller/guard evidence; no blanket acceptance or hiding.

### US4 — Work continues safely (P1)
Active benches, lane sessions and unrelated work continue throughout the repair.
Acceptance: before/after checkout and pane receipts; no active checkout reset, restart, rebind, command installation, release or queue activation.

## Functional Requirements

FR-001: Inventory exact root, code/spec and nested provider commits and all shipped extensionless commands.
FR-002: Enforce explicit language and test classification, including Python 3.9 compatibility.
FR-003: Generate measured Python and shell coverage; map copies only by exact committed bytes and checked line bounds. Keep executable denominators independent of hits, normalize Python multiline observations to canonical statements, retain every unexecuted statement/source, and refuse unequal per-file denominators for identical source inventories.
FR-004: Keep every existing full test, package, required-check and Sonar way threshold unchanged or stronger.
FR-005: Main uses the oldest retained analysis as baseline; fresh analysis must have evaluated conditions and non-NONE status.
FR-006: Triage all twelve original keys with auditable evidence; preserve history.
FR-007: Publish owner changes before coherent parent pins, then verify exact-head and published-main CI and analysis.
FR-008: Preserve active sessions, primary checkouts, lane data and installed commands.

## Success Criteria

The scanner inventory contains every expected command, measured coverage reaches Sonar, all twelve original findings have defensible dispositions, the real main gate evaluates with unchanged thresholds, all required existing checks pass, and preservation receipts show no work/session loss.

## Assumptions

The assembly project's existing organization SONAR_TOKEN is available to ordinary CI. Analysis targets opensoft_openRepoTools; no new project, app grant, queue, release or required-check migration is implied.
