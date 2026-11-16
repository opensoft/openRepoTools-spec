# Tasks

## 1. Bound the preparation

- [x] 1.1 Preserve the prior worktrees and create an isolated paired feature; verify bases and unchanged active source controls.
- [x] 1.2 Document the disabled cohort contract and request final owner heads/reviews; verify coordination comment6927765450 and keep P08 held.
- [ ] 1.3 Bind only actual final owner heads and fresh exact-head review/consent references, or explicitly retain pending draft bindings; verify against live owning-repository records.

## 2. Implement and validate

- [x] 2.1 Add the separate disabled manifest and non-gating Git inspector; verify immutable scope/history and ordered-preview tests, including foundation reporting.
- [x] 2.2 Add rejection/pending/full-fallback regressions and run the canonical targeted suite; verify live controls, workflows and all coverage definitions are unchanged.
- [ ] 2.3 Pass strict OpenSpec and diff checks; obtain independent exact-head code/spec review and resolve actionable findings. Master self-review is complete; independent review is still pending.

## 3. Publish preparation only

- [ ] 3.1 Open paired code/spec PRs and request exact-head Codex/owner review; verify actual pushed heads, PR links and comments. Leave PRs open and no live batch.
- [ ] 3.2 Record current owner evidence, test results and remaining activation boundaries; verify P08, lanes1–5, full fallback and all exclusions remain preserved.

## Validation and coordination evidence

Code base804a97be and spec base6e6427e4 were read before the prescribed helper
created paired feature017. Existing worktrees and task016 WIP were preserved.
Coordination6927765450 requests final P07/P09 heads and fresh exact-head reviews
from lanes2/3, explicitly holds P08, and authorizes no live batch. The last
public heads remain observations only; final bindings/review references are
still null pending actual owner responses.

An initial isolated QA run stopped during collection on a transient ENOMEM
file-read error, before tests. QA cgroup counters showed no OOM/limit event,
memory usage was about34MiB of2GiB and host available memory about39GiB.
No resource limit, bench or coverage change was made. The same canonical
focused selector then passed39tests. After added edge cases, the expanded
canonical selector `ci_v13_expansion or ci_v13_delivery or v13_shadow or
full_ci_coverage_parity` passed343tests,6571deselected,zero skips,in138.46s,
including42new preparation cases. Exact upstream dependency pin4bf963f0 was
initialized only in the new private feature. Strict OpenSpec and diff checks
passed. Master self-review fixed strict held-PR ID typing and added an
intermediate-scope-escape regression; no new live authority was introduced.

These are local preparation results, not hosted CI, independent owner review,
activation or live acceptance. P09's frozen helper transition remains a later
explicit review gate. Current source binding, Codex review and PR publication
evidence will be recorded without borrowing old-head approvals.
