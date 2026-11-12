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


## Additional isolated verification

- Original-provider source-flow regressions: 101 passed.
- Original-provider YAML reader and stock-reader comparisons: 350 passed, zero skipped with PyYAML 6.0.2.
- Upstream repair policy plus existing installer-pin contract: 3 passed.
- Current code helper and refusal-path checks: 21 passed, zero skipped.
- Source CI parity, package and fast checks retain their full contracts; hosted complete suites are still running.

## Current qualification and implementation details

The source helpers at code 35d60b80f26d497f2f3e706f467620ff10036d35 pass
40 focused inventory, provenance, report-binding and gate/refusal checks on the
hosted runtime version, Python 3.15.2, with coverage 7.17.3. An isolated
258-case composed smoke run also passed under that runtime. These selected
checks establish collector behavior; they do not substitute for full CI or
published-main coverage acceptance.

A real copied-command probe caught Python's default SysMonitor engine silently
not supporting file-tracer plug-ins. The collector now explicitly requires the
native C tracer and probes it before full testing: an identical renamed
lanes-index command credited 190 canonical lines, while a modified copy credited
zero. Probe data is separate from suite coverage and is never included in the
gate numerator. Bash tracing likewise records only source hashes and line
numbers, with exact-byte matching and no expanded command arguments.

The original Python 3.x test-runtime selection remains in place; the separate
Sonar Python analysis floor is 3.9. Optional PyYAML oracle comparisons were
qualified separately on Python 3.12 with PyYAML 6.0.2: no source-distribution
fallback is introduced for the Python 3.15 runtime, for which PyYAML currently
has no matching wheel.

An additional consumer compatibility check against merged provider
49b23bb688770dd89d58404f39fe0ed67d0c7345 passed 448 targeted pin, park/resume
and status cases. This is preflight evidence, not acceptance of a future provider
merge or replacement for the consumer's complete hosted CI.

The provider's first full Linux run passed 22,429 tests and exposed two existing
documentation guards against an unnecessary README addition in this repair.
The addition was removed; neither the README cap nor handbook hash was relaxed.
Its replacement exact-head full rerun remains pending.

Interim preservation comparison confirmed that primary root/code/spec/provider
heads, tracked/staged diff hashes, root worktree registrations and cloud-bench
tmux pane identities match the pre-work receipts. Final comparison remains part
of assembly acceptance.

Sonar PR APIs return changed files and new-code metrics. A root PR that changes
only workflow YAML and gitlinks legitimately has zero new coverable lines; its
PR receipt explicitly does not claim complete source indexing or overall
coverage import. Published main must independently prove the full inventory,
actual imported coverage, server revision, new-code period and evaluated
80-percent coverage condition before this feature is complete.
