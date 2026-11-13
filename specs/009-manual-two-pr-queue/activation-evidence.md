# CI-ACTIVATION-11: scoped source activation and two-member canary

Progress record: source activation is qualified. The first source-routing
attempt failed efficiency acceptance before queue use. No heavy or landing
acceptance is claimed; T040/T041 and the canary task sets remain incomplete.

## Authority and preserved boundaries

Brett approved required-check migration, reviewed enablement and a two-PR
canary on 2026-11-13. Lanes 1–5, their processes/worktrees, installed tools,
assembly pins, releases, app access and paid plans are outside this change.
No bypass actor, admin merge, queue-branch edit or force operation is allowed.

Scope remains two independent main-based claude-current task sets. Mixed,
uncertain, dependent or other-component changes retain full checks. Direct
merge of an opted-in fast-only source is not authorized: heavy integration is
held by the named manual Mergify queue, not the new required check by itself.

## Service migration

At 2026-11-13T10:27Z, with the flag false and baseline main22aac02 fully green,
only required contexts in repository ruleset25966308 were migrated from
guard-launch-mode/tests/tests-no-submodule/package to openrepotools-delivery,
GitHub Actions integration15368. Active enforcement, main-only condition,
empty bypass actors and every unrelated field/effective rule were preserved.
Classic status enforcement stayed off/empty. Before/after target and effective
readbacks matched the approved projection. No app or paid grant was added.

## Reviewed source qualification and landings

Code PR56 final head d4d42f936eb1c3f0cd416112fca014c829e4ae19, base
22aac02ba5cfcb2777e4bf05a574e26c555bd38b. Ordinary full43411905483 passed15/15;
reusable qualification43411905482 passed17/17; fast43411905733 passed1/1,
all first attempts. Both package logs identify actual tested merge
5d97e4e06883d95e0c31f64b15ecf6bc869525d9, ordered parents22aac02/d4d42f9,
treecfbf98c4343c21c49ea1bfc28bdcc84e0679bf88.

All four normal/no-submodule ordinary/reusable JUnit variants collected5204
unique cases. Normal5035passed/169existing skips; no-submodule3686passed/
1518existing skips. Explicitly normalize the two approved phase-test renames;
three added protection cases passed. Zero unaccounted removed identities,
duplicate cases, failures or skip-identity changes relative to main22aac02.

Master plus nine standard experts and CI specialist found no actionable issues
at codec2e2225/spec5cf876f. The master additionally reviewed final doc-only code
d4d42f9 and publication-only specd918a77 deltas. Hosted Codex was quota-blocked,
not approving; the approved local review path was used. No paid credits enabled.
Both PRs had complete review-thread inventories with zero unresolved threads.

Normally squash-merged PR56 at11:32:09Z as
30abf79690f7ac11384c7a634ddd14f7f308494f, sole parent22aac02,
treecfbf98c4343c21c49ea1bfc28bdcc84e0679bf88 exactly matching qualification.
Spec PR39 normally squash-merged at11:32:14Z as
c6c668ef7626bd8d972b598d8ca4b42398a978ef, sole parent9311051,
tree488d882904170970f18759b6cf836c60e4d48853 exactly matching reviewed source.
Canonical landing readers reported none open before normal protected merges.
Unrelated malformed historical log warnings were inspected, not repaired.

Actual enablement main full run43417843932 passed15/15 on30abf79, first attempt.
Both downloaded main JUnit variants exactly match the reviewed ordinary runs:
5204cases, normal5035pass/169existing skips, nosub3686pass/1518existing skips,
no added/removed/duplicate/failing cases or changed skip identities. Live main
flag reads true. Canary branches were created only after this qualification.

## Refusal and recovery fixtures

Final broad local CI contracts867passed/no skips. Six phase cases passed in a
deliberate no-submodule checkout, no skips. Before canary, an additional scoped
rerun of ci_cutover or ci_local_review passed261cases/4943deselected in51.41s.
This includes absent/unbound/legacy-retaining protection, malformed/disabled
policy, stale or revoked review, lost writer permission, live comment/thread
drift, uncertain source/candidate identity, incomplete heavy evidence and
revocation superseded by a fresh valid acceptance. These are offline fixtures,
not claims of intentionally sabotaging a live queue or removing writer access.

All local runs used existing py-bench:brett image in disposable nonroot,
network-disabled2CPU/2GiB containers and private temporary HOME. The reviewed
green2 executable patch was used; final source differed only in documentation.
No running bench restart, real credential use or installed-tool update occurred.

## Live canary

### First source-routing attempt: failed, never queued

Both independent source commits have the qualified enablement main 30abf79 as
their sole parent and preserve the original file modes:

- [Code PR 57](https://github.com/opensoft/openRepoTools-code/pull/57), set A:
  e2f2f1907112b166113f9d81fe9e7fdc4351534d, help prose only, mode 100755.
- [Code PR 58](https://github.com/opensoft/openRepoTools-code/pull/58), set B:
  8d8b28bf6ed271d2f013d445d879e29252880ee3, eight independent tests, mode 100644.

Local exact-bundle validation passed A's 19 focused cases, B's 27 focused
cases, and all eight new B cases in a deliberate no-submodule checkout. No
selected case skipped. Master, nine standard expert roles and a Bash/CI
specialist completed static review of both exact heads/base and spec b29cb9e
without actionable findings. Later factual evidence updates are coordinator
readbacks, not expert claims of live acceptance. Hosted Codex was requested on
both heads and returned quota failures; that is not hosted approval.

Both first-attempt fast runs passed:

- [A: 43423891441](https://github.com/opensoft/openRepoTools-code/actions/runs/43423891441):
  455 passed, 18 existing skips, 4,731 deselected.
- [B: 43423898443](https://github.com/opensoft/openRepoTools-code/actions/runs/43423898443):
  463 passed, 18 existing skips, 4,731 deselected.

However, both ordinary routing jobs selected mode=full, omit_full=false on
trusted main 30abf79. Full jobs started, so fast-only source acceptance failed.
Only these two newly created fallback runs were cancelled to avoid unnecessary
runner time: A 43423891433 and B 43423898734. Their cancelled status is not
full-CI success, and no rerun was used to conceal the failed initial attempt.

GitHub's complete userContentEdits histories identify an actual pre-routing
description race:

| Source | Created by brettheap | Summary appended by sourcery-ai | Full route logged |
| --- | --- | --- | --- |
| PR 57 | 12:17:16Z | 12:17:27Z | 12:17:32.150Z |
| PR 58 | 12:17:19Z | 12:17:30Z | 12:17:33.800Z |

All timestamps are 2026-11-13 UTC. The summaries preserve both unique opt-in
lines but change the description body from the opened event. The source router
compares the complete event/live policy.snapshot tuple, including body. This
confirmed difference fails that source predicate. The advisory observer also
reported pr-not-qualified, while the head-owned file classifier saw the allowed
claude-current scope. This is not evidence of missing model credentials or a
need for expanded app permissions.

An isolated offline fixture replay passed one diagnostic case: the valid source
first selects scoped omission; appending only summary prose then selects full,
with Git identities, rules, fast evidence and both opt-ins unchanged. This is a
reproduction of the observed body-drift mechanism, not a captured Actions-token
or complete webhook replay and not a production fix.

No local acceptance receipts or Mergify queue commands were published. No queue
candidate, heavy caller, qualified artifact, source merge or landing occurred.
Both PRs stay open. The required delivery gate remains non-green for these
cancelled source runs; they must not be direct-merged or counted as qualified.

### Proposed next step, not implemented or authorized by this record

Prepare a narrow code/spec fix that compares stable delivery decision metadata
for event-versus-live description changes, while retaining exact Git identities,
both unique opt-ins, dependency refusals, authentic fast evidence and strict
live rechecks. Add red-first regressions for harmless appended prose and real
intent/base/head/dependency changes, plus safe fixed-vocabulary diagnostics.
Review and fully qualify that configuration change before a fresh two-PR
canary. Do not trust the editing actor, disable Sourcery, widen app access,
change rules, weaken fallback or rerun this attempt into an efficiency claim.

The existing activation phase was limited to enabling prepared behavior. A
runtime metadata-contract fix and fresh canary require a separately approved
reviewed change. All successful heavy, prefix/final-tree and no-duplicate-main
acceptance gates remain pending.

## Remaining boundary

Owner follow-up approval on 2026-11-13 now authorizes the narrow repair above,
regression tests, reviewed/validated code/spec PRs and a fresh canary. The owner
also pre-approved directly related scoped fixes, checked normal source merges,
fresh canaries and final evidence updates without per-PR approval stops. This
supersedes the earlier pending-approval status, not the failed attempt. Use the
new fix-source-delivery-metadata change and sole tasks T044-T048. Assembly pins
and every other excluded resource remain excluded. No new live success is
claimed by that approval; append actual qualification as it completes.

Assembly adoption, exact-pin/composed acceptance and fresh-session rollout are
separate work and are not authorized by this source activation/canary approval.
No assembly or active lane checkout is refreshed here. Do not archive this
change or claim that all repositories/workflows are fully deployed.

## Metadata repair: source preparation and local qualification

Code head 887cd0dddf7bd5439570ba105f0689113b99dfd0 is based on qualified main
30abf79. Only the initial event/live source comparison, its runbook and routing
regressions change; no workflow, queue, baseline, review/artifact schema,
dependency, settings or excluded resource changes. Both event/live intents
qualify independently, and every other snapshot field plus strict subsequent
complete live reads remain enforced.

Red-first tests against the old router produced six expected positive-case
failures and 35 passing refusals/races. With the repair, canonical offline
nonroot bench QA passed 908 CI contracts with no skips, plus all 41 new cases
in a deliberate no-submodule checkout with no skips. The exact exported tested
patch matches the reviewed committed patch, SHA256
3518DC6042EE39F004847893664DF155F705C8E6FC13B373070CE232E924E6A2.

Master plus nine standard experts and a CI specialist reviewed code 887cd0d
and spec 41ab19e without actionable findings. This factual publication update
is coordinator-reviewed. T044/T045 are complete; hosted source/reuse CI,
checked landings, actual main and fresh-canary acceptance remain pending.

## Metadata repair: hosted qualification and code landing

At code head 887cd0dddf7bd5439570ba105f0689113b99dfd0, first-attempt ordinary
run 43450668152 passed all 15 jobs, reusable run 43450668194 passed all 17,
and fast run 43450667976 passed. Attempt-specific inventories contain the exact
expected job names, all successful, with no skipped full jobs. Both PRs had
complete review-thread reads with zero unresolved threads. Hosted Codex was
quota-blocked; the approved local review path remains the actual review evidence.

All four downloaded ordinary/reusable normal/no-submodule JUnit variants have
5,245 distinct cases, exactly the reviewed 41 additions to the 5,204 baseline.
Normal: 5,076 passed and 169 existing skips. No-submodule: 3,727 passed and 1,518
existing skips. No removed/duplicate/failing cases or changed skip identities.

Both completed package logs identify actual tested merge
d8f874df431239c837e0692ae6dbc6a1e28f94f2, ordered parents 30abf79/887cd0d,
tree 1dca115d61e4c46878830f32850c4224e0c473d7. Code PR59 was normally
squash-merged as 000743a3a3bb9eca92960d0c8fad1bf2075dc2e8, sole parent 30abf79,
with that exact tree. No bypass. Fresh canonical landing reads reported no open
hold for either repo; the same 15 malformed historical warning lines were
checked against the current registry remote and do not mention openRepoTools.
No lane log or shared checkout was rewritten.

T046 is complete. Spec landing and actual new-main full qualification remain
T047 gates; fresh canary, receipt, heavy and landing acceptance remain pending.
The first-attempt failure record above stays failed, and no new queue command
or source fast-only acceptance is claimed by this configuration landing.

## Metadata repair: actual main qualified for the fresh canary

Spec PR40 normally landed reviewed head 954fe3a as
28b5cf3d687a2a9f8680c59f260c5f3b701a3bfc, sole parent c6c668e, tree
a00e26de3f6b408398041be99e5072d017820068 exactly reviewed. The final publication
delta was acknowledged without actionable findings by the full local panel.

Actual code-main run 43458131280 passed all 15 expected jobs on
000743a3a3bb9eca92960d0c8fad1bf2075dc2e8, first attempt, no skipped full jobs.
Its complete normal/no-submodule JUnit inventories each contain 5,245 cases and
exactly match the qualified source variants, including skip identities. Normal
5,076 passed/169 existing skips; no-submodule 3,727 passed/1,518 existing skips.
Current code main remained at that exact qualified commit before fresh branches.

T047 is complete. New independent A/B branches may now retry T042/T043 from
that qualified main with new exact heads, reviews, receipts and PRs. T048 and
all live efficiency/heavy/landing acceptance remain pending. No fresh-source
success is inferred from configuration or main qualification.

## Fresh R2 attempt: source fallback before queue admission

Fresh independent PR60 / head df0871be2a873158ae2c55863097283cd05fd982 and
PR61 / head 5ada6dfbe5bfdfd11cc7f4a3e0381781521a4837 were opened from the
qualified 000743a3 base. Local QA passed A19, B27 and all eight new B cases in
deliberate no-submodule mode, zero skips. The full local expert panel reviewed
these exact refs without actionable findings. The new source patches are
byte-identical to the useful original failed-attempt patches, not old heads.

Actual first-attempt ordinary route logs selected full at 16:49:45.835Z for
A run 43466603976 and 16:49:52.134Z for B run 43466623324 on 2026-11-13.
Full jobs started, so source-efficiency acceptance failed. Only those two
coordinator-owned fallback runs were cancelled after exact identity checks;
both are terminal cancelled. No local acceptance receipt, queue request,
heavy caller or source merge was made. PR57/58 were closed with replacement
links while preserving branches/history. R2 remains failed evidence.

Read-only live diagnosis later confirmed effective protection/current-run
identity and authenticated pending fast evidence using reconstructed current
PR metadata. That is not the original triggering payload or API-read sequence.
The public event feed is sparse, and existing route logs reveal no refusal
stage. Nullable event metadata, incomplete early fast evidence and read-to-read
transitions remain hypotheses, not a proven cause. T049-T051 add diagnostic-only
observability before a genuinely new attempt, without changing eligibility.

## Diagnostic-only follow-up: local qualification

Code 07336fddb80dcd9f86221ff788fffcd0fe04c07d adds only fixed stderr stage/reason
codes and snapshot mismatch field names. Unknown exception text is never
emitted. Exact routing outputs, snapshots, retries, proof schema and every
gate remain unchanged. Against old main, six diagnostic tests failed and the
quiet-success case passed; these are logging regressions, not eligibility fixes.

With the change, 915 broad CI contracts passed, zero skips (4,337 deselected).
The focused routing/review subset passed 375, zero skips; all seven additions
also passed in deliberate no-submodule mode, zero skips (5,245 deselected).
Canonical tests/run.sh ran in disposable existing bench-image containers as
nonroot with no network, 2CPU/2GiB, private HOME and exact pinned shape bundle.
Early disposable QA setup attempts failed before test execution because of
incorrect fixture mounts; corrected runs above initialized the exact 4bf963f0
pin or deliberately omitted it. No live bench was restarted or rebound.

The exported tested patch exactly matches the committed code diff, SHA256
013B1544D108AAA6C6F89F3ACD6CE0D26CCB28610A381B34C9847D24C93A366C.
Master plus nine standard experts and CI specialist reviewed exact code 07336fd
and spec 04fc566 without actionable findings. This final QA/task-only update is
coordinator-reviewed. Both OpenSpec changes validate strictly. Shared py-bench
and cloud-bench assembly HEADs remain 4b3c3f30b08d39469a185cbb74afdace9579a857.
T049 is complete; T050 hosted qualification/landings and T051 actual new-event
diagnosis plus all live efficiency acceptance remain pending.
