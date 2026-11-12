# Cutover research

Baseline: code59c26e8/spec8813fa9; reference OpenBenches5df4fa0. Read-only
research was split between reuse/provenance and effective-rules/queue semantics.

| Decision | Evidence and rejected alternative |
| --- | --- |
| Verify effective main rules, read-only | GitHub GET rules/branches/main includes inherited active rules, paginated to100, metadata-read/public anonymous. Current repo has four enforced full contexts; do not copy OpenBenches' different effective floor or assume a true flag migrates it. |
| App-bound honest delivery check | Require integration15368 and no old four contexts before omission. Never fabricate greens under an unrun full-job name or choose protection-injection none. |
| Authenticated caller identity | Real reusable run43115161206 retains pull_request but run.path is full-ci-parity.yml, with referenced tests.yml SHA. Event name/user inputs alone cannot distinguish ordinary vs reuse. |
| Immutable bounded receipts | Pinned upload creates immutable artifacts; overwrite replaces ID. Archive SHA256 is over downloaded ZIP bytes. Check ID/digest/run/head/attempt, exactly one bounded JSON member and reread provenance. |
| Separate landing proof | OpenBenches project-stack-ci.py separates live admission from historical ordered prefix/GitHub merge records. Do not turn closed members into open admission or trust tree equality alone. |
| Top-level heavy check | New two-level nested job prefix schema needs later canary confirmation. Queue waits on a unique root job instead of a nested suffix. |
| Manual-only semantic disclosure | Changed GitHub source gate permits fast verification; Mergify holds heavy only for its named queue. Operator prohibition on direct merges is not machine enforcement. Ask before any new service control. |
| Layered classic protection | Rulesets can layer with classic protection. Public branches/main metadata exposes classic required contexts/checks with Contents:read. The observed clear-classic shape has protected:true, protection.enabled:false and empty arrays/enforcement:off; incomplete or legacy-required evidence retains full. |
| Independent synthetic merge objects | Pinned checkout with explicit main and full history fetches heads/tags, not PR merges. Both controls must acquire the validated immutable event commit independently from the fixed public repository without checkout/ref/FETCH_HEAD/credential changes. |
| Retry only authenticated pending evidence | Actual fast run43289185180 took3m19s. Definitive full-only cases must not wait; otherwise eligible pending work may poll30x10s plus bounded reads within the existing15-minute control budget. Exhaustion remains full and candidate admission remains success-only. |

Primary sources: [rules](https://docs.github.com/en/rest/repos/rules),
[reusable workflows](https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflows#calling-a-reusable-workflow),
[attempt jobs](https://docs.github.com/en/rest/actions/workflow-jobs#list-jobs-for-a-workflow-run-attempt),
[artifacts](https://docs.github.com/en/rest/actions/artifacts),
[pinned immutable upload behavior](https://github.com/actions/upload-artifact/blob/043fb46d1a93c77aae656e7c1c64a875d1fc6a0a/README.md#overwriting-an-artifact),
[queue rules](https://docs.mergify.com/merge-queue/rules/),
[two-step qualification](https://docs.mergify.com/merge-queue/two-step/).

Repair references: [rule layering](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/about-rulesets#about-rule-layering),
[public branch metadata](https://docs.github.com/en/rest/branches/branches#get-a-branch),
[pinned checkout full-history refspec](https://github.com/actions/checkout/blob/11d5960a326750d5838078e36cf38b85af677262/src/ref-helper.ts#L69).

No new permission, app access, settings write or lane contact was used. Code PR51
owns independent Sonar work; preserve its baseline and qualify the integrated
latest head before a later merge. Current preparation is not live proof.
