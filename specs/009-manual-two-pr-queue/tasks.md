# Tasks

| Set | Outcome | Tasks | Predecessor | Fast verification (code root) | Safe state |
| --- | --- | --- | --- | --- | --- |
| QUEUE-CONFIG-01 | Manual full-CI pilot queue configuration | T001–T003 | Landed feature 008 | `bash tests/run.sh -k manual_mergify_queue` | No automatic admission or live canary |

- [x] T001 [O] Define one bounded manual queue retaining full CI — files: `.mergify.yml` — verify: `bash tests/run.sh -k manual_mergify_queue`
- [x] T002 [O] Test parsed configuration, admission refusals and unchanged CI contracts — files: `tests/test_ci_pilot_queue.py` — verify: `bash tests/run.sh -k 'manual_mergify_queue or tests_workflow or ci_pilot'`
- [x] T003 [S] Document operator boundaries and link shared agent guidance — files: `.github/ci/batching-pilot.md`, `.github/ci/task-set-delivery.md` — verify: `bash tests/run.sh -k manual_mergify_queue`

Only this file owns executable implementation tasks. Publication, reviews,
future canary qualification and eventual assembly landing remain distinct gates.
