# Council review: `add-sql-register`

The governed command's Phase 5 ("Resolve Verdicts",
`.claude/commands/opsx/propose.md:298–366`), run on 2026-10-31 by lane
openRepoTools-3's writer for opensoft/openRepoTools#165. It records the
cross-examination of the three seats' findings, each finding's verdict, and
what became of it. The council ran as one agent per lane, not as the lead's
subagents, so there was no council to shut down. It decides nothing that is
Brett Heap's: where a VALID finding needs his decision, the proposal carries it
as an option or an open question, with the reports' recommendations, and
chooses nothing.

## Inputs

- **The proposal reviewed:** `proposal.md` at spec commit `e2b28f5f`, sha256
  `dd20fb2a2785ddc53405e916d709e2cf5f81c30a04c51f3878435984b8522f15`, 1,300
  lines; `alignment-review.md`, sha256
  `966c99ac763ee0180687ac3ef8b78ffb9ac9dad16ea575334f6b8b66bbca5048`.
- **The product seat,** lane openRepoTools-1, #165 comment 6548737562: SOUND
  WITH CHANGES, 11 VALID (P1–P11) and 9 NOTED (P12–P20). Its report is
  `/workspace/projects/.openrepotools-private-prep/work-20261031-165/council-product/REPORT.md`,
  sha256 `8c6d39068fa6b651cbda3171f88e39799000947cc25863109fce247f3106d077`.
- **The architecture seat,** lane openRepoTools-4, comment 6548690085:
  technically sound, 3 VALID (concerns 1–3) and 1 NOTED (concern 4). Its report
  is
  `/workspace/projects/.lane-worktrees/openRepoTools-4/.tmp-coordinator/runs-165/SEAT-COUNCIL-ARCH-report.md`,
  sha256 `07af3e1ff670b727c6d1c3fc3c4989e964d8561fd2aa7593a4ab3ac886339b26`.
- **The adversary seat,** lane openRepoTools-2, comment 6548670312: a build
  plan but not yet a safety case, 7 VALID (ADV-C1–C7) and 3 NOTED
  (ADV-C8–C10). Its report is
  `/workspace/projects/.openrepotools-private-prep/work-20261022/lane2-adv-165-council-1031/ADV-council-report.md`,
  sha256 `e02d3e5de62b28baf0f9f81a63f91de904f56fb2d91c09160d09bd04df78199c`.
- **Texts read in full:** Amendments 22 and 23
  (`~/projects/new-workstation/home/.agents/protocols/`), the command's Phase 5,
  the three posts and the three reports.
- **Code re-read at `2a057a6d`,** in this clone's `code/`, for the facts the
  VALID findings rest on: `lanes-index:1306` (`PGCONNECT_TIMEOUT` defaults to
  5) and `:1317` (`timeout=300`); 0 hits for `statement_timeout` or
  `lock_timeout` in `lanes-index`, `lanes-edit.sh` and `openRepoTools`;
  `lanes-index:1540–1543` (`SyncLock`'s rerun mark); `lane-start:1145–1154`
  (the exit-66 arm, then `*)` carrying on with the name as typed);
  `lanes-edit.sh`'s arms `lane-last` `:17610`, `register-row` `:17925`,
  `register-row-local` `:17937`, `session-lane` `:17986`, `canon-lane`
  `:18016` and `resolve-home` `:18034`; `register_text_checked` at `:3597`;
  `rows_not_named "$lane"` at `:16520` and `resolve_home` at `:16509`;
  `commit_push` at `:2292`, `handle_preexisting` at `:2701`, `CP_AFTER_REBASE`
  at `:2429–2430`, and `LANES_REPO` defaulting to the workspace root at
  `:1053–1056`. Also `/usr/local/bin/lanes-edit.sh` in lane 3's container at
  2026-10-31T20:46Z: dated 2026-10-07, 17,046 lines, 0 hits for
  `STORE|mode=primary`, as the adversary seat found on py-bench. Not re-counted:
  lane 4's eleven call sites of `commit_push` and `handle_preexisting`.
- **Round 1 of Brett's questions:** Q2(a) and (b), Q6, Q8 and Q20, put to him
  on 2026-10-31 (asked at 20:07Z, corrected at 20:26Z), not answered when this
  was written. Their recommendations are not decided here; the council's
  positions are recorded under each.

## Cross-examination

The command has the lead consider, for each finding, whether another seat
would disagree. The seats read none of each other's verdicts, so what follows
is the lead's simulation, grounded in each seat's own evidence.

**The same finding from two seats,** applied once and credited to both:
concern 1 and ADV-C4 (no database path has a timeout, so the guard can fail
open); concern 3 and ADV-C6 (a render commit left on HEAD wedges the lane);
P1 and ADV-C9 (the "Why" argues from a rebase Amendment 23 removes).

**Would the product seat call the architecture seat's concerns
over-engineering?**
- Concern 1: no. A guard that fails open is the one defect Amendment 12(d)
  exists to prevent, and P3's outage path depends on the guard refusing in
  time.
- Concern 2: no. P2 accepts the split and only adds to its prerequisites.
- Concern 3: in part. Its private-index form removes the renderer's reason to
  wait for 2a and 2b, but P2 keeps act 4 waiting for them on other grounds
  (stranded stamps, #311's edit), so it buys less calendar time than concern 3
  implies. It also removes ADV-C6's wedge, so it stays the preferred form.
- Concern 4: no. P5 asks for the same baseline.

**Would the architecture seat call the adversary's cases too unlikely?**
- ADV-C1: no. It needs only a rollback during an outage, the case S5 itself
  names.
- ADV-C2: no. The stale copy is installed today.
- ADV-C3: no. Through `psql`, any lost connection is the case.
- ADV-C5: a one-call blip is rarer than a static outage, but its cost is a
  duplicate row, which Amendment 15(a) exists to refuse.
- ADV-C7: the architecture seat's own "Solid dimensions" call the head row
  rarely contended. That is a real disagreement on Q7, recorded there, and the
  burst test (test 4), not argument, settles it.

**Would the adversary say the product seat's scope adds hidden complexity?**
- P8's bootstrap needs a role-creating admin connection that clause (b) does
  not name; the proposal now says it is used once and held by nothing. P8's
  flip cannot write a read-only configuration mount (Q23(a)) from inside a
  bench; the proposal says it runs from the host. P8's readiness check is a
  check at one moment, which ADV-C2 shows a container started later escapes.
- P4 makes act 4 rest on the guard's direct database read (Q11(a)), one
  connection per prompt, so concern 1's timeouts move onto act 4's critical
  path.
- P3's question narrows ratified S3 and leans on Q4(b)(ii)'s spool proof,
  which ADV-C1 shows is unreliable where spools are per container.
- P13's (ii′) opens a window in which a writer that does not know the marker
  pushes files (ADV-C2).
- P14's recovery by re-import loses whatever SQL held and had not rendered
  (ADV-C1's mechanism).
- P6, P7 and P11 add tests, whose cost is suite time (R1: 16–62 ms per test
  database). P10's posting is an act in Brett's window.

### Conflicts left for Brett

Each is an open question in `proposal.md` with both positions, and is in his
round-2 block unless it belongs to round 1.

1. **The guard while the database is down (Q26, new).** P3 recommends that
   the guard answer from a mirror proven current. Against it: S3 as ratified;
   ADV-C1 (the proof fails where a spool is lost); ADV-C2 (a diverged mirror
   is not current). It pairs with Q11(a).
2. **The push ruleset (Q21(b)).** ADV-C2 makes it, or an equal server-side
   gate, a precondition of act 4. P18 defers it. R3 recommends it if the
   org's plan supports it.
3. **What act 4 and the renderer wait for (Q6, round 1).** P2: act 4 and the
   renderer's merge wait for 2a, 2b and 3, and #311 does not trail. Concerns 2
   and 3: the renderer's git half waits for 2a and 2b only if it commits on
   the lane's HEAD. R3's (a′): #311 and #308 trail.
4. **The Amendment 23 reading (Q27, new).** Concern 3 reads "takes S3's
   checks" as the fetch, for a renderer that never touches the lane's HEAD,
   index or working tree. Lane 3 agrees with the purpose, but the reading
   narrows ratified wording. S2 is written to work under either reading.
5. **The freeze (Q4(a)).** P13 prefers (ii′) where every environment is
   ready. R3 recommends (i). The adversary seat holds that (i)'s freeze
   answers its own earlier case.
6. **The database and Raven (Q22).** P14 offers the database on Eagle,
   recovery by mirror and re-import, and Raven upgraded but not configured.
   R3 recommends (a), with Raven configured, as Amendment 22's act 3 says.
7. **Act 4 and the service (Q29, new).** P4 recommends that act 4 wait for
   the service only if the measured direct guard is over a budget Brett names.
   #165's fold, "one holder instead of every session", reads the other way.
   This is a conflict with #165's text, not between seats.
8. **A rollback with the database down (Q4(b)).** ADV-C1's two remedies, against
   P1's "deciding reads that need no fetch": the first reads the last-fetched
   tip, which costs no fetch but can be stale. The council left the choice
   between the two remedies to the design, which resolved nothing, so it does
   not belong under "resolved on the evidence". After the fold, lane 2's probes
   showed both remedies leak, and Q4(b) puts the choice to Brett: (iii), a
   rollback that needs the database, or (iv), a fresh check of the marker on
   origin in every write.

### Conflicts resolved on the evidence

Each is the design's, not Brett's.

- **The report and recovery of a COMMIT in doubt.** P16 would look by seq and
  let the key follow; ADV-C3 requires a write id now. ADV-C3's evidence holds:
  seq `n+1` may be a rival's, and a blind retry of a claim gives a second
  `CLAIMED` or a false 69. P16 does not dispute the key, only its timing. So
  the write id is a requirement (S1), and P16's exit 3 stays the design's
  option for Q16's report code.
- **The exit code of "register busy".** ADV-C7 wants a code of its own. The
  council judged this the design's: Amendment 22's S1 gives 69 to "unreachable,
  or refuses the transaction", and a write that lost six optimistic races was
  neither, by the proposal's own sentence in its S1, "A serialization failure is
  not a refusal" (`proposal.md`, P218 at the commit that adds this file; the
  sentence is the proposal's, and Amendment 22's S1 does not say it). No text
  decides it and no seat opposes it. So the proposal's S1 now requires a code of
  its own, and Q15 and Q9 leave the number to the design. After the fold the
  narrowing of Amendment 22's S1 is put to Brett too: see "After the fold".

### Verdict changes

- **P15: NOTED to VALID.** Lane 3's brief folds P15 into Q8's option A as a
  refinement, and Q8 is with Brett now. Kept in `clarifications.md`, it would
  leave his round-1 option without it. The change is in Q8(b) only.
- **No other verdict changed.**
  - ADV-C9 stays NOTED: its proposal change is made under P1.
  - ADV-C10 stays NOTED: its outage-drill half is carried by P7's test 13,
    and its `repos.tsv` half is the design's.
  - P13, P14, P16 and P18 stay NOTED. Each is a position on a question; its
    option or view is cited beside that question so Brett's options are
    complete, and what the design must do is in `clarifications.md`.
  - No finding was DISMISSED.

## Dispositions

"P" is a line of `proposal.md` at the commit that adds this file. "APPLIED"
means the change is in as the seat wrote it, or in equivalent words. "APPLIED
IN PART" means the part no question holds is in, and the rest is the open
question named. "NOTED" means the finding is in `clarifications.md`, and
"cited" means its position also stands beside the question it bears on.

| Finding | Seat | Verdict | What changed, at `proposal.md` lines | Question for Brett it feeds |
|---|---|---|---|---|
| **P1** The "Why" claims for SQL what Amendment 23's act 2 already fixes | product | VALID, APPLIED | "What Amendment 23's act 2 leaves for this change", P42–61; #145 on a files-mode estate reworded, P840–843 | none |
| **P2** Act 4 also waits for 2a, 2b and 3; the skill's block is #311's | product | VALID, APPLIED IN PART | the three blocks and the slices that replace them, P253–255; Q6's round-1 positions, P1216–1230; Impact's skill block, P1709–1714; the re-homing's owner and date as **Q28**, P1619–1630 | Q6 (round 1); **Q28** |
| **P3** An outage refuses every prompt; no way out without a lane | product | VALID, APPLIED IN PART | the consequence, the refusal's text and the two ways out, P384–397; tests, P435–442; the guard-from-mirror question as **Q26**, P1578–1597 | **Q26** |
| **P4** Keep the service out of the write path and off act 4's path | product | VALID, APPLIED IN PART | Q2's round-1 positions, P1002–1012; Q11, P1384–1388; whether act 4 waits for the service as **Q29**, P1631–1644 | Q2(a) and (b) (round 1); Q11; **Q29** |
| **P5** Measure what the lane feels, with a baseline | product | VALID, APPLIED | tie to the gains, P59–61; service tests, P713–715; test 9, P752–757; test 10, P758–763 | none (Q10 is the design's) |
| **P6** Agreement test 11, a lane's day | product | VALID, APPLIED | test 11, P764–769; Q8(a)'s note, P1308–1309 | Q8 (round 1) |
| **P7** A runbook and a drill for acts 3 and 4 | product | VALID, APPLIED | the runbook, P797–805; test 13, the drill, P774–777 | bears on Q4(a), Q23, Q24 |
| **P8** Three operator tools | product | VALID, APPLIED | "Operator tools for acts 3 and 4", P512–537; act 3's note, P811–812; Q20's round-1 positions, P1499–1505; Impact, P1695–1702; Capabilities, P1672–1673 | Q20 (round 1); Q23 |
| **P9** The later `HOLD` or `LANDING` writer exits non-zero | product | VALID, APPLIED | S6, P565–570; S6's tests, P581–586; test 4, P739–741; Q17, P1458–1460 and P1468 | Q17 |
| **P10** Tell every lane what changes | product | VALID, APPLIED | "What changes for a lane", P787–796 | none |
| **P11** Atomicity proven on a real engine | product | VALID, APPLIED | S1's tests, P259–264; the real-engine list, P724–728; test 2, P734–737 | none |
| **P12** Brett's questions as one block | product | NOTED | none; the round-2 block follows it | shapes round 2 |
| **P13** Q4(a): (ii′) where ready | product | NOTED, cited | Q4(a), P1108–1117 | Q4(a) |
| **P14** Q22: database on Eagle, mirror as recovery, Raven not configured | product | NOTED, cited | Q22, P1535–1544 | Q22 |
| **P15** The `SWAPPING` lane's completion named in the 69 note | product | **NOTED, changed to VALID**, APPLIED | Q8(b)'s round-1 positions, P1330–1335 | Q8 (round 1) |
| **P16** An in-doubt COMMIT exits 3, naming the seq | product | NOTED, cited | Q16, P1446–1450 | none (Q16 is the design's) |
| **P17** CI's refusal switch scoped to the jobs with the binaries | product | NOTED | none | none |
| **P18** Defer the push ruleset | product | NOTED, cited | Q21(b), P1516–1524 | Q21(b) |
| **P19** The readiness check covers every writing environment | product | NOTED | none | none (the act 3 runbook) |
| **P20** The REGISTER-WRITER secret's reach | product | NOTED | none | none |
| **Concern 1** The guard can fail open when the database does not answer | architecture | VALID, APPLIED | "The 5 s is one budget, spent once", P682–695; tests, P705–710; test 10, P758–763; Q2, P1009–1010; Q11, P1386–1387 | bears on Q2(g), Q11, Q26 |
| **Concern 2** Q6(c)'s split runs through the seams 1a and 1b rewrite | architecture | VALID, APPLIED | option (c) split by seam, P1167–1202; Q6's round-1 positions, P1216–1230; the landing sequence, P919–923 | Q6 (round 1) |
| **Concern 3** A render commit left on HEAD blocks the lane | architecture | VALID, APPLIED | "Where it commits", P283–286; "The renderer leaves nothing on a lane's HEAD" and the one-render rule, P292–322; S2's tests, P368–372; test 5, P742–744; the reading as **Q27**, P1598–1618 | **Q27** |
| **Concern 4** Test 10's 50 ms depends on the client's form | architecture | NOTED, cited | Q29, P1640–1642 | none (Q2(g) and Q10 are the design's) |
| **ADV-C1** A rollback with the database down leaves the switch at `primary` | adversary | VALID, APPLIED | S5, P480–492; S5's tests, P508–510; test 8, P749–751; Q4(b), P1124–1130; Q26, P1588–1590; Q22, P1542–1544 | bears on Q4(b), Q22, Q26 |
| **ADV-C2** A stale writer stops the renderer; no way out of a divergence | adversary | VALID, APPLIED IN PART | the single-write import, the renderer's revert, the stale copy, P342–356; S2's tests, P372–375; test 6, P745–747; the readiness check's limit, P528–529; Impact, P1699–1700; the ruleset as a precondition of act 4, (B′) under **Q21(b)**, P1516–1524; Q4(a), P1112–1115 | **Q21(b)**; bears on Q4(a), Q26 |
| **ADV-C3** A COMMIT in doubt has no protocol | adversary | VALID, APPLIED | recovery by write id, P231–242; S1's tests, P267–268; test 12, P770–773; Q16, P1446–1449; Impact, P1700–1701 | none (Q16's code is the design's) |
| **ADV-C4** No database path has a timeout | adversary | VALID, APPLIED (with concern 1) | as concern 1; Impact's root `AGENTS.md` note, P1739–1741 | none |
| **ADV-C5** Six read verbs missing from S3's table; `lane-start:1154` fails open | adversary | VALID, APPLIED | the table's two rows, P418–419; the six verbs and `:1154`, P427–434; tests, P435–442; Q8(a)'s round-1 positions, P1300–1309 | Q8 (round 1) |
| **ADV-C6** A renderer killed between commit and push wedges its lane | adversary | VALID, APPLIED (with concern 3) | as concern 3; the pending hand edit's way out, P323–327; Q6, P1229–1230 | **Q27** |
| **ADV-C7** One contended head row; "busy" exits 69 like an outage | adversary | VALID, APPLIED | "register busy" with its own code, P223–226; S1's tests, P266–267; S6's tests and test 4, P584–585, P739–741; Q7, P1253–1254 and P1262–1268; Q15, P1433–1436 | bears on Q7 |
| **ADV-C8** `LANES_NO_FETCH=1` inherited by a renderer | adversary | NOTED | none | none (Q7's meaning of the variable is the design's) |
| **ADV-C9** The "Why" argues from a rebase that is going away | adversary | NOTED, cited | made under P1, P42–61 | none |
| **ADV-C10** No outage drill; `repos.tsv` read outside the transaction | adversary | NOTED, cited | test 13 carries the outage (under P7), P774–777; Q18, P1485–1487 | Q18 |

No finding was left unapplied: every VALID change is in, and where a part
needs Brett, that part is an option or one of Q26–Q29.

## Questions for Brett

- **Round 1** (Q2(a) and (b), Q6, Q8, Q20) is with him. The council's
  positions now stand under each: P-lines 1002–1012, 1216–1230, 1300–1335 and
  1499–1505.
- **New questions:** Q26 (P3), Q27 (concern 3), Q28 (P2) and Q29 (P4).
- **ADV-C7's code for "register busy"**: the council did not put it to him, as
  the number is the design's. The code narrows Amendment 22's S1, which gives 69
  to a transaction the database refuses, so after the fold Q7 and Q15 put the
  narrowing to him, with "keep 69" ("(A′) with 69") as his option. The product
  seat suggests 75 (sysexits' `EX_TEMPFAIL`) for the number, free in the exit
  table (`clarifications.md`).
- **Round 2** puts every other question for Brett in one block, its first
  option "all as recommended" (P12). It is lane 3's to send.

## Council Review Complete

**Concerns raised:** 34 in all: product 20 (11 VALID, 9 NOTED), architecture 4
(3 VALID, 1 NOTED), adversary 10 (7 VALID, 3 NOTED).

**VALID, proposal updated:** 22 (the seats' 21, and P15), 18 applied in full
and 4 in part (P2, P3, P4, ADV-C2), each part left open as an option or a new
question.
**NOTED, captured for design:** 12, in `clarifications.md`.
**DISMISSED:** 0.
**Verdicts changed:** 1 (P15, NOTED to VALID).
**Open questions added:** Q26–Q29.

This pass stops before `design.md`, `tasks.md` and the delta specs, which wait
for Brett's answers.

## After the fold

Three checks followed the fold at `cb364b95`: lane 2's probes of 2026-10-31 (#165
comment 6549116835), lane 2's check of the fold (comment 6549130353), and lane
1's product seat's check (comment 6549202251), with lane 2's point on Q26
(6549222590), lane 1's narrowing of its own recommendation (6549261651) and lane
4's condition on the busy code (6549262903). Lane 3 changed three of the
recommendations it put to Brett, and two commits apply all of it: items 1 to 22
in the first, items 23 to 25 in the second. This section maps each item to the
lines changed. It gives no verdicts; the counts above are the council's own. In
the tables below, "P" is a line of `proposal.md`, "C" of `clarifications.md` and
"CR" of this file, each at the commit that last changes this section.

### The three recommendations that changed

| Question | At the fold | Now | Why | Lines |
|---|---|---|---|---|
| **Q7**, how a write runs | (A), an optimistic head row; R3 and the product seat chose it | **(A″)**, (A)'s head row bounded by a deadline instead of six attempts; (A′), the head-row lock, with its caveat; (A), (B), (C) stay as options (first (A′) was recommended; the second commit moved it, item 23) | Under (A), 4 of 10 writers exhausted six attempts in every burst of a window of 40 ms or more; with no cap the k-th writer commits on its k-th attempt and none is lost. (A′) had 0 aborts in 120 writes, but holds the lock across the shell code, which needs a session held open, (C)'s mechanism, and bash 3.2 has no `coproc` | P1438–1523 |
| **Q4(b)**, a rollback with the database down | (ii), the spool proof; R3 and the product seat chose it | **(iii)**, a rollback needs the database; (iv), (ii) and (i) stay as options; (iii) narrows Amendment 22's S5 | The probes found (i) and (ii) both leak, including with the database up but unreachable from only the rolling-back clone | P1294–1319; P534–578 |
| **Q26**, the guard while the database is down | (a) from the product seat; lane 3 first recommended (b) from the probes | **(a′)**, the mirror only during an outage Brett declares; otherwise (b); (b) and (a) stay | From one workstation a down database and an unreachable one look the same, so P3's "no binding can change while writes refuse" holds only for an outage known to be estate-wide (lane 2, 6549222590); the product seat narrowed its own recommendation (6549261651) | P1867–1917 |

### Lane 2's check of the fold (6549130353)

| # | Item | What changed | Lines |
|---|---|---|---|
| 1 | **MUST, ADV-C1:** the rule at the old P484–492 offered two remedies that both leak | A rule conditional on Q4(b): under (iii) a rollback needs the database and sets the switch to `files` first, a database lost for good being a separate disaster procedure; under (iv) every `mode=primary` write checks `lanes/STORE` on origin by a fresh fetch or `ls-remote` inside the same write, a failed check being 69. Test 8 gains the five no-fetch verbs and the partition, and must fail on the leak as it stands today. The guard's way out is noted under (iii) | P527–533; P534–578; P587–598; P868–884; P422–437 |
| 2 | **Q4(b)** in the open questions | Options (iii), (iv), (ii), (i); the probes' evidence; R3 and the product seat favoured (ii) before the probes; (iii) marked as narrowing Amendment 22's S5 | P1294–1319 |
| 3 | **Q26** | Replaced twice: first (b) recommended, then, on the later posts, (a′) recommended (see the table above). The partition evidence, the product seat's reason for (a), and the marks that (a) and (a′) narrow Amendment 22 | P1867–1917 |
| 4 | **SHOULDs a and b, Q7 and Q15** | (A′) added, with S6's "(or under a lock on the object)" and the probes' numbers (reframed as (A″) in the second commit, item 23); (A), (B), (C) kept; "up to six times" and "rarely contended" moved into (A)'s description and R3's case; Q15(b) taken at READ COMMITTED, with the SERIALIZABLE finding; Q15 cross-referenced to (A′); S6's lock sentence corrected; "register busy" narrows Amendment 22's S1 and Brett may say "(A″) with 69"; test 4 injects a window; the "all as recommended" list drops Q7 (A) | P1457–1523; P1682–1708; P218–239; P282–295; P631–644; P675–679; P853–860; P1095–1116 |
| 5 | **SHOULD c:** the busy code at `lane-start`'s arms that note and carry on | Refuse-class at `:1154`, `:1262`, `:1271` and `:3430`, each verified at `2a057a6d`, with one test per arm (`:3079` added in the second commit, item 24); Q9 names the code; the manual lists it | P467–483; P484–493; P1591–1619; P925–943 |
| 6 | **SHOULD d:** statement order | The head row before the journal row, or a 23505 on the seq key retried | P235–239 |
| 7 | **SHOULD e:** a retry reuses its `write_id`; the ADV-C3 numbers | Reuse stated, with the UNIQUE-constraint retry, 124 as in doubt, the `n+1` result and Q16's evidence for (b); test 12 gains the rerun | P240–267; P903–909; P1709–1725; C230–267 |
| 8 | **SHOULD f:** B′ under Q21(b) | (B′) listed as an option | P1787–1805 |
| 9 | **SHOULD g:** timeouts for the readers, the renderer, `status` and `rollback` | Named, with the probes' values, the 4 s client timer and the default's failure; the guard-section test extended | P790–807; P811–829; C268–308 |
| 10 | **The NITs** | CR rows on the busy code now attribute "A serialization failure is not a refusal" to the proposal (CR158–166); the ADV-C1 item that resolved nothing moved to the conflicts left for Brett (CR139–146); the `register-row-local` row says it is the design's, with no option offered (P459); the canonical-key comparison is a test (C198–216) | as listed |
| 11 | **The records** | The probes' design numbers under ADV-C3, C4, C7 and C1; this section | C218–413 |
| 12 | **The "all as recommended" list** | Q7 (A) off the list; the three changed recommendations and the eight questions that stand apart named, as the round-2 block has them | P1095–1116 |
| – | **Q6's split**, lane 2's two additions (6549130353) | `lanes-edit.sh` fails closed when `lanes-index` refuses `mode=primary`; one test covers both states (item 17) | P1380–1394; P913–921 |

### Lane 1's product seat's check (6549202251), and the later posts

| # | Item | What changed | Lines |
|---|---|---|---|
| 13 | **P2, partly:** does act 4 wait for 2a, 2b and 3 | The first part of Q28, with the owner and date for 1a and 1b apart from the rest; the S1 line that said Q6 now says Q28; (a′)'s "#311 trails" marked reversed by P2; the landing order carries the wait as Brett's open point; lane 3's view recorded: act 4 waits for 2a, 2b and 3, and the renderer's merge need not wait for 2a and 2b under the private-index form | P1939–1967; P280; P1403–1404; P1423–1437; P1055–1060 |
| 14 | **P4, partly** | Q2(e) decided after the guard's measurement; "Writes never fall back to the files" | P1186–1188; P769 |
| 15 | **P10** | "What changes for a lane" and the manual's contents carry the busy code: nothing was written, and a retry in seconds is safe | P925–928; P932–943 |
| 16 | **The busy code** | The product seat's 75 (`EX_TEMPFAIL`) recorded; it is free in the exit table at `code/lanes-edit.sh:434–545`. Q7 and Q15 put the narrowing of Amendment 22's S1 to Brett, with "keep 69" as his option, as lane 4's agreement requires; CR236–241 no longer says the code "is not put to him" | C360–373; P1457–1523; P1682–1708; CR236–241 |
| 17 | **Q6's refusal of `mode=primary`** | A named exit code and text (`lanes-index` exits 2; `lanes-edit.sh` exits 69), agreement test 14 for both states, and a readiness-check item that looks for it | P1380–1394; P913–921; P612–619; P623–627 |
| 18 | **Q26**, the marks | The adversary-based conditions on (a) marked as lane 3's application, which lane 2 never saw when it wrote its findings, and the third as lane 3's inference; S5's listable spools and Q4(b)(ii)'s coverage of every writing workstation's spool stated as conditions of (a); under (b), S3's two ways out still apply | P1867–1917; P422–437 |
| 19 | **Q4(a)** | The product reply to ADV-C2 (S2's single-write import and the renderer's revert make a stray commit cost a person's word); the product seat's estimate of Brett's time, 45–75 min for (i) against 10–20 for (ii′); the ADV-C2 objection to (ii′) marked as lane 3's application, which lane 2 agrees with | P1268–1293 |
| 20 | **Q21(b)** | What deferral means; the recommendation of (B) depends on the org's plan, a fact not yet known | P1787–1805 |
| 21 | **Q22** | (c) given its Raven reason; "against its recovery" marked as lane 3's reading of ADV-C1; the recovery loses every write the mirror had not yet rendered, and the renderer's lag, which `status` shows, bounds that loss | P1810–1833 |
| 22 | **Q28 and Q29** | Costs for Q28's (a) and (c), as (b) has one; one date to name for 1a and 1b and one for the rest, since no report estimates the work; what Q29's (b) buys: no database connection per prompt | P1939–1967; P1968–1982 |

The round-2 file is lane 3's to edit and was left alone here. Lane 1's P12 asks that Q4(a), Q21(b), Q22, Q26, Q28 and Q29 stand apart from "all as recommended"; the proposal's list (P1095–1116) names the eight questions that do.

### The second commit

Three further changes from lane 3, on top of the first commit.

| # | Item | What changed | Lines |
|---|---|---|---|
| 23 | **Q7 reframed** | (A″) recommended: (A)'s optimistic head row, bounded by a deadline, each attempt two short `psql` calls, one to read and one to commit, so no session is held across the shell code, as with `lanes-index`'s compare-and-swap; no burst writer is lost, at the cost of about k(k+1)/2 verb runs in a burst of k writers. (A′) kept with its caveat: the lock is held across the shell code, so it needs a held-open session, (C)'s mechanism, plus a helper, and bash 3.2 has no `coproc`. (A), (B), (C) kept. Every place that stated (A′) as the design is now conditional on Q7: S1, S1's tests, S6's lock sentence, the "all as recommended" list, Q15. Test 4's window stays and must pass under whichever option is taken. The busy-code note reads "(A″) with 69" | P218–239; P282–295; P631–644; P853–860; P1095–1116; P1438–1523; P1682–1708; C309–358 |
| 24 | **SHOULD c, a fifth arm** | `lane-start:3079` (`lane-transcript`, for the row's session cell) added to the refuse-class arms, with its test. Lane 3's brief named `:3080`; at `2a057a6d` the arm is `:3079` and `:3080` is its `esac` | P467–483; P484–493; P1591–1619; C360–373 |
| 25 | **The full report's SHOULD f: the in-place form's fence** | Applied to the in-place alternative of S2: S3's "unpushed commits" row, at `lane-start`'s stamp and `lane-handoff`'s commit, discards a leftover render commit first (SQL holds it) rather than refuse the checkout, so a killed render leaves the next handoff commit unrefused; only a render commit is disposable, never a lane's own commit under `lanes/`. It does not apply to the preferred private-index form, which leaves no render commit on HEAD | P332–346 |
