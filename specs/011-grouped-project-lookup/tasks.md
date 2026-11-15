# Canonical execution tasks

- [x] T001 Implement bounded discovery and ambiguity handling in four commands; verify Bash syntax and read-only canonical path discovery.
- [x] T002 Add isolated regression tests; verify grouped, alias, ambiguity and path-authority scenarios pass.
- [x] T003 Update command guidance and record targeted serialized tests, preservation checks, results and activation limitations.

See [local validation](validation.md) for results and adoption boundaries.

## Publication task sets

| Set | Owning leg and PR | Tasks | Outcome | Immediate predecessor | Fast verification | Safe intermediate state |
| --- | --- | --- | --- | --- | --- | --- |
| S01 | Code PR, targeting main | T001, T002 | Grouped discovery and regression coverage | None; independent source PR | `tests/run.sh -k grouped_project_lookup --tb=short -rA` | Source change only; existing assembly pins remain authoritative |
| S02 | Spec PR, targeting main | T003 | Behavior contract, manual and local qualification record | None; companion to S01 across repositories, not a branch stack | `openspec validate support-grouped-project-lookup --strict` | Documentation records local qualification without granting release or pin adoption |

Full CI remains required. Publication does not enqueue, merge, release, update
installed tools, advance assembly pins or change active lane state.
