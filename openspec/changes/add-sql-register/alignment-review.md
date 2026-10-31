# Alignment review: `add-sql-register`

The governed command's Phase 3 ("Resolve Alignment Findings"), run on
2026-10-31 by lane openRepoTools-3 for opensoft/openRepoTools#165. It records
what became of each finding of the two alignment seats. It decides nothing that
is Brett Heap's: where a fix needs a decision no text makes, the proposal now
carries an open question with its options.

## Inputs

- **The proposal reviewed:** `proposal.md` at spec commit `5a64a47a`, sha256
  `78c5a8a40163024cd0b006b91067c13dea3f12bdf8595bcc4097ec9f4b6fef60`.
- **The Stack Architect seat,** lane openRepoTools-4, #165 comment 6548171116:
  NOT ALIGNED, 6 MISMATCHES (M1–M6) and 12 DRIFTS (D1–D12). The full report,
  with each fix's wording, is
  `/workspace/projects/.lane-worktrees/openRepoTools-4/.tmp-coordinator/runs-165/SEAT-ARCH-report.md`,
  sha256 `293718ddcb2d3445c639e86561f92e6c6e365237b461664cf86e4653405fe403`.
- **The QA Lead seat,** lane openRepoTools-2, #165 comment 6548063141: 13
  findings, QA-1 to QA-13 (3 MUST, 8 SHOULD, 2 NIT), each labelled a mismatch
  by the seat; none was labelled a GAP. The reviewer's reply is
  `/workspace/projects/.openrepotools-private-prep/work-20261022/lane2-qa-165-1031/QA-reply.md`,
  sha256 `f151240ceebf115719a99496455f31c48bd4f37691b8f7921c755e5019a3779a`.
- **Research used to correct or complete facts:** R1 (lane openRepoTools-2,
  comment 6547987633, `R1-test-strategy.md`) and R2 (lane openRepoTools-4,
  comment 6548160647, `R2A`–`R2D` with the checks `R2V1` and `R2V2`, whose
  corrected citations are the ones used). R3 (lane openRepoTools-1) was not in
  when this pass was made; the schema and the import and rollback questions (Q3,
  Q4) stay open for it.
- **Brett Heap's ruling** of 2026-10-31T19:32:08Z, verbatim "1A and 2A" (#165
  comment 6548141614): PostgreSQL 16 in the bench images, and the real-engine
  tests required in CI, refused rather than skipped, skipped loudly elsewhere,
  the fake-`psql` tests always run. It answers Q1.
- **Texts checked:** Amendments 14, 22 and 23
  (`~/projects/new-workstation/home/.agents/protocols/`), #165's body and
  comments (lane 4's copies under `runs-165/inputs/`), and the code leg at
  `2a057a6d` in this clone's `code/`. Every code line cited below as the source
  was re-read in `code/` before the fix relied on it.

## Dispositions

"P" is a line of the new `proposal.md`. "APPLIED" means the fix is in as the
seat wrote it, or in equivalent words. "APPLIED IN PART" means the part a text
decides is in, and the rest is the open question named.

| Finding | Disposition | Where in `proposal.md` | Source checked |
|---|---|---|---|
| **M1** The scope sentences ("on no other", "byte for byte") contradict the marker refusal every writer makes | APPLIED: everything but the marker is `mode=primary`'s; every act-2 writer reads `lanes/STORE` in every mode; "Unchanged while `lanes/STORE` is absent" | P113–118, P948–951, P1005–1008 | A22:16, A22:91–95 ("Every act-2 writer reads it"); `code/lanes-edit.sh` has 0 hits for `lanes-index.conf`, `LANES_INDEX_CONFIG` or `psql` |
| **M2** The guard's fallback "as today" reads the mirror, which S3 forbids for the guard | APPLIED IN PART: on `mode=primary` the fallback reads the database, never the mirror, and refuses 2 where it cannot. Whether it holds the READER credential itself or refuses 2 whenever the service is down is OPEN QUESTION **Q11** (R2's B4) | P457–466, P835–844 | A22:126–133; `code/lanes-edit.sh:10498–10502` (`register_text` prefers `origin/<branch>`, then the file); #165 body:44 |
| **M3** The thin client's inputs and side effects are misdescribed | APPLIED: the client sends `session_id`, `cwd`, `agent_id`, `prompt`, the window reference and name, the live record's `name`, `nameSource` and liveness; the offer file, `/rename` and answer 2's two writes stay client-side, as transactions; a stdlib-Python socket client. Its form (a block in `lanes-edit.sh guard` or a new program) is **Q2(g)**, as the seat wrote | P433–455, P683–689 | `code/lanes-edit.sh:10399–10412`, `:10432–10433`, `:10455`, `:10483–10485`, `:11169–11176`, `:11196–11200`; `code/openRepoTools:2343`; no `nc -U` or `socat` in the code leg's files |
| **M4** `commit` is also #194's publish act, which Amendment 23 keeps | APPLIED: two halves; the hand edit refuses; the publish act stays with its fence narrowed to `handoffs/`; the captures, `add-row`'s compensating commit and the branch-wide push refuse; three tests. The refusal's code joins **Q9** | P165–179, P212–213, P948–950 | `code/lanes-edit.sh:2174–2200` (the fence admits `lanes/`, `lanes/log/`, the aliases and `handoffs/`), `:2330–2352`, `:2382–2434`, `:2701–2721`, `:3906–3913`, `:15485–15515`; A22:120–122; A23:267–270, A23:343–344 |
| **M5** Q1 says CI has "no Postgres" | APPLIED: no `services:` and no running database; the `ubuntu-24.04` image ships the PostgreSQL 16.15 server binaries with the service disabled; benches and `macos-latest` have none. Q1 is now answered (Brett's 2A) | P629–660 | `code/.github/workflows/tests.yml:94`, `:114`, `:174`, `:219`; no `initdb`, `pg_ctl` or `services:` in `code/tests` or `code/.github`; R1 section 1 for the runner image (not re-checked: network) |
| **M6** Impact misses `lane-worktrees`, the skills, the skill's commit block and `wip init` | APPLIED IN PART: all four surfaces are in Impact. The seat's "must not run under `lanes/STORE`" for `wip init` is a policy choice R2 puts to Brett (B8), so the proposal says it would be a stray commit and leaves the choice to OPEN QUESTION **Q14** | P958–966, P864–874 | `code/lane-worktrees:704–708`, `:972–976`, `:995–999`, `:5554–5559`; `code/skills/handoff/SKILL.md:97–118`, `:424–457` (`pull --rebase` at `:449`); `code/skills/restart/SKILL.md:48–65`; `code/openRepoTools:5024–5081` |
| **D1** The configuration leaves out who reads `mode=`, its default and the third credential | APPLIED: `mode=` defaults to `index`; a REGISTER-WRITER pair (for example `register_url=`, `register_passfile=`); `lanes-index` stays the one parser; an older `lanes-index` refuses `mode=` with 2, so act 3 installs first | P122–138, P384–386, P952–954 | `code/lanes-index:278–292`, `:1270–1300`; `code/lanes-edit.sh:2554–2568`; `docs/README-lanes.md:3752` |
| **D2** The service's held connection against the `psql`-only rule | APPLIED: a long-lived `psql` child on pipes unless the design names a driver for Brett's word; R1's 14.7 ms and 0.97 ms added to Q3 and Q7 | P416–421, P711–713, P759–762 | `code/lanes-index:1272–1276`; `code/pyproject.toml:44–52`; R1 section 3 |
| **D3** The `read: mirror` line is not tied to the existing `read:` lines | APPLIED: on stderr, the third of the family; on `mode=primary` `--index` prints no `read: index` line | P273–276, P281–282 | `code/lanes-edit.sh:2569–2573`; `docs/README-lanes.md:3726–3728` |
| **D4** The managed seam: the verbatim block and the absent writer | APPLIED: the writer is not in the code leg; it comes with T024; this change provides and fake-tests the storage-layer entry; the database answer wraps `lane_is_managed_owned` outside the verbatim block | P307–320 | `code/lanes-edit.sh:13176–13190`, `:13310–13313`, `:13324` |
| **D5** Packaging and CI pins a new installable would break | APPLIED: `pyproject.toml`'s `force-include`, the count lines in `tests.yml` and `release.yml`, and `test_tests_workflow.py` | P970–976 | `code/pyproject.toml:68–78`; `code/.github/workflows/tests.yml:522–526`; `code/.github/workflows/release.yml:1426–1429`, `:1443–1510`; `code/tests/test_tests_workflow.py:155–157`, `:834–842`, `:1018–1020` |
| **D6** Hook-string identity and the workBenches launcher are missing from Impact | APPLIED under "Outside the repositories", and in Q2(g) | P994–1004, P683–689 | `code/openRepoTools:2304–2311`, `:2340–2344`, `:2360–2366`, `:3317–3331` |
| **D7** S6's HOLD and LANDING report and its database clock are new behaviour | APPLIED: no writer reads another lane's hold today; `HOLD` is free `append-line` text; the report needs a parser or a verb; the database clock stamps the row and a caller's text UTC stays. What the later writer sees, and parser or verb, are OPEN QUESTION **Q17** | P362–375, P891–897 | `code/lanes-edit.sh:6422–6424`, the `append-line` arm `:15326–15383`, `grep -w HOLD` (5 hits, all comments); `code/lanes-index:679–684`; `code/skills/handoff/SKILL.md:743`; `code/lane-handoff:2889–2891` |
| **D8** Q8 asks only about `lane-start`; `lane-handoff` and the skill have the same conflict | APPLIED: Q8 retitled and split into (a) `lane-start` (R2's B2) and (b) `lane-handoff` and the skill (R2's B3), each with its options | P774–805 | `code/lane-handoff:1460–1464`, `:1649`, `:1725–1728`, `:1815`, `:2638–2640`, `:2874–2881`; `code/skills/handoff/SKILL.md:97–118`, `:147`, `:239`, `:276`; A23:231–236 |
| **D9** The renderer's home is stated as fact while Q5 asks it | APPLIED: a candidate home (Q5); the post-write nudge must not re-index the mirror (Q3) | P422–426, P707–710 | A22:105–110; A23:339–342; `code/lanes-edit.sh:1260–1266`, `:2554–2568` |
| **D10** "Starts on demand" is stated as decided; platform constraints unnamed | APPLIED: #165's body's words kept; how it starts on Linux and macOS and across containers is **Q2(c)–(d)**; bash 3.2 rules and stdlib Python 3.9+ | P427–432, P674–681 | #165 body:41; no `systemd`, `launchd`, `setsid`, `nohup` or `daemon` in `code/openRepoTools`; `code/.github/workflows/release.yml:1258–1263`; `code/.github/workflows/tests.yml:7–10`; `code/tests/test_repo_hygiene.py:4213–4216`; `code/pyproject.toml:45` |
| **D11** `commit_publish` is attributed to Amendment 23's S4 | APPLIED: "S4: the publish act"; `commit_publish` is the drafts' name and the hygiene suite's anticipation | P544–546, P734–736 | `grep -c commit_publish` on Amendment 23 is 0; `code/tests/test_repo_hygiene.py:2767–2771`, `:3320–3324` |
| **D12** Q9 leaves out exit codes 7 and 9 | APPLIED: `claim` no longer spends 7 under S6; 9's re-check reads local records. Brett's confirmation of 2 against 7 is OPEN QUESTION **Q12** (R2's B5) | P806–821, P845–853 | `code/lanes-edit.sh:434–530` (7 at `:505–506`, 9 at `:523–529`) |
| **QA-1** (MUST) A lost SERIALIZABLE claim exits 69, not 2; the later `HOLD` writer's result is undefined | APPLIED IN PART: a 40001 or 40P01 is not a refusal; the transaction re-runs its reads, so the loser exits 2; 69 stays for unreachable, other refusals and a spent bound; S6's tests run on a real engine. OPEN QUESTIONS **Q15** (the bound, or a lock on the object instead) and **Q17** (the later writer's exit and line) | P185–191, P351–354, P376–378, P875–897 | A22:83–86, A22:166–172; R1 section 3 (40001; the loser's `psql` exited 3) |
| **QA-2** (MUST) The stray-commit rule stops the renderer on legitimate commits | APPLIED IN PART: the rule covers commits to the paths the renderer renders; Amendment 22's sentence on commits outside them is restored and extended to `lanes/STORE` and `lanes/repos.tsv`; the test added. Of the seat's two fixes this takes the first, because S2's render list ("touches nothing else") already bounds what a render can collide with. Where `repos.tsv` lives on primary is OPEN QUESTION **Q18** | P237–249, P254–257, P898–906 | A22:108–119; `docs/README-lanes.md:72–76`, `:683–687`; `code/lanes-index:475` |
| **QA-3** (MUST) The service's "parsed register" against S3's fail-closed reads | APPLIED: every state-deciding answer is read fresh or proven current by journal sequence per request; with the database down the service refuses; two tests | P411–415, P482–484 | A22:126–129; #165 body:41 |
| **QA-4** A hung service makes the guard fail open | APPLIED: "unavailable" includes a service that accepts and never answers; the client's wait is bounded well under the 5 s hook timeout, leaving the fallback room; the test. The bound's value is **Q2(g)** | P467–472, P480–482 | `code/lanes-edit.sh:9436–9441` ("a killed hook does not exit 2"); `code/openRepoTools:2344` (`GUARD_TIMEOUT=5`) |
| **QA-5** An in-doubt COMMIT, and "nothing written anywhere" | APPLIED IN PART: the in-doubt case is named as neither success nor "nothing written"; the refusal tests cover a failure partway and assert rows, journal, git and no lock. How it is reported is OPEN QUESTION **Q16** | P192–196, P208–212, P503–504, P884–890 | A22:83–86; A23:243–247; `code/lanes-edit.sh:486–496` (exit 3) |
| **QA-6** The readers' expected exit codes contradict each other | APPLIED: a reader × outage table as the tests' oracle, with `lane-start`, `lane-handoff` and the skill "per Q8" and `managed-projection` at S4's 1 | P265–299 | A22:126–135, A22:143–145; A23:231–236, A23:427–428 |
| **QA-7** The renderer's failure paths are stated but not tested | APPLIED: the four tests | P254–261 | A22:112–119; A14's act-2 "hung indexer" test |
| **QA-8** Agreement tests 1–2 name no writes; "every row carries its mode" has no test | APPLIED: tests 1 and 2 run over each of S1's writes, the projection writer's entry included; each touched row's mode read back | P152–155, P208–209, P254, P501–504 | A22:96–97, A22:205–206; `code/lanes-edit.sh:15101–15106` |
| **QA-9** CI could pass with the agreement tests skipped | APPLIED per Brett's 2A: the real-engine tests (R1's list) are required in CI, which refuses rather than skips them; elsewhere they skip loudly naming `PG_BIN` and `postgresql-16`; the fake-`psql` tests always run | P492–500, P376, P391, P629–660 | root `AGENTS.md:349–355` (`OPENREPOTOOLS_COMPOSED=1` refuses); `code/.github/workflows/tests.yml:545–548`; R1 section 4; #165 comment 6548141614 |
| **QA-10** Neither timing measurement is defined | APPLIED IN PART: "byte-identical" is stdout, stderr less the fallback line, and the exit code; the PR body names host, store, path and statistic, with the cold first call. Where and how is measured, and whether 50 ms is recorded or asserted, is OPEN QUESTION **Q10**, with R1's and the seat's recommendations | P476–478, P512–518, P822–834 | #165 body:51; A22:50–51; R1 sections 3 and 4 |
| **QA-11** S5's success paths are untested | APPLIED: a successful cutover; rollback from a mirror last shown current | P338–344 | A22:149–162 |
| **QA-12** (NIT) Q1's "no Postgres" is wrong; Impact misses three files | APPLIED: Q1 corrected and answered, `listen_addresses=''` named; Impact gains `code/tests/test_tests_workflow.py`, `docs/README-lanes.md:3981` and root `AGENTS.md:341–346` | P629–660, P978–990 | `docs/README-lanes.md:3979–3983`; root `AGENTS.md:340–347`; `code/tests/test_tests_workflow.py:834–842` |
| **QA-13** (NIT) "One credential holder" depends on Q2 | APPLIED: the claim is conditional on Q2, with clause (b)'s holder named; the test covers every environment a session inherits | P403–410, P457–466, P484–486 | A22:187–190; #165 body:41, :50; A14(c) ("never in an environment a session inherits") |

No finding was left unapplied.

## Where the two seats met

- **`commit`.** The QA seat's "no finding" list says `commit` refusing follows
  Amendment 22; the architect's M4 shows that only its hand-edit half does.
  Both are kept: the hand edit refuses, the publish act stays (Amendment 23's
  S4).
- **The CI facts.** M5 and QA-12 are the same correction, taken from R1.
- **The credential.** M2 and QA-13 meet at Q2 and Q11.

## R2's questions for Brett, and where they are now

| R2 | Question | In `proposal.md` |
|---|---|---|
| B1 | The order against Amendment 23's act 2 | Q6 |
| B2 | `lane-start` with the database down | Q8(a) |
| B3 | `R-A11-11` under S1 | Q8(b) |
| B4 | The guard's fallback | Q11 |
| B5 | A claim loser's exit, 2 or 7 | Q12 |
| B6 | Writes while `lanes-register` is down | Q2(b) |
| B7 | `rename-lane`'s order | Q13 |
| B8 | `wip init` and the `LANES.md` link on a primary estate | Q14 |

## Alignment Review Complete

**Stack Architect:** 18 findings (6 mismatches, 12 drifts)
**QA Lead:** 13 findings (13 mismatches by the seat's own label: 3 MUST, 8 SHOULD, 2 NIT; 0 gaps)

**Fixes applied:** 31 (auto-corrected in proposal.md): 25 in full, 6 in part
(M2, M6, QA-1, QA-2, QA-5, QA-10), each with its undecided remainder as an open
question
**Not applied:** 0
**Gaps resolved:** 0 (no finding was raised as a GAP)
**Open questions:** Q1 answered (Brett's 1A and 2A); Q2–Q18 open, of which Q3
and Q4 wait for R3 and Q2(b), Q6, Q8, Q11–Q14 carry R2's B1–B8 for Brett

Proposal is aligned with the docs as far as the texts decide. This pass stops
before the council debate.
