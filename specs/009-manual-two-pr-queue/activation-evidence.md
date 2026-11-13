# CI-ACTIVATION-11: scoped source activation and two-member canary

Working evidence draft. Do not treat pending sections as live acceptance.

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

PENDING: two independent main-based source heads, exact-head/base local review
receipts, actual source and candidate fast-only CI, two-member queue order,
single first-attempt complete heavy caller, immutable qualified artifact,
matching tested/landed prefix and final trees, and no duplicate full main run.

## Remaining boundary

Assembly adoption, exact-pin/composed acceptance and fresh-session rollout are
separate work and are not authorized by this source activation/canary approval.
No assembly or active lane checkout is refreshed here. Do not archive this
change or claim that all repositories/workflows are fully deployed.
