# Feature 008: CI-only task-set pilot

Approved scope: Brett's request to implement the narrow code-leg pilot,
coordinate with PR #34 and add shared Claude/Codex instructions, preserving
lanes 1–5. Governance: `openspec/changes/ci-only-task-set-pilot/`.

## Acceptance

- FR-001: advisory scope is limited to `claude-current` and its own test file.
- FR-002: every result requires full CI and denies queue admission.
- FR-003: git/context uncertainty, mixed paths and unsafe events fail closed.
- FR-004: dependent PRs use their immediate base, including metadata updates.
- FR-005: both agents use one instruction reference; existing lane and triad
  protocols retain precedence. PR #34 remains unmodified and independent.
- FR-006: tests.yml, release.yml, assembly pins, app access and rulesets do not
  change. No live stack or canary is run.

Qualification is local offline tests plus ordinary exact-head hosted checks;
neither is live merge-queue acceptance. Archive only after later assembly landing.
