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

### Conflicts resolved on the evidence

Each is the design's, not Brett's.

- **The report and recovery of a COMMIT in doubt.** P16 would look by seq and
  let the key follow; ADV-C3 requires a write id now. ADV-C3's evidence holds:
  seq `n+1` may be a rival's, and a blind retry of a claim gives a second
  `CLAIMED` or a false 69. P16 does not dispute the key, only its timing. So
  the write id is a requirement (S1), and P16's exit 3 stays the design's
  option for Q16's report code.
- **The exit code of "register busy".** ADV-C7 wants a code of its own. This
  is the design's: Amendment 22's S1 gives 69 to "unreachable, or refuses the
  transaction", and a write that lost six optimistic races was neither, by S1's
  own "a serialization failure is not a refusal". No text decides it and no
  seat opposes it. So S1 now requires a code of its own, and Q15 and Q9 leave
  the number to the design.
- **ADV-C1's two remedies against P1's "deciding reads that need no fetch".**
  The first remedy reads the last-fetched tip, which costs no fetch but can be
  stale. The proposal leaves the choice between the two remedies to the design.

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
- **ADV-C7's code for "register busy"** is not put to him: it is the design's,
  for the reason given above.
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
