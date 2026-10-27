# The cutover as built — October 26, 2026

**For:** T015 of [tasks.md](tasks.md), under
[opensoft/openRepoTools#186](https://github.com/opensoft/openRepoTools/issues/186). It is the
as-built record of the final spec reconciliation batch, which T015 asks for with "net tested
effects, every amendment disposition, dated departures/as-built record, archived approval
provenance, deferred issues and actual landed code/evidence" (`tasks.md:110–113`). No task is
checked complete by this record, and it approves nothing.
**Writer:** lane openRepoTools-1, 2026-10-26, for the batch that S15 item 8 of the cutover's
runbook lands.
**Sources:** the cutover's private run folder, `cutover-20261026-r6`: its `exits.txt` (through
line 109, `S14 root-head green` at 22:13:08Z), `execute.log` and the S4 to S14 records they
name, `s14-root-head-1` among them. Lane 1's relays on opensoft/openRepoTools#186 (comments
6417706286, 6421077855, 6421955767, 6423405883, 6424440846, 6425830987, 6427399285 and
6428789445), and lane 2's read-only repeat of S12 (6427616680). Brett Heap's words, and their
times, are quoted from lane 1's record of his rulings, by round.
**What it covers:** the cutover through S12, and S14 as it stood at S12's head.
**What it cannot cover:** T015's items 1 to 6, this batch's own merge and the close of its
reconciliation issue, S15's approved bump push, the root-split merge and Gate D all come after
this record lands (section 9).

## 1. The baseline, against the cutover's

`openspec/changes/migrate-to-triad/design.md:5–7` and `:44–45`, and `plan.md:18–24`, give the
preliminary baseline. The cutover's is the one Brett Heap approved at Gate C (section 7).

| | The preliminary baseline | The cutover's |
| --- | --- | --- |
| Source | `main` `daed20957f2dd2f22cca24053bb5bc8636ff6b3f`: 64 tracked paths, 80 commits | `main` `33ce26dffc5286e68f1d29ab5c6458edef0330c0`: 200 tracked paths, 156 commits |
| Standard | openRepoShape `39d5c986fcfac1a160474bfe91c5f1c37fccc72c`, inspected | openRepoShape `adopt-project.py` at `190133d5e704e380c430f87ca9c4e31746de0012` (v1.1.0) |
| Placement | 18 spec, 40 code, six root, none dropped | 108 spec, 86 code, six root, none dropped |
| Follow-ups | 28 | 42 |
| Plan | the change's `adoption-plan.yaml` of that day | FP, generated 2026-10-22, sha256 `94af9924ebbf58aad5b916528e3e2f76e6f588acd0d65cff5efe534fdadaf2d9`, whose published copy is [adoption-plan-cutover-2026-10-26.yaml](adoption-plan-cutover-2026-10-26.yaml) |

- **The published copy** is S4's: FP's bytes but for its `local_path` line, which reads `.`,
  under a seven-line header that names FP's sha256 and Gate C's approval, and with two closing
  lines, `planning_status: cutover-record-not-an-approval` and `execution_authorized: false`;
  485 lines, sha256 `538e2521eaaeff8f99e4de7c3af26e016488cb53f1169aacab934a3062e5f66a`. Its
  header calls it "FP byte for byte but its local_path line"; the header and the two closing
  lines are S4's additions as well. Its `execution_authorized` is `false`, as in every copy S4
  writes: this record says when `execute` ran. S4's own regeneration from the source differed
  from FP only in `generated_on` and `local_path`, and its `check` gave `plan ok`, with no
  finding, no refusal and one note.
- **The change's `adoption-plan.yaml`** is now run C's rehearsal input (source `837928a`, 120
  paths, 29 follow-ups, openRepoShape `7f84ca4`), as its own header says. It is not the
  cutover's plan.

## 2. What the cutover did, S1 to S12

All times are 2026-10-26, UTC, from `exits.txt` unless a relay is named.

| Step | Time | What it did |
| --- | --- | --- |
| S1 | 13:52:26Z | The approval record and the notice of the hold, opensoft/openRepoTools#186 comment 6417706286, on Brett Heap's start (round 23, 13:44:57Z). `main` was `33ce26d`. |
| S2 to S5 | 16:12:44Z to 16:13:52Z | In the second run folder (section 8, departure 2): S1's register read again (result 0), the clones, identity and tools, the before-setup observation (exit 0), and S4's `decisions`, `plan`, `resolve`, `check`, `publish` and `check-published`, each exit 0. |
| S10's inputs | 16:13:58Z | The legs' and the root's inputs checked against their sums. |
| S6 | 16:15:13Z | The gate record, every line OK (comment 6421077855). Its A2 read found brettheap/new-workstation#62 merged, with six notes. |
| The go word | 16:41:38Z | "go de8fda1a", typed in lane 1's session after Brett Heap chose "Go: start S7 now" (round 25, 16:21:50Z); recorded at 16:42:10Z. |
| S7 | 16:42:24Z to 16:47:03Z | The HOLD on `main`; `execute` once, from 16:43:35Z to 16:44:23Z, exit 0; the HOLD released (comment 6421955767). |
| S7b | 16:48:05Z | The legs' first pushes, read from GitHub's rule suites (section 3). |
| S8 | 16:49:00Z | The root's pinned doctor, on a recursive clone of the split: `COMPLIANT (exit 0)`. |
| S9 | 16:50:37Z | The independent accounting and checks: `RESULT PASS`. |
| S14, raw | 17:00:12Z and 17:52:00Z | The runs on the raw split, recorded as runs on the raw split, not evidence for the cutover (comment 6423405883). |
| S13, acts 1 and 4 | 18:39:51Z to 18:48:37Z | Brett Heap's words for act 1 (SonarCloud bound to `openRepoTools-code`) and act 4: the code leg's `testpypi` and `pypi` environments and its tag ruleset 25903054, read back at 18:40:47Z, then the two PyPI publishers (comment 6424440846). |
| S10, the legs | 18:41:09Z to 18:42:24Z | Rows 1, 2, 3 and 3a pushed to `004-migrate-to-triad-code-follow-ups`, and row 4 to `004-migrate-to-triad-spec-follow-ups`; opensoft/openRepoTools-code#1 and opensoft/openRepoTools-spec#1 opened. |
| S14, code-pr | 19:50:24Z | The code PR's run read green (section 3). |
| S10, the code merge | 19:50:47Z | opensoft/openRepoTools-code#1 merged by merge commit, as `2a057a6d84e58c8acb4bda20d2b7b667b552c3fe` (comment 6425830987). |
| S14, code-main | 21:04:36Z | The merged code leg's push run read green (section 3). |
| S10, the spec merge | 21:04:55Z | opensoft/openRepoTools-spec#1 merged by merge commit, as `3c3816210b912759b350f77c2128e39f628edc39`. |
| S10, the root | 21:05:32Z | Rows 5, 6, 6a and 7 on a fresh clone of the split branch, at `96130bef…`, not pushed. |
| S11 | 21:06:10Z to 21:06:16Z | Rows 8 and 9, the two bumps, each after its `--dry-run`. |
| S12 | 21:06:34Z to 21:09:04Z | Row 10; the local checks; the HOLD (21:07:31Z); the one push of `adopt/three-repo-shape`, a fast-forward from the split to `fe7841b2…` (21:08:12Z); the same checks on GitHub's branch (21:09:00Z); the HOLD released at 21:09:04Z (comment 6427399285). |

No step from S1 to S12 wrote a STOP or a WAIT.

## 3. The net tested effects

**`execute` and its first pushes (S7, S7b).**
- `execute` ran once and exited 0. It created `opensoft/openRepoTools-spec` and
  `opensoft/openRepoTools-code`, both public, default branch `main`, and pushed each leg's
  `main`: the spec leg at `de756faf8797ea4ee598bf2a9cee84f2b358fa67` (69 commits kept) and the
  code leg at `d9e7d568011b339e8d4bb87f3f83f58ec653794a` (145 commits kept). Both equal
  rehearsal D's tips (`MATCH`).
- It pushed the split, `f36db81076b9d14d9d03baf4311d9b4e3bbe1234`, to
  `adopt/three-repo-shape` of opensoft/openRepoTools and opened its pull request,
  opensoft/openRepoTools#344, on `main`. The split's author and committer are Brett Heap, with
  N1's address. `main` stayed at `33ce26d`.
- It wrote the shape's five files beside the source's own (`shape/README.md`,
  `shape/AGENTS.md`, `shape/CLAUDE.md`, `shape/.gitignore` and `shape/.gitattributes`),
  overwriting nothing, and appended to the root's `.gitattributes` one `text eol=lf` line for
  each of the 9 pinned copies.
- Its own verification found every source path of `33ce26d` in exactly one place, by blob:
  spec 108, code 86, root 6 and drop 0, with 18 new paths.
- The topic `xf-project-openrepotools` is on all three repositories.
- GitHub's rule suites recorded each leg's creation push as `pass` (spec, suite 4729675498 at
  16:43:53Z; code, 4729678569 at 16:44:02Z), and org ruleset 18834180 stayed at its pinned
  baseline.

**The checks of the raw split (S8, S9).**
- S8: `COMPLIANT (exit 0)`. Every row was `ok` but `machine` (`n/a`) and a `note`: neither leg
  held `AGENTS.md`, `CLAUDE.md` or `.gitignore` yet.
- S9, on all 200 entries of `33ce26d`: spec 108, code 86 and root 6, none missing, duplicated
  or misplaced; 22 assembly additions; the root's `.gitattributes` the source's blob
  `3560973e9d45` plus the 9-line block, giving `a9ac338e09f6` (`MATCH`); the nested
  openRepoShape at `190133d`, whose pin digest is
  `7a66972c7de193c050f77f97bffa13d8543c28d74f61ea210644e65aa3f37912` (`MATCH`); `bootstrap ok`,
  `manifest ok` with 3 legs, and `pins ok`, with 11 copied shape files matching; every tree
  clean after validate. In the spec leg, openspec 1.6.0 passed all 6 changes, and openspec
  1.2.0 passed 3 and failed `interactive-lane-name-repair`, `migrate-to-triad` and
  `release-tags-pypi`.

**The runs on the raw split (S14, raw), recorded and not judged.** The root's `validate` passed
at the split (run 40361977131). The code leg's push run at `d9e7d568…` (40361975522) failed in
all four jobs, because the leg held no `LICENSE` and no `.gitattributes` yet; row 1 adds both.

**The follow-ups (S10 to S12),** in the order applied. Every commit's author and committer are
Brett Heap with N1's address; every patch applied with `git apply --check` and
`git am --3way`, exit 0, no hunk rejected; and each patch-id equals its file's.

| Row | Leg | Follow-up | Commit | Through |
| --- | --- | --- | --- | --- |
| 1 | code | T006 `code-0001`: the leg's own front door, and the LF rule, ignore list and licence it lost when the root kept them | `6314fc8d1cd852b3027c54e172300392b6e3eaeb` | opensoft/openRepoTools-code#1 |
| 2 | code | T010: the regenerated `tests.yml` patch, naming the leg's standalone checkout context | `bc7c8318f2bd01bad49bd2d1a9b508a7f458f98b` | opensoft/openRepoTools-code#1 |
| 3 | code | T009: the assembly root's two length caps, 354 and 529 | `6c53c9ec14f02e81bcdd4a50ecceb7c4cadf66a0` | opensoft/openRepoTools-code#1 |
| 3a | code | opensoft/openRepoTools#304's fix, the tenth follow-up: the row-stamp cases no longer answer by the length of `run.sh`'s `TMPDIR` | `1e6f2d1327712d0c17d51b897cb067781aa5685a` | opensoft/openRepoTools-code#1, merged as `2a057a6d…` |
| 4 | spec | T006 `spec-0001`: the leg's own front door, the LF rule and licence, and the claude-current manual's three test paths re-pointed at the code leg | `be77322ec7ef40cea580e6f046b255b0f8428571` | opensoft/openRepoTools-spec#1, merged as `3c381621…` |
| 5 | root | T006 `assembly-0001`, re-cut: the front door resolves the split's collisions | `ebe3940cee50ec00bd6df422db35825f55f77f5f` | `adopt/three-repo-shape`, opensoft/openRepoTools#344 |
| 6 | root | T010: the exact-pin integration job, `composed.yml` | `ce703eda6146bacdccce98a06b9a1457fcad2a4c` | the same |
| 6a | root | zsh installed in `composed.yml`, on Brett Heap's ruling on Ask 5 | `9e5cca256c66739fe6500f5be1671fa8d7965033` | the same |
| 7 | root | T007 v3: the assembly's `openRepoTools` front door | `96130bef8405ae3995adc768cd963ed4dc570c4a` | the same |
| 8 | root | the spec bump, to `3c381621…`, by `bump-leg.py` | `78f9783e0b6991f00707478a84f729314e3ce0d0` | the same |
| 9 | root | the code bump, to `2a057a6d…`, by `bump-leg.py` | `a057b23832326451ac998ffd6315d081d4b0d50f` | the same |
| 10 | root | the three `shape/*.md` copies deleted, and `CLAUDE.md`'s line that points to `AGENTS-shape.md` | `fe7841b26891cada6858a1da04cfab3b1b53d3e3` | the same |

- **The legs' merges** are merge commits of S7's tip and the reviewed head, with the head's
  tree: the code leg's `196299f14b2c2f5904813afdaf52c1aa103ef38d` and the spec leg's
  `b576f694fc158af2494567367a04d73a9baf7f3a`, the two trees S10 requires. Their author is Brett
  Heap with N1's address, and their committer is GitHub. No merge setting was changed.
- **The bumps** each changed only its leg's gitlink and pin file, and each carries
  `Cutover: opensoft/openRepoTools#186`. The spec pin's tree digest is
  `bc2d1813f9c60bc6119d2f724a947276527b46b4bf4c8c8b4068cc6a8eac8089`, rehearsal D's, and the
  code pin's is `31123e2d08efe36583e72956ed8b8dbbe4b968c75402b59af5b8952be83ef6d5`.
- **Row 10's** name-status is `M CLAUDE.md` and `D` for the three copies, and `CLAUDE.md`'s
  diff is its one added first line.

**The code leg's runs (S14).**
- `code-pr`, at `1e6f2d1…`, run 40380065869 (18:42:09Z to 19:49:59Z): `guard-launch-mode`,
  `tests`, `tests-no-submodule` and `package` each `success`, and `SonarCloud Code Analysis`
  passed. `tests-no-submodule` skipped 977: 884 for its own no-submodule reason, 60 for no
  zsh, 18 for a file of another root, 9 for composed acceptance, 5 for the built wheel and 1
  for the byte count. `tests` skipped 93.
- `code-main`, at `2a057a6d…`, push run 40390350509 (19:50:42Z to 21:04:13Z): the same four
  jobs `success`, with the same skips.

**The root at S12's head, `fe7841b2…`.**
- `root-check.sh s12`, on a local recursive clone (21:07:13Z) and on GitHub's branch
  (21:09:00Z): `bootstrap ok`; `make validate` with `manifest ok` and `pins ok`; the
  `composed` workflow's context step exit 0; no tracked file changed after validate; the legs
  at S11's targets and the nested openRepoShape at `190133d`; `AGENTS.md` 354 lines and
  `README.md` 529. The doctor gave `COMPLIANT (exit 0)`, with one `note`: the spec leg has no
  `.gitignore`.
- Lane 2 repeated these checks read-only, on its own recursive clone of GitHub's branch, and
  every one passed at `fe7841b2…`; row 10 is its reviewed diff and message byte for byte
  (comment 6427616680).

## 4. S14 at S12's head

At `fe7841b26891cada6858a1da04cfab3b1b53d3e3`, opensoft/openRepoTools#344's `validate` run,
40401761883, passed. Its `composed` workflow's `exact-pins` job, run 40401761978, passed too
(21:08:08Z to 22:11:59Z): 3003 passed and 6 skipped, in 1:02:10. The 6 skips are the most
that Brett Heap's ruling on Ask 5 leaves, and they are its cases: the five wheel cases of
`tests/test_package.py`, with `OPENREPOTOOLS_TEST_WHEEL` unset, and the byte-count case of
`tests/test_precision_90a_96.py`. S14 `root-head` read both runs green at 22:13:08Z. This is
T015's first look at S12's head: item 1 reads the runs again at the exact head, after this
batch's bump push. SonarCloud's check on opensoft/openRepoTools#344 is red, and no step
reads it (comment 6427399285).

## 5. The landed code and evidence

| Repository | Ref | Commit | Tree | State at S12's head |
| --- | --- | --- | --- | --- |
| opensoft/openRepoTools-spec | `main` | `3c3816210b912759b350f77c2128e39f628edc39` | `b576f694fc158af2494567367a04d73a9baf7f3a` | opensoft/openRepoTools-spec#1 merged; this batch lands on it |
| opensoft/openRepoTools-code | `main` | `2a057a6d84e58c8acb4bda20d2b7b667b552c3fe` | `196299f14b2c2f5904813afdaf52c1aa103ef38d` | opensoft/openRepoTools-code#1 merged |
| opensoft/openRepoTools | `adopt/three-repo-shape` | `fe7841b26891cada6858a1da04cfab3b1b53d3e3` | `9245133cfa86aefda40973da1b523d734f0e8552` | opensoft/openRepoTools#344, open on `main`: the split and its seven commits, rows 5 to 10 |
| opensoft/openRepoTools | `main` | `33ce26dffc5286e68f1d29ab5c6458edef0330c0` | | unchanged, under the landing hold until opensoft/openRepoTools#344 merges or is abandoned |
| opensoft/openRepoShape, at `code/upstream/openRepoShape` | gitlink | `190133d5e704e380c430f87ca9c4e31746de0012` (v1.1.0) | `dd0f94e86d8c97bc756335ffb68d3a5fd38c3919` | the pinned standard |

The rehearsal evidence behind them is the Gate B status walk at rehearsal D,
`gate-b-status-2026-10-22.md`, and D's record, `adopter-rehearsal-2026-10-21.md`, both on the
evidence branch `004-migrate-to-triad-rehearsal-d` of opensoft/openRepoTools (at `b0eb112` and
`94f8a3c`), until a spec-only continuation (T017) carries them into this leg.

## 6. Every amendment disposition

Each plan-text amendment that Brett Heap ruled, and where it went. Line numbers are
`33ce26d`'s.

| Amendment | His ruling | Disposition |
| --- | --- | --- |
| Ask 7(a): the capability spec's installation requirement puts its SHALL first | round 3, 2026-10-22T13:05:08Z | this batch: `openspec/changes/migrate-to-triad/specs/repository-triad-migration/spec.md:45–48` |
| Ask 7(b): "No account move is part of this migration" | round 3 | approved as written: no edit |
| Ask 7(c): the code leg's own `.gitattributes`, added after extraction, and each leg's front-door files | round 2, 2026-10-22T13:01:48Z, "Full amendment (Recommended)" | this batch: `plan.md` § Target placement |
| Ask 7(d): a Linux-only CI, in lane 3's wording | round 3 | this batch: `tasks.md:78–79`, `openspec/changes/migrate-to-triad/design.md:121–126`, `plan.md:127–129` and `:251` |
| Ask 6: the hosted-runner checks move to T015 | round 4, 2026-10-22T13:07:16Z, option 1 | this batch: `tasks.md` T010 |
| Ask 13: the post-cutover one-liner needs api.github.com and `jq` | round 4, "Accept and document it" | not in this batch: a note beside the one-liner in the root's `README.md`, after the root-split merge (round 20) |
| Card 16, option A: feature 005 and opensoft/openRepoTools#97 | round 7, 2026-10-22T14:53:08Z | this batch: `plan.md:89–90` named as overridden |
| Round 20 (1): Gate D's first clause met by receipts; the translations follow the root-split merge | round 20, 2026-10-23T12:24:37Z, "Met by receipts; translate after (Recommended)" | this batch: `plan.md:186–188` and `tasks.md:165–167` named as overridden |
| Round 20 (2): the ruled texts and the dated notes land inside the final batch | round 20, "Inside the final batch (Recommended)" | this batch, with the dated notes that the walk at rehearsal D lists in its section 5, items 1 to 6 |

## 7. Archived approval provenance

- **Gate C:** the sentence of 502 words (sha256
  `5883427591c68d43bc9bc60d0dcad47e9252ada84d0a928e14e8f90cd10ea785`), approved by Brett Heap
  in lane 1's session on 2026-10-23 at 11:01:55Z (round 16), relayed in
  opensoft/openRepoTools#186 comment 6363430219, and quoted byte for byte in S1's post
  (6417706286). It names the source, the standard, FP's sha256, the two public legs, the
  follow-ups in their order, the work dispositions in the refreshed table from G8's capture
  (`dispositions.tsv` v2.1, 181 rows, sha256
  `6d213139882a21e6b48381c8ace5f176443f84f4c1ed3abf4772cbba7d361ccf`), the owner's five acts
  and the landing hold.
- **`main` at `33ce26d`:** his "go with option 1", in lane 3's session on 2026-10-26 (comment
  6414151196): `main` reset to `33ce26d` as a one-time exception (section 8, departure 1).
- **The start:** round 23, 2026-10-26T13:44:57Z, "Start now (Recommended)".
- **The second run folder:** round 24, answered by 2026-10-26T14:29:00Z, "Fix S-1 before the go
  word (Recommended)", which was his word for a new run folder from S2.
- **The go word:** round 25, "Go: start S7 now" at 16:21:50Z, then "go de8fda1a" typed at
  16:41:38Z, naming the runbook scripts' `SHA256SUMS` digest `de8fda1a…`.
- **Acts 1 and 4:** his words in lane 1's session at 18:39:51Z, "act 1 done: SonarCloud bound to
  openRepoTools-code" and "act 4 GitHub done", and at 18:48:22Z, "act 4 PyPI done: both
  publishers exist".
- **The legs' merges:** his standing word "merge all when green for all merges thru v1.1.0"
  (opensoft/openRepoTools#228 comment 6303614082), which the Gate C sentence names.
- **N1's identity and the extracted history:** round 20, "History keeps its own
  (Recommended)". N1 covers only the commits lane 1 writes, the follow-ups, row 10 and the
  bumps; the extracted history keeps each source commit's own author and committer (GitHub is
  the committer of 131 of the 156 commits at `33ce26d`).

## 8. Dated departures

1. **2026-10-26, before S1: `main` reset.** `main` had moved past `33ce26d` to `ee07c0e`. On
   Brett Heap's word, lane 3 pushed it back to `33ce26d` with a lease, a one-time exception to
   the no-force-push rule, and opensoft/openRepoTools#341 re-lands in v1.2.0 (comment
   6414151196). The push, at 11:05:25Z, is the week's one org bypass suite (4723125513). It came
   before S7 began, so S7b records it and does not count it.
2. **2026-10-26, a second run folder, on the ruling of 14:29Z.** On his round 24 word, the
   cutover ran from S2, from 16:12Z, in a new run folder, `cutover-20261026-r6`, on the
   runbook as placed at R6 (scripts' `SHA256SUMS`
   `de8fda1ae1b3593b62240d16c71e6a2c08018cf26efb32945947c6f2e864ae2d`), which fixes lane 4's
   S-1 in `s15-merge.sh`. It reuses S1's post, which had named R5's scripts (`c30c515c…`). The
   first folder stays as the record of S1 to S5 there.
3. **S7b: `pass`, not `bypass`.** GitHub recorded each leg's creation push, and ruleset
   18834180's `pull_request` rule, as `pass`. The runbook predicted neither form, and S7b
   accepts either.
4. **S14, raw: the code leg's first push run red,** for the two causes the runbook expected; row
   1 removes both.
5. **S11: a new code pin digest.** The code pin's tree digest is new against rehearsal D's,
   because the code leg holds row 3a. Rows 3a, 6a and 10 were not rehearsed in D.
6. **S8 and S12: the spec leg has no `.gitignore`.** No row adds one, and the doctor's note stays
   for the pass after the cutover. A note does not move the verdict.

## 9. Deferred issues, and what comes after this record

**Deferred, as the records stand at S12's head:**
- **opensoft/openRepoTools#304** was open on GitHub when this record was written. Its fix is
  the code leg's `1e6f2d1`, row 3a, and `exact-pins` becomes a required check only
  after the code-leg bump that carries it (act 3).
- **Feature 005 and opensoft/openRepoTools#97** (card 16, option A): 005's bookkeeping closes as
  a spec-only change after the cutover, and the 8 open tasks of opensoft/openRepoTools#97 wait
  for a later numbered feature.
- **T016's and T017's translations** follow the root-split merge (round 20 (1)). T017 also
  carries the walk at rehearsal D and D's record into this leg.
- **Ask 13's note** goes beside the one-liner in the root's `README.md`, after the root-split
  merge (round 20 (2)).
- **T3's second case,** a lockstep-consistent pin at code from before
  opensoft/openRepoTools#191, stays a named T015 item (the walk at rehearsal D, line 25).
- **opensoft/openRepoTools#341** re-lands in v1.2.0.
- **The outside-consumer texts W6, S1 and B4** are not approved: round 23 asked again, and his
  answer was "Not yet — ask me later".
- **The spec leg's `.gitignore`** (section 8, departure 6).

**Not yet done at S12's head, and not recorded here:**
- Brett Heap's act 2 (each leg's `main` protected) and act 3 (`exact-pins` required on the
  root's `main`), in their windows at S12; act 5, v1.1.0 to PyPI, at S16.
- The delivery of the protocol and commands to Eagle through workBenches, from the
  workBenches owner, which the Gate C sentence names, against T006's distribution check and
  SC-006.
- T015's items 1 to 6, at the real exact assembled head.
- This batch's PR and merge, and the close of its reconciliation issue in
  `opensoft/openRepoTools-spec`, which Brett Heap opens.
- S15's approved bump push of the spec leg's new `main` into the root, and the root-split
  merge, on his own explicit word, with a reviewed squash subject and body.
- Gate D.
