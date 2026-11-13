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

Assembly adoption, exact-pin/composed acceptance and fresh-session rollout are
separate work and are not authorized by this source activation/canary approval.
No assembly or active lane checkout is refreshed here. Do not archive this
change or claim that all repositories/workflows are fully deployed.
