# Research: 007-sql-register

Phase 0 of `/speckit.plan`, keyed to commit Y (`a6497d00`) of the change
`add-sql-register`, which the spec branch merges at `066ad8f1`. The change is
frozen at Y but for the edits below. This file records what the rulings since
the freeze decide for 007, and the errors found in the frozen text.

**The edits to Y's files since Y,** by round. All but the first, round
B13's `design.md` fold, the test-plan edits that add lines in rounds B14,
B15 and B16 and in the follow-up, and the follow-up's fold of T1 are made in
place, so every line of `operations.md`, `clarifications.md` and
`proposal.md`, and of `design.md` to design:489, keeps Y's number:

- **Round B4, the two spec files** (`dabd74bd`, `ce3fad83`, `ad82c2f4` and
  `04a9de6b`; lane 3's rulings B4, #165 6738474549, and (a), 6727022920): the
  SHALLs that DEC-05 and ERR-03 name. They add lines after Y-spec:846 and
  Y-svc:124, which is why `spec:` and `svc:` lines are cited at `ad82c2f4`.
- **Fix round B2** (`991426a`; lane 1's clause, #165 6742453803): ops:141,
  item 2's Secret.
- **Round B3** (`a138b1b`; lane 1's fold, #165 6745214166, on the OpenXPKI
  ruling of 6742844912; lane 3's calls, 6745266800): design:2324, :2326,
  :6305, :6390 and :6393; ops:135, :146, :151, :153, :181, :190, :195 and
  :711; clar:495; proposal:1988.
- **Round B5** (`c5c4b46`; the interim-CA answers, #165 6746095035; lane 1's
  fold, 6746948770; lane 3's calls, 6747048305): design:2324, :6305 and
  :6393; ops:135, :153, :181, :195 and :711; clar:495; proposal:1988.
- **Round B8:** the seam and YS:685 deltas (DEC-27; ERR-34): spec:649, :664,
  :670, :685 and :692, in place, and design:3019; lane 1's install fold (#165
  6751628903, on opsXfactory-6's 6751205159): design:2321 and ops:141, :144,
  :161, :163, :191, :199 and :206; lane 3's calls on it: design:620–621 and
  :6402, ops:197 and the words quoted at ops:191; R2's S1 and S2, the words of
  #165 6743050889 and the root switch's order: design:2324 and :6390, ops:135
  and :151.
- **Round B9, part 1:** Brett's label "Job + storage in prod sub
  (Recommended)" quoted whole, as clar:503 records it (#165 6722593397), at
  design:621 and :6403 and ops:191.
- **Round B9, part 1c:** Brett's labels "Dump both (Recommended)" and
  "Count both (Recommended)" quoted whole, as clar:500 records them (#165
  6722541787), at design:621, :625, :3283, :6403 and :6406 and ops:191.
- **Round B12b:** case 6a (lane 3's ruling, #165 6771318480): design:1464
  and :1482, in place; and Brett's two-file allow-list worded to its letter
  (lane 3, #165 6770820384): spec:169 in place, and one scenario added under
  its requirement, so the sql spec's lines after that scenario sit five
  below their `ad82c2f4` numbers.
- **Round B13:** lane 1's fold-ready diff for `design.md` (#165 6774804435;
  `Y-B12b-all-lane1.diff`, `b67378e9…`): ERR-48's step 6 (c1), the index
  `object_holds_by_line` (idx1) and case 6a (lane 3's ruling, #165
  6771318480), in §1.2's tables and §1.3.1's `commit_write`, so that both
  SQL blocks equal lane 1's migration 1 as built. It adds 46 lines, so
  `design.md`'s lines after design:1192 sit up to 46 below Y's numbers, and
  round B12b's design:1464 and :1482 are now :1467 and :1485. And, in place:
  ops:190, where act 3 waits for the install, not for that change's merge or
  its archive (lane 3, #165 6768052557); ops:466, :470 and :473,
  `set-mode`'s one added ground, and a file that already says the mode (lane
  3's rulings, #165 6769217709, reading 1, and #165 6778959652).
- **Round B14:** lane 2's three test-plan edits in its F13b (#165
  6779583168; `edits-F13b-test-plan.json`, `39f8ca8a…`): AT-3's `wip init`
  oracle at test-plan.md:819 and :827, and the readers' arms at :2901, so
  `test-plan.md` is `bed85d82…`, 9,147 lines; its lines from :820 to :2900
  sit one below Y's numbers, and those from :2903 two below. And, in place,
  FR-005's dependents, narrowed to its words by Brett's "Narrow it
  (Recommended)" (2026-11-10T16:50:07Z, as lane 3 relays it, #165
  6773442813): spec:60, :135 and :144, with :137's note of his answer;
  design:142–146, :156–158 and :249–253, each re-wrapped to its own lines,
  and design:722's row, which keeps his Q14(a) and adds that FR-005 narrows
  it; and proposal:149–152 and :2275–2277, re-wrapped the same way.
- **Round B15:** lane 2's three test-plan edits in its F13c (#165
  6783432592; `edits-F13c-test-plan.json`, `5d7231dc…`), in place: :829's
  "with exit 2", :5313–5314's bare `check 12: ok`, and :8442 in FR-005's
  narrowed words. Then lane 3's texts on the plan, which lane 2 asked lane 3
  to fold: its batch 11's (#165 6783909985) at TP:1405–1408, :1507–1508,
  :3610, :3625–3626, :3641–3642, :4303–4304, :5386–5387 and :5433–5434, its
  batch 12's (#165 6808010497) at TP:3603, :4492–4493 and :5877–5879, and
  its L2-6 to L2-8 (#165 6809045090) at TP:1293, :1319, :1343 and :1358, so
  `test-plan.md` is `acb9416c…`, 9,160 lines, and its lines sit below B14's
  numbers: from B14's :1409, three below; from B14's :3643, five below; from
  B14's :4305, eight below; from B14's :4494, nine below; from B14's :5388,
  twelve below; from B14's :5880, thirteen below. And, in place: design:2363
  (Y:2317), the READER row, which no longer names the `SessionStart` block
  (ERR-09; the T341 draft's item 4); design:3564 (Y:3518), D9's guard row's
  "On expiry" cell, and design:5715 (Y:5669), the close-out gate's sentence
  (lane 3's batch 12, B1 and A3, #165 6808010497); design:5257 and :5264
  (Y:5211, Y:5218), the lane a rerun's text names and a record with no
  `call` line, and design:5759 (Y:5713), the hang row's timer (lane 3's
  batch e, Q106, Q107 and Q90, #165 6808937880); design:3564 (Y:3518) again,
  the guard row's exception, which a rollback's declaration does not take,
  and design:4272 (Y:4226), the cost note's second connect (lane 3's R07,
  round B15, #165 6809568632); ops:129 and :219–220, which name v1.3.0 as
  007's release, v1.2.0 being the way back (Brett's "v1.2.0 now, 007 later
  (Recommended)", 2026-11-10T13:22:23Z); ops:242, :244, :617 and :636, which
  name v1.3.0, and at :636 `release/v1.3`, as the release that carries 007
  or waits for its service (Brett's "v1.2.0 now, 007 later (Recommended)"
  (2026-11-10T13:22:23Z; #165 6768477034)); and ops:247 and :252, act 4's
  steps 1 and 3, which carry the holders' notice (Brett's "Holders release +
  check (Recommended)", #165 6782534165).
- **Round B16:** lane 2's TX13 on the plan (#165 6812272109, taken in
  6812295982; `TX13-TEXTS-lane2-for-spec17-6e534355.md`, `c6c2bf52…`):
  TX13-2, AT-14's render fork, re-wrapped in place at TP:2629–2630, and
  TX13-4, AT-P3's fixture, where B15's :2995 becomes three lines, so
  `test-plan.md` is `523f78b0…`, 9,162 lines, and its lines from B15's :2996
  sit two below B15's numbers.
- **The follow-up, T345's fold of T1:** T1's 116-column line (design:489) is
  folded at a space into design:489 and :490, word for word, and no other line
  of D8's bullet changes. It adds one line, so `design.md`'s lines after
  design:489 sit one below Y's numbers, and those after design:1192 up to 47
  below, and round B8's design:620–621 are now :621–622, round B9's
  design:621 and :625 are now :622 and :626, round B13's design:1467 and :1485
  (B12b's, as that entry says) are now :1468 and :1486, round B14's design:722
  is now :723, and round B15's design:2363, :3564, :5715, :5257 and :5264,
  :5759 and :4272 are now :2364, :3565, :5716, :5258 and :5265, :5760 and :4273.
- **The follow-up, lane 2's test-plan texts:** TX13b-1 and TX13b-6 (#165
  6813121206; `TX13b-TEXTS-lane2-for-spec17-6e534355.md`, `41cbe18c…`) at
  TP:2847–2848, `lane-worktrees`' direct reads, and :6414–6416, AT-F7's age
  at the client's check; and RD14d's two (#165 6814602159;
  `RD14d-TEXTS-lane2-for-followups-6e534355.md`, `75dc2c80…`) at :5568,
  AT-F2's rerun after a files-mode write, and :5578, AT-F2's Tests. So
  `test-plan.md` is `6cb2cdca…`, 9,221 lines, and its lines sit below B17's
  numbers: from B17's :691, four below, by lane 2's HW17 (opensoft/openRepoTools#165 6834015783); from B17's :1521, eleven below, by lane 2's TX15-4 (opensoft/openRepoTools#165 6826709932); from B17's :2849, thirteen below; from B17's :3351, sixteen below, by lane 2's TX18-1d (opensoft/openRepoTools#165 6834871295), AT-D8's G6 sentence; from B17's :3500, seventeen below, by lane 2's TX18-1e, AT-D8's test list; from B17's :4218, thirty-nine below, by lane 2's TX17-9a to -9e (opensoft/openRepoTools#165 6833867473), whose blocks at B17's :4183–4217 move the lines between them by twenty-four to thirty-five; from B17's :5566, forty-three below, by lane 2's TX19-C1 (opensoft/openRepoTools#165 6836739058), CF15-2's line wrapped; from B17's :5569, forty-six below; from
  B17's :5579, forty-eight below; from B17's :6259, fifty-three below, by lane 2's TX17-10j, AT-F6's row (d) (opensoft/openRepoTools#165 6833867473); from B17's :6417, fifty-four below; from B17's :6779, fifty-six below, by AT-F13 (j)'s findings (lane 3, opensoft/openRepoTools#165 6833678789; lane 2, 6834138362); from B17's :6877, fifty-seven below, by lane 2's N2-17 (opensoft/openRepoTools#165 6833722259); from B17's :8814, fifty-nine below, by lane 2's words for open point 8 (opensoft/openRepoTools#165 6834234333). And, in place, in
  the follow-up: design:2771–2772 (Y:2724–2725), T5's line, folded at a space,
  word for word; design:5022 (Y:4975), Q29's clause, which reads as settled;
  design:6001 (Y:5954), F29's `interpret-trailers` row, superseded by D15;
  and design:6078–6079 (Y:6031–6032), rows 5 and 6, applied in D15 (T345,
  R61's items 1 and 2; lane 4, #165 6726436890); design:6478–6479
  (Y:6431–6432) and proposal:2022, Q24's install question, answered as
  `./setup.sh` (Brett's "1a 2b", typed in lane 3's session at about 04:00Z
  on 2026-11-12; #165 6814260122); ops:229, act 3 step 8, whose scratch
  configuration leaves `render_key=` out (lane 3's review of T327, MUST-2);
  ops:247 and :252, act 4's steps 1 and 3, where `cutover`'s open-`HOLD`
  check is for only a run that will make step 1, and a `HOLD` with no
  readable time counts (lane 3, #165 6813195977); and ops:275, where a cutover's
  rerun after a 3 that proves the marker absent pushes again, and step 2's
  own ends follow (lane 3, #165 6770600106, T116's point 1; T327's review,
  NIT-1; the follow-up's check, S1). TP:2216 (B17's :2205; Y's :2201) stays as B17 has
  it: lane 2 withdrew the merge20 point behind lane 3's draft there (lane
  2, #165 6826474591, CF15-1). And TP:5603–5608 (B17's :5564–5565), AT-F2's "Unconfirmed": a cutover's rerun that proves the marker absent pushes again (lane 2's CF15-2, #165 6826474591; ops:275). And, in place, lane 2's TX17-2b, -2c and -10i (#165 6833867473): TP:4345 (B17's :4306; Y's :4297), AT-SPL's fixture, and TP:4575 (B17's :4536; Y's :4523), AT-LR4's fixture, which name AT-LR006's register in place of "the imported register", and TP:6392 (B17's :6339; Y's :6322), AT-F6's list of names, where row (d)'s new name, `test_two_sessions_binding_one_lane_in_files_mode_both_binds_land`, takes the place of `test_two_sessions_binding_one_lane_in_files_mode_the_second_is_refused_at_the_reread`; none moves a line. And, in place, in fold 1112g: design:1258 (Y's :1254), the `register_lines` comment, which now says "of a kind" (lane 1, #165 6834413025; ERR-49); design:5114 (Y's :5067), `migrate_state_cells`' row, which gains T413's whole seconds under the flag (lane 3, #165 6834516611; lane 4 confirmed it, #165 6836180079); and operations.md:283, act 4 step 9, where the lag of 0 is read after a fetch of W, since `status` never fetches (lane 3's ruling (a), #165 6833830078). And, in place, in fold 1112h: TP:6925–6927 (B17's :6869–6871; Y's :6852–6854), AT-F14's oracle, where each committed writer prints its success text (claim's `register seq <s>` line, on stderr) exactly once, in place of "stdout carries" (lane 2's TX19-B3, N2-17b's words, #165 6836739058); design:5114 (Y's :5067) again, whose cell now names "the `log_lines` CHECK on primary's `utc`" (lane 4, #165 6836180079); and TP:4214–4216 (B17's :4189–4190; Y's :4180–4181), AT-SET's oracle after the cutover's entry, and TP:5602–5612 (B17's :5563–5564; Y's :5547–5548), AT-F2's "Unconfirmed", re-broken in place, where a write behind the marker `adding` "exits 69 on the switch its snapshot reads, or on LR002" in place of failing with LR002 (lane 3's ruling, #165 6836905010; lane 1, #165 6837147488); none moves a line. And, in place, in two earlier edits this list had missed, and in fold 1112k: design:5547 (B17's :5546; Y's :5500), row (d)'s files-mode half, where the fourth cell says that two binds that both read the lane free both land, in place of `lane-start`'s re-read at `:2614` (lane 4, #165 6833596198; `88c10cc`); TP:6951 (B17's :6894; Y's :6877), AT-F14's oracle for a child whose parent is killed while it waits, where the fake `gh` "makes no comment call" in place of "is not called" (lane 2's N2-17, #165 6833722259; `e5017f9`); and TP:5518–5521 (B17's :5479–5482; Y's :5463–5466), AT-F1's oracle, where the write tried meanwhile becomes "A direct `commit_write` tried meanwhile" (lane 2's TX19h-3, #165 6838448299); none moves a line. And, in place, in folds 1112n and 1112o: TP:3442 (Y's :3419), the `[rename-lane-stops]` case, where the handoff file is emptied in the lane's checkout, in place of removed from it, so the replay's stamp proof refuses before the seam (lane 3, #165 6841366245; lane 2's T245v, #165 6841506953); and ops:278, act 4's step 8, which now names each host's need, before its flip, of the register checkout of every lane it may write for, or write routing on first (lane 3, #165 6841265575); none moves a line. And, in place, in fold 1112p, on lane 3's rulings on T510's points (#165 6841918192): TP:5928 (Y's :5864), the order oracle's guard's child, whose refused write's own code is never 0 while the guard's body exits 0 and the bind ends `bound`; TP:6152 (Y's :6087), AT-F6's case 8, whose scope is read in the attempt's `out` file while `why` stays `lane-start`'s refusal line; and TP:6159 (Y's :6094), case 10's `[absent]`, which ends 3 since its deadline's lookup fails too; and, on lane 3's ruling on T422's stops replay, TP:3442–3443 (Y's :3419–3420), where the handoff is committed empty in the lane's checkout, and the stamp proof refuses it with 5 though the checks before it pass it (lane 2, #165 6841965598; lane 3, #165 6841366245), in the words fold 1112q gave :3443 (the fourth adversarial review's S1); none moves a line. And, in place, in fold 1112r: TP:2843 (Y's :2828), where the restart skill's `lread` refuses 69 through the `69)` arm T407 adds, and 75 and 3 through its `*)` arm (lane 2, #165 6851002528; lane 3, #165 6851059362); TP:6168 (Y's :6103), where case 10's `[absent]` arm is disarmed before the next `2`, so that the lookup answers absent (lane 3, #165 6841918192; lane 5, #165 6850767626; lane 2, #165 6851156663); and TP:6173 and TP:7068 (Y's :6108 and :6994), where the replay's snapshot takes `--checkout <c>`, as the code passes it (lane 4, #165 6850778146; lane 2, #165 6851156663); none moves a line. And, in place, also in fold 1112r: TP:3447–3450 (Y's :3424–3427), the `[rename-lane-differs]` case, where the handoff file is removed by a commit on the lanes origin and the text names `lanes/LANES.md`, in place of moved by hand to the new path, whose replay meets `rename-lane`'s refusal of an occupied target before the seam and stops (lane 2, #165 6851227703; lane 3, #165 6851669848); none moves a line. And, in place, in the same fold: TP:3442–3443 (Y's :3419–3420), the `[rename-lane-stops]` case, where the handoff is committed empty on the lanes origin, so the rerun's lane checkout holds it, in place of committed empty in the lane's checkout (lane 2, #165 6850952424; lane 3, #165 6841366245); none moves a line. And, in place, in fold 1113d: TP:7824 and TP:7827 (Y's :7750 and :7753), section 4.4's remote-database option, where the whole-file fetch's 165 ms is brought lower by the fetch's own `lanes-index` start and second connect (design:4273), and no share expires on a link that answers once the image cache is filled, while a prompt that must fetch a whole file may answer 2 and leave the file for the next prompt (lane 5, #165 6852197902; lane 3, #165 6858923154); TP:8386 and TP:8389 (Y's :8312 and :8315), section 5.6's option B, where T229's relay connects upstream at once, so TCP's handshake crosses it with no RTT and it checks n less one round trip a connect (lane 5, #165 6852197902; lane 3, #165 6858923154); TP:580 (Y's :580), AT-1's message check, where `rename-lane`'s message on primary is the one the design names under Q13(a) (lane 3, #165 6859179515); TP:6168 (Y's :6103), case 10's `[absent]` arm, which cites the bind's own row, Y's :5516, beside Y's :5244 (lane 5, #165 6858973903); and design:3498 (Y's :3451), Q13(a)'s rerun, which names `rename-lane`'s entry message on primary under Q13(a) (lane 3, #165 6859179515); and, from the seventh review's S1, TP:546 (Y's :546), the rendering test's check that the journal entry carries the write's commit message, design:5144 (Y's :5097), "The rule: one run, one journal entry", design:2844 (Y's :2797), "The journal", and the delta spec's `specs/sql-register/spec.md:392` (Y-spec's :387), "The journal records each write", each of which now excepts `rename-lane`'s message on primary, the one Q13(a) names (lane 3, #165 6859179515), as does proposal:313, which restates the requirement, from the eighth review's N7 (lane 3, #165 6859179515); none moves a line. And, in place, also in fold 1113d: TP:6251 (Y's :6186), case 21's setup, where the image cache is filled by the offer made over the cluster's own port, whose whole-file fetch fills it, and no service is started, from a state home whose socket path is over 103 bytes, so that the client reads directly (Y's :3778–3779; lane 2, #165 6859896678; lane 5, #165 6852197902); TP:7826 and TP:7827 (Y's :7752 and :7753), section 4.4's remote-database option, where under section 5.6's option A or B a healthy delayed link is crossed by AT-F6's case 21 and AT-D9c (d) alone, in place of every case of the section, and the fault cases stay on loopback (lane 2, #165 6859896678; lane 5, #165 6852197902; lane 3, #165 6858923154); and TP:7096–7097, TP:7104 and TP:7105–7106 (Y's :7022–7023, :7030 and :7031–7032), AT-F34's rerun cases, where NIT-4 (b)'s case, two matching call lines, is one line after the "No record" case, its test name takes the line the run-time bullet began on, the run-time bullet is joined into one line and reads about 1.5 to 2 min, and lane 4's two-line bullet is joined into one, so that the plan keeps its count (lane 2, ID's T245ze, #165 6859896678; lane 3, #165 6851422597 and 6860075071); none moves a line. And, in place, in fold 1113e: TP:6255 (Y's :6190), case 21's oracle, where the guard's register read is the `2`'s own prefetch, `register read --for guard` in form 2 (the register-read contract's section 3.6), a call through the relay at `c` = 3 under the guard's 1500 ms statement bound, in place of the plan's naming it only as the hook's last database call (lane 2, #165 6866822075; lane 3, #165 6860626333); and, in the same fold, on Brett's answers of 2026-11-13 (`RULINGS-brett-20261113e-q1-q6.md`): TP:458–459 and TP:463 (Y's :458–459 and :463), where Q24's install route reads as answered, `./setup.sh` (Brett's "1a 2b", 2026-11-12), and check 1 there rests on the re-vendor's package ("Vendor release as package (Recommended)"); TP:901–902, TP:925, TP:1001, TP:1011–1012 and TP:1016 (Y's :896–897, :920, :996, :1006–1007 and :1011), AT-4's deadline and case (c), and TP:7579–7580, TP:7598–7599, TP:7630–7631 and TP:7633 (Y's :7505–7506, :7524–7525, :7556–7557 and :7559), section 4.1's deadline and its record, where the burst runs under the runner's own D7, `max(30 s, 10 × the runner's attempt p95)`, set from the test's own measured attempts, its oracle still strict ("(d) Both (a) and (c) (Recommended)"); TP:3781 (Y's :3755), AT-D9c (d)'s burst, which keeps the variable unset, as AT-4 (h) does; TP:9059 (Y's :8983), open point 32, answered for AT-4 (c); and design:304 and design:6340 (Y's :304 and :6293), Q24 (a) and the release, where the re-vendor vendors the release as a package ("Vendor release as package (Recommended)"); none moves a line. And, in place, in fold 1114b: TP:925 (Y's :920), AT-4 (c)'s burst, whose test rounds the deadline up itself, since the writer honours whole seconds only, and takes the runner's attempts from a measuring burst of the same shape run just before it (lane 2, #165 6869910867); design:5108 (Y's :5061), the seam's step 3, whose `msg` holds `$msg` as files mode's `git commit -m` stores it: cleaned once by `git stripspace`, git's default cleanup (`whitespace`), with no final LF of its own, in place of verbatim (lane 3's ruling, #165 6859073786; lane 1, #165 6869810000; lane 4, #165 6870452610); and ERR-56 (lane 3, #165 6867981064) leaves design:5589 (Y's :5542) and spec:318 as they are; none moves a line. And, in place, in fold 1114b's commit 15: TP:580 (Y's :580), AT-1's message check, whose message equals the twin's commit message less that message's one final LF, and design:5108 (Y's :5061), whose `msg` clause now says that files mode's commit message ends in the one LF git adds, which the twin test allows (lane 4, #165 6870452610); none moves a line. Lane 2's slices of waves 1 to 14 that no As built line in tasks.md names, 133 of them, are recorded in lane 2's inventory rather than in tasks.md (lane 3, #165 6837015299): `INVENTORY-lane2-007-slices-for-followups-09096c6dc267.tsv` (`082d49cd…`), in lane 2's `BUILD-W18/TX19/`, one row per slice bundle in lane 2's share, with its task, commit, tag, report, files and subject, and `group` 4 for 131 of those 133 and `TX19-B5/after-B5` for the other two, T278b and T278c (a third row so marked, T278f, is in tasks.md at T278's line).

So a `Y:<n>`, `ops:<n>`, `clar:<n>`, `Y-spec:<n>` or `Y-svc:<n>` citation
quotes Y's text at `a6497d00`; where a round above changed that line, the
branch holds the newer text at the same number, except in `design.md` after
design:489, which the follow-up's fold of T1 moved one line down, and after
design:1192, which round B13's fold moved up to 46 more. A
`test-plan.md:<n>` or `TP:<n>` citation is likewise Y's line; in the branch,
round B14's edits put the plan's lines from :820 to :2900 one line down, and
those from :2903 two; round B15's texts move them further, as its entry
above says, and round B16's AT-P3 fixture (lane 2's TX13-4) moves those from B15's :2996 two more; the follow-up's test-plan texts move them further, as its entry above says.

- **Part 1, the errata** (ERR-01 to ERR-56; ERR-19 is now DEC-21; ERR-56 is appended at the file's end, so that no line above moves): a line
  at Y that 007 reads otherwise. The frozen text keeps its words; the code
  and the manual follow the erratum.
- **Part 2, the decisions** (DEC-01 to DEC-36): a choice the change leaves
  open, or a ruling that 007 builds on.
- **Part 3, the plan's choices** (PC-1 to PC-16).

**Each entry gives** the decision, whose it is, and its source lines.
Whose is written one of three ways:

- **Brett's:** only with his own words, verbatim, and their source.
- **Lane 3's ruling:** with the #165 comment that carries it. R01 to R61 are
  the numbers of lane 3's rulings memo, which lane 3 ruled in #165
  6726556972; where the post and the memo differ, the post holds.
- **A lane's own:** labelled with that lane, as its author's decision or
  answer.

**Citations** are spec.md's: `spec:<n>` and `svc:<n>` are lines of the sql
spec and the service spec on this branch, numbered as at `ad82c2f4`; `Y:<n>`
is `design.md`, `ops:<n>` `operations.md` and `clar:<n>` `clarifications.md`,
which keep Y's numbers (above). `Y-spec:<n>` and `Y-svc:<n>` are the two spec
files' lines at Y, used only where an entry quotes Y's words that the
branch has since changed. A line of commit X above X:3955 is Y's line
less one, since Y rewrote only SVC-5's X:3951–3955 as Y:3951–3956; lane 3's
memo cites X, and each X line is mapped to Y here by its words. Lane 1's
contract is its draft 4.28 (`contracts/register-cli.md`, `b6597236…`,
draft 4.27, #165 6834642319, with lane 3's K9, #165 6870473237) and its block is v4.11 (`625a0b84…`, #165 6754645319),
both keyed to Y. The block keeps v4.2's line numbers; the draft keeps them
to :637 and moves each later line down one, as draft 4.12 does.

## Part 1: errata against the frozen text

### ERR-01. `:1315` is lane 1's, not lane 4's (R12)

- **The erratum:** Y:3494 ("lane 4 changes the `-d <url>` at
  `code/lanes-index:1315`") and Y:3804 ("lane 4 changes `:1315`") both give
  Question 11's site to lane 4. It is `PostgresStore._run`'s `-d self.url`,
  lane 1's database side, and lane 1's T103 changes it, as group A's first
  `lanes-index` pull request.
- **Whose:** lane 3's ruling R12 (#165 6726556972), extended to Y:3494 in
  lane 3's ruling #165 6727022920.
- **Source:** Y:3494; Y:3804; Question 11 (Y:4967).

### ERR-02. `login rotate` gains `--dump-job` (ops §2.4)

- **The erratum:** ops:483 gives `rotate <engineer>` alone, which sets new
  passwords for an engineer's two logins. But the disaster path has Brett run
  `lanes-index login rotate` "for each engineer and the dump job" after a
  point-in-time restore (ops:372). So `rotate` gains a form,
  `lanes-index login rotate --dump-job`, which sets a new password for
  `lanes_register_dump` alone, as `add --dump-job` makes it alone (ops:482;
  ops:710), and prints its one password-file line once, to a terminal, for
  Brett to store as the Key Vault secret the dump CronJob reads. Lane 1's
  T110 carries it, with its case `rotate_dump_job`.
- **Whose:** lane 3's ruling, #165 6726725691, on lane 2's finding
  (6726708085). Lane 3 there called the gap "lane 1's text, folded
  faithfully", so it went into no erratum commit. Lane 1's words are at line
  285 of its texts file (its D8), which Y carries at ops:372 (lane 3, #165
  6727022920).
- **The decision:** lane 3's #165 6726725691, carried by lane 1's T110 and
  its `rotate --dump-job`.
- **Source:** ops:483; ops:372; ops:482; ops:710. The sql spec already
  has the dump job's password "rotated" by `lanes-index login` (spec:846).

### ERR-03. `--roles-only` also runs three database-level statements (the dump route's (a))

- **The erratum:** ops:411 says `--roles-only` makes "only the roles". On the
  dump's restore route it also runs the three database-level statements that
  a dump of both schemas does not carry, as it carries no role:
  - Y:2243's `REVOKE ALL ON DATABASE … FROM PUBLIC`;
  - Y:2244's `GRANT CONNECT`, to the writer and reader groups and the
    indexer's login;
  - Y:2256–2257's global `ALTER DEFAULT PRIVILEGES FOR ROLE
    lanes_register_owner REVOKE EXECUTE ON FUNCTIONS FROM PUBLIC`.

  `bootstrap` keeps ops:434: with the switch at `primary` it "changes nothing
  else". Without the three, PUBLIC would keep `initdb`'s default CONNECT on
  the new Cluster's database: lane 1's dump of both schemas held no
  `ON DATABASE` statement and no global default privilege. Lane 1's T109
  carries (a), with its case `dump_restore_privileges`.
- **Whose:** lane 3's ruling, #165 6727022920, option (a) of lane 1's check
  of X (6726995665).
- **Not a ruling:** lane 1's block v4 reports a dialog in lane 1's session,
  answered at 21:01:25Z with the option "--roles-only runs them
  (Recommended)" (#165 6727094958). By lane 1's own account there, the
  request that opened it, "ask me the CNPG choices with multichoice", came
  in as an accepted prompt suggestion, and the answers then came in the
  dialog. It is not taken as a ruling here, and nothing rests on it.
- **Source:** ops:411; ops:434; Y:2239; Y:2243–2244; Y:2256–2257.
- **The SHALL, changed:** at Y the sql spec's bootstrap requirement said "A
  roles-only form SHALL make only the roles" (Y-spec:846). Two commits on this
  branch after `aa190a7f`, on lane 3's ruling (a), rewrite that SHALL so the
  roles-only form makes the roles, with the admin's membership in the owner
  and the indexer (Y:2239), which is T109's first part, and these three
  statements, and nothing else. They keep its reason clause, and add the
  scenario "A dump restored after the roles-only form", whose THEN also
  checks lane 1's case 5: a function the owner makes after the restore is
  not executable by PUBLIC. No spec delta is left. ops:411 keeps its words,
  so this entry stays its erratum.
- **The restore's database and order:** lane 3's ruling on test-plan open
  point 41, 2026-11-09. Its first step is that the register's database
  exists. Every `lanes-index` step after it runs through one admin service,
  which names that database: `lanes-index` never picks a database. The
  order:
  1. the register's database exists. On a new CNPG Cluster, the install's
     `initdb` made it when the Cluster was created (ops:190). Where none
     exists, as on AT-LRMPG's `stoppable_cluster`, the admin creates it
     through his own maintenance connection;
  2. `bootstrap --roles-only`;
  3. `login add <engineer>` for each engineer, and `login add --dump-job`;
  4. `pg_restore` into the database;
  5. `bootstrap`, which prints `unchanged`.

  Under (a) the database must exist first: the `REVOKE` and the `GRANT …
  ON DATABASE :"db"` name it, and `ALTER DEFAULT PRIVILEGES` holds only in
  the database the connection names.
  - **It corrects** the test plan's restore from the dump, AT-LRMPG's
    (`test-plan.md:5346–5362`), which holds two admin services, one naming
    the cluster's `postgres` database for the steps before the register's
    database exists and one naming it for the steps after, and runs
    `--roles-only` and `login add` before the admin creates it. It corrects
    ops §2.3's step 6 (ops:371), which runs `--roles-only` first without
    naming the database its admin service names; ops §2.3's dump route
    (ops:372), which still runs `login add` after `pg_restore` and
    `bootstrap`; and ops §2.4 (ops:410; ops:486), which give the admin
    service and the roles-only form and `login add` with no database named.
    Those texts keep their words, so this entry is their erratum.
  - **Whose:** lane 3's ruling, which answers the test plan's open point 41
    (`test-plan.md:9064–9072`). Its record is lane 3's #165 6739605144, and
    lane 1's block v4.2 follows it (#165 6739714248).

### ERR-04. The guard's second read is always direct, at `n` (B7)

- **The erratum:** Y:4159–4160 has the guard's read for a missing cache file
  make "one more `register read`, direct if the flag file exists", which
  implies the service otherwise. It reads "always direct, at `n`": form 4 of
  `register read` never asks the service. The service's request carries only
  file names (svc:59; Y:4611), so it cannot be asked for a log at the
  prefetched `n`, and svc:59 stands.
- **Whose:** lane 3's ruling B7, #165 6738474549, on lane 5's §3.6 review
  (6738431117). These are lane 3's rulings, not Brett's.
- **The cost and its review:** one `psql` start on the second-read path only:
  about 83 ms at a round trip of 0, and about 882 ms at 100 ms (lane 5's B6;
  Y:4365–4366). If T511's and T512's measurements show that path often out
  of budget, lane 5 brings it back; a request naming `n` would change the
  spec, so it would go to Brett.
- **Source:** Y:4159–4160; svc:59; Y:4611; Y:4365–4366.

### ERR-05. "From the configuration" means from `lanes-index`'s report (R05)

- **The erratum:** Y:5877 says `T_write` and `bound_s` come from the timers
  "which `guard-bind` computes from the configuration". `guard-bind` reads no
  configuration (D3): it sums `bound_s` from the `c`, `N_a` and `w_s` that
  `lanes-index` reports, through `.head` and the bind record. `lanes-edit.sh`
  parses no configuration, and `lanes-index` stays its one parser.
- **Whose:** lane 3's ruling R05 (#165 6726556972), on lane 1's O9 and lane
  5's open point 3.
- **Source:** Y:5877; D3 (Y:368).

### ERR-06. `commit` on primary: only the carry form is refused (R16)

- **The erratum:** the phase plan's seam table says of `commit`: "Never
  reached: refused 2 before the loop (D10)" (Y:5073). That predates slice
  1b's publish act. The sql spec keeps the publish of queued commits, with
  its fence narrowed to `handoffs/`, and refuses a queued commit under
  `lanes/` as a divergence (spec:169). So on primary only `commit`'s carry
  form is refused with 2; the publish form runs outside the attempt loop.
- **Whose:** lane 3's ruling R16 (#165 6726556972), on lane 4's Q-L3-1 and
  OB-14.
- **Source:** Y:5073; spec:169; D10 (Y:508).

### ERR-07. Row 43's lock is the workstation's sidecar lock (R19)

- **The erratum:** row 43 (Y:6069) and Y:5044, Y:5446 and Y:5561 say that
  under the flag the tail's sidecar acts take "the lane checkout's mutex". At
  slice 1b, `set-lane-state` takes the workstation's sidecar lock, so the
  tail serialises with it only under that lock: the parent writes its path
  into the attempt directory's `lock` file, `sidecar_lock_dir` returns it
  under the flag, and `lane_state_rename` runs under `sidecar_try`.
- **Whose:** lane 3's ruling R19 (#165 6726556972), on lane 4's Q-L3-4
  (F-B1).
- **Source:** Y:6069; Y:5044; Y:5446; Y:5561.

### ERR-08. `lanes` decides under `--decide` (R38)

- **The erratum:** the read door's class table puts `lanes` in the mirror
  class (Y:4895), but `lane` and `lane-end --retire-dormant` act on its
  answer, and must refuse 69 (spec:626; spec:634). `lanes` stays a mirror
  read and decides under `lanes --decide`, which those two callers pass:
  lane 5's T509 adds the flag, and lane 4's T408 and T403 pass it.
- **Whose:** lane 3's ruling R38 (#165 6726556972), amended by lane 3's
  #165 6726831985: lane 5's T502 accepts `--decide` and ignores it until T509
  gives it meaning, so that T403's and T408's lines land after T502.
- **Source:** Y:4895; spec:626; spec:634.

### ERR-09. The `SessionStart` block makes no register call (R39)

- **The erratum:** the mirror class reads SQL first and falls back to the
  mirror (Y:4895). The `SessionStart` block reads the mirror offline, always,
  and exits 0 (spec:626; Y:4493–4495). So it is in the mirror class with no
  register call.
- **Whose:** lane 3's ruling R39 (#165 6726556972), on lane 5's open point 2.
- **Source:** Y:4895; Y:4493–4495; spec:626.

### ERR-10. The outer sweeps `timeout`'s group (R47)

- **The erratum:** Y:5401 says `timeout` "passes it to the body's group and
  sends `KILL` 10 s later". GNU `timeout` returns once its own child has
  died, so that `KILL` never reaches a grandchild that ignored `TERM`. After
  `timeout` returns 124 or 137, or after the outer's trap fired, the outer
  sends `TERM` and then `KILL` to the group `timeout` led, within the bound,
  and never after the body's own exit. The renderer fork, in the body's
  group, may die with it, since the next render redoes the mirror.
- **Whose:** lane 3's ruling R47 (#165 6726556972), taking lane 5's answer to
  lane 2's point 39(a), with lane 4's confirmation (6726296119).
- **Source:** Y:5401.

### ERR-11. The bind child also unsets `LANES_WRITE_GENERATION` (R52)

- **The erratum:** the bind child's environment table unsets
  `LANES_WRITE_ID` and not `LANES_WRITE_GENERATION` (Y:5414). A printed
  rerun's generation must not reach the `lane-start` inside the bind, which
  mints its own write, so the child unsets both.
- **Whose:** lane 3's ruling R52 (#165 6726556972), on lane 5's open point 21.
- **Source:** Y:5414.

### ERR-12. AT-F6 has twenty-two cases (R53)

- **The erratum:** AT-F6's list runs from (1) to (22) (Y:5563), while Y:3672,
  Y:4320, Y:6633 and Y:6738 say twenty. Twenty-two is the count.
- **Whose:** lane 3's ruling R53 (#165 6726556972), on lane 5's contradiction
  1.
- **Source:** Y:5563; Y:3672; Y:4320; Y:6633; Y:6738.

### ERR-13. Row 37 carries the service's two-address figures (R57)

- **The erratum:** row 37 gives `bound_s` as 500 s and 530 s with one
  address and 550 s and 610 s with two (Y:4770–4776). With the service and
  two addresses it is 560 s and 620 s (Y:5462), and row 37 carries those
  too.
- **Whose:** lane 3's ruling R57 (#165 6726556972), on lane 5's contradiction
  7.
- **Source:** Y:4770–4776; Y:5462.

### ERR-14. Answer 2's text is "BINDING" (R58)

- **The erratum:** Y:5389 records answer 2's text as ruling 16 had it,
  "binding in progress". Y:4660 supersedes it: "BINDING" for answer 2
  itself, and "BINDING IN PROGRESS" for each prompt while the bind runs.
- **Whose:** lane 3's ruling R58 (#165 6726556972), on lane 5's contradiction
  8.
- **Source:** Y:5389; Y:4660.

### ERR-15. The phase plan cites SVC-8 by name (R59)

- **The erratum:** the class table cites "SVC-8's exit mapping
  (`design.md:3019–3030`)" (Y:4896), and the guard's phase-1 row cites
  `design.md:2870–3030` (Y:4939). Those are the frame's lines. They read as
  SVC-8 (Y:4116) and its exit mapping (Y:4336), by name.
- **Whose:** lane 3's ruling R59 (#165 6726556972), on lane 5's contradiction
  9.
- **Source:** Y:4896; Y:4939; Y:4116; Y:4336.

### ERR-16. Lane 1's texts in X cite W's lines

- **The erratum:** lane 1's texts, folded into X, cite W's line numbers: 149
  citations shift. The frozen text keeps them. Lane 1 re-cited its own parts
  against Y, and its contract draft 4.1 and block v4.2 keep Y's numbering.
  Three citations were already wrong at W: C1's
  `proposal.md:607-611` and `:683-693` (Y:2232), and D2's
  `proposal.md:1810-1813` (ops:190).
- **Whose:** lane 1's finding (#165 6726995665); lane 3's ruling to record it
  here, #165 6727022920.
- **Source:** Y:2232; ops:190.

### ERR-17. D1's item 4 "suggests" the issuer Brett chose

- **The erratum:** the install contract's item 4 says "lane 1 suggests a CA
  issuer under a root of the install's own" (ops:153). Brett then chose it:
  "Install's private CA (Recommended)" (clar:495; #165 6726078173). So the
  issuer is the install's private CA, and each workstation's `sslrootcert=`
  names its root, never `system` (Y:2324).
- **Whose:** Brett's choice, in his words above, as Y records it; lane 3's
  ruling to record it here, #165 6727022920.
- **Source:** ops:153; clar:495; Y:2324.
- **Superseded on 2026-11-09** by Brett's "add openXPKI to prod and use that.
  both QA and Prod need openXPKI" (14:38:23Z; #165 6742844912): the endpoint's
  certificate now comes from the prod OpenXPKI, as `operations.md`'s item 4
  read from round B3 until round B5. This record keeps what was decided on 2026-11-08. Then, on 2026-11-09 at 16:58:23Z (answers observed), Brett's "Interim CA now, OpenXPKI later (Recommended)" (#165 6746095035) made the install's private CA the issuer again, as the interim CA until the prod OpenXPKI exists.

### ERR-18. D1's item 5 keeps the physical backups 30 days

- **The erratum:** the install contract's item 5 names the `ObjectStore`'s
  `retentionPolicy` without a value (ops:155). It is 30 days, as the daily
  dump's container keeps 30 days.
- **Whose:** lane 3's ruling, #165 6726078173, which Y records (Y:624;
  clar:505–506); recorded here by lane 3's ruling #165 6727022920. Lane 1's
  dialog of 21:01Z (6727094958) also answered it, with `enablePDB: false`
  and the dump route's (a). By lane 1's own account there, the request that
  opened it was an accepted prompt suggestion, and the answers then came in
  the dialog; it is not taken as a ruling here, and nothing rests on it.
- **Source:** ops:155; Y:624; clar:505–506.

### ERR-19. Moved to DEC-21

It adds a refusal Y never had, so it is a decision, not an error in Y.

### ERR-20. Lane 4's live-owner 3 gains its record-less form (R26, point 42)

- **The erratum:** the pending record's live-owner 3 names "write `<id>`"
  (Y:5218), but a claim with no record beside it has no id yet. Its 3 names
  the claim (pid, host, container, UTC) and gives the design's own effect
  check before any rerun, since a plain rerun could make a finished write
  twice. Lane 4's T417 carries it, with its `_EXIT_3_CAUSES` entry.
- **Whose:** lane 3's ruling R26 (#165 6726556972), taking lane 4's answer to
  lane 2's point 42 (6726296119).
- **Source:** Y:5218.

### ERR-21. The design's "too late" and failed-read refusals are the prefetch's (B4)

- **The erratum:** six passages of the design read as a refusal for any
  late or failed read of the guard's:
  - Y:4185, the parse's start line: "a read, through the service or direct,
    that completes after it is a refusal";
  - Y:4266–4271, the clamp, at the direct read's cap of 3.75 s, or 3.65 s
    under a declaration;
  - Y:4344, the exit-mapping row that maps a late read to 2;
  - Y:4343, the exit-mapping row that maps a failed read, `--for guard`'s 69,
    to "SVC-12's declared-outage check; otherwise 2";
  - Y:4346, the row that maps Q6(c)'s 78 to 2;
  - Y:4445, SVC-12's condition 1: "its database read has failed".

  Each holds for form 2, the prefetch, only. Form 4, the guard's second
  read, has B4's ends: 0 with the one log's files written, and every other
  end a log that could not be read, which the guard notes and does not
  refuse for. Its cut is `T0` + 3.75 s whatever a declaration says, since it
  reads no declaration (B5).
- **Whose:** lane 3's ruling B4, with B5, #165 6738474549, on lane 5's §3.6
  review (6738431117).
- **Source:** Y:4185; Y:4266–4271; Y:4344; Y:4343; Y:4346; Y:4445.

### ERR-22. A run with no snapshot clock waits on the record's clock (R26, point 36)

- **The erratum:** Y:5236 says "A run with no snapshot clock calls at once".
  Under DEC-12, a takeover whose own first snapshot failed waits on the
  record's clock instead; "calls at once" stays for a record from another
  host. It was part of DEC-12.
- **Whose:** lane 3's ruling R26 (#165 6726556972), taking lane 4's answer to
  lane 2's point 36 (6726296119).
- **Source:** Y:5236.

### ERR-23. Cases 7 and 9 rename the window back to `<from>` (point 7)

- **The erratum:** the "Record after" cells of Y:5503, the failure on the
  third attempt, and of Y:5505, the stale record, add "window renamed back to
  `<from>`" (Y:5502), as the failure below the third attempt has it. Y:5506's
  record from another host or container renames nothing. T502's cases 7 and 9
  follow it in `tasks.md`.
- **Whose:** lane 3's ruling on lane 5's T501 point 7, #165 6746215510.
- **Source:** Y:5502; Y:5503; Y:5505; Y:5506.

### ERR-24. Two code citations in the bind's record and case table (point 14)

- **The erratum:** Y:5507's citation of today's text for an unreadable record,
  `:11097–11101`, reads `:11091–11094`. Y:5428's citation of the offer's
  identity, `:10965–10973`, reads `:10963–10971`.
- **Whose:** lane 3's ruling on lane 5's T501 point 14, #165 6746215510.
- **Source:** Y:5507; Y:5428.

### ERR-25. The service's connect timeout is `c` (OP-1)

- **The erratum:** Y:3892's "which the service overrides with 2", said of
  `PGCONNECT_TIMEOUT`, reads "which the service, as every register path, sets
  to `c` (SVC-6)". Every other place in Y already says `c`.
- **Whose:** lane 3's ruling on T511's OP-1, #165 6746215510.
- **Source:** Y:3892.

### ERR-26. The service's answer names the register generation it read (OP-2)

- **The erratum:** Y:4064–4066's "The answer is `text <n>`" reads: the answer
  is "`text <n>` with the register generation that transaction read", as
  `register read` reports both (Y:4833). Its bytes stay lane 5's OP-3, and
  lane 1 confirms the form.
- **Whose:** lane 3's ruling on T511's OP-2, #165 6746215510.
- **Source:** Y:4064–4066; Y:4833.

### ERR-27. An owner file from another pid namespace or boot means the service is unavailable, and starts none (OP-8)

- **The erratum:** Y:3881–3883's start list drops "another namespace or". A
  client starts `serve` where it finds no socket (`ENOENT`), a dead one
  (`ECONNREFUSED`), or an owner file from another install, unless a failure
  stamp is fresh; the last is what Y:3914–3918's `unavailable restarting`
  covers. An owner file from another pid namespace or another boot means the
  service is unavailable: the client reads directly and starts none, as SVC-2
  says (Y:3782–3788). `ENOENT` and `ECONNREFUSED` still start one, whatever
  the owner file says.
- **Whose:** lane 3's ruling on T511's OP-8, as corrected in #165 6746655077,
  which replaces the OP-8 line of 6746215510 and of its analysis.
- **Source:** Y:3881–3883; Y:3914–3918; Y:3782–3788.

### ERR-28. `dir-check` is never stamped (OP-9)

- **The erratum:** Y:3927–3929 lists `dir-check` among the causes that `serve`
  stamps. It is never stamped: `serve` exits 2 naming the directory and writes
  nothing in it, and `lanes-index status` reports the failure itself
  (Y:3761–3768). Lane 1 reviews that `status` line (R44), and lane 1's commit
  `eca2da3d` gives `contracts/exit-codes.md`'s run-directory row the same
  reading.
- **Whose:** lane 3's ruling on T511's OP-9, #165 6746215510.
- **Source:** Y:3927–3929; Y:3761–3768.

### ERR-29. D9 gains a row for the service's background whole-file fetch (OP-15)

- **The erratum:** D9 (Y:502), whose table is at Y:3501, gains a row for the
  service's background whole-file fetch: `SET LOCAL lock_timeout = '1000ms'`
  and `statement_timeout = '10s'`, the renderer's row (Y:3527), with an 11 s
  timer. On expiry the fetch is dropped, and the next miss retries.
- **Whose:** lane 3's ruling on T511's OP-15, #165 6746655077, accepting lane
  1's answer in 6746620707.
- **Source:** Y:502; Y:3501; Y:3527.

### ERR-30. `LANES_READ_MIRROR=1` covers every read that names the lane in a handoff (lane 4's item 1)

- **The erratum:** Y:4895's mirror row and Y:4928 name only `window-lane` and
  `swapped`, and `test-plan.md:2198–2204` has `canon-lane` exit 69 with the
  variable set. With it set, these read the mirror, by verb: `window-lane`,
  `swapped`, `register-row`, `canon-lane`, `last-session`, `lane-state`,
  `set-lane-state`, `handoff-path` and `write-handoff`, and the skill's own
  calls of these verbs (after slice 3, `handoff-path --create` and
  `write-handoff --prepend`), and, at the ownership seam, `managed-projection`
  (DEC-27). So do `lane-handoff`'s two reads that gate the handoff file, each with the variable on its own call: `lane-last` at the closed-lane fence, which opensoft/openRepoTools#118 moved from the late path into every other mode (code `ba2e6b9`; `lane-handoff:2153–2155` at `8a5edd97`), and which still refuses 2 where the mirror's last lane-kind line is `ENDED` or `RETIRED` and 1 where the mirror read fails too (Amendment 7(d)); and `restart-intent` at the preservation check (`:2164`), which Y:5646 calls host-local and T504's table reads as a deciding read. Only `who` still ignores the variable; `--late`'s own `lane-last` read and `lane-start`'s (spec:664) carry none and still exit 69, so `--late` in an outage refuses 1 there and writes nothing; and `lane-handoff` and, with lane 4's strip join (merge96 `baaaf517`), `lane-start`, `lane-end` and `lane`, and with its follow-up join (merge99 `69e0e94c`), `lane-rename` and `lane-worktrees`, unset an inherited `LANES_READ_MIRROR` at entry, so that no value a caller exported lowers those reads (lane 3, #165 6861148117 and 6866808028; lane 4, #165 6867496126; lane 1, #165 6867564602). No register
  write rests on those answers: `PAUSED`, the `LANES.md` line and the row
  still exit 69 with their notes, and the lane stays `SWAPPING`. spec:692
  already requires it.
- **Whose:** lane 3's ruling on lane 4's item 1, #165 6746035594, with its list by
  verb (#165 6747422030); widened to those two reads by lane 3's ruling on round 6h's #56 (#165 6859801732). The
  ownership seam in an outage (spec:648–649 against `lane-handoff:1728–1741`
  at code `ddc972d`) was his spec-PR list item 9, and is answered: DEC-27.
- **Source:** Y:4895; Y:4928; `test-plan.md:2198–2204`; spec:692;
  spec:648–649.

### ERR-31. A reader's request may name member sets and the render set's listing

- **The erratum:** Y:4036–4037's "The request names the files it wants" reads:
  it names "the files it wants, singly or by the contract's member sets, or
  the render set's listing at `n`", and still nothing else: register files at
  `n`, and no pid, session or host fact (SVC-11). A member set names fixed
  render-set files; `--all` and `paths` ask for the render set's listing at
  `n`, which the service reads in the same transaction. Contract draft 4.6's
  :665 and :730 rest on it, and draft 4.7's :665 states it.
- **Whose:** lane 3's ruling, #165 6747048305, on lane 1's question in
  6746948770. The citation is Y:4036–4037, as lane 1 corrected it (#165
  6747151224), since the line before it is blank.
- **Source:** Y:4036–4037.

### ERR-32. `lane-start`'s two routing reads are phase 2's (T402's point 1)

- **The erratum:** Y:4924's table of the lifecycle scripts' sites gains
  `lane-start:1269` (`ls_rows_source`) and `:1791`, at lane 4's T402 commit
  `ce8076a`, as phase 2. They are slice 2b's routing reads of
  `origin/<branch>`, made only under `LANES_ROUTE_WRITES=1`, and on primary
  `:1269` would decide new lane against resume from the mirror. T428 moves
  both to the register before the cutover.
- **Whose:** lane 3's ruling on T402's point 1, #165 6747281902.
- **Source:** Y:4924.

### ERR-33. `request-handoff`'s arm in `lane-start` is a write's arm (T402's point 2)

- **The erratum:** Y:5730's row of arms that already refuse and pass the code
  lists `:2511–2512`, `request-handoff`'s arm. It moves to Y:5731's row, the
  writes. The arm keeps 1 in every mode until T428, which passes the code and
  relays the writer's rerun on a 3, as T432 does for `lane`'s arm.
- **Whose:** lane 3's ruling on T402's point 2, #165 6747281902.
- **Source:** Y:5730; Y:5731.

### ERR-34. Two `lane-transcript` refusals after the rename name what was written (T402's point 5; a spec delta)

- **The erratum:** Y-spec:685's "nothing was written" does not hold for the
  two `lane-transcript` refusals after the rename where `request-handoff`
  answered 0. There the refusal names what it wrote, the HANDOFF-REQUESTED
  line or the forced PAUSED, and gives the window back its name, as #138
  item 3 does.
- **Whose:** lane 3's ruling on T402's point 5, #165 6747281902. The change to
  the SHALL is Brett's. He chose "Accept the delta (Recommended)" by
  interactive choice in lane 3's session (transcript
  `83619980-06bb-4469-ae73-2a8c5e61981a`), answered at 2026-11-09T21:09:40Z,
  on his typed request at 21:03:13Z, "ask anything waiting on me in
  interactive multi choice". The option read: "YS:685 gains an exception:
  there the refusal names what it wrote and gives the window back its name, as
  #138 item 3 does (lane 3's ERR-34). It is added to your spec-PR list."
- **The SHALL, changed** (round B8, in place): spec:685 keeps its words and
  gains the exception for the two `lane-transcript` arms (`:3079`, `:3430`),
  which run after the window is renamed: where `request-handoff` answered 0,
  the refusal names what it wrote, in place of saying that nothing was
  written, and gives the window back its name. This entry is the record of
  that delta; T402's line cites it.
- **Source:** Y-spec:685.

### ERR-35. The register's role names are fixed (ops:413)

- **The erratum:** ops:413's "the role names: by default `design.md` §1.4's"
  drops "by default". The role block, inside which migration 1 runs (Y:2242),
  is Y:2235–2278 byte for byte,
  and lane 3's ruling (a) on T107 (DEC-23) keeps the three role names fixed,
  so no other names can be configured.
- **Whose:** lane 3's answer, #165 6747313608, to lane 1's question in
  6747233899.
- **Source:** ops:413; Y:2235–2278.

### ERR-36. "Pass the code" is for 69, 75 and 3 (T408)

- **The erratum:** Y:5722's "Pass the code" gains "for 69, 75 and 3; any other
  code keeps the arm's exit of today", read with Y:5746, under which the read
  arms change nothing in files mode. Files mode stays byte-identical, exit
  codes included. It narrows lane 3's point 4 for T402 (#165 6747281902) the
  same way.
- **What round B8 found** (R2's N9; a trace of `lanes-edit.sh` at code `main`
  `a2073eae`, reading each verb behind the arms of Y:5726–5730 and
  Y:5733–5740): no read verb there emits 3 in files mode. Two 3s inside read
  paths never reach a caller: `retired_identity_hits`' at `:4160`, which the
  `retired-identity` arm turns into 1, and `lane_alias_target`'s at `:5953`,
  which `canon_lane` turns into a note. But two arms in those rows run a
  write, `log`, not a read: `lane-end:2490` (Y:5735) and `lane-handoff:2817`
  (Y:5737). `log`'s `commit_push` dies 3 in files mode (`:2374`, `:2751`,
  `:2823`), as `request-handoff`'s arm does (ERR-33). This entry does not
  rule on those two.
- **The write arms** (lane 3's ruling, #165 6770733371): at a write's arm
  that exits a fixed code (`lane-handoff:2817`, `lane`'s and `lane-start`'s
  `request-handoff` arms), 69, 75 and 3 pass in every mode and every other
  code keeps today's exit; `lane-end:2490` exits with any code (Y:5735).
- **Whose:** lane 3's ruling on T408, #165 6747422030; the write arms,
  lane 3's ruling on T429's question 3, #165 6770733371.
- **Source:** Y:5722; Y:5746; Y:5735.

### ERR-37. `next-free`'s two arms join `lane`'s row (T408)

- **The erratum:** Y:5740's `lane` row gains `next-free`'s two arms, `go_new`
  and the listing's `next-free` read, made before the listing's first line,
  in phase 1. They pass 69, 75 and 3, since Y:3014 makes `next-free` a 69
  reader (spec:599–600), so a 69, 75 or 3 leaves with that code and nothing
  on stdout. T408 owns them.
- **Whose:** lane 3's ruling on T408, #165 6747422030. Its source is Y:3014,
  not Y:3022, and the listing's read comes before its first line (option
  B), both by lane 3's ruling #165 6754439281, item 1, which lane 4's B9
  delta writes into T408 (#165 6754773693).
- **Source:** Y:5740; Y:3014; spec:599–600.

### ERR-38. The printed rerun's `LANES_SESSION` is the value the digest took (T412's G2)

- **The erratum:** the printed rerun at Y:5200, and the same rerun in exit 3's
  text (Y:2664) and in the live-owner 3 (Y:5218), reads
  `LANES_SESSION=<the value <d> took, empty where the run had none>`, in
  place of `LANES_SESSION=<uuid>`. The digest `<d>` takes `<LANES_SESSION>`
  "as given on the original command line" (Y:5171), so a rerun must carry
  exactly that value, or its own digest differs and it meets LR007. Lane 5
  confirmed that `start-record`'s lookup (Y:5431) never meets the empty case
  (#165 6747708996), and lane 3 closed G2 (6747722621).
- **Whose:** lane 3's ruling on T412's G2, (a), #165 6747592174; widened to
  Y:2664 and Y:5218 in round B8 (R2's S4). T412 and T417 cite it.
- **Source:** Y:5200; Y:2664; Y:5218; Y:5171; Y:5431.

### ERR-39. On register paths the URL is the whole connection

- **The erratum:** Y:4970 drops only the inherited `PGPASSWORD`, `PGPASSFILE`,
  `PGSERVICEFILE` and `PGSYSCONFDIR`. Every path drops five, `PGSERVICE` too,
  as T103's `PG_ENV_DROPPED` holds them (`lanes-index:271`), #160's index
  paths included: libpq applies a service's entries before the `PG`
  variables, so an inherited `PGSERVICE` would outrank the URL once the URL
  travels as variables. On register paths every inherited variable whose
  name begins `PG` is dropped first, and `psql` gets only the URL's mapping
  (SVC-2), the configured `PGPASSFILE`, `PGCONNECT_TIMEOUT` at `c`,
  `PGOPTIONS` with the call's bounds (Y:3503) and `PGAPPNAME`, set to
  `lanes-index` (`lanes-index:1756` at T105 `6928ffb`; contract :216;
  `config.md:110`), so that "the direct read and the service connect
  identically" (Y:4970) whatever the shell exports. The register paths are
  the writer's and reader's connections built from `register_url=` and
  `reader_url=`, the direct read, and lane 5's `serve` through `pg_url_env`.
  The admin path keeps `PGSERVICE=<service>`, as T109's `--admin-service`
  sets it, and nothing else inherited. Contract draft 4.13's §2.2 states
  both, the register paths' rule and the five that #160's index paths drop
  too (:214).
- **Whose:** lane 3's ruling, (a), #165 6747611042, on lane 1's question in
  6747565816. Its rule that drops every `PG` variable narrows only the new
  register paths, inside FR-010; since round B9, every path, #160's index
  paths included, drops the five.
  `PGSERVICE` among the five: lane 1's account of T103 (#165 6752086222),
  which lane 3 accepted (6752115241). `PGAPPNAME`: lane 1's (6752336451),
  which lane 3 accepted (6752360829).
- **Source:** Y:4970.

### ERR-40. Y:5172's `database` is written empty (T412's G1)

- **The erratum:** "Y:5172's `database` is written empty in format 1. No
  source reaches `lanes-edit.sh` (R05), and no reader compares or prints it;
  `generation` tells databases apart (Y:632–634)." The key stays, so that a
  later source can fill it with no format change.
- **Whose:** lane 3's ruling on T412's G1, as built, #165 6750702819.
- **Source:** Y:5172; Y:632–634.

### ERR-41. Another generation's refusal may name no `<g′>` (T412's G4)

- **The erratum:** "Where the first snapshot refused the record's generation
  (LR007 `generation`), it prints no generation (contract §3.2). So Y:5209's
  `<g′>` reads 'this register is another generation', and Y:5207's 'The run's
  first snapshot says which generation the register is' reads 'says whether
  it is the record's'." T415 maps the snapshot's exit 2 carrying
  `DETAIL:  generation` to that refusal, with no `<g′>`.
- **Whose:** lane 3's ruling on T412's G4, as built, #165 6750702819.
- **Source:** Y:5207; Y:5209.

### ERR-42. A claim file is created whole (T412's G10)

- **The erratum:** Y:5184's "created with noclobber" reads "created whole: its
  body is written to a temporary file in the same directory, which is then
  linked to `<d>.take.<k>` with `ln`. The link fails where the name exists,
  as noclobber's create does." The temporary file is removed whether the
  link succeeds or fails, and no reader may take its name for a record or a
  claim. A failed write of the body is then exit 1, never a wait on the run's
  own empty claim.
- **Whose:** lane 3's ruling on T412's G10, #165 6750702819. Its analysis
  probed the built claim: a maker killed between noclobber's create and its
  write, or a write that fails on a full disk, left an empty claim that
  wedged its command for ever.
- **Source:** Y:5184.

### ERR-43. The bind's files take an index of their own (T501's point 18)

- **The erratum:** Y:5394's `<k>`, in `<offers>/<uuid>.bind.<k>.out`, is the
  files' index, not the attempt: one above the highest index among the uuid's
  `.bind.*` files, and 1 where there are none. `bind` stays the attempt
  count, 1 to 3 per offer, as Y:5429 has it, and the record's `out`, already
  a key at Y:5431, names the attempt's own file, where the outer and the
  guard find it and the facts file sits beside it. Case 11's check of
  `bind`, the guard's three attempts per offer (Y:5503–5504) and `bind=k+1`
  at the next `2` are unchanged. The facts file is `out=`'s
  `<offers>/<uuid>.bind.<k>` stem with `.facts`. The outer, the body and the
  guard's sweep all find it from `out=`, never from `bind`; where the body
  cannot read the record itself, the outer hands it that path.
- **Whose:** lane 3's ruling, (a), #165 6751818233, on lane 5's 6751803196.
  It corrects lane 3's 6751772589, point 18, question 1, which had tied the
  files' index to `bind`. T501's draft 4 records it. The facts file's name:
  lane 3's ruling, #165 6752064118, on lane 5's 6752026548.
- **Source:** Y:5394; Y:5429; Y:5431; Y:5503–5504.

### ERR-44. A `psql` older than 15 is refused (T106)

- **The erratum:** Y:1417's fallback, "A client with psql older than 15,
  which prints only a Query's last result, reads the parts instead as one
  `COPY` of tagged rows", is not built. A `psql` older than 15 is refused
  with 69, naming its version, and nothing is sent, as FR-012, `plan.md:110`
  and T106's line require, except `journal --write-id`'s lookup, which ends
  3 at once with nothing sent (DEC-33). The test plan's oracle at
  `test-plan.md:3719–3721`, "With the fake's `psql --version` at 14, the
  snapshot is one `COPY` of tagged rows, with no `BEGIN`", reads the same:
  the fake's `psql --version` at 14 gives 69, one `--version` call and no
  request. Lane 2's `[snapshot-psql-14]` case (T257) follows it in F9, and
  contract drafts 4.11 to 4.13 state it at their :234; draft 4.13 states
  the lookup's 3 at its :618.
- **Whose:** lane 3's ruling, #165 6752697506, on lane 1's point 1 in
  6752661856.
- **Why:** no SHALL in the change's spec requires the fallback, spec.md's
  assumptions already have `psql` 15 or later on every workstation, and
  `ready`'s check 2 asks for 16 (ops:445), so the fallback could only serve
  a workstation that fails `ready`.
- **Beside it:** `serve` with a `psql` older than 15 stamps `no-psql` and
  exits 2 before it listens, as with no `psql` (DEC-31, OP-10); and the
  test plan's no-`psql` oracle reads the same (ERR-45).
- **Source:** Y:1417; `test-plan.md:3719–3721`; ops:445.

### ERR-45. With no `psql`, `serve` exits before it listens (OP-10's open half)

- **The erratum:** `test-plan.md:2028–2030` says that
  `test_the_service_reaches_the_database_only_through_psql` checks that,
  "with no `psql` on its `PATH` it answers `refuse`". It reads: "with no
  `psql` on its `PATH`, `serve` stamps `no-psql` and exits 2 before it
  listens, and the client reads directly". So lane 2's T237 keeps the
  test's first half, every answer from the database a call in the fake's
  log; its no-`psql` half asserts the stamp and the exit before any socket
  exists, then the client's direct read answering.
- **Whose:** lane 3's assembly reading, #165 6754633102, on lane 2's
  6754620468. It changes no SHALL.
- **Why:** Y:3926–3931 names `no-psql` among the causes that make `serve`
  exit before it listens, so a `serve` with no `psql` never answers; DEC-31
  reads `no-psql` as "no usable `psql`".
- **Not covered:** a `psql` lost after the start. Y gives it no answer,
  and no SHALL needs one, so 007 adds no case for it. If lane 5's T511
  states a behaviour for it, T237 may follow in a later round.
- **Source:** `test-plan.md:2028–2030`; Y:3926–3931.

### ERR-46. `lane-start:1555` and `lane-end:955` refuse `managed-projection`'s 1, not "pass the code" (Y:5730)

- **The erratum:** Y:5730's row and the test plan's list of "the arms that
  already refuse, which pass the code" (`test-plan.md:2832`) both name
  `lane-start:1555`. That arm reads `managed-projection`, a
  projection-class verb, whose failed read answers 1, so `lane-start`
  exits 1, UNKNOWN, as T402 is built. It is never 69. So
  `lane-start:1555` moves from "pass the code" to "refuses 1, UNKNOWN (S4;
  DEC-27)". `lane-end:955`, beside it in the same list
  (`test-plan.md:2834`) and in Y:5734's `lane-end` row, moves the same
  way: its `seam_refuse` has `lane-start`'s shape, 0 refusing as managed,
  8 passing and every other status dying UNKNOWN. The rest of both rows
  and of the list stands. Lane 2's cases drop the stubbed 69, 75 and 3 at
  `:1555` and `lane-end:955`, and drive the real 1 (T217, T275; lane 2's
  `lane-end-955-1` stays).
- **Whose:** lane 3's ruling, #165 6754439281, item 6, on lane 2's NOTES
  30. It applies DEC-27 with spec:626 and spec:692, and it adds no SHALL.
  `lane-end:955`: lane 3's ruling, #165 6755040648, on lane 2's 6755019553.
- **Source:** Y:5730; Y:5734; `test-plan.md:2832–2835`.

### ERR-47. F17 reads `lanes/STORE` with `git ls-tree`, not `cat-file -e` (Y:4946)

- **The erratum:** "Y:4946: F17 reads `lanes/STORE` with `git ls-tree`, not
  `cat-file -e`. A tip git cannot read is a failed read, never 'no marker',
  and ends by class as for a marker (Y:4948)." `git cat-file -e` returns 128
  both for an absent `lanes/STORE` and for a tree git cannot read, so F17 as
  Y words it reads a failed read as "no marker", which fails open. Ending
  by class as for a marker is decide 69, guard 2, mirror reads on with its
  line, and projection 1. T504's F17 shares T419's `ls-tree` read, and
  whichever of T419 and T504 lands first defines it, as R45 has it for
  `REG_MODE`.
- **Whose:** lane 3's erratum, #165 6754859418, on lane 5's 6754850098,
  after lane 3's ruling #165 6754806910, item 4, on lane 4's T419 question
  4 (#165 6754561694). It keeps Y's own rule that a failed read is never an
  answer (Amendment 7(d)) and Y:4948's ends by class, so it is an erratum
  to a mechanism and changes no SHALL.
- **Source:** Y:4946; Y:4948; lane 4's T419 question 4 (#165 6754561694);
  lane 3's ruling 6754806910, item 4; lane 5, 6754850098.

### ERR-48. `commit_write`'s step 6 deletes a changed line's structured rows (T112's gap)

- **The erratum:** Y's §1.3 step 6 (Y:1463) and §1.3.1's step 6 (Y:1825)
  gain this, as `commit_write` already derives the touched keys (Y:1481):
  "As a `replace` or `delete` changes a line, delete that line's structured
  rows: `lanes` by `row_line_id`, `log_lines` and `register_lines` by
  `line_id`, and `object_holds` by `(file, ordinal)`. A `remove` deletes
  those rows for every line of its path. A `rename` deletes the old path's
  `object_holds`, and moves `log_lines.file` and `log_lines.lane` to the new
  file's lane. A `rename` also moves a lane-kind object to `lane:` and the
  new file's lane, as `parse_log` derives it. Step 7 then inserts what the
  entry sends. An entry carries no deletes." Step 7 (Y:1464) and the
  entry's `object_holds` comment (Y:1610–1611) change with it (#165
  6755386771). Where step 6 deletes a lane's hold and the entry sends no
  hold on that object (case 6a), step 7 still re-marks `superseded_by` on
  every stored row of the object, as for an object the entry sends, and
  stamps no `seq` or `mode`; step 5a adds `objects:` for every lane holding
  an object whose hold sits on a line the entry replaces, deletes, removes
  or renames, so that the flip is reported (lane 3's ruling, #165
  6771318480). Step 5a still runs first, before step 6 deletes anything: it
  reads the old side, those holds included, so the scopes gain their lanes'
  `objects:` keys (case 6a). Where the re-mark leaves two non-exempt claims
  live on one object (`edge-two-live`), `one_live_claim` refuses with LR005
  `one-live-claim`, and nothing is written: only an import sets `exempt`
  (Y:1232, Y:1948). The way out is T324's `render --revert <sha>` (lane 3,
  #165 6778890211, T107's `edge-two-live`). T107 writes it, and T112 no
  longer relies on the order it sends rows in.
- **Why:** without it, every `rename-lane` on primary ends 69. Y:1193's
  `DEFERRABLE INITIALLY DEFERRED` binds only the `REFERENCES` clause, so
  `row_line_id`'s `UNIQUE` is checked row by row, and the rewritten row
  meets 23505 while the old row still holds the line. A rename also left
  the old lane's `object_holds`, and a `delete` shifted only the holds below
  the deleted line. Deferring the check, (b), does not fix renames. Deletes
  a client sends, (a), would have the database trust them, where Y's
  touched keys are "never sent, so no client can under-report them"
  (Y:1481).
- **A backstop,** lane 1's choice in T107: `row_line_id`'s `UNIQUE` made
  `DEFERRABLE INITIALLY IMMEDIATE`, as `doc_lines_pos` is (Y:1185), only
  where that constraint is never an `ON CONFLICT` arbiter.
- **Y's limitation, recorded beside it:** there is no insert op, and none
  is added, since every replayer of `journal_edits` would need one and no
  verb adds a line before a file's end. A stray that does rewrites every
  later line and re-stamps `written_seq`, the mode and the workstation on
  lines whose text did not change; step 8a then over-reports scopes, which
  only refuses more (Y:1496). `LANES.md` is 6,716 lines, so a stray near
  its top would be about 6,600 replaces under the commit call's 5 s
  timeout. T324's runbook already prefers the revert (PC-4).
- **Whose:** lane 3's ruling, (c), as an erratum, #165 6755386771, on lane
  1's 6754969723; its `rename` clause for a lane-kind object is lane 1's
  point 1 on erratum (c), `5e04dec`, which lane 3 accepted (#165
  6768006915); case 6a: lane 3's ruling, #165 6771318480, on lane 1's
  6767113440, point 2. It changes no SHALL, and it makes spec:147–166 hold.
  It moves no ratified decision, since `commit_write` stays free of verb
  rules (Q7's A″), and migration 1 exists on line R only, so it changes in
  place.
- **Source:** Y:1463; Y:1464; Y:1610–1611; Y:1825; Y:1481; Y:1193; Y:1185;
  Y:1496.

### ERR-49. Four of Y's lines on the derived rows, read as T112 and T115 build them (T112's reading 7)

- **The errata:** T112 and T115 derive the structured rows with one set of
  helpers, and four of Y's lines read:
  - Y:1222: `object_holds` holds no `lane:` object, as #160's index does
    (`code/lanes-index:1594`);
  - Y:1254: `register_lines` holds the lines that have a kind, not every
    non-row line; nothing reads a row that has no kind;
  - Y:1197 and Y:1396: `owner` is sent NULL by the import and by writes
    alike, since S4 answers from the row's own text; otherwise the column
    would change between the import and the first write;
  - the import keeps a NULL workstation rather than take the
    `--workstation` fallback, since files mode allows the NULL (Y:1273).
- **Also in reading 7,** and not errata: `repo_key` takes Rule 6's order,
  the shipped `repos.tsv` and then the per-wip one, the last row winning,
  as `rule6_aliases` reads them, so that F13's equality (Y:1989) cannot
  answer `expired-none` for an imported `LANDING`; and `spellings` are
  kept on CLAIMED and TAKEOVER only, as Y:1231 has it.
- **Whose:** lane 3's ruling, #165 6755386771, reading 7, on lane 1's
  6754969723.
- **Source:** Y:1222; Y:1254; Y:1197; Y:1396; Y:1273; Y:1989; Y:1231.

### ERR-50. The in-doubt lookup is four statements, not three (T114)

- **The erratum:** `test-plan.md:3716–3717` and `:7338–7339` spell the
  lookup as three statements, `BEGIN`, `find_write` and `ROLLBACK`. Y makes
  it four, sent as one request (Y:5235): `BEGIN`, `find_write`, the `COPY`
  of the found seq's `journal_files`, which the client reads "in the same
  transaction" (Y:5300), and `ROLLBACK`. A one-statement join would read
  `journal_files` in a snapshot taken before `find_write`'s wait, and miss
  an orphan's rows committed during it, so the four are required, not only
  allowed. Lane 2's T257 `[lookup]` case expects four.
- **Whose:** lane 3's ruling, #165 6755922262, reading 2, on lane 1's
  6755628366.
- **Source:** Y:5235; Y:5300; `test-plan.md:3716–3717`;
  `test-plan.md:7338–7339`.

### ERR-51. A launch with no transcript uuid is not yet a bind (T428)

- **The erratum:** spec:318's bind is the act that writes the `STARTED` or
  `RESUMED` line; until then W1, or W2–W6, stay separate verbs with
  Y:5650–5653's expectations. spec:318 is unchanged.
- **Why:** at lane 4's `692dea2`, a `lane-start` launch whose `lane_sid` is
  no transcript uuid writes no `STARTED` or `RESUMED` line (Amendment 7(b)),
  and the line and the stamp go to the first act that knows the uuid. Each
  of W1, or W2 to W6, carries its own expectation and its arm until then.
  Were spec:318's bind read as covering that launch, the exception would be
  Brett's.
- **Whose:** lane 3's ruling, #165 6770733371, T428's question 1.
- **Source:** spec:318; Y:5650–5653.

### ERR-52. `lane-worktrees:508–524` exits 2, naming the read's code (N1)

- **The erratum:** `test-plan.md:2901–2902` has `lane-end:930–938` and
  `lane-worktrees:508–524` refuse 69, 75 and 3 with the verb's own code. At
  `lane-worktrees:508–524`, `canon_lane`'s arm instead exits 2, naming the
  read's code, and never passes it. `lane-end:930–938` is unchanged.
- **Why:** Y:5722 defines "Pass the code", and Y:5739 says only that the arm
  refuses 69, 75 and 3, where `lane-start:1154`'s row adds passing the code.
  A passed-through 3 would read as "something to retire" at `lane-end`'s
  gate (`lane-end:2102–2106`).
- **Whose:** lane 3's ruling on lane 2's N1, #165 6770688147. T404 carries
  it, and lane 4 changes nothing.
- **Source:** Y:5722; Y:5739; `test-plan.md:2901–2902`.

### ERR-53. A slot line Rule 6 cannot pair, and a `LANDED` naming no repository (T112, T115)

- **The erratum:** Y:1409's no-slot-role rule extends to every slot kind for
  a missing lane or repository, and to a landing slot with no PR. A
  repo-less `LANDED` is stored with its pair's `repo_key`, by Y:3383.
- **Why:** in brett-wip `c445b07` (`LANES.md` blob `aff13af6`), 375 slot
  lines name no repository: 356 `LANDED`, 6 `HOLD` and 13 `HOLD` closes, the
  last on 2026-10-10. As built, an import of it would end 23514 at line 142.
  Without the pairing, by lane 3's model, which was not run on PostgreSQL,
  an import leaves 286 `LANDING`s open on 14 repositories, where `RULE6_AWK`
  at `12ff809` leaves none; with it, none stay open.
- **Whose:** lane 3's ruling, #165 6771569269, on lane 1's 6771151979. It
  keeps Y:3383's parity, so it is not Brett's.
- **Source:** Y:1258; Y:1269–1270; Y:1409; Y:3383; Y:5141.

### ERR-54. At its 16-pass cap the renderer also re-forks for a rerun mark (T323)

- **The erratum:** Y:2899–2901 and Y:5784 re-fork the renderer at its
  16-pass cap only if its last pass made progress. It also re-forks once
  where, after it releases the lock, it finds a rerun mark: T321's promise
  of a pass after every write, the rerun mark's half of D14's handshake
  (Y:2896–2897; spec:485), wins.
- **Why:** lane 3's review of T322 and T323 (its probe H, at `aaef9f4`): 15
  passes made progress, and during pass 16, which found nothing missing, a
  write and its mark landed. The holder ended 0 with no re-fork and the mark
  left, so that write stayed unrendered until the next write anywhere, or
  `reconcile`.
- **Whose:** lane 3's ruling on MUST-1 of its review
  (`REVIEW-007-T322-T323-aaef9f4`), built at lane 3's local `40a1f5e`. It
  keeps D14's handshake, so it is not Brett's.
- **Source:** Y:2896–2901; Y:5784; spec:485.

### ERR-55. The import stages in a transaction it rolls back (T115)

- **The erratum:** Y:3166–3172, ops:261 and FR-049 load the import "into a
  staging schema" (Y:3168) and swap "the staged tables in" (Y:3172). As
  built, the load is staged in one request that rolls back, from origin
  only, and the swap re-sends the same entry, write id, base and
  generation, which LR001 refuses if the head moved. Only `not_after` and
  the line ids differ, and the proof compares neither.
- **Why:** it cannot be built as worded. Y:2243's `REVOKE ALL ON DATABASE …
  FROM PUBLIC` takes away PUBLIC's default TEMPORARY. The writer gets back
  only CONNECT (Y:2244), plus "no INSERT, UPDATE, DELETE or DDL anywhere"
  (Y:2263). Running the import as the owner would break ops:259, which has
  the operator lane run it.
- **Whose:** lane 3's ruling, #165 6781033556, on lane 1's T115 at
  `abdaab48`. It keeps FR-049's order (load, prove, only then swap), F23
  (one request ending in ROLLBACK before any git act; Y:3159–3162) and S5,
  so it is not Brett's.
- **Source:** Y:2243–2263; Y:3159–3162; Y:3166–3172; ops:259; ops:261;
  FR-049. Batch 9 cites these lines at `8523aa0c` (design:2289–2309,
  :3205–3208 and :3212–3218), 46 below Y's.

## Part 2: decisions

### DEC-01. The service is built beside groups A and B, and the v1.2.0 tag waits for it (R35)

- **The decision:** `lanes-index serve` is built beside groups A and B, not
  last. The cutover and the v1.2.0 tag wait for it, since from Eagle the
  guard without the service takes about 1.2 to 2.1 s, which misses Brett's
  1 s.
- **Whose: Brett's.** His words, verbatim, typed in lane 3's session at about
  2026-11-08T20:33Z: "4A, 5A, 1 let them stand, 3A", as lane 3's #165
  comment 6726766577 quotes them. Option 4A, as lane 3 asked it in text:
  "Build it beside groups A and B, and the tag waits for it (recommended).
  X already says this." It changes his Q29 (a) answer, "the service is
  built last" (clar:473), by his own word. Lane 3 had ruled the move first
  (6724332510, 6726078173), and named it on the spec PR's list as a change
  to his answer.
- **Lane 3's rule beside it:** no service pull request lands before the spec
  PR (#165 6726556972).
- **Source:** svc:293; Y:4826; Y:3951–3956; clar:524–525.

### DEC-02. The launcher's window reads are met outside 007 (R41)

- **The decision:** spec:626's launcher half, the launcher's bare window
  reads of the mirror, offline, is a change in workBenches, which no 007
  task can carry. It rides the v1.2.0 workBenches re-vendor, whose scope
  gains it, and the release checklist names it. 007 meets the rest of
  spec:626.
- **Whose: Brett's.** His "5A" in the same words, "4A, 5A, 1 let them stand,
  3A" (#165 6726766577), for lane 3's option "A. Carry it in the v1.2.0
  workBenches re-vendor I'm already drafting (recommended)."
- **Source:** spec:626.

### DEC-03. TLS is enforced at load, at `ready`, and at the server (R09)

- **The decision:** FR-011's `sslmode=verify-full` and root file are enforced
  in three places:
  - `load_config` refuses, as a refused configuration, a `register_url=` or
    `reader_url=` at a TCP host that is not loopback, unless it carries
    `sslmode=verify-full` and an `sslrootcert=` that names an existing
    regular file; `sslrootcert=system` is refused there. A Unix socket and a
    loopback host (`localhost`, `127.0.0.0/8`, `::1`) are allowed, so the
    fixture's socket cluster and its 127.0.0.1 stand-ins keep working, and
    `reader_url=` is checked only where `register_url=` is set, so #160's
    files load as today. Its exit is a refused configuration's: 2, 3 on
    `journal --write-id`, and 1 under `register read --for guard`. Its case
    is `tls_at_load`, in lane 1's T104;
  - `ready`'s check 11;
  - the server's `hostnossl all all all reject`.
- **Whose:** lane 3's ruling R09 (#165 6726556972) left the load-time check
  to lane 1; lane 1 took it in T104 (its own decision), and lane 3 took
  lane 1's text in #165 6727201960.
- **Source:** Y:2324; ops:454; ops:144.

### DEC-04. The guard's second read stays at `n`; every other second read is at `n′` (O17)

- **The decision:** Y:4159–4161 (SVC-8) and Y:4900 (the phase plan) each
  hold in their own scope.
  - **The guard:** one prompt decides on one snapshot, so its second read
    resolves the log at the head it prefetched, `n`, and refetches nothing.
    It is form 4 of `register read`:
    `--for guard --at <n> --pid <pid> --cache <file> --lane=<key>`, with the
    generation from `.head`.
  - **Every other primitive on primary** may run long after a prefetch, so
    its second read is form 3, `register read --since <n>`, at the current
    head `n′`, refetching the cached family there. Y:4900's "SVC-8's rule"
    covers only "never a git read".
  - `journal --since` does not serve the second read: it serves the
    journal's history (Y:4834).
- **Whose:** lane 3's ruling, #165 6727201960. Lane 1 picked form 4's argv
  (6727229519), lane 5 confirmed it with B1 (`--pid`) and B2 (`--cache`) in
  its §3.6 review (6738431117), and lane 1's contract draft 4.1 carries it.
- **Source:** Y:4159–4161; Y:4900; Y:4834.

### DEC-05. Form 4 fails open on every end but 0 (B4)

- **The decision:** form 4 ends 0 with the one log's files written. Every
  other end, "too late" included, is a log that could not be read: the read
  site returns non-0, and the guard notes it, as it does today, and does not
  refuse. Form 4 writes no `.outage`, never opens the mirror path, reads no
  declaration, and caps at `T0` + 3.75 s. The prefetch, form 2, keeps its
  "too late" refusal with 2.
- **Whose:** lane 3's ruling B4, #165 6738474549, taking lane 5's text word
  for word. Lane 3's reasons: the refusal belongs to the prefetch; form 4 is
  an advisory read, which today's guard does not refuse over; failing open
  answers a handoff request one prompt late; and the cap ends form 4 before
  the hook's 5 s.
- **Where it is proven** (lane 1's block v4.1, on lane 5's check, #165
  6739068493): form 4's "too late", a start at or past `T0` + 3.75 s, is
  proven in lane 5's T507's own verify, beside its form-2 test for "too
  late", since only T507 has `T0`, the clamp, the cap and the floor. Lane
  1's T113 ships the bounds: `--pid`, the cap at `T0` + 3.75 s and D9's
  guard row. Its case `guard_second_read_at` keeps items 1 to 8, and item
  9's ends 69, 78, 1 and 64.
- **Source:** Y:4269–4271.
- **The SHALLs, changed:** at Y the service spec's budget requirement said
  that a guard's client starting at or past the direct read's cap "SHALL
  exit as "too late to verify", and the guard SHALL refuse with 2"
  (Y-svc:124), for any client, and a guard's read that failed was a 2 in
  Y-svc:23, Y-svc:104, Y-svc:124 ("A database that does not answer SHALL be
  a 2 inside the hook"), Y-svc:174 and the declared outage's check
  (Y-svc:188), and in the sql spec's Y-spec:87, Y-spec:188 (the guard in the
  write requirement) and Y-spec:574; Y-svc:30's scenario lost the database
  with no word of when. Four commits on this branch after `aa190a7f`,
  `dabd74bd`, `ce3fad83`, `ad82c2f4` and `04a9de6b`, on lane 3's ruling B4:
  - scope each of those refusals to the guard's prefetch, its first read;
  - add that the second read, started at or past `T0` + 3.75 s whatever a
    declaration says (B5), exits as "too late to verify", and that it fails
    open on every end but 0, in B4's words: every other end is a log that
    could not be read, which the guard notes and does not refuse for;
  - name the prefetch's direct read in the declaration's sentence;
  - scope the scenarios "A guard that starts too late" and "A read held
    behind a lock" to the prefetch, and add "A second read that starts too
    late" and "A second read that cannot read the database";
  - say in Y-svc:30's scenario that the database is lost "before the prompt".

  No spec delta is left. ERR-21 records the design's six passages that
  still read as a refusal for any late or failed read.

### DEC-06. `register read`'s keys take the `=` form (L1-D1)

- **The decision:** `register read`'s `--lane` (forms 2 and 4) and
  `--log-key` take their value only as `--lane=<key>` and `--log-key=<key>`,
  so that a key opening with `-`, which is outside `check_lane_name`'s
  class, is never read as a flag. The parser takes everything after the
  first `=` verbatim. It applies to those two flags only: group B's
  `--lane <lane>`, on `register snapshot`, `register commit` and
  `journal --write-id`, keeps the interfaces table's form (Y:4834).
- **Whose:** lane 1's own decision, as the contract's author, in draft 4
  (#165 6738871108).
- **Source:** Y:4834; Y:4956, which sends a key outside the class down
  the second-read path.

### DEC-07. LR004 under the scratch tree (R02)

- **The decision:** on LR004, `register commit` re-reads each base from SQL,
  bypassing the image cache, and resends nothing. A moved head is 75; a base
  that differs, of any file the entry reads from `tree/`, is 69, naming the
  image cache; equal bases are 69, naming the entry and LR004's own message;
  and a failed re-read gives 69.
- **Whose:** lane 3's ruling R02 (#165 6726556972), taking lane 1's form (its
  O2), with lane 4's CO-4. The resend is dropped by lane 3's 6755615338, on
  lane 1's 6755590769: the rebuilt entry was the first byte for byte, so with
  the head still at `n` it could only meet LR004 again. A failed re-read
  gives 69 by lane 3's 6755386771, reading 2; lane 3's 6768006915 trims "or
  recompute" from it, after lane 1's T112 follow-up.
- **Source:** Y:1567; Y:2614; Y:2516.

### DEC-08. `register classify` is a subcommand beyond the interfaces table (R04)

- **The decision:** `append-line`'s three refusals on primary use
  `lanes-index register classify`, the one classifier, which prints for each
  register line the fields `parse_register_line` gives. A bash classifier in
  `lanes-edit.sh` would be the second classifier the design warns of. Lane
  1's T122 adds it, with a parity test, and lane 4's T424 lands after T122.
- **Whose:** lane 3's ruling R04 (#165 6726556972), on lane 1's O6 and lane
  4's Q-L3-9.
- **Source:** Y:5116; Y:6247; spec:1007; the interfaces table
  (Y:4831–4834), which does not list it.

### DEC-09. D12 governs register writes (R16)

- **The decision:** "A primary write makes no git fetch" (D12) is about
  register writes. A git act whose paths are all under `handoffs/`, as the
  publish act and `rename-lane`'s handoff move are, keeps S3's fetch.
  `commit`'s publish form keeps slice 1a's routing by `LANES_LANE`, and lane
  4's T420 refuses at the push a commit that changes a path outside
  `handoffs/`.
- **Whose:** lane 3's ruling R16 (#165 6726556972), on lane 4's Q-L3-1.
- **Source:** D12 (Y:522); spec:311; spec:169.

### DEC-10. Where the writers' mode ask runs (R17)

- **The decision:** each writer process asks the mode once, before slice
  1a's routing. On primary it skips `lane_checkout_enter`, takes `<c>` from
  `lane_checkout_path` with no S3 work, and refuses with 2, naming
  `lanes-edit.sh lane-checkout <lane> --create`, which makes it,
  where that checkout does not exist, an explicit `LANES_REPO` included. In
  files and `index` mode it routes as today, and carries the answer across
  1a's `exec` as `LANES__MODE`, with the `LANES__PID` check. `reg_open`
  takes the same answer. Lane 4's T409 adds the carry, so slice 1a does not
  change.
- **Whose:** lane 3's ruling R17 (#165 6726556972), on lane 4's Q-L3-2; the
  carry's place, lane 3's #165 6726831985; the command the refusal names,
  lane 3's #165 6769061039, T415's question 2; `LANES_REPO`, lane 3's #165 6841265575.
- **Source:** Y:4983; Y:5452; Y:4858; D12 (Y:522).

### DEC-11. `rename-lane`'s handoff half runs in the parent (R18)

- **The decision:** on primary, after `committed`, the parent moves the
  handoff and writes its stamp in `<c>`, outside the flag, as a git commit.
  It is not a second register write, so the one-entry rule holds.
- **Whose:** lane 3's ruling R18 (#165 6726556972), on lane 4's Q-L3-3.
- **Source:** spec:369; Y:5092; Y:5097.

### DEC-12. A takeover whose snapshot failed waits on the record's clock (R26, point 36)

- **The decision:** a takeover whose own first snapshot failed passes
  `journal` the clock of the record's newest `call` line as `--clock`, and the
  whole seconds since the record's `updated_utc`, on this host, as
  `--clock-age`. The estimate lags the database's clock, which only lengthens
  the wait. "Calls at once" stays for a record from another host. ERR-22
  records the erratum this makes on Y:5236. Lane 1's T114 and contract §3.4
  take the two flags.
- **Whose:** lane 3's ruling R26 (#165 6726556972), taking lane 4's answer to
  lane 2's point 36 (6726296119).
- **Source:** Y:5236.

### DEC-13. The lookup's tries run start to start (R26, point 40)

- **The decision:** each try starts at least 1 s after the last try's start,
  never before the last try has ended, and may start up to and including the
  window's 10 s mark, on a monotonic clock finer than a second. It reads
  Y:5250 and keeps Y:5279's worst case. No text changes.
- **Whose:** lane 3's ruling R26 (#165 6726556972), taking lane 4's answer to
  lane 2's point 40 (6726296119).
- **Source:** Y:5250; Y:5279.

### DEC-14. A first-snapshot 69 names no id (R26, point 38)

- **The decision:** a new write whose first snapshot ends 69 has no id to
  name: its note says that nothing was sent, and the 69 removes its own
  claim. A run that had an id, from `LANES_WRITE_ID` or a record it took
  over, prints it again with its generation. This was the memo's one spec
  delta; commit Y carries it, so spec:318 and spec:338 now read "its
  generation included where the write has an id (a pending record, or
  `LANES_WRITE_ID`), or saying that nothing was sent where it has none".
- **Whose:** lane 3's ruling R26 (#165 6726556972), taking lane 4's answer to
  lane 2's point 38; the spec change is in Y (#165 6727039049).
- **Source:** spec:318; spec:338.

### DEC-15. The service's idle check and idle stop have test-only variables (R37)

- **The decision:** `LANES_REGISTER_SERVE_IDLE_CHECK_S`, default 60, and
  `LANES_REGISTER_SERVE_IDLE_STOP_S`, default 900, each honoured only where
  set, with a floor of 1 s, and the check capped below the load balancer's
  240 s idle reset. A value that is not a positive whole number is ignored,
  and the default kept (OP-14). The starter's reduced environment keeps both
  where they are set.
- **Whose:** lane 3's ruling R37 (#165 6726556972), taking lane 5's proposal
  (its open points 24 and 25), and lane 3's ruling on T511's OP-14 (#165
  6746215510) for a value under the floor.
- **Source:** the reduced environment (Y:3887–3888); the idle check
  (Y:3994); the idle stop (Y:3912).

### DEC-16. A sixth class, `writer` (R46)

- **The decision:** the read door's hygiene test holds every dispatcher arm
  in exactly one class. The write guard's verbs, `start-record` among them,
  ask the mode at the write guard, so they get a sixth label, `writer`,
  beside decide, mirror, guard, projection and none.
- **Whose:** lane 3's ruling R46 (#165 6726556972), on lane 5's open point 14.
- **Source:** Y:4892–4898; Y:4858.

### DEC-17. The `LANES_BIND_BODY` seam (R48)

- **The decision:** a test seam `LANES_BIND_BODY`, beside `LANES_LANE_START`,
  kept for the outer and unset in the body, which the outer runs in place of
  `"$RESOLVED" guard-bind --body`. The body's `set-row-state` goes through
  `LANES_EDIT` where it is set, else `$RESOLVED`, as `lane-start`'s own
  writes do.
- **Whose:** lane 3's ruling R48 (#165 6726556972), taking lane 5's answer to
  lane 2's point 39(b).
- **Source:** Y:5397.

### DEC-18. The pipe pair waits for phase 2 (R24, PC-2)

- **The decision:** no `psql` is kept open across an attempt's two calls in
  phase 1. Tests 9 and 4 decide it at phase 2; a cached module or lazy
  imports come first. The cached module comes now, in 007: Brett's "(d) Both (a) and (c) (Recommended)", 2026-11-13, picked in a dialog in lane openRepoTools-3's session and received by 22:32:28Z, takes PC-2's cached module, and the install ships it (T124, T347); the pipe pair still waits for phase 2.
- **Whose:** lane 3's ruling R24 (#165 6726556972), which PC-2 settles.
- **Source:** Y:5868.

### DEC-19. Where lane 2's open points 36, 38, 40 and 42 went

- **The decision:** lane 3 routed them to lane 4 as 007 tasks (#165
  6726110116), and took lane 4's four answers: DEC-12, DEC-13, DEC-14 and
  ERR-20. Lane 2's AT-F3, AT-D8 and AT-F10 follow them.
- **Whose:** lane 3's rulings, #165 6726110116 and R26 (#165 6726556972).
- **Source:** Y:5236; Y:5250; spec:318; Y:5218.

### DEC-20. Never a pooler (R51)

- **The decision:** every register connection reaches the primary's own
  endpoint directly, never through a pooler of either pool mode, since
  CNPG's PgBouncer drops the startup `options` that carry the bounds. X
  settled it; it is recorded here because lane 2's point 43 and lane 5's
  open point 20 asked it.
- **Whose:** lane 3's ruling, "never a pooler" (#165 6726110116), which X
  wrote in.
- **Source:** Y:1575.

### DEC-21. `outage declare` refuses a `--why` with a line break

- **The decision:** `outage declare` refuses with 2 a `--why` that holds a
  line break, so that the declaration's label, which the guard copies into
  `.outage`, stays on one line. ops §2.5 lists `outage declare`'s refusals
  (ops:490), and Y has no such refusal: this adds one. It was ERR-19.
- **Whose:** lane 1's own decision, as the contract's author, taking lane 5's
  O15 (#165 6738431117): lane 1's block v4, T121.
- **Source:** ops:490; D20 (Y:599).

### DEC-22. The delayed loopback is a relay in the standard library

- **The decision:** AT-D9c (d) and AT-F6's case 21 (T510) run through a
  relay in the standard library, between the client and the primary
  fixture's TLS port, which delays each byte by the delay the case names. It
  needs no root, and both cases keep the delay and TLS they name. The relay
  is lane 2's, a test fixture placed with lane 2's block. Lane 5's
  `tc netem` harness stays open as a bench-only check, never a CI gate.
- **Whose:** lane 3's ruling, option B, #165 6741861590, on the test plan's
  open point 23, the delayed loopback (lane 2's question 3 of 6722518182).
  It is not R35 or R36, which rule lane 5's own point 23.
- **Why:** option A needs root and a `sch_netem` no one has checked on
  GitHub's runner; option C leaves the guard's timing under a slow read, and
  libpq's connect path under delay, to act 3's rows. The relay delays the
  SSLRequest, TLS, the startup, SCRAM and each query round trip, the terms the design's figures count but TCP's handshake, which they count too (design:5921) and the ruling named, and which T229's relay does not delay, since it connects upstream at once (lane 5, #165 6852197902; lane 3, #165 6858923154).
  It models no slow start and no loss: lane 5's netem run and
  act 3's rows cover those.
- **Source:** the test plan's open point 23 at Y (section 5.6); T510.

### DEC-23. The bootstrap renders migration 1 for the configured schema (T107's scratch schema)

- **The decision:** the bootstrap renders migration 1 for the configured
  `register_schema=` (Y:2323), and Y's bytes stay the template (Y:1039–1040),
  so no erratum is needed.
  - Only whole identifier tokens are substituted, in `CREATE SCHEMA`, every
    `search_path`, every `lanes_register.` qualifier, and the role block's
    bare schema names after `ON SCHEMA` and `IN SCHEMA`, its 8 sites in
    Y:2243–2278 (#165 6748109738). Migration 1's own statements (§1.2,
    §1.3.1), which run inside the role block at Y:2242, hold none of the last;
    all 8 are the role block's.
  - The three role names, `lanes_register_owner`, `_writer` and `_reader`,
    stay as they are.
  - After rendering, the bootstrap refuses with 2 if `lanes_register` remains
    as anything but those three roles.
  - `register_schema=` is a plain lowercase identifier,
    `[a-z_][a-z0-9_]{0,62}`; anything else is the configuration's own refusal.
    It may not equal the index half's schema, `lanes_index` or the configured
    `schema=`, since that would meet #160's `schema_version` table, and may
    not begin `pg_`, PostgreSQL's reserved prefix (#165 6748109738).
  - The migrations table keeps Y's template digest as `script_sha256`, and the
    bootstrap prints the rendered script's digest beside it; no column is
    added (lane 1's 6746948770, its suggestion A, which lane 3 takes). Y's
    `migrations` table (Y:1051–1056) has only `version`, `script_sha256`,
    `applied_utc`, `by_lane` and `by_ws`, and a new column would change
    migration 1's digest. A run on `lanes_register` renders to the same bytes,
    so the two digests are equal.
- **Whose:** lane 3's ruling (a), #165 6746655077, on lane 1's question in
  6746601663; its last condition is lane 1's suggestion A in 6746948770, which
  lane 3 takes; its sites and refusals as lane 3's ruling (a), #165
  6748109738, widens them, on lane 1's T109 question in 6748099727.
- **Why:** it keeps Y:2323, :3175 and :6035 as they stand, and the rehearsal
  stays in the same database as Y planned. Option (b) would need three errata
  and drop `register_schema=`. Lane 2's `schema` module pins the rendering
  both ways: PENDING-L2: F10, which adds those tests after F9 (lane 2, #165
  6751920725); F8b's map has none yet.
- **Source:** Y:2323; Y:1039–1040; Y:1051–1056; Y:2243–2278; T109, whose task
  text blocks v4.7 and v4.10 carry.

### DEC-24. A 007 task widens to an existing test file only where its own change fails that test

- **The decision:** a 007 task widens to an existing test file only where its
  own change fails that test and no code form avoids it, and lane 2 reviews
  the hunk. T404 meets it: its move breaks the fake `lanes-edit.sh` and the
  report's text in `tests/test_lane_worktrees.py` and
  `tests/test_lane_worktrees_report.py`. T401 does not: its array form, headed
  by `--`, passes `repo_hygiene` with git's argv unchanged, and the scanner's
  false positive fails safe.
- **Whose:** lane 3's rule, #165 6747281902, on lane 4's T404 item 1 and lane
  2's NOTES Q23; lane 2 takes it for T404 (#165 6746979269).
- **Why:** the fix belongs with the change that needs it, and keeps that pull
  request green on its own. It follows Q-L2-3 (a), where lane 4's T417 adds
  its `_EXIT_3_CAUSES` entries and lane 2 reviews the hunk.
- **Source:** T404; T401; T417; T403's `failedit` re-point, which meets it (lane 3, #165 6851059362).

### DEC-25. A claim whose owner cannot be tested here counts as live for the seam's cap, then dead (T412's G10)

- **The decision:** where Y is silent, a claim whose owner cannot be tested
  on this host counts as live for the seam's cap, 2×(D+30) s, 120 s at D7's
  30 s (Y:5001) (`LANES_REGISTER_SEAM_CAP_S`), then as dead. Y:5220 gives a
  claim from another host or container that cap "since its pid cannot be
  tested here", and DEC-25 applies the same reason here. It covers two
  cases:
  - **a claim with no pid, or one that cannot be read:** the cap runs from
    the later of the file's mtime and the record's `updated_utc`;
  - **a claim whose pid lives but whose `pid_start` is empty,** so reuse
    cannot be told (lane 4's contract :176; code `:4441` at `fb2732f`): the
    cap runs from the later of the claim's UTC and the record's
    `updated_utc`.

  R49 is unchanged: `pid_start_of`'s exit 2 still counts as live, for every
  caller. T417's exit 3 gains a form for an untested claim at the deadline,
  one from another host or container included (Y:5220), which names the
  claim file and the UTC after which a run may take it. T412
  changes `wr_claim_create`, `wr_claim_live` and `wr_claim_take`; the
  contract's §4.1 and §4.3 rows change, and its §9's G10 row closes, citing
  ERR-42 and DEC-25.
- **Whose:** lane 3's ruling on T412's G10, #165 6750702819; a claim from
  another host or container in T417's exit 3, lane 3's #165 6769061039,
  T417's question 2.
- **Why:** as built, an empty claim counted as live for ever. Every later run
  of its command waited to its deadline and stopped, and on `start-record`'s
  digest it blocked every bind of the session (Y:5515). Nothing cleared it.
- **Source:** Y:5184; Y:5189; Y:5220; Y:5515.

### DEC-26. A record's claim files are the owner's and those below it (T412's G11)

- **The decision:** Y:5193's "with its claim files" means the owner's claim
  and those numbered below it. A higher claim belongs to a later run that
  judged the owner dead; removing it would make that run fail at every
  record write, and would let a third run take `take.0` beside it. A claim
  left behind never blocks: a dead one is passed by `take.<k+1>` (Y:5187),
  and that owner's removal clears it. The one claim that could block was
  G10's empty one, which DEC-25 now bounds.
- **Whose:** lane 3's ruling on T412's G11, as built, #165 6750702819.
- **Source:** Y:5193; Y:5187.

### DEC-27. The ownership seam reads the mirror in an outage, and `lane-start` refuses `managed-projection`'s 1 (a spec delta)

- **The decision:** under `LANES_READ_MIRROR=1`, the ownership seam
  (`lane-handoff:1728–1741` at code `ddc972d`) reads the mirror's marker,
  labels the read, and still refuses on 0 and 1. So with the primary down a
  handoff still writes its file (spec:692; R-A11-11), and an ownership nobody
  could establish is never read as legacy. With it, at `lane-start`,
  `managed-projection`'s 1 refuses as UNKNOWN, and 69 stays for the row
  count, `lane-last` and `canon-lane`. T405 and T406 set the variable at the
  seam, and `managed-projection` joins ERR-30's list there.
- **Whose: Brett's.** He chose "(i) Mirror read (Recommended)" by interactive
  choice in lane 3's session (transcript
  `83619980-06bb-4469-ae73-2a8c5e61981a`), answered at 2026-11-09T21:09:40Z,
  on his typed request at 21:03:13Z, "ask anything waiting on me in
  interactive multi choice". The question was his spec-PR list item 9. The
  option read: "Under LANES_READ_MIRROR=1 the seam reads the mirror's marker,
  labels the read, and still refuses on 0 and 1. With it you accept the
  YS:664/Y:3019 erratum: at lane-start, managed-projection's 1 refuses as
  UNKNOWN, and 69 stays for the row count, lane-last and canon-lane." The
  recommendation was lane 3's (#165 6746035594; 6747281902).
- **The SHALLs, changed** (round B8, in place, a spec delta with his
  approval, as B4's were):
  - spec:649 gains: under the variable, which `lane-handoff` and the handoff
    skill set at the seam, `managed-projection` answers from the mirror's
    marker, labelled, and those callers still refuse on 0 and 1;
  - spec:692 gains the seam's read of the mirror's marker, labelled, still
    refusing on 0 and 1;
  - spec:664 and its scenario's THEN, spec:670, read as his spec-PR list
    item 9 words the erratum: "…refuse before anything is renamed or
    written: with managed-projection's 1, UNKNOWN (S4), at `:1555`, and with
    69 at the row count, `lane-last` and `canon-lane`", spec:664 keeping its
    line cites after it; design:3019, the `lane-start` row of Y's table,
    reads the same.
- **Source:** Y-spec:649; Y-spec:664; Y-spec:669–670; Y-spec:692; Y:3019;
  `lane-handoff:1728–1741` at code `ddc972d`.

### DEC-28. Under `LANES_INDEX=off`, `sync` and `reconcile` keep #160's no-op, and `mode` always reads the configuration

- **The decision:** under `LANES_INDEX=off`, `lanes-index sync` and
  `reconcile` keep #160's no-op exit 0, even over a `mode=primary` file:
  FR-003's "every other command with 2" holds where a configuration is read.
  One condition: `lanes-index mode` reads the configuration whatever
  `LANES_INDEX` says, and answers `primary`, or this build's 78, wherever the
  file says `mode=primary`, so that a writer never takes files mode on a
  primary estate. T105 carries both; lane 2's test 14 gains the case of
  `LANES_INDEX=off` with a `mode=primary` file, which gives 78 from `mode`
  (F9's).
- **Whose:** lane 3's ruling, #165 6747957479, on lane 1's question in
  6747929926. Lane 3 reads it as a reading of spec:87, not a change to it.
- **Why:** under `LANES_INDEX=off` the two write nothing, so nothing is
  lost, and #160's switch stays as it is; on primary they are not the
  register's write path. And `LANES_INDEX=off` "must never skip the query or
  the renderer fork" (Y:5718), while `lane-worktrees` sets it on every
  `lanes-edit.sh` call.
- **Source:** spec:87; Y:5718.

### DEC-29. `append-line` refuses a `LANDING` take whose lane cannot be read

- **The decision:** on primary, `append-line` refuses with 2, before the
  lock, a `LANDING` take whose lane cannot be read, as a fourth text beside
  the three of Y:5114–5116, since a take that holds `main` with nothing able
  to close it would block every landing. Contract draft 4.10's §3.5 states
  it, with `register classify`'s `<lane>` field; T122 and T424 carry it.
  Files mode keeps writing and skipping such a line, as today.
- **Whose:** lane 3's ruling, (a), #165 6747761107, on lane 1's question 3 in
  6747719970.
- **Why:** taking the lane from the writer's row, (b), would let the slot's
  lane differ from the line's own text, so that a later `LANDED` or
  `WITHDRAWN` would close nothing. Refusing at the write fails closed and
  tells the writer, the same kind of check as Y:5115's refusal of a
  hand-typed `LANDING EXPIRED`.
- **Source:** Y:5114–5116.

### DEC-30. Form 3 exits 69 only for a key the call asked for (lane 1's N-g)

- **The decision:** `register read`'s form 3 exits 69 for a log that matches
  two render-set logs (C11) only where the call asked for that key, by
  `--files` or `--log-key=`. A cached member that the call names only
  because the family holds it, and that now matches two logs, is dropped
  from the family: its `.log.<key>` and `.logpath.<key>` are removed, so the
  first read that asks for it ends 69. Contract draft 4.11 states it at its
  :673, and drafts 4.12 and 4.13, in the branch since rounds B10 and B11,
  at their :674; lane 5's T511 draft 11 follows at `socket-api.md:485`.
- **Whose:** lane 3's ruling, #165 6752360829, on N-g of lane 1's U5
  re-check of T511's draft 4 (6752336451).
- **Why:** a read should not fail on a member it never asked for, and the
  ambiguity still surfaces at the first read that does ask. Y gives no rule
  for a cached member that turns ambiguous, so this is a decision, not an
  erratum.
- **Source:** contract §3.6 (draft 4.13's :674, C11).

### DEC-31. `serve` with a `psql` older than 15 stamps `no-psql` (OP-10)

- **The decision:** `serve` checks `psql --version` once, at start, before
  it listens. Where the version is older than 15, it stamps `no-psql` and
  exits 2, as it does with no `psql`. The client then reads directly, as
  after any stamp, and that direct read's own check refuses with 69,
  naming the version, and sends nothing (ERR-44), so the operator still
  sees the version. T511's §8.6 states the check, and T512 builds it in
  `serve`, both lane 5's.
- **Whose:** lane 3's assembly reading, (a), which was lane 5's
  suggestion, #165 6754338646, on lane 5's 6754249002. T512 builds it, by
  lane 3's #165 6768050756, point 3, on lane 5's 6767221832.
- **Why:** Y:3926–3931 stamps only the causes that make `serve` exit
  before it listens, from a fixed list, with "the UTC and that fixed
  cause, and no other text" (Y:3930–3931). A `psql` that cannot join a
  request's statements (contract :234) cannot serve, so `no-psql` reads
  "no usable `psql`", and Y's cause list stays as it is. Not (b), a new
  cause word, which would change Y's fixed list. Not (c), under which
  every request would fail late and the service's `unavailable` would
  hide the cause.
- **A decision, not an erratum:** Y:3929's list keeps its words, and Y
  gives `no-psql` no meaning past its name. Once ERR-44 drops Y:1417's
  fallback, Y says nothing of `serve` with an older `psql`, so lane 3
  chose among three options, as DEC-30 does where Y gives no rule.
- **Open:** whether one of lane 2's `serve` tests gains an older-`psql`
  case, and which task holds it, is lane 2's to say (6754338646).
- **Source:** Y:3926–3931; Y:1417; contract §2.3 (:234); ERR-44.

### DEC-32. T221's three departures from the test plan (lane 2's)

- **The decision:** lane 2's T221, `tests/test_sql_register_schema.py`
  (AT-MIR's phase-N cases, AT-VER and AT-LR010), departs from the test
  plan in three ways:
  - it sends its own migration 2 through an admin `psql` session in §1.5
    step 3's form, so that it can hold the migration open;
  - it does not cover AT-VER's "the guard exits 2, and
    `managed-projection` 1" (`test-plan.md:4491`), which belong to lanes
    4 and 5;
  - it makes two logins of its own, `t221_register_writer` and
    `t221_register_reader`, members of the two groups, which T228 may
    replace.
- **Whose:** lane 2's own, #165 6754462442, recorded here for round B9.
- **Where AT-VER's two exits are held** (round B9's check of tasks.md):
  both are lane 5's. The guard's 2 is in T507's side of the prefetch's
  exit mapping, "69: SVC-12's check, else 2 naming the store, the cause
  and the two ways out".
  `managed-projection`'s 1 is T504's class list, "projection: 1,
  UNKNOWN", and T506's `managed-projection` line, "1, UNKNOWN, on a
  failed read". Lane 4's T402 is a caller of the second: at `:1555`,
  `lane-start` refuses `managed-projection`'s 1, not "pass the code": it
  exits 1, UNKNOWN (S4; DEC-27; ERR-46).
  Whether a test of lane 2's asserts either exit for a reader outside the
  version window is not checked here.
- **Source:** `test-plan.md:4476–4509` (AT-VER); T221's line.

### DEC-33. The lookup's retried ends, its `psql` check and its cap (T114)

- **The decision:** the in-doubt lookup, `journal --write-id` (Y:5235):
  - tries again, beside Y:5250's three ends (a failed connect, a try cut
    by `T_L`, and a 55P03), on exit 1, a signal, an exit 3 with no
    SQLSTATE, an exit 0 whose answer cannot be read, and 57014;
  - ends 3 at once, with nothing sent, where `psql` is missing or older
    than 15;
  - cuts its wait at `C` + 2 s, counted from `journal`'s start;
  - does not try LR011 again, which is an answer (Y:5254).
- **Whose:** lane 3's ruling, #165 6755922262 (readings 1, 3 and 4, and
  57014 for lane 4), on lane 1's 6755628366.
- **Why:** under Y:5250, a try that ends without an answer is made again,
  and each of the extra ends is no answer. No retry can mislabel a write:
  an answer comes only from a try whose `find_write` took the head-row
  lock past `<n>`, and once a try holds it no call of the write can commit
  (Y:5264). 57014 is the server ending the lookup at its own bound
  (Y:3524), and its transaction ends and frees the lock. The cap never cuts
  a legitimate wait, since `not_after` is the clock plus `C` + 2 s; without
  it, a bogus `<n>` would wait without end.
- **Source:** Y:5235; Y:5250; Y:5254; Y:5264; Y:3524.

### DEC-34. Brett's four answers on Opensoft-Tenant#276, recorded for 007

- **The decision:** none for 007. Brett's four answers to #276's open
  decisions agree with `operations.md` item 8, the server's size
  (ops:164–178), and with its access line, a public load balancer whose
  source ranges are the engineers' addresses (ops:193), so nothing else in
  007 changes.
- **Whose: Brett's.** He was asked by interactive multiple choice in lane
  opsXfactory-6's session, and his answers were observed at
  2026-11-10T11:29:15Z. His answers, verbatim, as that lane's #165
  6766195257 quotes them:
  1. the engineers' source ranges (D7, task 6.1):
     "QA's 108.208.120.119/32 now (Recommended)";
  2. sizing (D12, task 5.1): "Contract example: 120 (Recommended)";
  3. where the Azure resources live (D14, tasks 2.1 and 3.1):
     "Security RG, beside Keycloak (Recommended)";
  4. the images (D15, tasks 5.1 and 5.2):
     "Import PG16 under codexfactory/ (Recommended)".

  That they agree with 007 is lane 3's reading, #165 6766219958. Lane
  opsXfactory-6's post says they are not a ratification of #276, or a
  merge word.
- **Source:** ops:164–178; ops:193; #165 6766195257; #165 6766219958.

### DEC-35. Brett's round-2 answers for Opensoft-Tenant feature 045, recorded for 007

- **The decision:** none for 007. Brett's four answers for feature
  `045-codexfactory-prod-register-database` (draft #290) govern that
  feature's acts, its IAM and its repository. None of them changes 007's
  code, its contracts or `operations.md`. His second answer keeps the dump
  identity at "write alone", which lane openRepoTools-1 confirmed in #165
  6779237137.
- **Whose: Brett's.** He was asked by interactive multiple choice in lane
  opsXfactory-6's session, after an independent review of 045's documents,
  and his answers were observed by 2026-11-11T01:44:06Z. His answers,
  verbatim, as that lane's #165 6784524017 quotes them:
  1. the point-in-time drill runs before act 3 step 2:
     "Before step 2, as ruled (Recommended)";
  2. the dump identity gets a custom data role holding only `blobs/write`
     and `blobs/add/action`: "Switch to a custom role (Recommended)";
  3. his IAM grants (T2.1) may be applied from phase 1's reviewed, unmerged
     head: "Yes, with byte proof (Recommended)";
  4. a CODEOWNERS line `/docs/codexfactory-register/ @brettheap`:
     "Add CODEOWNERS (Recommended)".

  That none of them changes 007 is lane 3's reading, #165 6807694145.
- **Source:** #165 6784524017; #165 6779237137; #165 6807694145.

### DEC-36. Brett's three answers of 2026-11-11, recorded for 007

- **The decision:** by his third answer, T346 takes the draft flow, and
  round B15 adds its two 006 files and its draft-flow bullet. His first and
  second answers need no tasks.md text.
- **Whose: Brett's.** He typed them in lane openRepoTools-3's session
  (pyBench-openRepoTools-3, window `@3`, pane `%3`), received by
  2026-11-11T22:42:44Z, answering lane 3's three numbered questions. His
  message, verbatim: "1a 2a 3a". Each answer is the option he chose, with
  its words as lane 3's reply showed them:
  1. "A standing landing word for 007 code PRs once spec#17 lands, each
     landing on an independent READY plus green CI through a merge queue.":
     "a. Yes (Recommended)";
  2. "Lane hours a day.": "a. about 18 h with prompt swaps (Recommended)";
  3. "T346's release flow. Feature 006 says each GitHub Release is made by
     hand, but T346 has the workflow create a draft holding the list, which
     you then publish.": "a. Accept the draft flow, and amend FR-034, FR-042
     and `data-model.md` (Recommended)"; its longer form, from the 01:49Z
     UPDATE: "Accept the draft flow, and amend FR-034, FR-042 and
     `data-model.md` in T346's spec PR (Recommended)".

  Lane 3's readings, its own and not Brett's:
  - 1a: It applies once spec#17 has landed. It covers each 007 code PR that
    has an independent review reading READY and every check green, landed
    through a merge queue. It is NOT a word to land spec#17 itself. Before
    1a, no merge word existed for the spec PR (lane 3's 11-10 note), and 1a
    gives none.
  - 2a: Lane 3 reads it as lanes working about 18 hours a day, across
    account swaps by prompt.
  - 3a: T346's spec PR amends feature 006's FR-034 and FR-042
    (`specs/006-release-pypi/spec.md`) and
    `specs/006-release-pypi/data-model.md`, including its job table's
    `asset` row. T346's `files:` in 007's tasks.md gains those two files, in
    round B15. Step 5 of the release procedure says to re-run `asset` before
    publishing if it failed, and never to publish without the list. That is
    the review's decision 3, which lane 3 had already accepted.
- **Source:** #165 6808879327; `RULINGS-brett-20261111-1a-2a-3a.md`.

## Part 3: the choices the design leaves to the plan

Lane 3 took PC-1 to PC-16 as plan.md proposed them, in its 007 call (#165
6724332510); each lane could object in its block, and none did. Each row is
lane 3's ruling.

| # | Choice | Taken | Source at Y |
| --- | --- | --- | --- |
| PC-1 | The image cache's form | Question 13's: content-addressed objects, per-seq manifests, a forward-only head under a short `fcntl.flock`, collection after 10 minutes, and the service writing through | Y:4960 |
| PC-2 | The pipe pair | not in phase 1; decided at phase 2 (DEC-18); its cached module is taken in 007 by Brett's "(d) Both (a) and (c) (Recommended)", 2026-11-13 (T124, T347) | Y:5868 |
| PC-3 | The renderer's batch and cap | 64 seqs a pass and 16 passes; the gap after six lost races accepted, and `reconcile` closes it | Y:5779 |
| PC-4 | Which way out the runbook prefers | both tools ship; the runbook prefers `render --revert` | Y:2933 |
| PC-5 | Test 14's two states | a copy of `lanes-index` with the constant rewritten, in the test's temporary directory; no environment override | Y:4872 |
| PC-6 | Test 3's agreement form | in group C, with `cutover`'s push | Y:5808 |
| PC-7 | The rehearsals | one composed `exact-pins` run per group | ops:599 |
| PC-8 | Spec-leg paths in task lines | `../spec/<path>`, relative to the code worktree | — |
| PC-9 | Task IDs | ranges per lane, T1xx to T5xx, renumbered by `/speckit.tasks` | — |
| PC-10 | The codes of `import --at`'s refusal and the cutover's abort | lane 1's `contracts/exit-codes.md`; T102 proposes 2 for each | Y:3164; Y:3181 |
| PC-11 | The `psql` seam for fault injection | the lookup by `PATH`, stated in the contract | Y:6113 |
| PC-12 | Test 6's v1.1.0 writer | a digest-pinned copy under `tests/fixtures/`, governed | Y:5808 |
| PC-13 | Whether `--leave <from>` joins `start-record` | no: each verb keeps its expectations to one lane | Y:6064; Y:5554 |
| PC-14 | D9's values beyond the guard's | D9's table as written; lane 2's tests confirm each bound | Y:3501 |
| PC-15 | The CI cost of tests 9 and 10 | every trigger until measured, then lane 3 decides | Y:6084 |
| PC-16 | Governing all of `lanes-edit.sh` | yes, for 007, so its edits are `[O]` | — |

### ERR-56. Off primary, a resume's `start-record` 2 is Amendment 23's refusal (T428)

- **The erratum:** Y:5542 ("or any other 2", `die 2`), spec:318 ("or
  any other refusal that exits 2") and FR-029 ("any 2 at W7's `log` arm
  dies") hold under a seq base, on `mode=primary`. Under a base
  `<act-id>@files`, a `start-record` that makes no row and exits 2 is
  Amendment 23's refusal, with nothing written: `lane-start` keeps the
  writer's words, prints the note it prints for a resume's other codes,
  with the command that writes the record later, writes the Rule 3 stamp
  and launches, as W2 to W7's refusals did before T428. A `start-record`
  that makes the row still dies with 2, and the guard's child still ends
  `bind-failed` with `rc=2`. Under a seq base the write refuses without the
  lane's checkout (R17), so there the note on a checkout that could not be
  made does not say that the launch goes on (`12fe0e43:lane-start:4566`);
  under `@files` it says "This alone does not refuse the launch"
  (`:4564`). After an `@files` 2, `lane-start` runs `act-begin` again and
  dies 2 if the base is now a seq one, so a launch whose host flipped to
  primary in between does not take the refusal path
  (`12fe0e43:lane-start:5173–5196`; item 2 of lane 3's ruling, #165
  6867981064). Y:5542, spec:318 and FR-029 keep their words.
- **Why:** off primary no write reads `LANES_EXPECT` (Y:5596, Y:5609), so
  no 2 there is the second binding Y:5542 gives as its reason, and two binds
  that both read a lane free both land (design:5547, Y's :5500). The 2s a
  resume's `start-record` gives there are Amendment 23's: S1's failed clone,
  S3's refusals, a failed fetch among them (its ratified choice 2), and the
  router's before the clone. Of these S3 says that `lane-start` "prints a
  note with the reasons and the commands that write the record later, and
  the launch is not refused" (Amendment 8(d)), and its act 2's tests prove
  "every launch still exiting 0". Y:5542's rule entered with the arms row's
  "any 2 at `:4181` die, LR010's included" (Y:4689) and was written against
  `2a057a6d`, before act 2's code was read (Y:65; Y:6073); Y's differences
  from Amendments 22 and 23 for Brett (Y:696–729) list none from S3, and off
  primary only the bind as one write (Y:722). Under a seq base every 2 still
  dies, LR009's and LR010's included, so no second binding goes through.
- **Whose:** lane 3's ruling, #165 6867981064, on lane 4's cause B (#165
  6867422776). It narrows lane 3's own ruling behind Y:3029 and Y:5542
  (#165 6710770304, on lane 4's check of v2.5, 6711949237) so that it keeps
  Amendment 23 S3, which Brett Heap ratified on 2026-10-17 ("ratify 61";
  "apply the edits, 7-9 as recommended"), and it keeps every primary
  outcome, so it is not Brett's. T428's fix round builds it, in two commits
  on line R's merge99 `69e0e94c`: `47b7d15d` (lane 4, #165 6868674698) and
  `12fe0e43`, lane 3's wording of the note (lane 3, #165 6868697201; lane 4,
  #165 6869332549 and 6869523533). It joined line R in merge101
  `44f487d1`, as those two commits (lane 1, #165 6869553734).
- **Source:** Y:3026–3034; Y:4689; Y:5539–5544; Y:5731; spec:318;
  spec:332–334; FR-025; FR-029; tasks:1351;
  `lane-collision-protocol-amendment-23.md:7–10`, `:112–129`, `:231–233`,
  `:335–337`, `:424–428`; at line R's merge91 `78933393`,
  `lane-start:4529–4539` and `:5112–5130`, `lanes-edit.sh:12786–12788`,
  `:28313` and `:3677`, and the code leg's
  `tests/test_a23_lane_start_read.py:163–186` and
  `tests/test_a23_lane_start.py:170–188`, `:306–336` and `:368–394`; and,
  in T428's fix round, `12fe0e43:lane-start:4548`, `:4564`, `:4566`,
  `:5173–5196`, `:5210` and `:5247–5248`.
