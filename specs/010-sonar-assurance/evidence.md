# Acceptance evidence

## Baseline and service configuration

The root project is opensoft_openRepoTools. The pre-repair main analysis
returned NONE with no conditions or new-code period; automatic analysis excluded
coverage and omitted eighteen extensionless commands.

On 2026-11-12, after publishing the replacement as root PR 361, automatic analysis
was disabled for this root project and the oldest retained main analysis was set
as its manual new-code baseline: f34ab174-0521-432f-936c-1d8c0920bb01,
2026-09-10T20:01:30+0000, revision
7cf0bd5a08ebdc8811230257ad7d96b6bcc42ee2. Readback confirmed
manualNewCodePeriodBaseline: true. The current repair was not made the baseline.

The assigned built-in Sonar way gate (ID 9) retained all six exact conditions:
security/reliability/maintainability ratings at 1, new coverage at least 80%,
new duplication at most 3%, and new security hotspots reviewed at 100%.
No organization-wide settings, other project analysis methods, permissions or
required GitHub check names were changed.

## Verification in progress

The initial collector unit checks passed (10 tests, isolated Ubuntu 24.04,
unprivileged). Broader local verification found three installer cases needing
jq in the disposable container; this environment dependency was installed for
their rerun. Complete code, provider, composed, measured-coverage and live
main gate results must be added before acceptance is marked complete.

The preservation receipts remain outside source control in the task's local
evidence cache. They record shared root/code/spec/provider revisions, tracked
diff hashes, worktree registrations and tmux pane identities. No installed
commands, shared checkout, lane register, session binding or handoff is part of
this implementation.
