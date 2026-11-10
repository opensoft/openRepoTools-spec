# Contracts: feature 007

This directory holds the contract files that plan.md lists. Each is written
by the lane that owns it, and lands before the code that rests on it.

## `register-cli.md`: lane 1's contract draft 4.14, keyed to Y

[register-cli.md](register-cli.md) is lane 1's contract draft 4.14, keyed to
Y (`a6497d00`), copied here byte for byte by lane 3 at round B12b, on
2026-11-10. It replaces draft 4.13, which lane 3 copied in at round B11;
draft 4.12 came in at round B10, part 1, draft 4.11 at round B9, part 1c,
draft 4.10 at round B6, draft 4.7 at round B5, draft 4.6 at round B4, draft
4.5 at round B3, draft 4.4 at fix round B2, draft 4.2 at fix round B, and
draft 4.1 at assembly phase A.

- **Source:** lane 1's contract draft 4.14, `contracts-register-cli.v4.14.md`,
  posted on #165 at 6769472222.
- **sha256:**
  `34f343cde4e17715f288a52366debac8a7d5c18b9ebd5e4addc600f9252160cb`, 964
  lines. The copy here has the same digest. Drafts 4.3 to 4.11 keep every
  line number of draft 4.2; draft 4.12 keeps them to :637 and moves each
  later line down one; draft 4.13 keeps every line number of draft 4.12, and
  draft 4.14 every line number of draft 4.13.
- **What draft 4.14 changes:** it is draft 4.13 (`0a914e14…`, #165
  6766798190) with lane 1's edits E1 to E11 (#165 6769472222), on lane 3's
  #165 6768006915 (items 3 to 5), lane 4's Q4 and draft 4.13's three open
  points. Every edit sits inside an existing line: D18's and D19's spellings
  follow `rule6_aliases`, the shipped `repos.tsv` and then the per-wip one,
  the last row winning (:501, with :494 and :915); a shipped table that
  exists but cannot be read is 2, with nothing sent (:501, :543); the
  lookup's exit row gives both stderr lines, as T114's code prints them
  (:616, with §2.7's 3 row at :335); `journal --last --at -1` prints `n=-1`
  with no `seq=` line and exits 0 (:798); §2.7's 75 row names an LR004 whose
  one re-read finds the head moved (:337), marked [P], which lane 3 has
  since ruled (#165 6771318480) and draft 4.15 unmarks; §3.3's quote of
  ERR-48 gains its `rename` clause (:501); and the status and basis lines
  (:5, :12).
- **What draft 4.13 changes:** it is draft 4.12 (`7211b9f1…`, #165
  6755305741) with lane 3's rulings for it, as round B10's part 2 lists
  them (#165 6756017599): on draft 4.12's four open points (#165
  6755449007), on T112 (6755386771), on R02's resend (6755615338) and on
  T114 (6755922262), and NOTE-1 (6755592026). Every edit sits inside an
  existing line: `render --checkout` is 64 before T321, and "git half not
  installed" is the test stub's answer (:804, with T321's scope at :108,
  :174 and :828); after LR004, one re-read and no resend (§2.7's rows at
  :335–336, §3.3 at :537 and :548–560, and :929); §3.3's Group B bullet
  takes ERR-48's step 6 text, and its malformed-directory refusals (:501,
  :505, :510); the lookup's four statements, its retried ends, its `psql`
  check and its cap (:236–237, :590, :593, :617–618); `journal --since`'s
  and `--last`'s additions (:790, :798); NOTE-1 (:800); and the status and
  basis lines (:5, :12).
- **What draft 4.12 changes:** it is draft 4.11 (`60b397f9…`, #165
  6754645319) with lane 3's rulings on T424 (#165 6754806910, which accepts
  lane 1's two [P]s of 6754605207 with one addition) and on its review of
  T301 (#165 6754819099), nine edits: §3.5 gains the caller's sub-bullet at
  :638, the one added line; §2.4's `mode` row also names `register
  classify`, with T424's `timeout -k 1 2` and 124 or 137 as 69 (:253);
  T301's choice texts go into §3.6 (:800–802); `render --checkout` before
  T321 is 64 (:804); and the status line changes (:5). Lane 3's answers to
  its four open points (#165 6755449007) go into draft 4.13.
- **What draft 4.11 changes:** it is draft 4.10 (`e2b51782…`, #165
  6748303517) with four fixes from lane 1's U5 re-check of T511's draft 4
  (#165 6752336451), which lane 3 accepted (6752360829): a cached set member
  gives a `file=` row for every path it holds at `n`, its text cached or not
  (S-a, :703); `.paths` never holds `lanes/STORE` (:680); form 3 exits 69
  only for a key the call was asked for (N-g, :673; research.md's DEC-30);
  and a direct read the client chooses writes no `.direct` (:688). It also
  refuses a `psql` older than 15 with 69, naming its version, in place of
  Y:1417's fallback (:234; lane 3, #165 6752697506; research.md's ERR-44).
  Its status and basis lines change too (:5, :12). Every other line is
  draft 4.10's, byte for byte.
- **What drafts 4.8 to 4.10 change:** draft 4.10 is draft 4.7 with three
  steps. Draft 4.8 (`1ded8739…`) narrows §2.1's resolver to the mode and SQL
  paths, `index_nudge` keeping `PATH` alone (:195; lane 3, #165 6747281902).
  Draft 4.9 (`903f1da8…`) drops every inherited `PG` variable on register
  paths, the admin path keeping only `PGSERVICE` (§2.2, :214; lane 3, #165
  6747611042; research.md's ERR-39). Draft 4.10 gives §3.5 four refused texts,
  the fourth a `LANDING` take whose lane cannot be read, and five fields,
  `<slot_role>` as lane 4's (a) has it (:629, :632, :636, :638). Its status
  and basis lines change too (:5, :12). Its §3.1 to §3.4 are draft 4.7's, byte
  for byte.
- **What draft 4.7 changes** (`429f74ef…`, #165 6747384945): it is draft 4.6 with lane 3's ruling that a
  reader's request may name member sets and the render set's listing (#165
  6747048305; :665, as research.md's ERR-31 records), and three fixes from
  lane 1's U5 review of T511's draft 2: `.head` has a `file=` row for every
  path read at `n`, present or not (:699, :703); form 3 counts a path created
  or removed under a cached set member (:730, :732); and a `schema` refusal
  names the client's `REGISTER_SCHEMA_VERSION`, the database's `readers_from`
  and `version`, and Y:2338's remedy (:767). Its status and basis lines change
  too (:5, :12). Its §3.1 to §3.5 are draft 4.6's, byte for byte.
- **What draft 4.6 changes** (`028937e6…`, #165 6746620707): it is draft 4.5 with lane 1's answers to T511's
  open points for lane 1 (T511's §13, #165 6745670828), inside existing lines
  of §3.6 (:653, :665, :673, :730, :764 and :767): `register read` reads the
  switch and the readers' schema window, with the 69's cause line fixed
  (OP-4); names its members to the service (OP-5); has the service resolve a
  log's case (OP-6); finds form 3's changes by digests and asks the service
  first (OP-7); and prints the fallback line whenever it falls back (OP-11).
  Its status and basis lines change too (:5, :12). Its §3.1 to §3.5 are
  byte-identical to draft 4.5's, so lane 4's confirmation holds (lane 3, #165
  6746783992).
- **What draft 4.5 changes** (`da5a23ce…`, #165 6745394391): it is draft 4.4
  with lane 3's call on the endpoint's name (#165 6745266800): §2.5's
  `register_url=` row (:273) and §9's Hosting row (:957) keep `<endpoint>`,
  and say only that Brett makes its one A record by hand once the install
  reports the load balancer's static IP; and its status and history lines (:5,
  :12).
- **What draft 4.4 changes** (`8600078b…`, #165 6742751889): it is draft 4.3
  with lane 4's log set in §3.6's C2 text (:811) and §8's O21 row (:947): each
  `lanes/log/<lane>.md`, and 2 for `lanes/log/README.md`; and its status and
  history lines.
- **What draft 4.3 changes** (#165 6742023901, `2cf9fd54…`): it is draft
  4.2 with lane 5's 6741787745 taken. `reg_flush` also runs
  `lane_alias_flush` (:815); `journal --last --at <n>` requires
  `--generation <g>`, ending 64 without it and 69 for an `n` above the head
  or another generation (:792, :796); and §1 names `--store-line` (R43,
  T509) and `--decide` (R38, T502) as `lanes-edit.sh`'s own flags, outside
  the contract (:182).
- **What draft 4.2 changes** (`dea51e43…`, #165 6741494851): it is draft 4.1
  with O21 closed by lane 4's (a) (#165 6741093519). §8's O21 row reads
  closed; §3.6's "Not settled here" leaves nothing unsettled; the C2 text in
  §3.6 and the header's note on lane 5's review say O21 is closed; and the
  status line, its history and a one-row "Changes from draft 4.1" section
  record it.
- **Draft 4.1, posted:** #165 comment 6739202842, 2026-11-09T11:42Z,
  sha256 `df40136e…`, 954 lines, with lane 1's block v4.1
  (`lane1-tasks-block.v4.1.md`, `53e66ced…`, 644 lines, T101 to T122).
- **The block in force:** lane 1's block v4.2 (`cad0fa93…`, 660 lines, T101
  to T122), #165 6739714248. It follows lane 3's restore order, the ruling
  on test-plan open point 41 (#165 6739605144). Block v4.5 (`4d740972…`,
  660 lines, #165 6745214166) replaces it for 007's tasks, and keeps its
  line numbers: it is block v4.4 (`a4ce9e3b…`, #165 6742356416) with T119's
  check 11 and its tests naming the prod OpenXPKI's root (:386, :388). The
  contract, at draft 4.14 as at 4.1, gives no restore order. Block v4.11
  (`625a0b84…`, 667 lines, #165 6754645319) replaces v4.10 for 007's tasks
  since round B9, part 1c, and keeps its line numbers to :660: it pairs
  with draft 4.11 and takes round B8's changes to lane 1's lines (:142,
  :143, :164, :177, :200, :212), lane 2's F9b in T106's and T111's verifies
  (:146, :252), `PGSERVICE` (:108), `PGAPPNAME` and the `psql` 15 erratum
  (:147, :152), S-a, the `lanes/STORE` filter and N-g (:290, :292) and
  T119's line form (:384), and it adds T123 (:663). Block v4.10
  (`fe74285f…`, 660 lines, #165 6748303517) replaces v4.8 for 007's tasks
  since round B6, and keeps its line numbers: it re-keys 17 verify lines
  to lane 2's F7 map, takes lane 2's regression half, and carries the
  inherited-variable rule (:147, :179), DEC-23 as widened (:186),
  `REGISTER_SLOTS` (:367) and §3.5's texts and fields (:419–:421). Block v4.8
  (`8d3e6ce2…`, 660 lines, #165 6747384945) replaces v4.5 for 007's tasks
  since round B5, and keeps its line numbers. It is block v4.7 (`041c88d3…`,
  #165 6746948770) paired with contract draft 4.7 (:278, :289, :290); v4.7
  is block v4.6 (`823aaaa3…`) with T109's rendering rule (:186), T113's form
  3 and snapshot as draft 4.6 has them (:278, :289), and T119's check 11 and
  its tests back to the install's CA (:386, :388); and v4.6 is v4.5 with
  :212's ERR-03 correction.
- **What draft 4.1 changes:** it is draft 4 (#165 6738871108, `5d6ce7ca…`,
  939 lines) with lane 5's check of draft 4 taken (#165 6739068493). The
  quote of the guard's second read is cited as Y:4160–4161; the change list
  names draft 4's two citation-only edits, in §3.2 and §3.3; and an empty
  `--lane=` ends 64 in form 2 as well as in form 4. Block v4.1 moves form
  4's proof of "too late" to lane 5's T507, in T507's own verify, since
  only T507 has `T0`: lane 1's T113 ships the bounds, `--pid`, the cap at
  `T0` + 3.75 s and D9's guard row, and its case `guard_second_read_at`
  keeps items 1 to 8, with item 9's ends 69, 78, 1 and 64.
- **What it is:** the contract between group A (`lanes-index`, lane 1) and
  group B (the write path, lane 4), FR-013 to FR-017, and in its §3.6 the
  readers' four forms of `register read`. Lane 1 writes it, lane 4 confirms
  group B's sections, lane 5 reviewed §3.6 (#165 6738431117), and lane 3
  rules (R01, R06).
- **Its open points:** none. Its §8 closes O14 to O21. O21 is closed by
  (a), lane 4's choice (#165 6741093519): T401's `register-text` and
  `register-grep` answer `lanes/LANES.md`, `lanes/aliases.tsv` and each
  `lanes/log/<lane>.md`, and 2 for any other path, `lanes/log/README.md`
  included (draft 4.4, :811 and :947).
- **L1-D1's scope** (lane 1's own decision, 6738871108): the `=` form,
  `--lane=<key>` and `--log-key=<key>`, applies to `register read`'s
  `--lane` and `--log-key` only. Group B's `--lane <lane>`, on
  `register snapshot`, `register commit` and `journal --write-id`, keeps the
  form of the phase plan's interfaces table (Y:4834).
- **Its landing:** tasks.md's T101 rides the spec PR, since its output is
  this file, in the spec branch at draft 4.14 (lane 3, #165 6745826156). A
  later draft replaces this copy as a fix before the spec PR leaves draft,
  with its own source and digest here.

## `render-commit.md`: lane 3's contract, written from Y

[render-commit.md](render-commit.md) is lane 3's own contract, tagged `[L3]`
in plan.md. Lane 3 wrote it at assembly phase B on 2026-11-09, from Y's
`design.md` and `operations.md`, with `Y:` and `ops:` citations. Its
sections 1 to 4 add nothing Y does not say.

- **What it is:** D15's render commit (its author, committer, dates, build
  and message), `Register-Seq` (its value, its trailer, which commits carry
  it, and who reads it), and the one push function that the renderer,
  `cutover` and `rollback` share (FR-044, FR-045).
- **Its last section** lists what Y leaves open or unprobed, which stays
  open for the build. It also records lane 3's PC-3 values (64 seqs a pass,
  16 passes, and the gap accepted; research.md's PC-3) and reads Y's two
  conflicts by D15, which governs both (lane 3, fix round B, 2026-11-09). T345
  marks both in the change's `design.md` before the archive.
- **Its landing:** tasks.md's T344 rides the spec PR, since its whole
  output is this file, already in the spec branch. Group C (T320, T322,
  T326 and T327) codes against it.

## The other contracts

Each owner writes its file at this directory's path. Of these, lane 3's
`render-commit.md` is in since phase B, and lane 1's `exit-codes.md` and
`config.md` since round B4, from lane 1's commits `31196cd9` and `eca2da3d`;
tasks.md's T102 rides the spec PR (lane 3, #165 6745826156):

| File | Owner | Task |
| --- | --- | --- |
| `exit-codes.md` | lane 1, for the `lanes-index` rows; in at round B4 | T102 |
| `config.md` | lane 1; in at round B4 | T102 |
| `pending-record.md` | lane 4 (R34) | T412 |
| `bind-record.md` | lane 5 (R50) | T501 |
| `socket-api.md` | lane 5 (R50) | T511 |
| `render-commit.md` | lane 3; in at phase B, from Y (above) | T344 |
