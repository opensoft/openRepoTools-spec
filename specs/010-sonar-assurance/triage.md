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

Regression sources at the original provider pin: tests/test_repo_shape.py.

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

