# Original twelve findings

The original root analysis was 35359032-968b-4948-912b-9db4d55591e7 at
4b3c3f30b08d39469a185cbb74afdace9579a857. Every finding predates this repair.
The [machine-readable record](findings.json) retains each exact issue identity,
owner and rationale. No rule, threshold, source directory or real finding is
suppressed to obtain a green result.

Six findings require changes: five package installation sites allowed source
distributions, and the provider lacked a recognized runtime lock. They are fixed
at their owning repositories, then consumed through published, validated pins.
The old build-tool lock was real but did not replace the missing runtime lock.
The new runtime lock keeps git-filter-repo 2.47.0, whose PyPI wheel declares no
runtime dependencies.

Six are false positives based on the complete source-to-sink flow. A generic
CLI argument is not by itself a vulnerability; the review below identifies the
actual executable, destination, escaping and authority boundary. The code's
existing guards remain in place. Regression results and service readback are
recorded in [acceptance evidence](evidence.md).

## AaHNkAKzuDHHi52pWjoI — pythonsecurity:S8705

Location: code/upstream/openRepoShape/scripts/shape_materialize.py:1204.

Disposition: **false-positive**. A branch value reaches fixed git/gh argv, not a shell command. main calls checked_branch before any apply path; the branch alphabet and git check-ref-format --branch reject option/ref syntax. Push uses -- plus refs/heads/<branch>:refs/heads/<branch>. run checks the executable allowlist. No HTTP handler or privilege boundary exists on this CLI flow.

Regression sources at the original provider pin: tests/test_update_shape_landing.py, tests/test_push_branch_refspec.py.

## AaHNkAShuDHHi52pWjoK — pythonsecurity:S8705

Location: code/upstream/openRepoShape/update-shape.py:4951.

Disposition: **false-positive**. The flagged landing_failed sink constructs a NEXT string for display. shlex.join quotes every argument; the push uses -- and a fully qualified refspec; create is the fixed gh pr create argv. This line does not execute the printed string. Its branch has already passed checked_branch.

Regression sources at the original provider pin: tests/test_update_shape_landing.py, tests/test_next_refspec_branch_length.py.

## AaHNkAShuDHHi52pWjoJ — pythonsecurity:S8705

Location: code/upstream/openRepoShape/update-shape.py:5609.

Disposition: **false-positive**. The flagged next_line sink constructs a printed manual recovery command. Both the root path and argv are quoted with shlex.join; branch refspecs are fully qualified. No shell evaluates this output. The caller validates the branch before entering this function.

Regression sources at the original provider pin: tests/test_next_refspec_branch_length.py, tests/test_push_branch_refspec.py.

## AaHHsu91IZXyb5gijwTS — pythonsecurity:S2083

Location: code/upstream/openRepoShape/scripts/shape_materialize.py:883.

Disposition: **false-positive**. The alleged file-content path traversal has no file-content-derived destination: _append_eol_block uses target / ATTRIBUTES, where ATTRIBUTES is the fixed .gitattributes name. It writes only on the exact attributes collision and refuses disk symlinks, non-files and index-mode symlinks, including core.symlinks=false checkouts. Existing content is payload for with_eol_block, not a destination path.

Regression sources at the original provider pin: tests/test_adopt_attributes_eol_block.py.

## AaGD6tPOxLux756IlU7J — githubactions:S8541

Location: .github/workflows/composed.yml:102.

Disposition: **confirmed-fix**. Require wheels for pytest and coverage tooling in the composed job.

Repair: [source-owner PR](https://github.com/opensoft/openRepoTools/pull/361).

## AaHAbodRidUOawbE2mJx — pythonsecurity:S8707

Location: scripts/repo_shape.py:1151.

Disposition: **false-positive**. load_yaml is an intentionally caller-selected local YAML reader. The traced CLI callers read --project, a fixed project.yaml below the selected repository root, or the selected local policy. Their explicit purpose is to validate an operator-selected filesystem tree. No network request, lower-privilege principal, confined-directory promise or privileged service is involved; an operator selecting a local input file is not a traversal across an authority boundary.

Regression sources at the original provider pin: tests/test_load_yaml_refusals.py, tests/test_yaml_reader_spaces.py.

## AaFMxuNRjy8gvDmEH5jA — text:S8565

Location: code/upstream/openRepoShape/pyproject.toml.

Disposition: **confirmed-fix**. Add recognized, fully pinned and hashed requirements.txt for git-filter-repo 2.47.0; its wheel has no Requires-Dist dependencies. Retain the separate full build hash lock.

Repair: [source-owner PR](https://github.com/opensoft/openRepoShape/pull/1330).

## AaFMxvaxjy8gvDmEH5kE — pythonsecurity:S8705

Location: code/upstream/openRepoShape/src/openreposhape/cli.py:164.

Disposition: **false-positive**. The org text entered on the terminal becomes the value after the fixed --org flag. run_child receives sys.executable followed by a known standard-owned Python script, using list argv and shell=False. The tools intentionally forward their documented options to that script, whose parser/validators own those values; stdin cannot select a program or add a shell fragment. This is a local CLI launcher, not an HTTP handler.

Regression sources at the original provider pin: tests/test_openreposhape_package.py.

## AaDPP3_OrPHmB1oAsIwL — githubactions:S8541

Location: code/upstream/openRepoShape/.github/workflows/tests.yml:95.

Disposition: **confirmed-fix**. Require wheels in the source-owned test workflow.

Repair: [source-owner PR](https://github.com/opensoft/openRepoShape/pull/1330).

## AaDPP3_OrPHmB1oAsIwM — githubactions:S8541

Location: code/upstream/openRepoShape/.github/workflows/tests.yml:105.

Disposition: **confirmed-fix**. Install the same runtime dependency from its hashed wheels-only requirements file.

Repair: [source-owner PR](https://github.com/opensoft/openRepoShape/pull/1330).

## AaDPP35qrPHmB1oAsIwI — githubactions:S8541

Location: code/.github/workflows/tests.yml:173.

Disposition: **confirmed-fix**. Require wheels; preserve the complete parsed CI job contract.

Repair: [source-owner PR](https://github.com/opensoft/openRepoTools-code/pull/51).

## AaDPP35qrPHmB1oAsIwJ — githubactions:S8541

Location: code/.github/workflows/tests.yml:267.

Disposition: **confirmed-fix**. Require wheels; preserve the complete parsed CI job contract.

Repair: [source-owner PR](https://github.com/opensoft/openRepoTools-code/pull/51).


## Verified source-flow and policy receipts

At the original provider pin 50178305e4229275d18e71c2152403b4e9d3004e,
101 targeted branch/refspec/symlink/argv regressions passed. The YAML reader
checks passed 350 cases with zero skips after installing PyYAML 6.0.2 to run
the optional stock-reader comparisons. The provider repair's two new policy
checks plus the existing installer-pin contract passed (3 cases).

All six false-positive dispositions were applied in Sonar with per-issue
source-flow comments and the immutable triage record link. The six confirmed
findings remain open until their source-owner repairs are consumed and analyzed.

## Source-owner landing and consumer selection

The provider repair merged normally in PR 1330 at
4bf963f0ca416b142652bb608cafe5c76d893faa, with full Linux/Windows/package/Sonar
checks passing. The consumer repair merged normally in PR 51 at
b227d53c42449eeafbd4a57979cfbd541f74fbd5 after every ordinary/reusable full-CI
check passed on c72c619510d88ae8eb6dcb0e220ce3c299708819. Its provider gitlink
and contract select that merged provider commit with the digest recomputed by
the provider's own helper. The expanded source-flow tests passed 414 cases and
the current YAML oracle set passed 625 with zero skips.

These source repairs are not an invented Sonar closure: the root project's six
confirmed findings still require readback after the assembled main analysis.
The existing issue identities, original source commits and earlier evidence
remain intact. No real finding has been marked false-positive or accepted.


## Additional findings from complete main analysis (2026-11-13)

Main `dab458e2f774637cccea490bfc6bdb1a5dafe212`, analysis
`d61100b9-ea1e-4b47-9400-d0d68868e601`, evaluated all six unchanged
conditions. Coverage 86.0%, duplication 2.6%, maintainability A and hotspot
review 100% passed. Reliability E (4 bugs) and security D (16 vulnerabilities)
correctly failed the required gate. [The exact 20 issue records](main-findings.json)
retain the original keys, paths, rules and proposed dispositions.

| Group | Count | Source review and proposed action |
| --- | ---: | --- |
| shell:S6506, root/implementation launchers | 8 | Real HTTPS-to-HTTP redirect exposure. Fix the existing three root and five code curl invocations with `--proto '=https' --proto-redir '=https' --tlsv1.2`; retain normal TLS verification and gh-first fallback order. |
| python:S5708, provider adopt-project.py | 4 | `_Unasked` is a class deriving from imported `repo_shape.Refusal(Exception)`; all four handlers refer to that class. Verify runtime class identity and exercise refusal handling before false-positive disposition. |
| python:S5332, lane-worktrees/lanes-index | 2 | HTTP strings are accepted input spellings in repository-slug normalization, then removed. Neither function connects to that URL. Verify pure normalization and no transport call. |
| python:S5443, lane-worktrees | 2 | `sandbox_roots` enumerates existing candidate roots; it creates no temporary file or predictable filename. Candidate cleanup separately checks ownership/liveness/age and keeps arbitrary `tmp.*` directories. Verify the function and preservation regressions. |
| pythonsecurity:S8707, lanes-index | 2 | `--out` is the local operator's explicit export destination, not data beneath a promised confinement root. The written basenames are fixed `worktrees` and `meta`. Verify the export contract and reader regressions. |
| pythonsecurity:S8705, provider update-shape.py | 2 | One call passes a fixed git argv and a full `refs/heads/<branch>` value; the other uses `shlex.join` to print a manual recovery command. Verify branch/quoting regressions and source flows. |

No new issue is accepted or waived. Apply false-positive dispositions only
after the recorded source and regression proof; leave the eight real findings
open until a fresh main scan closes them automatically. Preserve the original
twelve records and server history, including cases the new analyzer closed.

The eight real curl call sites each reproduced the downgrade with native curl
against ephemeral loopback HTTPS/HTTP servers before the repair (8 failed
security assertions). The stubs captured actual expanded function arguments;
no GitHub request or downloaded payload was executed.


### Verified additional false-positive evidence

[Machine-readable runtime and regression proof](main-findings-proof.json)
confirms the twelve proposed false positives before any service transition:

- All four `_Unasked` handlers bind the actual class whose MRO is
  `_Unasked -> repo_shape.Refusal -> Exception -> BaseException`. The
  context manager preserves a thrown instance, and the submodule/throwaway
  handlers convert it to the expected finding. Existing entry-finding
  regressions exercise the remaining path.
- Both HTTP normalizers returned `example/repo` for HTTP/HTTPS input with
  `subprocess.run` forbidden; they perform no connection.
- The default sandbox roots enumerate existing `/tmp` with
  `open`, `os.open` and `os.mkdir` forbidden. They create no file.
- Ten existing code regressions passed, zero skipped: live writers, arbitrary
  marked/unmarked `tmp.*` directories, bare repositories/notes, symlink removal
  preservation, and the fixed-basename export/read contract.
- The four provider regression modules cover throwaway refusals and branch/
  manual-command quoting. Across 371 distinct selected cases: 365 passed,
  6 require a case-insensitive filesystem unavailable on these hosts. The
  first local run also skipped 15 for missing git-filter-repo; all 15 were
  rechecked in the prepared environment with git-filter-repo 2.47.0:
  11 passed and 4 remained filesystem-only skips. The whole provider source
  tree matches the pinned merged revision, tree
  `e4605a5c4f30426a79e8009febc398e5bb20d2ee`.

The four modules are
`test_adopt_check_throwaway_repos.py`,
`test_update_shape_branch_any_directory.py`,
`test_update_shape_paste_lines.py` and
`test_update_shape_next_quoting.py` at provider
`4bf963f0ca416b142652bb608cafe5c76d893faa`.
The ten code cases are in `test_lane_worktrees.py` and
`test_lanes_index.py`; their production sources match code
`22aac02ba5cfcb2777e4bf05a574e26c555bd38b` unchanged.

Apply only these twelve false-positive dispositions, with per-issue source-flow
comments and this published evidence. Keep all eight real download issues open
until automatic closure in a fresh main scan. No accept/wontfix transition,
threshold change or exclusion is authorized by this triage.

## Accepted main finding readback

Main revision `4377a702913f3a096eae42131d131bdf4480b0e3`, analysis
`52558a56-c6f2-432b-a179-102a63bd4a57`, computes a green gate with zero
open bugs and vulnerabilities. [Final per-issue states](final-acceptance.json)
and the appended readbacks in both inventories preserve every original key.

The original twelve are eight CLOSED/FIXED and four RESOLVED/FALSE-POSITIVE.
Six original findings had substantiated source repairs; two of the six
originally documented false positives disappeared and closed automatically.
Those two closures are not claimed as extra source fixes.

The additional twenty are eight CLOSED/FIXED automatically after the five
implementation and three root curl calls enforce HTTPS for requests and
redirects, and twelve RESOLVED/FALSE-POSITIVE after published source-flow and
runtime/regression proof. No accept/wontfix resolution, rule suppression,
threshold change, new exclusion or baseline-to-today change was used.
Maintainability remains A with 7,569 code smells (7,561 in the old new-code
period); this bounded repair does not claim every maintainability issue fixed.
