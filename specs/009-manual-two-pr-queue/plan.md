# Plan

Code baseline `ad740a1e0ba0923859d16fd022bd09c374b007db` (code #37, including #34).
Spec baseline `dd0fd170ecdbeaf77b34add8ca0e405a84f42481` (spec #24).

Add `.mergify.yml`, offline policy/CI-contract tests and a code-local runbook;
link the runbook from the existing shared agent reference. No production code,
workflow, dependency, shape copy or pin changes. Use isolated paired worktrees
under the assembly's ignored worktrees directory, not active lane checkouts.
The isolated assembly still lacks an installed Speckit hook; no global/bootstrap
or active feature selection is changed to create this bounded paired feature.

Validate the real config with Mergify's public schema validator where available;
test parsed queue structure and negative eligibility vectors offline. Use the
existing pinned YAML reader and pytest 8.3.4 through tests/run.sh in a disposable
instance of the existing py-bench image. Retain existing CI/sharding contract
tests, which ensure the required jobs still run on arbitrary PR bases.

## Governed invariants

- `.mergify.yml` — manual admission and full-CI merge policy.
- `.github/workflows/` — existing triggers, gate names and release contracts; unchanged.

Review app/ruleset/plan access before any future live test; no permissions or
feature purchase is inferred from a valid configuration. The current task ends
with open PRs and exact-head Codex review requests, not merge or activation.

## Accepted rollout — 2026-11-11

The preceding preparation-only scope is the original delivery. Brett now
approved normal checked merges of #44, the general manual code queue and
matching spec/assembly pins/instructions. Baselines: code #44 head
`1a49e7bf3c4609791011e13ecf40e67571413848`, spec main
`dbfed9ebfe57484c868b8be1a2a79e1f012f4def`, assembly main
`a29ba3f519a1db386240f7341de479c6a6de63dc`.

Use the existing isolated paired worktrees and a safe-character path allowlist.
Queue commands attest fresh exact-head review; eligibility never auto-enqueues.
Do not change the classifier, full jobs, releases or active lane branches.
Reconcile batches #41/#43, code-local amendments and #44 under spec issue #26.
Keep historical approval records; record current as-built behavior separately.
Use openRepoShape's bump-leg.py to advance merged legs, gitlinks, recomputed
digests and workflow SHA references together. Run make validate and composed
exact-pin CI before refreshing authorized clean local mounts. Do not archive
until assembly landing, or replace installed lane tools/sessions.

## Coverage-parity preparation — 2026-11-11

Authority: Brett approved implementing the coverage-parity PR first, keeping
ordinary full CI and lanes 1–5 unchanged. Baselines: merged code d3c55e55aa3516cbaa1d68a77061ddd50191d49a,
spec 66ba22f429caea8da4c664f04f5567bb924064ac. Reuse the existing clean paired
feature worktrees; do not change the pinned mounts or active feature selection.

Add workflow_call without modifying the full workflow's jobs/environment/events.
Add a PR-only same-commit local caller, reviewed baseline, parity/mutation tests,
and bounded code amendment/operator notes. Run focused workflow/shard/queue
regressions through tests/run.sh in a disposable resource-limited py-bench image.
Verify ordinary hosted full CI separately from reusable-call qualification.
Open code and spec PRs with exact-head Codex requests; do not merge or activate.

Local implementation evidence: code bd56ea742233c76e7aefb2465882facde50d86ab.
Red-first parity run: 12 failed / 24 passed before the entry point/caller existed.
Final focused workflow/shard/pilot/parity regression: 419 passed, no skips,
through tests/run.sh in an offline disposable py-bench image (2 CPUs / 2 GiB).
Actionlint and strict OpenSpec validation passed. Hosted ordinary/reusable full
CI, review and eventual landing remain separate pending qualification gates.

## Trusted shadow-classifier preparation — 2026-11-11

Authority: Brett approved the shadow-classifier PR next, ordinary full CI and
lanes 1–5 preserved. Merged baselines: code a16ac8f7a5d14c8dc215a1b82832bc6e9a9e40ca,
spec 476dadc5172be2f88a2a0cd21c744c35b9521d58. Reuse the clean isolated paired
feature worktrees without changing active feature selection or pinned mounts.

Implement a separate ci-shadow workflow, bounded read-only observer, mocked API
and real local-Git tests, plus code-local notes/amendment. Port proof patterns
from OpenBenches stack-ci.py/project-stack-ci.py, not their live skip authority.
Test read failures, stale refs, main moving during observation, third-party edits,
post-merge stale retarget metadata, closed/dequeued/candidate events, unknown
payloads, topology, paths/modes and non-execution of untrusted content. Run focused
workflow/parity/shard/pilot/queue regressions offline through tests/run.sh in the
resource-limited py-bench image. Validate OpenSpec and workflow lint. Open PRs,
request exact-head Codex review and retain full ordinary hosted CI. No merge.

Local qualification: 76 new observer/contract vectors passed; 495 focused tests
passed with no skips across shadow, original pilot/queue, workflow, coverage-parity
and shard-accounting regressions. Native Git fixtures and mocked APIs ran offline
through tests/run.sh in a disposable py-bench image limited to 2 CPUs / 2 GiB.
Workflow lint, strict OpenSpec and whitespace checks passed. The initial red test
failed before shadow_scope.py existed. Bootstrap policy-unavailable is expected
on the preparation PR and is not a live positive-scope/candidate qualification.

## Additive fast/candidate preparation — 2026-11-11

Continue the full-rollout goal under existing restrictions. Baselines: code
5fb492f1c49bf0e251795f4d1a3deedc29080ef9 and spec
ed85f6dc99f95243185138fd4d8ebfc485fe67a1. Task set DELIVERY-PROOF-07 prepares
source fast feedback, read-only immutable candidate/member/review proofs and a
reusable complete heavy entry without wiring any existing queue or disabling CI.
Use existing clean isolated paired worktrees; active lanes and mounted project
checkouts remain unchanged. No new credentials or app installation.

Test real local Git candidate chains with mocked GitHub metadata: missing/foreign/
closed members, duplicates, stale main/refs/heads, ambiguous or truncated reads,
forged/wrong-workflow or old-head fast checks, missing/quota/dirty Codex receipts,
unresolved threads, mixed modes/paths and unsuccessful aggregates. Validate no
queue/dispatch trigger, no head-owned token execution and unchanged full/release
contracts. Normal latest-head CI/review is required before source merge. Keep
activation and exact required-check proposal separate for user approval.

Local qualification at code 0614ce06ec4e8db54fbcd8068356a06875104803:
545 focused regressions passed with no skips, including 50 new delivery vectors.
Native Git candidate fixtures and mocked read APIs ran offline through tests/run.sh
in a disposable py-bench image limited to 2 CPUs / 2 GiB. Workflow actionlint and
whitespace checks passed. Original full/reuse/shadow/pilot/release workflows,
coverage baseline and Mergify configuration are unchanged. Hosted full CI/reuse
qualification, exact-head Codex, normal source merges and coherent assembly
adoption remain separate gates; this record is not live canary acceptance.

## Local review repair — 2026-11-12

The user selected local skill review instead of waiting for connector quota.
Nine roles plus master reproduced four failing rejection cases at code29685ef:
dependent heads, alternate-base fast receipt, new run attempt and new thread.
Reopen T015/T017 and retain the same source/spec PR pair. Reject overlapping
non-main commit histories with Git merge-base; require a unique current run/PR
association plus complete typed base-change events, all strictly earlier than
immutable run.created_at. This avoids trusting mutable run.pull_requests snapshots
as historical execution context. Repeat fast/check/run/review/thread evidence
and compare stable snapshots before final qualification. Add all red reproducers
and positive fresh-after-retarget coverage to the offline suite. Preserve every
existing workflow, queue, required check, lane, app and release boundary.

Repair local evidence: code a644168af6c39dda17016aaaf8fda40244419833,
561 committed focused tests and the four originally red review reproducers passed
(565 total/no skips) through tests/run.sh in an offline disposable 2CPU/2GiB
py-bench image. Actionlint and committed-range whitespace checks passed. The
source test fixtures now have 66 delivery vectors; no existing workflow or gate
was changed. Hosted CI and latest-head local review are still distinct gates.

## Non-gating candidate caller proposal — 2026-11-12

Authority: prepare/open only, retaining full CI and no live canary or merge.
Baselines: code aa93f99fd965269f07254e16cb8960642394dd53 and spec
bc24929427a1551a3ed17f625fefb0234aa56ac5. Reuse our clean isolated paired
worktrees on new branches; keep the shared assembly at 4b3c3f3 and do not select
a new active plan or touch lanes 1–5. Extend the existing capability/amendment
and sole task list; no new acceptance or review authority is introduced.

Implement only a queue-push shadow caller with a trusted-main positive probe
and the unchanged reusable candidate entry. Keep full CI and Mergify control
files byte-identical. Test the probe shell, graph, permissions, missing/failed
and unknown fallbacks, plus existing proof/parity/shard/queue regressions. Run
unprivileged offline QA in an isolated 2CPU/2GiB instance of the existing bench
image through tests/run.sh; lint workflows and strictly validate OpenSpec.
Open code/spec PRs and request exact-head Codex review. Source/full hosted CI,
merge permission, live qualification, check migration and eventual coherent
assembly adoption remain separate gates; this stage saves no heavy runs yet.

## Disabled CI-efficiency cutover — 2026-11-12

Authority: prepare/open only. Full proposal:
`openspec/changes/prepare-claude-current-ci-cutover/`; sole tasks T022–T026.
Baseline code59c26e8/spec8813fa9; code PR51 separately owns Sonar collectors and
its wheel-only baseline amendment. Preserve that work and require fresh integrated
qualification before a later source merge. Do not regenerate its baseline here.

Implement strict default-off policy, app-bound effective-protection verification,
trusted source/candidate/landing routing, immutable artifacts and an independently
held manual queue. Keep ordinary full CI in force. The original thirteen jobs
remain hashed after only exact needs/if projection; freeze the added controls.
Reusable callers grant only the same four read permissions and force full based
on authenticated caller identity. No selector or write/status grant is exposed.

Research and decisions: `cutover-research.md`; record boundaries:
`cutover-data-model.md`; preparation/operator checks: `cutover-quickstart.md`.
Use real temporary Git, mocked read APIs/artifact bytes and actual shell guards.
Run the complete focused CI regression set offline as UID1000 in a disposable
existing bench image with 2CPU/2GiB, plus workflow lint, strict OpenSpec and
whitespace checks. Hosted ordinary/reuse CI and exact-head review stay distinct.

Open code/spec PRs and request @codex review. Do not merge, enable, apply settings,
enqueue, run a live canary, touch app/paid access/releases/tags, bump assembly pins
or refresh lanes. Document the future changed source floor: the delivery gate is
honest fast verification, and the named manual queue holds heavy. Direct fast
source merging is prohibited by operator instructions, not magically prevented
by the changed GitHub floor. Any machine-enforced queue-only control needs
separate approval. No archive/full-rollout completion claim on preparation.

Local preparation evidence: initial router test run failed with fourteen missing
module errors before implementation. Final focused source/CI regressions passed
741 cases, zero skips (4,289 unrelated cases deselected), as UID1000/offline in
the disposable 2CPU/2GiB existing bench image through tests/run.sh. This includes
real Git source/candidate/landing prefix fixtures, mocked read APIs/ZIP bytes,
actual shell gates and coverage/control mutation tests. Actionlint, strict
OpenSpec and whitespace checks passed. The baseline JSON, shard planner/weights,
canonical test runner and release workflow remain unchanged. Shared py-bench and
cloud-bench assembly readbacks remain4b3c3f3. No live canary/efficiency acceptance,
hosted exact-head qualification or merge is implied by these local results.

Prepared code commit9771e61bcd92509990f030c22c7c26121cd07e4c is published in
[code PR52](https://github.com/opensoft/openRepoTools-code/pull/52). It remains
unmerged and disabled; latest-head hosted qualification/review are separate.

Sonar found an arbitrary CLI-selected proof destination in the initial published
head. Follow-up coded4b3ae1591201775109c195f6b2f75bce70ef92c removes that
argument entirely: exclusive proof.json creation in canonical runner.temp only,
rejecting preexisting files/symlinks, relative/missing/redirected directories and
oversized records. Seven added regressions bring focused QA to748 passed/no skips.
Reported duplicated constants/assertions were repaired too. Lint and whitespace
passed; hosted security reanalysis and full/reuse CI are not yet claimed green.
Both GitHub Codex requests returned quota failure, not approval; no paid credits,
review waiver, security issue dismissal or merge is inferred.

## Approved local-review repairs — 2026-11-12

At coded4b3ae1/specd3f297a, the local master plus nine standard specialist roles
and CI specialist found three reproducible defects. Brett approved fixes/tests,
spec updates and renewed validation/local review in existing PR52/33, leaving
both open. The same CI-CUTOVER-09 checklist continues at T027–T030; no archive,
new active-plan selection, merge, activation/canary/settings/release or lane change.

Acquire the validated synthetic PR merge object independently in both trusted
controls using fixed-repository/SHA fetch without credentials, refs/FETCH_HEAD
or checkout changes. Include classic branch metadata alongside all effective
ruleset pages with existing read access and fail closed on incomplete/raced data.
Separate authenticated pending receipts from definitive full-only outcomes;
only pending may retry, without weakening strict success-only candidate admission.
Bound pending polling at30 ten-second intervals plus read budgets: actual fast
run43289185180 took3m19s, longer than the prior one-minute limit. Test delayed
success, exhaustion, unknown/failed proof and immediate permanent fallback.

Red-first committed regression preparation reproduced all14 initial failure
vectors against d4b3ae1: both independent clean controls, eight classic context/
check variants and four permanent-delay cases. Use real separate object databases
and a mock fixed-URL transport, not preloaded merge objects. Run offline UID1000
focused CI contracts and strict/lint/whitespace, then exact-head hosted full/reuse
qualification and the complete local specialist panel. Keep old13 full jobs,
their baseline JSON, legacy gates, queues, managed review trust and all live
systems unchanged. PR51 remains independently owned; preserve its Sonar work.

Repair-focused validation:791 passed/no skips through tests/run.sh as UID1000
in the offline disposable2CPU/2GiB image, including43 new repair cases. Workflow
actionlint passed. Hosted ordinary/reusable qualification and latest-head local
panel remain separate pending gates; both PRs must stay open.

Completed repair qualification at code2c6eed1458ee022b5544ff84d74e9ceb94af441e:
ordinary43309287917 passed15/15 jobs, reusable43309287854 passed17/17, first
attempts. Each full variant collected/reported5,080 items once. Normal results
are4,911 passed/169 skipped; deliberate no-submodule results3,562 passed/1,518
skipped. Compared with qualified d4b3ae1, all43 additions are repair regressions,
all pass in every variant, no original case was removed and no skip identity
changed. SonarCloud, Mergify configuration, fast CI and coverage parity passed.

Actual checkout5f94ea86cc7626e9c7c855033b326025cef0c3ad has parents59c26e8 /
2c6eed1 and exactly source treee7b80bda5922e709daeab8c9ac2cdac491399ecb.
Reuse references tests.yml at that immutable merge SHA. A fresh disposable Git
checkout also anonymously fetched the absent merge object using production
flags without changing HEAD/refs or creating FETCH_HEAD; not a live canary.

Local master plus nine standard expert roles and CI/GitHubActions/Mergify
specialist re-reviewed the repaired implementation and matching contracts with
no remaining actionable finding. The GitHub connector requests still returned
quota failure; no official bot approval or future admission receipt is claimed.
Both PRs remain OPEN, policyfalse and oldfour full requirements unchanged.
Shared py-bench/cloud-bench assembly still4b3c3f3. No merge/activation/canary,
settings/app/bypass/paid/release/tag/pin/active-plan or lane changes occurred.

## Approved current-main reconciliation and source landing — 2026-11-12

User authorization now permits reconciling code PR52 with current main and
merging PR52/33 after fresh CI and local review. Current code main is
`b227d53c42449eeafbd4a57979cfbd541f74fbd5` (Sonar PR51); spec main remains
`8813fa9d2bb5901aa75eb4f7afffcf9e69aba29b`. Preserve the historical preparation
and repair records above, extend this sole checklist with T031–T032, and do not
archive or advance assembly pins. Existing active lanes remain on their mounts.

Merge current main into the isolated PR52 branch without rewriting its history.
Preserve Sonar's complete source/coverage collectors, amended full-CI baseline,
wheel checks and dependency pin. Resolve only coupled contract conflicts; do
not regenerate baseline hashes or weaken coverage to obtain a green check.
Qualify focused CI/Sonar regressions as UID1000 in offline disposable2CPU/2GiB
QA, then ordinary full and reusable qualification on the published exact head.
Use the mandatory local review panel as authorized; GitHub connector quota is
not approval or a future candidate receipt. Merge normally with an exact-head
guard only after the integrated tested tree and current bases are verified.

The disabled flag, four legacy full requirements, every release definition,
Mergify access/settings and live lanes remain unchanged. Source merges are not
activation, live efficiency, composed-pin or installed-session acceptance.

Reconciled code94d4659897f8dad8ff86a4ab7a58497dc6e173f5 is a conflict-free
merge with parents2c6eed1/b227d53. Sonar collectors, amended baseline JSON,
dependency manifest/gitlink, shard planner/weights, canonical test runner and
release workflow exactly match current main. No baseline regeneration or
additional implementation edits were needed. Focused CI/Sonar QA passed836
tests/no skips (4,289 deselected) through tests/run.sh as UID1000 in offline
disposable2CPU/2GiB QA at the new dependency pin4bf963f. Actionlint and whitespace
passed. Fresh hosted ordinary/reuse CI and local review remain pending.

### Completed qualification and code source landing

Exact reconciled code `94d4659897f8dad8ff86a4ab7a58497dc6e173f5` passed fresh
ordinary full run `43321898110` (15/15 jobs) and reusable qualification
`43321898191` (17/17), first attempts. All four downloaded JUnit variants contain
5,125 unique case identities. Normal: 4,956 passed/169 existing skips; deliberate
no-submodule: 3,607 passed/1,518 existing skips. Relative to qualified repair head
`2c6eed1`, all 45 additions are inherited Sonar tests, no original cases were
removed and no skip identity changed. All 43 repair regressions pass everywhere.

Actual package checkout `684c21fd39ffff29f9951efa3e2b11843dff927b` has parents
`b227d53`/`94d4659` and exactly source tree
`0ef9640b600b1ffacc542ed40ab79db14e2bca21`. Reuse references that exact
`tests.yml` revision. Later regenerated PR merge `68baece` has the same parents
and tree, but was not substituted for the actually tested revision.

The local master, nine standard specialist roles and CI specialist re-reviewed
the integrated code/spec pair with no actionable findings. Focused QA, actionlint,
strict OpenSpec, SonarCloud, Mergify configuration and coverage parity passed.
GitHub connector requests remained separate from the authorized local review;
no future managed-admission trust or review waiver was introduced.

After fresh required checks, source/base/tree, disabled policy and review-thread
guards, code PR52 was normally squash-merged without bypass at
2026-11-12T23:21:37Z as `5173ad25910ca4d7acfd6052090246ccc084f1a1`, parent
`b227d53c42449eeafbd4a57979cfbd541f74fbd5`. GitHub readback confirmed the
landed tree exactly equals the qualified source/test tree above. Spec PR33's
final checked source landing and tree readback are recorded on that PR.

Source acceptance is not post-merge-main CI completion, activation, live savings,
composed/assembly adoption or installed-session acceptance. Keep the policy false,
all four legacy full requirements, existing shared mounts, lanes 1–5 and releases
unchanged. Do not archive or advance assembly pins in this source-landing act.

### Final spec-base reconciliation

The final merge guard stopped before issuing the spec merge because spec main
had advanced to `180c61136f99f0da455a718cdd489f0a44849bf1` through Sonar
reconciliation PR34. Its six files are disjoint feature010 governance/evidence.
Merge that main into PR33 without rewriting history, preserve all six files
exactly from main, and repeat strict validation/local review on the union.
Code source/landed trees and all exact-head CI qualification remain unchanged;
no new implementation or policy adjustment is introduced by this docs-only merge.
Final normal spec landing still requires fresh head/base/check/thread/tree guards.
