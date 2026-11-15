# Disabled source validation — 2026-11-15

Code candidate: `04e1e75b5cddbec3098ea18b6d6e3d16379fc7ae`, based on
`0119ab0bd872f7ba5c628c953f202f0f7280c67a`.

## Local evidence

The canonical wrapper in isolated task-owned QA passed:

```sh
tests/run.sh -k 'ci_v13_delivery or ci_delivery or ci_cutover or full_ci_coverage_parity or v13_shadow or ci_shadow'
# 749 passed, 4988 deselected, zero skipped; 267.65 seconds
```

The provider was initialized at the unchanged recorded pin
`4bf963f0ca416b142652bb608cafe5c76d893faa`. These are focused offline contract
tests, not the complete suite or actual required-PostgreSQL qualification.
An earlier new-test failure was an inconsistent mocked PR-number lookup; its
fixture was corrected before this candidate. No production gate was weakened.

The pre-PR panel's architecture, security, QA, reliability, data-integrity,
maintainability, API-contract, performance and static-analysis specialists found
no actionable findings on this code candidate and the original paired spec
`bea0e102228997aae86ea027c6c8ea2debf96f30`. The subsequent spec-only main merge
preserved the independent socket API contract; these task/evidence updates do
not change the reviewed normative delivery requirements. Hosted exact-head
review of the final published pair remains required. This report is not a
hosted Codex receipt or an independent human approval.

`openspec validate prepare-v13-delivery-cutover --strict` and `git diff --check`
passed. Diff checks confirm unchanged tests.yml, coverage baseline, Mergify
configuration and provider pin. Only qualification caller path filters expand.

## Remaining gates

Latest-head hosted full/reusable CI and exact-head review, normal source merges
and disabled-main readback are pending publication. No source result here
establishes activation, queue admission, live one-heavy-run efficiency, actual
tested/landed service trees, assembly adoption or release acceptance.

The v1.3 delivery policy remains false. P01 and owner dependency resolution,
future enablement/manual-queue review and separately approved live qualification
remain required. Running lanes, tools, settings, app access, paid tiers, releases
and assembly pins are unchanged.

## Checked source publication and landed readback — 2026-11-15 13:08 UTC

The pending source gates above subsequently completed:

- Code [PR99](https://github.com/opensoft/openRepoTools-code/pull/99), exact
  `04e1e75b5cddbec3098ea18b6d6e3d16379fc7ae`: ordinary full CI
  [43713913817](https://github.com/opensoft/openRepoTools-code/actions/runs/43713913817)
  succeeded with all 15 jobs; reusable qualification
  [43713913751](https://github.com/opensoft/openRepoTools-code/actions/runs/43713913751)
  succeeded with all 17 jobs. No integration job was skipped. Fast, parity and
  Sonar checks also passed.
- Genuine managed Codex summaries were completed with no findings/unresolved
  findings on the exact published heads: code comment `6894308803` for
  `04e1e75b5cddbec3098ea18b6d6e3d16379fc7ae`, spec comment `6894313642` for
  `4be235a6ef57dfacf7967ca17a3eaa307af4eb96`. Final complete thread inventories
  were empty; both PRs were clean and mergeable. Neither a quota reply nor the
  local panel was substituted for those hosted receipts.
- Spec [PR63](https://github.com/opensoft/openRepoTools-spec/pull/63) merged
  normally at `16b542219bdaf897a9fcff51934216d022cadf8b`, then code PR99 merged
  normally at `45a12a619903825ca1f6c41bc69c549114f1eca8`. Both operations matched
  the approved source heads; no bypass or queue command was used.

The landed code tree `e53ea81ee132cf4386304dffb0e1c40f2a732d15` equals the
reviewed/tested source tree. The spec's base advanced concurrently with the
owners' PR60 to `775b72c94bd77dbc3a70aec283f0b2f9227219e5`; its landed tree is
therefore not the earlier source-only tree. Recomputing the clean merge of that
actual parent and reviewed head yields exactly the landed spec tree
`b0f058aa65cb754a7077059db487b65afe2391da`. The seven owner-updated SQL-register
documents are preserved; this change's artifacts are unchanged from the
reviewed head. Strict OpenSpec validation passed on the actual landed spec.
Only task-owned feature checkouts were used; no shared checkout was refreshed.

Remote main readback confirms `delivery-v13-policy.json` still has `enabled:
false`, while the pre-existing legacy policy remains true. Main's completed
disabled shadow run [43717443342](https://github.com/opensoft/openRepoTools-code/actions/runs/43717443342)
reports policy SHA `45a12a619903825ca1f6c41bc69c549114f1eca8`, P01 pending,
required CI full, efficiency false, skip false, queue admission false and
not-qualified. Trusted shadow and ci-pilot also succeeded.

Post-merge full main CI
[43717443361](https://github.com/opensoft/openRepoTools-code/actions/runs/43717443361)
is still running at this snapshot; its route, guard and package jobs passed and
full test shards started. It is not reported as completed here. Checked source
publication and disabled readback are complete; actual required-engine main
qualification, activation, canaries, wider rollout, assembly adoption and release
acceptance remain separate, unachieved gates.
