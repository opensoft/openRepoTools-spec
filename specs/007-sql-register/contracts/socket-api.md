# Contract: the register service's socket API

- Owner: lane 5 (R50). Tag: [L5]. Feature 007, `contracts/socket-api.md`, which tasks.md's T511 writes.
- Built on by T512 (`lanes-index serve`, with `register read` as its client) and T513 (the `SessionStart` block's `lanes-index serve --start`). It is the source for lane 3's T341, "the socket API" in the manual's part 2 (tasks:1382).
- Status: draft 6, written by lane openRepoTools-5 on 2026-11-09, under lane 1's review (U5), which lane 1's U5 re-check of draft 4 passes (#165 6752336451). Draft 1 is commit `51fbd17b`; draft 2, which added section 13, the bytes on the socket, as lane 5's proposal, is `7d462a5e`; draft 3, which folded in lane 3's rulings on OP-1, OP-2, OP-8, OP-9 and OP-14 (#165 6746215510, with its correction of OP-8 and its ruling on OP-15's bounds in #165 6746655077) and lane 1's answers on OP-4 to OP-7, OP-11, OP-13 and OP-15 (#165 6746620707, with lane 1's contract draft 4.6), and encoded the set requests, is `e4528577`; draft 4 is `735b4e22`; and draft 5 is `b21199e2`. Draft 4 folded in lane 1's U5 review of draft 2 (#165 6747151224): M1 to M3, S1, and the NITs N1 to N5 and N7 to N9, with lane 1's bytes in place of lane 5's wherever both gave them. It also folded in lane 3's ruling that a request may name sets and the listing (#165 6747048305, ERR-31), lane 3's confirmation of `schema`'s two numbers (#165 6747313608), lane 3's ruling on register paths (#165 6747611042, ERR-39) with lane 1's `pg_url_env` (#165 6747233899), OP-16 as lane 1 confirmed it (#165 6747475637, with lane 3's 6747456383), lane 1's contract drafts 4.7 and 4.10, and the errata numbers of rounds B4 to B6. Draft 5 folded in lane 1's U5 re-check of draft 4 (#165 6752336451): the SHOULD S-a and the NITs N-a to N-g, with lane 1's bytes in place of lane 5's wherever lane 1 gave them; lane 3's acceptance of N-g (#165 6752360829); and `PGAPPNAME` on the service's path, which round B9's ERR-39 lists (#165 6752360829). Draft 6 cites lane 1's contract draft 4.11 directly, as `CLI411:<n>`, where draft 5 cited it as "draft 4.11, to come" (lane 1's post, #165 6752661856; lane 3 takes it into round B9, #165 6752697506), with draft 4.11's words wherever they differ from draft 5's; notes the service child's `psql` flags beside lane 1's §2.2 argv (lane 1's point 2, #165 6752661856); and takes round B8 `270236bb` as the spec PR's head, whose ERR-39 now names `PGOPTIONS`. Section 14 lists what each draft changed. It lands no earlier than the spec PR, and before T512 (tasks:1378).
- In one line, as the manual will say it: the service answers the register's text at `n`, and the reader decides (svc:286; Y:4093–4100).
- What it fixes: where the socket lives and how both ends check it, who may connect, what a request carries, the three answers, the limits, how the service connects, how it starts and stops, `serve --start`, and the client's side as far as the socket reaches. Section 13 gives the bytes on the socket, lane 5's proposal with lane 1's U5 bytes in place of lane 5's wherever lane 1 gave them; section 14 lists each draft's changes, and section 15 lists the open points, each closed with its ruling or answer, or still open with its owners. Nothing here adds a value the sources do not give, except section 13's proposals, each marked "(proposal, lane 5)" with its reason, and lane 1's bytes, each marked with its item of lane 1's U5 review or of its U5 re-check.

## 0. Basis, and how to read it

| Input | sha256 | Lines | Cited as |
|---|---|---|---|
| The branch's `openspec/changes/add-sql-register/design.md` at `722078c9` (lane 3's `007-sql-register`, fix round B), byte-identical to Y's `design.md` as lane 1's drafts 4.4 and 4.5 key it | `35ca5179d5e9e47d…` | 6,745 | `Y:<n>` |
| The spec PR's head when drafts 2 and 3 were written, B3 `a138b1b1`, its `design.md`. It differs from Y's only at lines 2324, 2326, 6305, 6390 and 6393, and no draft of this contract cites any of them | `ea5fcc52a1252a66…` | 6,745 | `Y:<n>`, the same lines |
| The branch's `specs/register-service/spec.md` at `722078c9`, and B3's, the same bytes; newer than Y's (`a416df1d…`, 293 lines): it carries lane 3's B4 | `9da1fa8d0523a82b…` | 303 | `svc:<n>` |
| The branch's `test-plan.md` at `722078c9`, and B3's, the same bytes | `8702b70a799001f1…` | 9,145 | `tp:<n>` |
| W `bb08d080`'s `design.md`, which the task line's `W:<n>` cites | `76d6a4a030e0ddc4…` | 6,636 | `W:<n>` |
| W's `specs/register-service/spec.md` | `11a6ee5886c11e6f…` | 293 | `Wsvc:<n>` |
| Lane 5's texts for commit X, `TEXTS-lane5-X-20261108T1809Z.md`; text 13 above all | `f29328f28139bffd…` | 264 | text 13 |
| Lane 5's text 8″, `TEXTS-lane5-X-8pp-20261108T1839Z.md` | `e7a34f164124f207…` | 44 | text 8″a, 8″b |
| Lane 1's contract draft 4.4, `contracts-register-cli.v4.4.md` | `8600078b571b36ae…` | 963 | `CLI44:<n>` |
| Lane 1's contract draft 4.5, B3's `specs/007-sql-register/contracts/register-cli.md`. It differs from draft 4.4 only inside lines 5, 12, 273 and 957, none of which this contract cites | `da5a23ce00bff824…` | 963 | `CLI45:<n>` |
| Lane 1's contract draft 4.6, `contracts-register-cli.v4.6.md`, which lane 3 takes into B4 in place of draft 4.5 (#165 6746655077). It differs from draft 4.5 only inside lines 5, 12, 653, 665, 673, 730, 764 and 767, and keeps every line number (its `rebuild-check-v4.6.md`) | `028937e66f9bce3f…` | 963 | `CLI46:<n>` |
| Lane 3's rulings memo, R01 to R61, `MEMO-007-rulings-20261108.md` | `b2184b1adae0e060…` | 839 | `R<nn>`; `M:<n>` for its lines |
| Lane 3's ruling on B4 and B7, #165 6738474549, as saved in `read-6738474549.md` | `6b798557b61d0cef…` | 31 | B7 |
| Lane 3's rulings on OP-1, OP-2, OP-8, OP-9 and OP-14, #165 6746215510, as saved in `read-6746215510.md`, with their analysis, `RULINGS-L5OP-analysis-20261109.md` (`6999a644068fbfdb…`, 77 lines) | `a185844391d48fee…` | 42 | "lane 3's ruling, #165 6746215510, OP-n" |
| Lane 3's correction of its OP-8 line, and its ruling on OP-15's bounds, #165 6746655077, as saved in `read-6746655077.md` | `c53cf4748f28e7ff…` | 28 | "lane 3's ruling, #165 6746655077, OP-n" |
| Lane 1's answers on OP-4, OP-5, OP-6, OP-7, OP-11, OP-13 and OP-15, and its two fixes to draft 2, #165 6746620707, as saved in `read-6746620707.md` | `ea027400276f91c0…` | 44 | "lane 1's answer, #165 6746620707, OP-n" |
| Lane 1's U5 review of draft 2, `REVIEW-U5-t511-draft2.md`, which cites draft 2 as `D2:<n>` | `5d9618488c465528…` | 226 | "lane 1's U5 review", with its item: M1 to M3, S1, N1 to N9, 1(a) to 1(c) |
| Lane 1's post of that review, #165 6747151224, as saved in `read-6747151224.md`. Where the post and the review differ, the post rules: it withdraws S2, N6 and the review's restated reason in 1(c), and has the review's 1(b) cite Y:3914–3918 alone | `298ed7cea44908fe…` | 51 | "lane 1's U5 review, #165 6747151224" |
| Lane 3's ruling that a reader's request may name sets and the listing, #165 6747048305, as saved in `read-6747048305.md`, its "Your coming question" bullet | `6a73dd37b9af039f…` | 29 | "lane 3's ruling, #165 6747048305" |
| Lane 3's confirmation of `schema`'s two numbers, #165 6747313608, item 3, as saved in `read-6747313608.md` | `0b9e89beb5640a03…` | 16 | "lane 3, #165 6747313608" |
| Lane 1's contract draft 4.7, `contracts-register-cli.v4.7.md`, with its post #165 6747384945 (`read-6747384945.md`, `394870ad2c2fe391…`, 26 lines). It differs from draft 4.6 only inside lines 5, 12, 665, 699, 703, 730, 732 and 767, and keeps every line number | `429f74ef7a7c811d…` | 963 | `CLI47:<n>` |
| Lane 1's contract draft 4.10, `contracts-register-cli.v4.10.md`, with its post #165 6748303517 (`read-6748303517.md`, `fef9d16f2fe95b0d…`, 35 lines). It differs from draft 4.7 only inside lines 5, 12, 195, 214, 629, 632, 636 and 638, and keeps every line number. The spec PR's head, B7, holds it as its `contracts/register-cli.md`, byte for byte | `e2b517824510c8d9…` | 963 | `CLI410:<n>` |
| Lane 1's T103, #165 6747233899, as saved in `read-6747233899.md`, its "For lane 5" bullet: `pg_url_env(url, what)`, in T103's `a3e1ddee` | `d4a05fa58257501c…` | 41 | "lane 1's T103, #165 6747233899" |
| Lane 1's confirmation of OP-16, #165 6747475637, as saved in `read-6747475637.md`, and lane 3's agreement, #165 6747456383, as saved in `read-6747456383.md` (`b6d73dac03b87cf7…`, 17 lines) | `72ad250db1a2b6c2…` | 18 | "#165 6747475637"; "#165 6747456383" |
| Lane 3's ruling on register paths, (a), #165 6747611042, as saved in `read-6747611042.md` | `a98c1091fe6d27ff…` | 26 | "lane 3's ruling, #165 6747611042" |
| Round B4, #165 6747379291, as saved in `read-6747379291.md` (`f904be3ec5cf9b10…`, 44 lines), and B4 `6fe94d17`'s `research.md`, which numbers T511's errata ERR-25 to ERR-29 at its lines 377–428 | `674418629e6ec30d…` | 807 | ERR-25 to ERR-29 |
| The spec PR's head when drafts 4 and 5 were written, round B7 `2b034546`, its `research.md`: ERR-25 to ERR-29 at B4's lines, ERR-31 (B5) from its line 448, and ERR-39 (B6) from its line 530. B7's `design.md` differs from Y's only at lines 2324, 2326, 6305, 6390 and 6393, as B3's does | `4a0f38757e161e33…` | 931 | ERR-25 to ERR-29, ERR-31, ERR-39 |
| Lane 1's U5 re-check of draft 4, `REVIEW-U5-t511-draft4.md`, which cites draft 4 as `D4:<n>` | `424c581722af78bc…` | 248 | "lane 1's U5 re-check", with its item: S-a, N-a to N-g |
| Lane 1's post of that re-check, #165 6752336451, as saved in `read-6752336451.md`. Where the post and the re-check differ, the post rules: it cites the code leg's `tests.yml:7–8` for Brett's decision of 2026-10-15, which the re-check gives as lines 7–10, and it takes the sentence on a direct read the client chooses into draft 4.11, at CLI410:688, which the re-check leaves optional | `68577b99a5e6b615…` | 55 | "lane 1's U5 re-check, #165 6752336451" |
| Lane 3's ruling on lane 1's re-check, #165 6752360829, as saved in `read-6752360829.md`: N-g accepted, and round B9's ERR-39 list | `fef0a4d9247b245b…` | 19 | "lane 3's ruling, #165 6752360829" |
| Lane 1's block v4.10, `lane1-tasks-block.v4.10.md`, for its U5 gate at its lines 452–465, which lane 1's re-check cites | `fe74285fecb49fb2…` | 660 | `L1:<n>` |
| Lane 1's contract draft 4.11, `contracts-register-cli.v4.11.md`, shared in lane 1's post #165 6752661856 (the next row). It differs from draft 4.10 only inside lines 5, 12, 234, 673, 680, 688 and 703, and keeps every line number (CLI411:5). Lane 3 takes it, with block v4.11, into round B9, after lane 2's F9 (#165 6752697506); the spec PR's head, B8, still holds draft 4.10 | `60b397f992718939…` | 963 | `CLI411:<n>` |
| Lane 1's post sharing draft 4.11, #165 6752661856, as saved in `read-6752661856.md`: the lines draft 4.11 changes; point 1, a `psql` older than 15, refused with 69 as FR-012 requires, in place of Y:1417's fallback (CLI411:234); and point 2, `VERBOSITY`: lane 1's §2.2 gives every register call one argv, with `VERBOSITY=verbose` (CLI410:206), and only the service's child runs `VERBOSITY=sqlstate` (Y:3973) | `8a3424499c47af9b…` | 43 | "lane 1's post, #165 6752661856", with its point |
| Lane 3's acceptance of lane 1's point 1, #165 6752697506, as saved in `read-6752697506.md`: B9 takes draft 4.11 (`60b397f9…`) with block v4.11 after F9, and numbers an erratum to Y:1417 and to the test plan's oracle: a `psql` older than 15 is refused with 69, naming its version, and nothing is sent. This contract says nothing of `psql`'s version or of Y:1417's fallback, so the erratum changes nothing here | `52f484c5f4760da2…` | 11 | "lane 3's ruling, #165 6752697506" |
| The spec PR's head, round B8 `270236bb`, extracted read-only. Its `research.md` holds ERR-25 to ERR-29 from its line 405, ERR-31 from its line 477, and ERR-39 at its lines 587–604, which now names `PGOPTIONS` with the call's bounds (Y:3503). Its `design.md` differs from Y's only at lines 620–621, 2321, 2324, 2326, 3019, 6305, 6390, 6393 and 6402, none of which this contract cites; its `contracts/register-cli.md` is draft 4.10, byte for byte; its register-service `spec.md` and `test-plan.md` are the bytes `svc:<n>` and `tp:<n>` cite; and its `tasks.md` leaves T511's block as B7's | `c6cafe86d9e4240b…` | 1,157 | ERR-25 to ERR-29, ERR-31, ERR-39 |
| The branch's `openspec/changes/add-sql-register/operations.md`, Y's, as lane 1's drafts key it | `c94ce7d75212dca5…` | 735 | `Y-ops:<n>` |
| Lane 5's block v5, `lane5-tasks-block-v5.md` | `5d427c42b2a2a7bf…` | 839 | `L5:<n>` |
| `tasks.md` at `722078c9` with lane 5's 43 fold edits applied, `tasks.md.lane5-722078c9`. B3's `tasks.md` (`e7b63983…`, 1,816 lines) carries T511 to T513 with the same text, 14 lines lower (T511 at its line 1392); `tasks:<n>` stays this copy's line | `e3c81f8e19cc3d8c…` | 1,779 | `tasks:<n>` |
| 007's `research.md` at `722078c9`. B3's (`9956dc7f…`) carries ERR-04 and DEC-15 unchanged | `80b3a0f1f4fcf7c4…` | 663 | ERR-04, DEC-15 |
| 007's `spec.md` at `722078c9`. B3's (`a04c4ba4…`) carries FR-057 unchanged | `003348040d618a2c…` | 1,589 | FR-057 |

- **The branch governs W.** Where the branch's text for a passage differs from W's, this contract follows the branch. Section 12 lists each such passage it uses. Passages were found by their words; a `W:<n>` is given where the task line cites W.
- **Draft 2's basis is the spec PR's head, B3 `a138b1b1`,** extracted read-only. Where B3's file differs from the one draft 1 read, the row above says where, and no line this contract cites is among the differences.
- **`Y:<n>` is a line of Y's `design.md`,** which the branch holds byte for byte at `722078c9`. B3's `design.md` is not byte-identical to Y's: it differs at lines 2324, 2326, 6305, 6390 and 6393 only, and no draft cites any of them. B7's differs from Y's at the same five lines only. So a `Y:<n>` here is the same line in Y, at `722078c9`, at B3 and at B7, and the line lane 1's drafts 4.4 to 4.10 cite by the same number. B8's differs from Y's at those five and at lines 620–621, 2321, 3019 and 6402, none of which this contract cites, so a `Y:<n>` here is B8's line too, and draft 4.11's, which is still keyed to Y (CLI411:5).
- **`CLI44:<n>` is also draft 4.2's line.** Draft 4.2 is the feature's `contracts/register-cli.md` (`dea51e43…`), and drafts 4.3 and 4.4 changed text only inside existing lines (CLI44:5).
- **`CLI44:<n>` and `CLI45:<n>` name the same lines.** Draft 4.5, B3's `contracts/register-cli.md`, keeps every line number of draft 4.4 (CLI45:5). Draft 1's citations stay `CLI44:<n>`, and draft 2 cites its new ones as `CLI45:<n>`.
- **`CLI46:<n>` names the same line too.** Draft 4.6 keeps every line number of draft 4.5, and changes text only inside lines 5, 12, 653, 665, 673, 730, 764 and 767, adding at each a sentence marked `[P, T511's OP-n]` (CLI46:5). Draft 3 cites those eight lines as `CLI46:<n>`, in its new text and wherever sections 1 to 13 cited them before, and cites its other new lines as `CLI46:<n>` too. The records kept in section 15 keep drafts 1's and 2's labels, since they say what those drafts read: OP-11's record quotes CLI44:764 as draft 4.4 had it, before draft 4.6 changed it. Every other `CLI44:` and `CLI45:` citation stays, ranges included.
- **`CLI47:<n>` and `CLI410:<n>` name the same line too.** Draft 4.7 keeps every line number of draft 4.6, and changes text only inside lines 5, 12, 665, 699, 703, 730, 732 and 767 (CLI47:5). Draft 4.10 keeps every line number of draft 4.7, and changes text only inside lines 5, 12, 195, 214, 629, 632, 636 and 638 (CLI410:5). Draft 4 cites draft 4.7's eight lines as `CLI47:<n>`, in its new text and wherever sections 1 to 13 cited one of them alone before, and cites draft 4.10's line 214 as `CLI410:214`. None of draft 4.10's changed lines is one that draft 4 cites as `CLI47:<n>`, so those cites hold at draft 4.10, the draft the spec PR's head holds. Every other `CLI44:`, `CLI45:` and `CLI46:` citation stays, ranges included, and section 15's records keep the labels they were written with.
- **`CLI411:<n>` names the same line too, and draft 4.11 is shared.** Lane 1 has shared its contract draft 4.11 (#165 6752661856), and lane 3 takes it into round B9 with block v4.11, after lane 2's F9 (#165 6752360829, 6752697506). Draft 4.11 keeps every line number of draft 4.10, and changes text only inside lines 5, 12, 234, 673, 680, 688 and 703 (CLI411:5). Where lane 1's post of its U5 re-check said that draft 4.11 would carry a sentence, at CLI410:673, CLI410:680, CLI410:688 or CLI410:703, draft 5 cited the line of draft 4.10 with "draft 4.11, to come" and lane 1's post (#165 6752336451). Draft 6 cites draft 4.11's own line, as `CLI411:<n>`: :673, :688 and :703 where draft 5 cited them so, and :680 beside section 13.4's list, each checked against draft 4.11's line; where draft 4.11's words differ from draft 5's, draft 6 takes draft 4.11's (section 14.5). Section 14.4 and section 15's "Draft 5" lines keep "draft 4.11, to come", since they record what draft 5 read. Draft 5 also cites draft 4.10's line 216, `PGAPPNAME`, as `CLI410:216`, and draft 6 its line 206, lane 1's §2.2 argv, as `CLI410:206`; draft 4.11 keeps both lines as they are.
- **Whose rulings.** R36, R37, R44, R50, R51 and B7 are lane 3's rulings, not Brett's, and so are #165 6746215510, 6746655077, 6747048305, 6747313608, 6747456383, 6747611042, 6752360829 and 6752697506, which give no word of Brett's. Lane 1's answers, #165 6746620707, its U5 review and its post, #165 6747151224, its confirmation, #165 6747475637, its T103, #165 6747233899, its U5 re-check and its post, #165 6752336451, its block v4.10, its post sharing contract draft 4.11, #165 6752661856, and its contract drafts are lane 1's, as the author of `register-cli.md` and the U5 reviewer, and give no word either. Brett's own answers appear only where svc or the task line cites them, with their comment.
- **Errata.** Lane 3's errata go into `research.md` Part 1, and Y's frozen text keeps its words (#165 6746215510). Round B4 numbers T511's (#165 6747379291), and draft 4 gives each its number beside the `Y:` lines it corrects, as B7's `research.md` holds them: ERR-25, Y:3892 (OP-1); ERR-26, Y:4064–4066 (OP-2); ERR-27, Y:3881–3883 (OP-8, as corrected); ERR-28, Y:3927–3929 (OP-9); and ERR-29, D9's row for the background fetch (OP-15). Rounds B5 and B6 number two more that this contract rests on: ERR-31, on Y:4036–4037, lane 3's ruling on sets and the listing (#165 6747048305); and ERR-39, on Y:4970, lane 3's ruling on register paths (#165 6747611042). Round B8's ERR-39 now names `PGOPTIONS` with the call's bounds (Y:3503) beside the URL's mapping, `PGPASSFILE` and `PGCONNECT_TIMEOUT`; round B9 adds `PGAPPNAME`, and records lane 3's acceptance of N-g in `research.md`, as a decision or as an erratum where a Y line needs one (#165 6752360829).
- **#165** is opensoft/openRepoTools#165, and a number after it is a comment id.
- **Names.** The service is `lanes-index serve`; the documentation and its log call it `lanes-register` (Y:3835–3842). Its client is `lanes-index register read` (Y:3843–3848). "The client" below means `register read`'s socket path, and "the guard" means `lanes-edit.sh guard`.

## 1. What the socket serves, and to whom

- **One service per environment,** a bench container or a host shell (Y:3701; tasks:1390). It connects as its engineer's reader login, `lanes_r_<engineer>`, a member of READER, which the environment's `lanes-index.conf` names (Y:3708–3713).
- **What it answers:** the register's text at the journal's head `n`, read fresh for each request. The reader runs today's parser on that text in its own process, and decides (svc:10; Y:4093–4100). The service answers no question by key (Y:4093–4095).
- **Its clients** are `register read`'s socket path, "every reader's path" (Y:3843–3845), and the guard's prefetch, `register read --for guard --prefetch` (Y:3845–3848, Y:4127–4133). In lane 1's terms these are forms 1 and 2 of `register read` (CLI44:655–660), and form 3, the readers' second read, which asks the service first too (lane 1's answer, #165 6746620707, OP-7; CLI47:730; section 13.9).
- **Not its clients:**
  - **form 4,** the guard's second read: it "always reads directly, never through the service" (B7; CLI44:747; research.md ERR-04; tasks:1384);
  - **the `SessionStart` block,** which "only starts the service and asks it nothing" (Y:3828–3829; svc:93; Y:4493–4499). Its start is `serve --start` (section 9);
  - **every writer:** "No writer is a client (4A): a write's snapshot and commit open their own connections with the REGISTER-WRITER credential, and never wait for or start the service" (Y:3829–3831; svc:10).
- **Only on `mode=primary`.** Clients ask the socket only on a primary workstation, and in files and `index` mode the guard and the readers "stay exactly as today" (Y:3948–3951). Section 8.5 says when `serve` runs at all.

## 2. The socket's place, and its checks

### 2.1 The path

- **One path, no choice:** `${XDG_STATE_HOME:-~/.local/state}/openRepoTools/run/register.sock`, in `run/` under `lanes-index`'s state directory (Y:3753–3755, Y:3767; tasks:1391). `XDG_RUNTIME_DIR` is not consulted (Y:3755–3757).
- **A process with another `XDG_STATE_HOME`** finds no service, starts its own at its own path, and is answered correctly by it, at the cost of a second connection (Y:3757–3760).
- **Not `/tmp`:** a name there can be taken first by another user, and `tmp` reapers delete sockets in it (Y:3771–3773).
- **The length.** The client checks the path's length before it connects or starts anything. Over 103 bytes it reads directly and starts nothing, and `lanes-index serve` refuses the same path with exit 2 and the stamp `path-too-long` (Y:3777–3780, Y:3927). "Nothing shortens a path silently" (Y:3780–3781). 103 bytes is macOS's limit; Linux's is 107 (Y:3774–3776).

### 2.2 The directory: 0700, this uid, never a symlink

- **Checked on every use,** by the service before it binds and by the client before it connects or starts one: it must be a directory, not a symlink (`lstat`), owned by this uid, with mode 0700 (Y:3761–3763; tasks:1391).
- **Made so when absent:** `lanes-index` creates it that way (Y:3763–3764).
- **Never used, never repaired.** A directory that fails the check is not used: "the client reads directly, the service exits 2 naming it, and `lanes-index status` reports it" (Y:3764–3766).
- **Never stamped.** `dir-check` is never stamped: `serve` exits 2 naming the directory (Y:3765) and writes nothing in it, and `lanes-index status` reports the failure itself (Y:3761–3768). That is lane 3's ruling (#165 6746215510, OP-9), with an erratum on Y:3927–3929, whose stamped causes name `dir-check`: ERR-28. Lane 1 reviews the `status` line (R44). Section 8.6 follows it.

### 2.3 The files

- **Five files,** all in `run/`: `register.sock`, `register.lock`, `register.owner`, `register.failed` and `register.log` (Y:3767–3768).
- **The bind:** the service binds under `umask 077`, and the directory's check is what stops another user (Y:3768–3770).
- **Nothing secret in them.** `register.log` and every `refuse db <cause>` carry fixed phrases and the SQLSTATE only, never `psql`'s message text and never a URL (Y:3797–3799). The one label the service may name is `where`, with any userinfo removed (Y:3816–3818).

### 2.4 The owner file, `register.owner`: install, namespace and boot

- **It holds** the service's pid, its start UTC, its install's identity, and on Linux its pid namespace (`readlink /proc/self/ns/pid`) and the boot id (`/proc/sys/kernel/random/boot_id`) (Y:3782–3784). The install's identity is the size, mtime and inode of the service's own `lanes-index` file (Y:3915–3916).
- **Another namespace or boot.** A client whose own pid namespace or `boot_id` differs treats the service as unavailable (Y:3784–3786; tasks:1393): it reads directly and starts none, so SVC-1 holds where a bench bind-mounts `~/.local/state` across containers (Y:3786–3788). That is SVC-2's text, and lane 3's ruling (#165 6746655077, OP-8, which corrects 6746215510's OP-8 line).
  - **`ENOENT` or `ECONNREFUSED` still starts one,** whatever the owner file says (6746655077). So the owner file's namespace and boot decide only for a socket that answers the connect (section 13.8).
  - **In B4:** an erratum, ERR-27, drops "another namespace or" from Y:3882–3883's start list, which keeps "an owner file from another install, unless a failure stamp is fresh"; and tasks:1393's "as absent" (B3's `tasks.md:1407`) becomes "as unavailable, and starts none", for another namespace or boot (6746655077).
- **Another install.** A client that finds an owner file from another install starts a service (Y:3881–3883). The old service answers its next request `unavailable restarting`, and exits (Y:3915–3918). Lane 3's correction keeps this case as it stands (#165 6746655077, OP-8).
- **On macOS** the owner file holds no namespace or boot id: Y gives those "on Linux" (Y:3783).

### 2.5 A stale socket

- **What leaves one:** a service killed by `SIGKILL` leaves its socket file, and a container restart leaves the same stale file on the container's overlay (Y:3907, Y:3910–3911).
- **How it is cleared:** the next client gets `ECONNREFUSED` and starts a service, which takes `register.lock`, unlinks the stale file under the lock, and binds (Y:3901–3903, Y:3907–3909; tasks:1398).

## 3. Who may connect: this uid only

- **The service checks each client.** It reads `SO_PEERCRED` on each connection and closes any whose uid is not its own (Y:3789–3790). A client of another uid is answered nothing.
- **The client checks the service.** It reads `SO_PEERCRED` on its connected socket and treats a server of another uid as unavailable, "so a socket planted in a directory that somehow passed the check still answers nothing" (Y:3792–3794).
- **On macOS,** `LOCAL_PEERCRED` takes `SO_PEERCRED`'s place at both ends (tasks:1392). Y calls it "untested here", and where neither is available, "the directory's owner and mode are the whole protection" (Y:3794–3796). See OP-12.
- **Not yet shown:** no second uid was available to show the service's refusal (Y:3790–3792). T512's guess `test_a_client_of_another_uid_is_closed_by_so_peercred` may need a stand-in, or must record the refusal as not shown (L5:640).
- **Trust rests here, not on the digests.** The digests in an answer prove only that the bytes arrived as sent, "not a wrong service. Trust in the service rests on SVC-2: the checked directory, the owner file and the uid check at both ends" (Y:4070–4076).

## 4. The request: file names only

- **It names the files the client wants, and nothing else** (Y:4036–4037; svc:59; tasks:1416): `lanes/LANES.md`, `lanes/aliases.tsv`, `lanes/log/<lane>.md`, "and the rest of the render set" (Y:4036–4037). ERR-31 reads Y:4036–4037's "The request names the files it wants" as "the files it wants, singly or by the contract's member sets, or the render set's listing at `n`", and still nothing else: register files at `n`, and no pid, session or host fact (SVC-11). That is lane 3's ruling (#165 6747048305), which B5 records.
- **The guard's request** names `lanes/LANES.md`, `lanes/aliases.tsv` and `lanes/log/<window name>.md`, the log of the lane the window is normally bound to (Y:4142–4144), the last with `--lane=`, and only for a name in the class (CLI46:666, CLI46:670; Y:4956; lane 1's fix, #165 6746620707).
- **It never carries** a session id, `cwd`, agent id, prompt, window, live record or liveness fact (svc:59, svc:61–63; tp:2045–2047).
- **It never carries `n`,** nor a generation: "the request carries file names only, ... So it cannot be asked for a log at an older `n`, and form 4 never asks it" (tasks:1416; B7). So the guard's second read, at the `n` it prefetched, always reads directly and never asks the socket (B7; CLI44:747), and never reads `.direct` (B7; CLI44:688; lane 1's fix, #165 6746620707). svc:59 stands.
- **B7's condition stands with it:** "if T511's and T512's measurements show that path often running out of budget, lane 5 brings the point back; a request naming `n` would be a spec change, for Brett" (tasks:1384; B7). This contract measures nothing; T512's tests do.
- **The request's bytes** are section 13.3's: lane 5's proposal, with lane 1's U5 bytes in place of lane 5's (OP-3).
- **Sets, by the names they cover.** Form 1's members that the client cannot name before the read go in the same request by the render-set names they cover, file names only: `archive` as the set `lanes/archive/`; `--all`'s logs as the set `lanes/log/`, which the client filters to `check_lane_name`'s class; and `paths` as the render set's listing at `n`. The service resolves a set in the request's own transaction at `n`, and a set names files only, never a host-local fact or an `n` (lane 1's answer, #165 6746620707, OP-5; CLI47:665; Y:4036–4041; Y:4611; B7). Section 13 encodes them in lane 1's bytes (lane 1's U5 review, M1).
- **The spec question is closed.** Lane 3 has ruled that a reader's request may name the files it wants singly or by the contract's member sets, or ask for the render set's listing at `n` (#165 6747048305), and B5 records it as ERR-31, an erratum on Y:4036–4037, the lines as lane 1 corrected their cite (#165 6747151224). Contract draft 4.7 states it (CLI47:665). So form 1's and form 3's sets go through the socket (13.9).
- **A log named in another case** is resolved by the service, case-insensitively among the render set's paths at its `n`, by T113's one function, which `serve` reuses (lane 1's answer, #165 6746620707, OP-6; CLI46:673; SVC-3). That lookup compares bytes and folds A–Z only, for every key, in the class or outside it (lane 1's U5 review, 1(a)). Section 13.4 gives its records.

## 5. The answers

The service gives one of three answers to a request, and the client turns each into an exit (section 5.4). "`unavailable` never leaves the client" (Y:4336–4337).

### 5.1 The text at `n`, with digests, `n` and the generation

- **One snapshot per request.** For each request the service runs one transaction at REPEATABLE READ READ ONLY. It reads the journal's head `n`, the requested files' digests at `n`, and, in the same transaction, any journal entries or whole files it needs (Y:4036–4041; tasks:1410), and the members of any set the request names (CLI47:665). Like every form's snapshot, it reads the switch and checks `REGISTER_SCHEMA_VERSION` against `readers_from` and `version`, as a reader's first SELECT does, in the request's own transaction, never from a cached answer, and with T113's function (Y:2334, Y:2336; CLI46:653; lane 1's U5 review, M2): a switch not `primary` answers `refuse db switch-files -`, and a number outside the window answers `refuse db schema - <readers_from> <version>` (section 5.2). Its statements, from `BEGIN ISOLATION LEVEL REPEATABLE READ READ ONLY` to `COMMIT`, are joined with psql's `\;` and sent as one query, so a request is one round trip (Y:3985–3988).
- **The generation, from the same snapshot.** The answer carries the register generation that transaction read, beside `n`. Y's interfaces table gives `register read` "text at `n` with digests, and the `n` and the register `generation` it read, in the same snapshot" (Y:4833), and the task line asks for "`n` and generation". The client writes them to `.head`'s `n=` and `generation=` (CLI44:694–695). Form 4 takes its generation from `.head` (CLI44:745), and the offer's `act` is built from that `n` and generation (L5:364). SVC-7's own words give the answer's head as `text <n>` alone (Y:4064–4066). Lane 3's ruling makes that an erratum, ERR-26: Y:4064–4066's head reads "`text <n>` with the register generation that transaction read" (Y:4833) (#165 6746215510, OP-2). The bytes are OP-3's: section 13.4's head line, `text <n> <generation>`, which lane 1's U5 review passes (#165 6747151224).
- **Fresh at `n`.** It answers `text <n>` only for that transaction's `n`, so the files of one answer are never from two seqs (Y:4064–4065). It "never answers from images it read before" (Y:4088; svc:23).
- **Where the images come from** (Y:4043–4050):
  - images it holds in memory with the digests at `n` are answered;
  - otherwise it brings them to `n` with the journal entries read in that transaction, by the function the writers' snapshot uses, and checks them against the digests;
  - on a mismatch it takes the file from the per-environment image cache, where the cache holds it at the digest at `n`;
  - otherwise it answers `unavailable`, and fetches the file whole in the background, in a transaction of its own, never on a request's timer: on the one held connection, between requests, with its own bounds (section 7; OP-15, closed).
- **The write-through.** What it fetched goes to the per-environment image cache, the one cache the writers' snapshot reads (Y:4054–4062; tasks:1418). It writes through T302's cache API, in the U5 gate's words (L1:457; lane 1's U5 re-check, N-e, #165 6752336451).
- **Its contents:** `text <n>`, then for each file its path, its byte count, its digest and its bytes (Y:4065–4066); a set's members are files like any other, and the list is the render set's paths (CLI47:665; section 13.4). The digest is the database's at `n` (Y:4070–4072): the `sha256` of `journal_files` (Y:2394), which `.head`'s `file=` rows also carry (CLI47:699). The bytes are section 13.4's proposal.
- **The client's checks.** It checks each file against the digest in the same answer, which catches a cut or corrupted transfer (Y:4072–4074). Then it writes the cache family at `n`, each file in the form its read site expects, `.head` last (Y:4142–4151; CLI44:675–688), and exits 0 (Y:4341).

### 5.2 `refuse db <cause>`

- **Only where the failure is definitive in this request:** "the connection refused, an authentication error, the switch not `primary`, the schema version refused", with a fixed cause and the SQLSTATE (Y:4082–4085). It carries fixed phrases and the SQLSTATE only, never `psql`'s message text (Y:3797–3799).
- **The four causes.** Y names two cause words, `auth` and `unreachable` (Y:3931–3932). Lane 1's answer names all four: `<cause>` is one of `unreachable`, `auth`, `switch-files` and `schema` (#165 6746620707, OP-4; CLI47:767). Lane 5 reads them as Y:4083–4085's four definitive failures, in their order: the connection refused, an authentication error, the switch not `primary`, the schema version refused. Only these four are sent; any other word is malformed, so `unavailable` (lane 1's U5 review, M2; section 13.5).
- **Definitive in this request.** Lane 1 reads Y:4082–4091 as letting the service answer `unavailable` whenever the request itself did not see the cause, such as a past background connect's outcome, so that needs no ruling (6746620707, OP-4). The direct read then decides, and the outcome is the same 69 or 2 (Y:4089–4091).
  - **`switch-files` and `schema` are always the request's own.** The request's transaction reads the switch and checks the window (section 5.1), so a switch other than `primary` answers `refuse db switch-files -`, and a number outside the window answers `refuse db schema - <readers_from> <version>`, each definitive in the request's own transaction (Y:4082–4085; lane 1's U5 review, M2).
  - **`auth` and `unreachable` are sent only where this request met them.** Otherwise the answer is `unavailable` (Y:4086–4091; lane 1's U5 review, M2).
- **The client refuses exactly as the direct read would** (Y:4085–4086), and makes no direct read after it. `register read` exits 69, in form 2 too (Y:4343; CLI44:775). The guard then makes SVC-12's declared-outage check, and otherwise refuses with 2 (Y:4343; svc:23).
- **What the client prints.** Under `--for guard`, the first stderr line on that 69 is `lanes-index: refuse db <cause> <SQLSTATE>`, with `-` where there is no SQLSTATE, built from the answer's fixed cause and SQLSTATE, never `psql`'s message text (CLI47:767; Y:3797–3799). A direct read that found the switch not `primary`, or the number outside the window, prints the same line, with `-` for the SQLSTATE, before any other; every other failed direct read keeps lane 1's §2.6 line 1 first (CLI47:767). No fallback line follows a `refuse db` (section 11.2).
- **The second line, for `schema`,** is as CLI47:767 has it: the next stderr line names the client's `REGISTER_SCHEMA_VERSION`, the database's `readers_from` and `version`, and the remedy Y:2338 names for the case, so that the guard's and the operator's text is the direct read's. The remedy is "upgrade the tools" where the client's number is below `readers_from`, and "the database is behind: run the bootstrap" where it is above `version` (Y:2338; lane 1's U5 review, 1(b)). Through the service, the answer carries the two database numbers (section 13.5), and T113 prints the same line on the direct path.
- **A rollback's declaration** counts only where the cause is `switch-files`, by either path (Y:4449–4454; CLI47:767), so SVC-12's condition 2 reads one fact.
- **The line's bytes** are lane 1's (lane 1's U5 review, M2 and 1(b), #165 6747151224), confirmed by lane 3 (#165 6747313608): `refuse db <cause> <sqlstate>`, four fields, for `unreachable`, `auth` and `switch-files`, and `refuse db schema - <readers_from> <version>`, six fields, for `schema` (section 13.5).

### 5.3 `unavailable`

- **When the service answers it:**
  - on any timeout (Y:4086–4087);
  - at its per-request timer, as `unavailable slow` (Y:4012–4013), whether the request's query is running or the request is still queued behind another's (section 6.2; lane 1's U5 review, S1);
  - before its `psql` child has connected, after a restart (Y:4014–4016);
  - on a cache miss the image cache cannot fill (Y:4046–4049);
  - at once, to a request that arrives during the background whole-file fetch (lane 1's answer, #165 6746620707, OP-15; section 7);
  - when its install has changed, as `unavailable restarting` (Y:3916–3918).
- **What the client counts as `unavailable` too:**
  - no socket (`ENOENT`), or a dead one (`ECONNREFUSED`) (Y:3881–3882);
  - a failed directory or length check (Y:3764–3765, Y:3778–3779);
  - an owner file of another pid namespace or `boot_id`, on a socket that answers the connect; the client then starts none (Y:3784–3788; section 2.4);
  - a server of another uid (Y:3792–3794);
  - a service that accepts a connection and never answers (svc:117);
  - "a bad header, a byte count that does not match, a digest that does not match, a file it did not ask for, an answer over 16 MB, or one not complete within the 300 ms share" (Y:4076–4078). Section 13.7 lists each case under section 13's format.
- **What the client does.** It discards all of the answer and reads directly (Y:4079), within what its budget has left (Y:4087–4088), and writes `.direct` (Y:4153–4154). The guard's client prints the fallback line once, whether that direct read succeeds or fails (section 11.2).

### 5.4 From answer to exit

The client's exits through the socket, from Y's exit mapping (Y:4336–4343) and lane 1's forms (CLI44:769–779):

| What happened | A reader's `register read` | `--for guard`, form 2 | The guard |
|---|---|---|---|
| the text at `n`, through the service | 0 | 0, files cached | parses; decides 0 or 2 |
| `unavailable`, then a direct read that succeeded | 0 | 0, files cached, and `guard: direct (service unavailable)` on stderr | parses; decides 0 or 2 |
| `refuse db <cause>` | 69 | 69; first on stderr, `lanes-index: refuse db <cause> <SQLSTATE>`, and for `schema` the numbers' line next; no fallback line | SVC-12's declared-outage check; otherwise 2 |
| `unavailable`, then a direct read that failed | 69 | 69; first on stderr, the direct read's cause line, and for `schema` the numbers' line next, then `guard: direct (service unavailable)` | SVC-12's declared-outage check; otherwise 2 |

The stderr in the last two rows is lane 1's answer (#165 6746620707, OP-4 and OP-11; CLI46:764, CLI47:767), and section 11.2 gives it. The numbers' line is CLI47:767's second line (section 5.2). The last row is draft 2's `unavailable`-then-failed row, with the fallback line that CLI46:764 prints on every fallback (lane 1's U5 review, M3). The other rows (too late, mode `index`, Q6(c)'s 78, and any other failure) are not the socket's, and stay as Y:4344–4347 and CLI44:769–782 give them.

## 6. The limits

### 6.1 The client's wait: 300 ms

- **300 ms, connect and answer together,** or the service's timer plus 100 ms where SVC-6 raises that timer (Y:4182; tasks:1412). So it is 300 ms with no `register_rtt_ms=`, or one of 100 or less, and `register_rtt_ms=` plus 200 ms above that (CLI44:301): 350 ms at 150 (tp:1966–1971), and 656 ms at Eagle's p90 of 456 ms (Y:4193–4195).
- **At its end** the client stops, discards what it read, and reads directly: an answer "not complete within the 300 ms share" is `unavailable` (Y:4076–4079). A silent service costs one such wait a prompt, "never one per read", because the first fallback writes `.direct` (Y:4153–4156).
- **It is one share of the guard's 5 s,** spent once (svc:124; Y:4182). The direct read after it has `T0 + 3.75 s − now` (Y:4184), and a read through the service that completes after `T0 + 3.75 s` is too late, as any read is (Y:4185). So under `--for guard` the wait ends at the earlier of the `connect()` call plus `w_s` and `T0 + 3.75 s` (section 13.8; lane 1's U5 review, N4).
- **A reader waits the same** `w_s`, at most once a `register read`, and never again in an invocation once `.direct` is written (Y:5443; Y:4153–4156; lane 1's U5 review, N3). Form 3 asks the socket too (section 11.1), so a process whose form 1 was answered can wait again at form 3; `bound_s` has room for it, at least one read above `B` (Y:5460).
- **The guard never waits for a start** (Y:3921–3925; section 8.2).

### 6.2 The service's timer: 200 ms

- **200 ms,** or `register_rtt_ms=` plus 100 ms where `register_rtt_ms=` is over 100 ms; with none, or 0, it is 200 ms (Y:4001–4002, Y:4008; D9's row, Y:3519).
- **`register_rtt_ms=` is a p90:** "the p90 of act 3's `select 1` round trips in one session, taken while the workstation is at work and written when it joins, as `c` and `N_a` are" (Y:4005–4008; text 8″a). Act 3 takes "the median, and the p90, which is `register_rtt_ms=`" (Y:5915; text 8″b). Text 8″ changed only how act 3 measures the input, not the timer's rule (tasks:1408).
- **Inside the client's wait,** so the service answers `unavailable` before the client gives up (Y:4183).
- **The server's bounds** per request, set in `PGOPTIONS`: `lock_timeout` 150 ms and `statement_timeout` 180 ms, so that the server cancels its own query just inside the 200 ms timer (Y:4017–4020, Y:3976).
- **On expiry** the service sends `SIGINT` to `psql`, which cancels the query, and answers `unavailable slow`. If `psql` has not printed the request's marker within 100 ms more, the service kills it; the next request starts a new one in the background, and is answered `unavailable` until it connects. "A reconnect never runs on a request's time" (Y:4012–4016).
- **`SIGINT` goes only to the request whose query `psql` is running,** with Y:4013–4016's 100 ms kill rule. The service takes connections concurrently but sends one query at a time (Y:3982–3984), and each request's timer runs from its arrival (section 13.3), so a request can expire while it is still queued behind another's query. Such a request is answered `unavailable slow` and dropped from the queue, with no `SIGINT`, which would cancel the other request's query or the background fetch (lane 1's U5 review, S1, #165 6747151224). A request that arrives during the background fetch is answered `unavailable` at once (OP-15; section 7).
- **From the request's arrival.** AT-10 reads `unavailable slow` "no earlier than 200 ms after the request reached it", and no earlier than 250 ms at `register_rtt_ms=150` (tp:1966–1970).
- **Not yet shown:** "the 150, 180, 200 and 100 ms values are the design's, for lane 2's fixture to prove" (Y:4026–4029).

### 6.3 16 MB

- **The client reads at most 16 MB,** and stops at its wait, whichever comes first (Y:4079–4080; tasks:1414).
- **An answer over 16 MB is `unavailable`:** the client discards all of it and reads directly (Y:4076–4079).
- **For scale:** the real `lanes/LANES.md` is 2,590,927 bytes (Y:4050), and a 1.35 MB answer took 2.35 ms median from the connect to the last byte (Y:4066–4068).
- **What the 16 MB counts, and its unit,** are section 13.6's proposal: every byte the client reads from the socket for one answer, against 16·2^20 bytes (OP-3).

## 7. The service's connection

- **One long-lived `psql` child on pipes,** logged in as the reader login, run as `psql -X -q -A -t -v ON_ERROR_STOP=0 -v VERBOSITY=sqlstate` (Y:3971–3973). No database driver, and `dependencies = []` stays (Y:3979–3981; svc:34). This is the service's own argv, not the one-shot argv of `lanes-index`'s register commands (CLI44:205–206), which lane 1's §2.2 gives every register call with `VERBOSITY=verbose` (CLI410:206); only the service's child runs `VERBOSITY=sqlstate` (lane 1's post, #165 6752661856, point 2).
- **Its credential:** `reader_url=` and `reader_passfile=` (Y:3460; CLI44:277), read from the 0600 configuration and its password file (Y:3889–3891).
- **Its connection is in its environment, never its argv.** The URL's host, port, database and user, and its `sslmode`, `sslrootcert`, `sslcert`, `sslkey`, `target_session_attrs` and `connect_timeout`, go to `PGHOST`, `PGPORT`, `PGDATABASE`, `PGUSER`, `PGSSLMODE`, `PGSSLROOTCERT`, `PGSSLCERT`, `PGSSLKEY`, `PGTARGETSESSIONATTRS` and `PGCONNECT_TIMEOUT`; `PGPASSFILE` and `PGOPTIONS` are the service's own (Y:3799–3812). A password in the URL, or any other query parameter, makes `serve` exit with the stamp `config-refused`, and clients read directly (Y:3812–3815).
- **The mapping is T103's `pg_url_env`, after every inherited `PG` variable is dropped.** `serve` maps the URL with T103's `pg_url_env(url, what)`, SVC-2's one mapping, which `serve` reuses (lane 1's T103, `a3e1ddee`, #165 6747233899). Before it, every inherited variable whose name begins `PG` is dropped, so `psql` gets only what the configuration gives it: the URL's mapping (SVC-2), the configured `PGPASSFILE`, and `PGCONNECT_TIMEOUT` at `c` (Y:4970); beside them stand the server bounds the service sets itself in `PGOPTIONS` (Y:3976, Y:4017–4020), never an inherited `PGOPTIONS`. That is lane 3's ruling on register paths, which names lane 5's `serve`, through `pg_url_env`, among them (#165 6747611042); B6 records it as ERR-39, an erratum to Y:4970, and contract draft 4.10 states it (CLI410:214). The service's `psql` also gets `PGAPPNAME`, set to `lanes-index`, as T103's `_env` sets it on every path (`lanes-index:1756` at T105 `6928ffb`; CLI410:216; `config.md:110`; lane 1's U5 re-check, #165 6752336451), and its only outcome is `application_name` in `pg_stat_activity` (lane 1's U5 re-check, F1). Round B8's ERR-39 now lists, beside the URL's mapping, the configured `PGPASSFILE`, `PGCONNECT_TIMEOUT` at `c` and `PGOPTIONS` with the call's bounds (Y:3503), and round B9 adds `PGAPPNAME`, set to `lanes-index` (lane 3's ruling, #165 6752360829).
- **The connect: `c` for each address.** `PGCONNECT_TIMEOUT` is `c` for each of the endpoint's `N_a` addresses (Y:3974–3975; text 13, at Y:3923–3925; D9's row, Y:3519), so one connect may take `N_a`·`c` (Y:3507).
  - `c` is the URL's `connect_timeout=`, the connect bound each workstation writes from its own p90 when it joins; with none it is 2, and a value under 2 is refused (Y:3810–3812, Y:5877).
  - `N_a` is `register_addresses=`, default 1 (Y:5877).
  - On register paths `PGCONNECT_TIMEOUT` is set to `c`, not defaulted, so an inherited value cannot lengthen it (Y:4970).
  - Y:3892 (W:3857) still says the service "overrides with 2". Lane 3's ruling makes that an erratum, ERR-25: Y:3892's "which the service overrides with 2" reads "which the service, as every register path, sets to `c` (SVC-6)", as every other place says (#165 6746215510, OP-1).
- **In the background only.** The child starts at the service's first request and restarts lazily after any failure (Y:3971–3973). Its connect runs "outside any share, so that no request waits for it" (svc:124; Y:4024–4025).
- **Where it connects:** the primary's port 5432, directly, through the install's load balancer, never through a pooler (Y:3976–3979; text 15; R51).
- **One query at a time,** one round trip a request. The service handles connections concurrently, but each query, ended by an `\echo` of a per-request marker, goes to the one `psql` in turn (Y:3982–3988).
- **The background whole-file fetch** (section 5.1), on a cache miss the image cache cannot fill (Y:4046–4050):
  - **One connection.** The fetch runs on the held connection, in its own transaction, between requests (Y:3714; Y:4048–4050), so lane 1's install formula, one connection per environment, stands (lane 1's answer, #165 6746620707, OP-15).
  - **A request that arrives during the fetch** is answered `unavailable` at once, and a request's `SIGINT` (Y:4012–4016) never cancels the fetch (6746620707, OP-15).
  - **Its bounds:** `SET LOCAL lock_timeout = '1000ms'` and `statement_timeout = '10s'`, the renderer's row (Y:3527), with an 11 s timer. On expiry the fetch is dropped, and the next miss retries. That is lane 3's ruling, as lane 1 suggested, and B4 records it as ERR-29, an erratum that adds a D9 row for the fetch (#165 6746655077, OP-15).
- **The idle check.** While no request comes, the service sends `SELECT 1` on the held connection every 60 s, under the same timer, and a failure restarts `psql` in the background (Y:3993–3995). So the connection is never idle for the 4 minutes after which the load balancer resets a flow (Y:3995–4000; text 16). Section 10 gives the test-only interval.
- **The server's TCP keepalives** keep it open too: every 60 s in lane 1's install contract (Y:3997–3998), set by the install as `tcp_keepalives_idle: "60"`, `tcp_keepalives_interval: "10"` and `tcp_keepalives_count: "6"`, so that no connection is silent for the load balancer's 4-minute idle reset, the service's held connection among them (Y-ops:143). They are the server's parameters, which the install sets, not the service (Y-ops:143; lane 1's U5 review, N8).
- **What it never does:** write, render or re-index; read the mirror or the outage declaration (Y:4030–4032); read a pid, a `/proc` entry of a lane, a session record or tmux (Y:4436–4437).

## 8. The start on demand

No supervisor of its own: no systemd and no launchd (svc:41; Y:3872–3876).

### 8.1 Who starts it

- **The `SessionStart` block,** on a primary workstation, after its own answer, without waiting, through `serve --start` (Y:3878–3881; section 9).
- **Any client** that finds no socket (`ENOENT`), a dead one (`ECONNREFUSED`), or an owner file from another install, unless a failure stamp is fresh (Y:3881–3883). Item 1 also names an owner file from another namespace, which SVC-2 contradicts. Lane 3's ruling: SVC-2 governs, so a client that finds an owner file from another pid namespace or another boot on a socket that answers reads directly and starts none, and Y:3882–3883's start list drops "another namespace or", as an erratum in B4, ERR-27 (#165 6746655077, OP-8, correcting 6746215510; section 2.4).
- **Never form 4,** which never asks the socket (B7), **and never a writer** (Y:3829–3831).

### 8.2 How

- **From Python, never bash.** The starter runs `lanes-index serve` detached: `subprocess.Popen(..., start_new_session=True)`, which is `setsid`; standard input and output on `/dev/null`, standard error to `register.log`; working directory `/` (Y:3884–3887; tasks:1396). "No bash code detaches anything" (Y:3892–3893).
- **A reduced environment:** `PATH`, `HOME`, `LANG`, the `XDG_*` variables and `LANES_INDEX_CONFIG` (Y:3887–3888), and section 10's two test-only variables where they are set (R37; tasks:1427; research.md DEC-15). No password and no URL is in it (Y:3888–3889; svc:177).
- **Outside the hook's process group.** The new session survives a group kill of the hook, and a timed-out hook's SIGTERM (Y:3893–3898).
- **The guard never waits for a start.** A guard that finds no service starts one, and takes the direct read for that prompt; readers do the same (Y:3921–3925). Waiting for the new service's connect could take its `connect_timeout`, `c` for each of the `N_a` addresses, "which the budget cannot give" (Y:3923–3925; text 13).

### 8.3 One instance: `register.lock`

- **One instance per environment.** `lanes-index serve` takes `flock` on `register.lock` without waiting, and holds it for its whole life (Y:3899–3900; tasks:1397).
- **A second starter exits 0** at once when it finds the lock held (Y:3900–3901).
- **Holding the lock,** the starter tries the socket: one that answers means a live service, and it exits 0; `ECONNREFUSED` means a stale socket, which it unlinks; then it binds and listens (Y:3901–3903).
- **Shown** by probe B1: ten racing clients started ten starters; one bound, nine exited on the held lock, and all ten clients were answered (Y:3903–3906).

### 8.4 When it stops

- **The idle stop.** After 15 minutes, 900 s, with no request, it unlinks its socket under its lock and exits (Y:3912–3914; tasks:1399). Section 10 gives the test-only value.
- **An install change.** It checks its own `lanes-index` file's size, mtime and inode on each request, one `stat`, and when they differ it answers `unavailable restarting` and exits (Y:3914–3918).
- **A configuration change.** It re-reads `lanes-index.conf` and its password file when their mtime changes, and exits if the mode is no longer `primary` (Y:3918–3920).

### 8.5 Only on primary

- **`serve` refuses with exit 2 unless the configuration says `primary`** (Y:3948–3949; tasks:1402), with the stamp `config-refused` (Y:3928–3929). It runs only on primary: it asks T105's mode function and refuses otherwise, in the U5 gate's words (L1:459; lane 1's U5 re-check, N-e, #165 6752336451).
- **Under Q6(c)'s refusal,** `load_config` "refuses with 2 for any subcommand but `mode` and `register read`" (Y:4872), so the refusing build's `serve` exits 2, with no departure from Y (R36; tasks:1403).
- **So it is dormant on every real host** until T328 lifts that refusal. Its behaviour is tested on lane 2's flipped copy of `lanes-index`, L2-0d (tasks:1404; Y:3951–3956).
- **Clients ask the socket only where the mode answer is `primary`** (Y:3948–3950; L5:405).

### 8.6 The failure stamp, `register.failed`

- **What is stamped:** only the causes that make `serve` exit before it listens: `path-too-long`, `config-refused` (a refused configuration, a mode other than `primary`, or a URL parameter SVC-2 does not map) and `no-psql` (Y:3926–3929).
- **Never `dir-check`.** Y:3928 also names `dir-check`, but `register.failed` is a file in that same directory (Y:3767–3768), which is never used once it fails its check (Y:3764–3765). Lane 3's ruling takes it out, as an erratum on Y:3927–3929, ERR-28: `serve` exits 2 naming the directory and writes nothing in it, and `lanes-index status` reports the failure itself (#165 6746215510, OP-9; section 2.2).
- **What it holds:** the UTC and that fixed cause, and no other text (Y:3929–3931).
- **What is not stamped:** a database that refuses or does not answer (`auth`, `unreachable`, a refused schema version). The service listens, answers `refuse db <cause>` or `unavailable`, and reconnects in the background (Y:3931–3934).
- **Who reads it:** a client, only after `ENOENT` or `ECONNREFUSED`; a stamp younger than 60 s means it starts nothing and reads directly (Y:3934–3936; tasks:1400). And `lanes-index status`, which reports a stamp and its cause (Y:3939), and a directory that fails its check, which has no stamp (OP-9), in T512's lines in `cmd_status` (R44; tasks:1419).
- **When it goes:** the service removes any stamp once it listens, and again at its first successful connect (Y:3936–3938).
- **One cause is open:** `no-psql` (OP-10). `dir-check` is closed (OP-9).

## 9. The start-only form: `lanes-index serve --start` (R50)

- **What it does:** it runs the starter of section 8.2 and returns at once, so the bash `SessionStart` block detaches nothing itself (tasks:1401; R50, M:714–720; Y:3892–3893).
- **What it does not do:** ask the service anything, or wait for it (svc:93; Y:4493–4499; tasks:1436).
- **What it runs** is "the start a client runs on `ENOENT`" (Y:4498–4499): the length and directory checks of section 2, the stamp of section 8.6, then the start. It makes no socket try of its own, and runs the client's start path (Y:4497–4499). A live service is left as it is: the `serve` it starts finds `register.lock` held, and exits 0 (section 8.3). This bullet and the next two are lane 5's decision, which adopts lane 1's review position as the contract's text (lane 1's answer, #165 6746620707, OP-13).
- **Its exits:**
  - **0** where it launched `serve`, or where a failure stamp younger than 60 s stands, so that it starts none (Y:3934–3936; section 8.6). Those are its two exit-0 cases, as lane 1 confirmed them (#165 6747475637);
  - **2** for a refused configuration, Q6(c)'s included (Y:4872), a mode other than `primary` (Y:3948–3949), or a failed path or directory check (section 2); a failed directory check is never stamped, by lane 3's OP-9 ruling (section 2.2);
  - **64** for usage.
- **No stamp of its own.** `serve --start` writes no `register.failed`; the `serve` it launches stamps as section 8.6 says.
- **It never meets OP-8's start-none case** (OP-16, closed). That case applies only to a socket that answers: `ENOENT` or `ECONNREFUSED` starts one whatever the owner file says, and only a socket that answers with an owner file from another namespace or boot starts none (lane 3's correction, #165 6746655077; section 2.4). `serve --start` makes no socket try, and runs the start a client runs on `ENOENT` (Y:4497–4499), so it launches `serve`, which exits 0 where another namespace's service holds a shared `register.lock` (section 8.3), and binds where the owner file is stale. So a stale owner file from before a reboot never stops the warm start. Lane 1 confirmed this reading (#165 6747475637), and withdrew one clause of its answer's exit-0 line, "or lane 3's OP-8 case" (#165 6746620707, its :25), since it rested on the OP-8 text lane 3 corrected. Lane 3's corrected OP-8 agrees (#165 6747456383). Lane 1's U5 re-check finds that the clause was quoted two ways for one case, and that keeping the posted words is the more exact choice (#165 6752336451).
- **Its caller** is T513's block, on a `mode=primary` workstation, after its own answer, without waiting (svc:93; tasks:1432). The block exits 0 always (svc:93), so it never branches on `serve --start`'s exit; R36 reads the block the same way (M:594).
- **Under Q6(c)'s refusal** it exits 2 at load, as every subcommand but `mode` and `register read` does (Y:4872). Before group C no real host is `primary`, so the block's start never fires there (tasks:1437).

## 10. The two test-only variables (R37)

- **`LANES_REGISTER_SERVE_IDLE_CHECK_S`:** the interval of section 7's `SELECT 1`, default 60 (tasks:1421; Y:3994).
- **`LANES_REGISTER_SERVE_IDLE_STOP_S`:** section 8.4's idle stop, default 900 (tasks:1422; Y:3912).
- **Test-only** (R37; research.md DEC-15). They shorten AT-10's idle-check case and the idle-stop guess (tasks:1428).
- **Honoured only where set.** Each names whole seconds. A value that is not a positive whole number is ignored, and the default kept, so each has a floor of 1 s (tasks:1423; R37, M:603). A value under the floor is ignored and the default kept, never `config-refused` (lane 3's ruling, #165 6746215510, OP-14; Y:3928–3929). DEC-15 gains "a value that is not a positive whole number is ignored, and the default kept", in B4.
- **The idle check's cap:** below 240 s, the load balancer's idle reset. A value of 240 or more is ignored, and 60 kept (tasks:1423; R37; DEC-15; Y:3995–3997). The idle stop has no cap.
- **The starter keeps both** in its reduced environment where they are set, or the service never sees them (tasks:1427; R37; DEC-15).
- **R37 says "refuse" where T512 says "ignored":** R37's "refuse a value under 1 s" means the value is ignored and the default kept (lane 3's ruling, #165 6746215510, OP-14). OP-14 is closed.

## 11. The client's side, as far as the socket fixes it

### 11.1 One client

- **`lanes-index register read` asks the socket first,** and reads directly when the service is unavailable (Y:3843–3845; tasks:1394). "One socket client, one fallback, one set of timeouts" (Y:3847–3848).
- **Phase 1's `register read` has only its direct path.** The socket, the `.direct` flag and the write-through come with `serve` (Y:4868–4870; CLI46:653). Phase 1 fixes what `serve` must also return: "the files at `n` with per-file digests, written by the client into the cache files" (Y:4870).
- **By form** (CLI44:655–662): form 1, the readers' prefetch, asks the socket; form 2, the guard's prefetch, asks it; form 3, the readers' second read, asks it first, and reads `.direct` as forms 1 and 2 do (lane 1's answer, #165 6746620707, OP-7; CLI47:730); form 4 never asks it (B7). Section 13.9 says what each form sends.

### 11.2 The fallback, and its line

- **The fallback is the direct read** (svc:104; Y:3843–3845). On `mode=primary` it reads the database, never the mirror, except under a declared outage, whose three conditions the client checks, never the service (svc:104; Y:4441–4443).
- **The guard's fallback line,** on stderr, exactly:

  ```text
  guard: direct (service unavailable)
  ```

  `--for guard` sets it (Y:3845–3848). Form 2 prints it once, wherever the service was unavailable and the client then read directly, whether that read succeeded (Y:4342) or failed (svc:104, svc:116–121; tp:1846–1849). It never comes after a `refuse db <cause>`, which is not unavailable and is followed by no direct read (Y:4085–4086). On a 69 it follows the cause line, which stays first (section 5.2). That is lane 1's answer (#165 6746620707, OP-11; CLI46:764, CLI47:767).
- **Test 10's comparison.** Through the service and through the direct read, the guard's stdout, its stderr less this one line, and its exit code are equal, for `ok`, `refuse` and `offer` (svc:86–90; tp:1912–1916).

### 11.3 The service-down flag, `.direct`

- **A file,** `$SE_CACHE_FILE.direct`, written by the first fallback in the invocation. Every later read in that invocation, inside any subshell, finds it and goes straight to the direct path (Y:4153–4156; tasks:1413). Forms 1, 2 and 3 read it (CLI44:688; CLI47:730).
- **Form 4 never reads it** (B7; CLI44:688). `reg_flush` keeps it (CLI44:815).
- **A request the client does not send writes none.** Where a request would pass 65,536 bytes, the client reads directly by its own decision, and writes no `.direct`, since the service is not at fault (lane 1's U5 review, N5; section 13.3). A form-3 call with a `--log-key=` key the request cannot carry is the same, which lane 1 takes (lane 1's U5 re-check, #165 6752336451; CLI411:688; section 13.9).

### 11.4 `w_s`, the client's wait, in `.head`

- **`.head`'s `w_s_ms=`,** in whole milliseconds: 300, or `register_rtt_ms=` plus 200 where `register_rtt_ms=` is over 100 (CLI44:298–301; Y:5457).
- **Wherever this `lanes-index` has `serve`, on primary,** whether or not the service answered this read (O16; tasks:1415; CLI44:303). It is 0 where no service can answer (CLI44:302).
- **The guard copies it** into the bind record, and `guard-bind` sums it into `bound_s` (CLI44:304; Y:5459).

### 11.5 Host-local facts stay with the client

- **The guard keeps them:** the hook's `session_id`, `cwd`, `agent_id` and `prompt`, the window reference and name, and the live record's `name`, `nameSource` and liveness facts, read from this container's session records and tmux (svc:59; Y:4430–4435). It sends none of them (section 4).
- **The guard decides.** It runs today's parser on the text and exits 0 or 2; the offer file, the `/rename` typing and answer 2's writes stay client-side, run detached from the hook (svc:59; Y:4166–4173).
- **The declared outage is the client's.** The client reads the declaration, never the service (Y:4441–4443, Y:4476–4478).
- **`CLAUDE_NO_LANE=1`** exits 0 before any input is read, with no service contacted and nothing started (svc:79–83; Y:4290–4292).

## 12. Where the branch differs from W, and from lane 5's texts

Each passage this contract uses where the branch's text is not W's. The contract follows the branch in each.

| Passage | W | The branch | From |
|---|---|---|---|
| SVC-1's login | W:3674–3682, one login per engineer | Y:3708–3717, two logins; the service connects as `lanes_r_<engineer>` | lane 5's text 11 |
| SVC-3's credential | W:3802, "the READER credential" | Y:3837, "the reader login (§1.4)" | text 12 |
| SVC-4 item 6, the new service's connect | W:3888–3890, "its 2 s `connect_timeout`" | Y:3923–3925, "`c` for each of the endpoint's `N_a` addresses" | text 13 |
| SVC-5, the build order | W:3916–3920, after item 3 lifts Q6(c)'s refusal | Y:3950–3956, beside groups A and B | Y's own citation: Q29, and Brett, #165 6726766577 |
| SVC-6, the `psql` child's login | W:3935, "the READER credential" | Y:3971, "logged in as the reader login" | text 14 |
| SVC-6, never a pooler | W:3940–3943, Flexible Server's PgBouncer | Y:3976–3979, the install's load balancer; CNPG's `Pooler` drops `options` | text 15 |
| SVC-6, the idle check | W:3958–3960 | Y:3995–4000, inside the load balancer's 4-minute idle reset | text 16 |
| SVC-6, `register_rtt_ms=` | W:3965–3968, the median of ten round trips | Y:4005–4012, the p90 of act 3's round trips | text 8″a |
| SVC-6, the timeouts row | W:3973 and W:3980, a 2 s connect | Y:4018 and Y:4024, `c` | text 17 |
| SVC-8, the wait at a raised timer | none | Y:4193–4196, 0.656 s at Eagle's p90 of 456 ms | the branch, citing lane 5's O3, #165 6725361742 |
| Act 3's round trip | W:5864, the median | Y:5915, the median and the p90 | text 8″b |
| The service spec's guard refusals | Wsvc:23, Wsvc:104, Wsvc:124: the guard | svc:23, svc:104, svc:124: the guard's prefetch, its first read; and the second read's ends | lane 3's B4, #165 6738474549 |
| The service spec's one budget | Wsvc:124, "where the read's own timer ends the read within it" | svc:124, "because the read's own timer SHALL end the read, its connect included, within that share" | text 22 |
| The service spec's build order | Wsvc:283, the feature's last slice | svc:293, beside groups A and B | svc:295's sources |
| The guard's second read | Y:4159–4160, "direct if the flag file exists" (as W) | research.md ERR-04: "always direct, at `n`" | B7 |

- **Against lane 5's texts.** The branch carries texts 11 to 22, 8″a and 8″b as written: each new text occurs once in the branch, and no text they replaced remains.
- **Where text 13 stops.** Text 13 replaced SVC-4 item 6 (W:3888–3890) only. Item 2's "which the service overrides with 2" (W:3857) stands at Y:3892, and lane 3's erratum, ERR-25, reads it as "sets to `c` (SVC-6)" (#165 6746215510, OP-1; section 7).

## 13. The bytes on the socket (proposal, lane 5, with lane 1's U5 review folded in)

Draft 2's answer to OP-3, with draft 3's additions and draft 4's fold of lane 1's U5 review. The sources fix what a request and an answer carry (sections 4 and 5); this section fixes how they travel. Drafts 2 and 3 marked each of OP-3's items "(proposal, lane 5, for lane 1's U5 review)"; draft 4 marks each such subsection "with lane 1's U5 review folded in", or says that the review passes it. Every value here that no source gives is marked "(proposal, lane 5)", with its reason. Where a choice touches Y, svc, CLI45, CLI46, CLI47 or one of lane 3's rulings, the bullet says which, and why it stays inside it. Lane 1 has answered OP-4 to OP-7, OP-11, OP-13 and OP-15 (#165 6746620707), and draft 3 encoded what those answers need: OP-4's cause words (13.5); OP-5's sets and listing (13.3 and 13.4); OP-6's resolution (13.4); and what forms 1 and 3 send under it (13.9).

**Lane 1's U5 review** (#165 6747151224) passes the frame: one request per connection; ASCII lines with byte-counted bodies; `text <n> <generation>`; `file <path> <sha256> <size>`, `absent` and `ambiguous`; `end` and the close; the one-line refusals; 16,777,216 bytes over the whole answer; the malformed cases; one monotonic deadline from `connect()`; and nothing written until the answer is whole. Wherever lane 1's review gives bytes or text for something draft 3 proposed, lane 1's replace lane 5's, since lane 1 writes `register read`'s contract (`register-cli.md`'s §3.6) and its direct path (T113), which T512's socket client shares, and reviews that client under U5 (lane 1's U5 re-check, N-d, #165 6752336451). Each such place is marked "(lane 1's U5 review, <item>)", and section 14.3 lists them. Lane 1's U5 re-check of draft 4 passes the section, and OP-3 is closed (#165 6752336451). Draft 5 folds the re-check's SHOULD and NITs, with lane 1's bytes in place of lane 5's by the same rule; each such place is marked "(lane 1's U5 re-check, <item>)", and section 14.4 lists them. Draft 6 cites lane 1's contract draft 4.11 as `CLI411:<n>` where draft 5 cited it as to come, and takes draft 4.11's words where they differ from draft 5's; section 14.5 lists them.

### 13.1 In one view

```text
The request, client to service:

read 1\n
file <path>\n                          a file asked by name; a log's <key> is any non-empty bytes of 0x21-0x7E (13.3)
set <prefix>\n                         a set: lanes/archive/ or lanes/log/ (13.3; OP-5)
list\n                                 the render set's paths present at n (13.3; OP-5)
end\n

The answer, service to client, then the service closes the connection. One of:

text <n> <generation>\n                the head line; then one item for each request line, in the request's order:
file <path> <sha256> <size>\n          for a file line: a file present at n, followed by its <size> bytes and one \n
absent <path>\n                        for a file line: a file absent at n
ambiguous <path>\n                     for a file line: a log name that two logs at n match, differing only by case (13.4; OP-6)
set <prefix> <count>\n                 for a set line: then <count> file records, one per member, in bytewise order of <path>
list <count>\n                         for the list line: then <count> lines, path <path>\n each, in bytewise order
end\n                                  after the last item

refuse db <cause> <sqlstate>\n         <cause>: unreachable, auth or switch-files; four fields (13.5)
refuse db schema - <readers_from> <version>\n
                                       <cause> schema: six fields (13.5)

unavailable\n
unavailable slow\n
unavailable restarting\n
```

### 13.2 What the format must meet

- **The cache family, unchanged.** The client writes from an answer what the direct read writes from its own snapshot: `.head`'s `n=`, `generation=`, `file=` and `absent=` rows, the files, and `.paths` where form 1 or form 3 asks for it, from the list (CLI45:655–704; CLI47:665; CLI46:680; 13.4). `.head` has a `file=` row for every path read at `n`, present or not, and one for each file of a set member (CLI47:699, CLI47:703). So an answer carries the database's values only: `n`, the generation, and, for each file asked, its path at `n`, whether it is present there, its digest and its bytes; for a set, the same for each of its members; and for the list, the render set's paths present at `n`. `.head`'s `c=`, `n_a=` and `w_s_ms=` come from the client's own configuration, as the direct read takes them (CLI45:296–303).
- **No byte added or lost.** A file's bytes travel behind their count, never escaped, quoted or re-encoded. The client never decodes them as text: it hands them to the writer that the direct read hands its own bytes to (T113's). So each cache file holds the same bytes whichever path read it, which is what lane 2's test 10 compares through the guard's output (svc:86–90; tp:1912–1916).
- **Standard-library Python at both ends.** `lanes-index serve`, and `register read` as its client (T512), need `socket`, `select` and `hashlib`, among the modules SVC-3 names (Y:3854–3857). ASCII lines and decimal counts need no `struct` and no third-party framing.
- **One round trip a request.** An answer holds nothing that the request's one transaction, sent as one query, does not read (Y:3985–3988; Y:4038–4041).
- **Small.** ASCII lines that end in `\n`, a byte count before each body, and one end line.

### 13.3 The request (proposal, lane 5, with lane 1's U5 review folded in)

- **One request per connection, said outright** (proposal, lane 5). The client connects, sends one request and reads one answer; the service answers it and closes the connection. Reason: Y's wait runs "connect and answer together" (Y:4182) and is timed "from the `connect()` call to the last byte" (Y:4393), and the close is how the client knows the answer has ended (13.4).
- **Its form, and how it ends** (proposal, lane 5): the line `read 1`, the request's kind and this format's version; one line for each thing the client wants, in the order it wants the answer's items: `file <path>` for a file it names, `set <prefix>` for a set, and `list` for the render set's listing; then the line `end`. Each line ends in one `\n` (0x0A), with no `\r`, and one space (0x20) separates its fields. Reason: both ends are one install, since a client starts a new service when the owner file names another install (Y:3881–3883) and the old one answers `unavailable restarting` (Y:3915–3918). So the version only guards the moment between an upgrade and the old service's exit, for seven bytes.
- **Its encoding** (proposal, lane 5): ASCII. Every name is bytes 0x21 to 0x7E, so no name is quoted or escaped. Reason: every name a client sends is in that range (next bullet).
- **The names** (lane 1's U5 review, M1, in place of draft 3's key rule): `lanes/LANES.md`, `lanes/aliases.tsv`, and `lanes/log/<key>.md`, where `<key>` is any non-empty key whose bytes are all 0x21 to 0x7E, in `check_lane_name`'s class or not, `/` and `,` included.
  - **Names are byte strings.** The service compares names as byte strings, folding A–Z only, and never as file-system paths. So `lanes/log/../LANES.md` resolves to no log, and answers `absent`.
  - **Who sends which key.** Forms 1 and 2 name a log only for a `<key>` in `check_lane_name`'s class, `[A-Za-z0-9._-]`, not opening with `.` or `-`, and skip a key outside it (Y:4956; CLI46:670). Form 3 also names a `--log-key=` key outside the class (CLI47:730), and a key outside the class may hold `/` (CLI46:670). Form 4 never asks (B7).
  - **A key with any other byte is not sent:** a space, a control byte or a byte above 0x7E. Only form 3 can hold such a key, and that form-3 call reads directly, the whole call, so that its one snapshot stays one (Y:4890; 13.9). Reason, lane 1's: reading directly is narrower than a hex line, whose resolved path would then need an encoding too.
- **Sets and the listing** (lane 1's U5 review, M1, under lane 3's ruling, #165 6747048305, ERR-31): `set lanes/archive/` and `set lanes/log/`, those two only, each prefix byte for byte, ending in `/`; and the line `list`, with no field, in place of draft 3's `listing`. These are lane 1's three: `archive` is the set `lanes/archive/`; `--all`'s logs are the set `lanes/log/`; `paths` is the render set's listing at `n` (lane 1's answer, #165 6746620707, OP-5; CLI47:665). A set covers every path of the render set present at `n` that begins with its prefix; the render set's paths are `lanes/archive/<name>.md` and `lanes/log/<name>.md`, with no `/` in `<name>` (Y:258–259; Y:1145–1146, Y:1170–1171), so a set has no subdirectory.
- **The version stays `read 1`** (lane 1's U5 review, 1(c)). No service has shipped (Y:4868), so the `set` and `list` lines belong in `read 1` from the start, as draft 3 already put them, not under a new version, as draft 2 planned. The service answers only `read 1`; a future format bumps to `read 2`, and a service answers any version it does not speak with `unavailable` (below). A mismatch then fails as `unavailable`, the safe outcome, instead of a misparse. Draft 2's reason for the version line stands (above), and `read 1` also guards the case where the owner file names another install (#165 6747151224).
- **File names only, inside B7.** The `read 1` and `end` lines frame the request and carry no fact. A set or the list names render-set files by their paths, never a host-local fact or an `n` (CLI47:665), and lane 3's ruling lets a reader's request name them: "the files it wants, singly or by the contract's member sets, or the render set's listing at `n`" (#165 6747048305; ERR-31, on Y:4036–4037). So a request carries nothing but the names of the files the client wants: no `n`, no generation, no session id, `cwd`, agent id, prompt, window, live record or liveness fact (Y:4036–4037; svc:59, svc:61–63; Y:4611; B7). That is what the recording stand-in of `test_the_guards_request_carries_only_file_names` reads (tp:2045–2047).
- **Its size limit** (proposal, lane 5): 65,536 bytes, the `end` line included. Reason: the largest request a client sends names the register, the aliases and one log for each lane, about 3 KB for a register of 73 lanes, the real one's shape (tp:1903–1906). The limit is about twenty times that, and bounds what a stray connection can make the service hold. With sets, a form-3 request names its family's sets, not each file in them (lane 1's U5 review, M1).
  - **A request over the bound is not sent** (lane 1's U5 review, N5). A client whose request would pass 65,536 bytes reads directly, by its own decision, and writes no `.direct`, since the service is not at fault (section 11.3).
- **How long the service waits for it** (proposal, lane 5): from the accept to the `end` line, at most the client's own wait, `w_s`: 300 ms, or `register_rtt_ms=` plus 200 ms where that is over 100 ms (CLI44:298–301; Y:4182). The service computes it from the key it already reads for its timer (CLI45:278). At that wait it closes the connection with no answer. Reason: a client that has not sent its whole request by then has stopped waiting (Y:4182), so an answer would reach no one. It leaves SVC-6's per-request timer as it is (next bullet).
- **The request's arrival** is the moment the service has read the `end` line. The per-request timer, 200 ms, or `register_rtt_ms=` plus 100 ms where that is over 100 ms (Y:4001–4002), runs from there, as AT-10 reads `unavailable slow` "no earlier than 200 ms after the request reached it" (tp:1966–1970).
- **A connection that sends no request** gets no answer (proposal, lane 5). The service closes it when the client closes, or at the wait above. It is not a request, so it does not move the idle stop's clock, which counts time "with no request" (Y:3912). Reason: a try that only connects, such as the starter's under the lock, where "one that answers means a live service" (Y:3901–3903), then costs the service nothing. There Y:3902's "answers" is the accepted `connect()`, so the starter's try sends no request (lane 1's U5 review, N7). `serve --start` makes no try of its own (section 9).
- **A request the service cannot take** (proposal, lane 5): a first line other than `read 1`; a line that is none of `file <path>`, `set <prefix>`, `list` and `end`; a name outside the three shapes, an empty `<key>` included; a prefix other than the two; no line between `read 1` and `end`; or more than 65,536 bytes before `end`. The service answers `unavailable` (13.5), and writes the fixed phrase `bad request` to `register.log`, never the request's text. Reason: the client then reads directly, as for any `unavailable`, and the log keeps to fixed phrases, as Y:3797–3799 has it.

### 13.4 The text at `n` (proposal, lane 5, with lane 1's U5 review folded in)

- **The head line, with `n` and the generation** (proposal, lane 5, for OP-2; lane 1's U5 review passes it): `text <n> <generation>`.
  - `<n>` is the head the request's transaction read, in ASCII decimal, with no sign: `0`, or a number with no leading zero, as `<size>` is written (lane 1's U5 review, N1). Lane 2's fixture can stand at an empty journal.
  - `<generation>` is the register generation the same transaction read, as PostgreSQL prints a `uuid`: 36 characters, lowercase hex in groups of 8, 4, 4, 4 and 12, joined by `-`.
  - Reason: Y's head, `text <n>` (Y:4064–4066), stays the line's opening, and the generation joins it on the same line because it comes from the same snapshot (Y:4833) and goes to `.head`'s `generation=` beside `n=` (CLI45:694–695). It touches Y:4064–4066, which names no generation, and stays inside Y by adding only what Y:4833 requires. Lane 3's ruling makes that an erratum on Y:4064–4066, ERR-26, whose head reads "`text <n>` with the register generation that transaction read" (#165 6746215510, OP-2); the bytes are this proposal, which lane 1's U5 review passes.
- **One item for each request line, in the request's order** (proposal, lane 5). The i-th item answers the request's i-th line between `read 1` and `end`: one record for a `file` line, a set's head line and its members for a `set` line, and the list's head line and its `path` lines for the `list` line. Reason: the client knows from its own request how many items come and what each answers, so no index travels; a set's or the list's own count says where it ends (below).
- **A file present at `n`** (proposal, lane 5): the line `file <path> <sha256> <size>`, then exactly `<size>` bytes, the file's body, then one `\n`, which is not part of the file.
  - `<path>` is the file's path at `n`: the name asked; for a log, the path it resolved to; for a set's member, its own path (below).
  - `<sha256>` is the database's digest of the file at `n` (Y:4070–4072), the value `.head`'s `file=` row carries (CLI47:699), written as §3.2's canonical row writes it: 64 lowercase hex (CLI45:452–456).
  - `<size>` is the body's length in bytes, in ASCII decimal, with no sign and no leading zero, and `0` for an empty file.
  - Reason for the `\n` after the body: "a byte count that does not match" (Y:4076–4077) then fails at the next byte, before the digest is computed.
  - The client writes `.head`'s `file=` row for it from `<path>`, `1` and `<sha256>` (CLI47:699).
- **How a file absent at `n` is marked** (proposal, lane 5): the line `absent <path>`, with `<path>` the name asked, byte for byte. Reason: the client needs only the fact, and the name ties it to its request. From it the client writes what the direct read writes for a member absent at `n`, so that `.head` and the files are the same both ways (CLI45 §3.6):
  - for `lanes/aliases.tsv`, `.aliases` as the one line `# no lane aliases` (Y:4150; CLI45:682), and the `file=` row `0` and `-` that contract draft 4.7 gives a path absent at `n` (CLI47:699, CLI47:703; CLI45:453–454), which draft 3 left to "whatever `file=` row the direct read writes" (lane 1's U5 review, S3);
  - for a log, an empty `.log.<key>` and `.logpath.<key>`, and the row `absent=<key>` in place of a `file=` row, since no path names it (CLI45:683–684, CLI45:700; CLI47:703).
- **A log name** (lane 1's answer, #165 6746620707, OP-6; CLI46:673; the records' bytes are lane 5's proposal, which lane 1's U5 review passes). The request names `lanes/log/<key>.md` in the key's own spelling, and the service resolves it as the direct read does: case-insensitively among the render set's paths at its `n`, in the request's own transaction, by T113's one function, which `serve` reuses (SVC-3; CLI46:673; Y:4957). That function compares bytes and folds A–Z only, as Python's `bytes.lower()` does, for every key, in the class or outside it (lane 1's U5 review, 1(a)). Form 2 caches no `.paths` (CLI45:680), so the client cannot resolve the name itself. The answer:
  - one log resolves: a `file` record whose `<path>` is the resolved `lanes/log/<File>.md`, which may differ from the name asked in case. A resolved path in another case answers the name asked, and is not "a file it did not ask for" (CLI46:673; Y:4077). The client writes `.log.<key>` from the body, and `.logpath.<key>` as that `<path>` (CLI45:683–684);
  - none resolves: `absent lanes/log/<key>.md`, and the client writes both files empty, with an `absent=<key>` row (CLI46:673);
  - two or more differ from `<key>` only by case: the line `ambiguous lanes/log/<key>.md`, with the name asked. Forms 1 and 2 write neither `.log.<key>` nor `.logpath.<key>`, as the direct read writes neither, and form 3 exits 69 for a key the call was asked for, and drops from the family a cached member that turns ambiguous (C11; CLI46:673, CLI45:683; lane 1's U5 review, 1(a); lane 1's U5 re-check, N-g, as lane 3 accepted it, #165 6752360829; 13.9).
  - It stays inside D17: resolving a path decides nothing, and it is the resolution the direct read makes, in the same `lanes-index` (Y:3835–3838). Both ends resolve alike because they run one function (CLI46:673), and lane 1 has decided that it folds A–Z only (lane 1's U5 review, 1(a)), which answers draft 2's question.
  - `ambiguous` stays a record of its own, as lane 1's answer has it: the answer "marks it as matching two logs" (CLI46:673). The service never answers the whole request `unavailable` for it. That closes the choice draft 2 left open, in `ambiguous`'s favour, and lane 1's U5 review takes it: two logs that differ only by case stand until a hand merge ends them, and a whole-request `unavailable` would cost every prompt a direct read, a `.direct` flag and a fallback line for as long as they stand (lane 1's U5 review, 1(a)). An `ambiguous` record for a name that is not a log is malformed (13.7).
- **A set** (lane 1's U5 review, M1, which draft 3's proposal already met): the line `set <prefix> <count>`, then `<count>` `file` records, one for each member, in bytewise order of `<path>`, as `LC_ALL=C` sorts, none repeated.
  - `<prefix>` is the request's prefix, byte for byte. `<count>` is the number of members, in ASCII decimal, with no sign: `0`, or a number with no leading zero (lane 1's U5 re-check, N-c).
  - Each member is a path of the render set present at `n` that begins with `<prefix>`, read in the request's own transaction (CLI47:665; Y:4036–4041), and its record is a `file` record as above: its path, its digest at `n`, its size, then its body and one `\n`. A set holds no `absent` or `ambiguous` record: it lists what is present.
  - **An empty set** is the line `set <prefix> 0`, with no record after it.
  - The client writes `.head`'s `file=` rows for a set member from its records: one `file=` row for every path the set's answer names, its text cached or not, from its `<path>`, `1` and `<sha256>`, as the direct read does, where a set member "gives one row per file it holds at `n`, its text cached or not" (CLI47:699, CLI411:703; lane 1's U5 re-check, S-a, #165 6752336451). A log that `--all` drops from the class, or one of two that differ only by case (C11), keeps its row, so form 3 never reads it as created (CLI411:703; 13.9). T113 builds the same rows on the direct path (#165 6752336451).
  - **`lanes/archive/`:** the client hands every member to the writer the direct read uses for `.archive`, which holds "`lanes/archive/*`, as `archive_text` prints it today" (CLI46:681).
  - **`lanes/log/`, filtered to the class:** the client keeps a member `lanes/log/<File>.md` only where `<File>` is in `check_lane_name`'s class, tested without dying (Y:4956), and takes it as `--all`'s member `log:<File>` (CLI47:665, which keeps this filter: "of which the client keeps the names in the class"). It resolves `<File>` among the set's paths by T113's one function, as a named log is resolved, and writes `.log.<File>` and `.logpath.<File>` as the direct read writes them, or neither where two differ only by case (C11; CLI46:673, CLI46:683–684). A member outside the class it drops: it writes no file for it, but writes its `file=` row, as every member has one (above; lane 1's U5 re-check, S-a), and checks its record like any other (13.7). The render set never holds `lanes/log/README.md` (Y:258–259; Y:1145–1146).
  - Reason: the count says where the set ends with no closing line of its own, so a cut set is caught as a cut answer is; bytewise order is `.paths`' own order (CLI46:680), and lets the client check that each member follows the last; and each member travels as a named file does, so the client writes it with the same code and the same checks.
- **The list** (lane 1's U5 review, M1, in place of draft 3's listing lines): the line `list <count>`, then `<count>` lines `path <path>`, each one path of the render set present at `n`, in bytewise order, none repeated. This is `.paths` (CLI46:680). `<count>` is written as a set's is: `0`, or a number with no leading zero (lane 1's U5 re-check, N-c).
  - **An empty list** is the line `list 0`, with no line after it.
  - The render set does not hold `lanes/STORE` (Y:258–260; Y:5050; CLI411:680), so neither does the list.
  - The client writes `.paths` from the `path` lines' paths, "one per line, sorted bytewise (`LC_ALL=C`)" (CLI46:680).
  - The list carries paths and no text, so it takes neither a digest nor a body, and its paths, in `.paths`' order, are what `.paths` holds.
- **A path the lines cannot carry** (lane 1's U5 review, M1, with draft 3's reason). Every path an answer names is bytes 0x21 to 0x7E, as every line but a body is (13.7). The render set's names may hold any byte but `/` (Y:1145–1146, Y:1170–1171). So where a path the answer would carry, in a set or the list, has a byte outside 0x21 to 0x7E, the service answers the whole request `unavailable` (13.5), writes the fixed phrase `bad name` to `register.log`, and the client reads directly. A resolved log's path differs from the name asked only in A–Z case, so it always fits. Reason: the lines stay ASCII and split on single spaces. Such a name costs a direct read, which then decides, never a wrong cache, and paths made by lane tooling are in the class, so this is a guard, not a path anyone takes (lane 1's U5 review, M1).
- **How an answer ends** (proposal, lane 5): the line `end` after the last item; then the service closes the connection. The client takes an answer as whole only where it has read the head line, one item for each request line, the line `end`, and then the end of the stream, with no byte after `end\n`. Reason: a cut answer then always lacks its end line or its close, so it is never taken for a whole one, and the digests still catch a corrupted body (Y:4072–4074).
- **Built whole, then sent** (proposal, lane 5). The service writes no byte of a text answer until its transaction has answered and every body has been checked against its digest at `n` (Y:4043–4050). Reason: its timer then ends a request with `unavailable slow` (13.5), never with half a text answer, and no answer mixes two seqs (Y:4064–4065).

### 13.5 `refuse db` and `unavailable` (proposal, lane 5, with lane 1's U5 review folded in)

- **Each is one line, then the service closes the connection** (proposal, lane 5). The client takes one only where the line ends in `\n` and no byte follows it. Reason: a cut or padded refusal then reads as malformed, which is `unavailable` (13.7), so a cut never turns into a refusal.
- **`refuse db`** (lane 1's U5 review, M2 and 1(b), #165 6747151224, in place of draft 3's optional fifth field), in two forms:
  - **`refuse db <cause> <sqlstate>`,** exactly four fields, where `<cause>` is `unreachable`, `auth` or `switch-files`;
  - **`refuse db schema - <readers_from> <version>`,** exactly six fields, where `<cause>` is `schema`. The SQLSTATE field is `-`, since the window is a check on values read (Y:2334), and each number is ASCII decimal: `0`, or a number with no leading zero.
  - **`<cause>`** is one fixed word, never `psql`'s message text (Y:3797–3799, Y:4082–4085), and only these four: `unreachable`, `auth`, `switch-files` and `schema` (lane 1's answer, #165 6746620707, OP-4; CLI47:767). Any other word, or a field count that does not match its cause, is malformed, so `unavailable` (13.7). Both ends are one install, so no unknown word can arrive except from a fault.
  - **`<sqlstate>`** is the five-character SQLSTATE (Y:4085), of `[0-9A-Z]`, or `-` where the refusal has none, as for a check the transaction makes on a value it read (proposal, lane 5). Reason: `-` is what §3.2's canonical row writes for a value that is not there (CLI45:454). It stays inside Y:4085, which gives the SQLSTATE where a refusal has one. Lane 1's answer gives the same `-` for the client's line (CLI47:767). A switch other than `primary` is `refuse db switch-files -` (section 5.1).
  - **Why `schema` carries the two numbers.** A version outside the window "names both numbers and the remedy, 'upgrade the tools' or 'the database is behind: run the bootstrap'" (Y:2338), and after `refuse db` "the client then refuses exactly as the direct read would" (Y:4085–4086). The client knows its own `REGISTER_SCHEMA_VERSION`, and the service's is the same install (Y:3914–3918). But which remedy applies depends on which bound failed, and only the database's `readers_from` and `version` tell (lane 1's U5 review, 1(b)).
  - **Inside Y:3797–3799.** Its "fixed phrases and the SQLSTATE only, never `psql`'s message text" bounds message text, and two integers the database gives are not `psql`'s message text. That is lane 1's reading, and lane 3 reads it the same way, so `refuse db schema - <readers_from> <version>` stands (#165 6747151224; #165 6747313608, item 3).
  - **What the client prints from it** is CLI47:767's second line, after the cause line `lanes-index: refuse db schema -` (section 5.2).
- **The three `unavailable` lines,** exactly:
  - `unavailable`: the server's `lock_timeout` or `statement_timeout`, and any other timeout but the per-request timer's (Y:4017–4021, Y:4086–4087); a request before `psql` has connected (Y:4014–4016); a cache miss (Y:4046–4049); a request that arrives during the background whole-file fetch (lane 1's answer, #165 6746620707, OP-15; section 7); a cause the request itself did not see (section 5.2); a request the service cannot take, a version other than `read 1` included (13.3); and an answer that would name a path the lines cannot carry, with `bad name` in `register.log` (13.4);
  - `unavailable slow`: the per-request timer (Y:4012–4013), for the request whose query `psql` is running and for one that expires while still queued, which is dropped from the queue with no `SIGINT` (section 6.2; lane 1's U5 review, S1);
  - `unavailable restarting`: an install change (Y:3916–3918).
  - The client treats the three alike: it discards the answer and reads directly (Y:4079; Y:4087–4088). The word after `unavailable` is for the record, and for AT-10, which reads `unavailable slow` (tp:1966–1970).

### 13.6 What 16 MB counts (proposal, lane 5; lane 1's U5 review passes it)

- **Every byte the client reads from the socket for one answer:** the head line, each record's line, each body and the `\n` after it, each set's and the list's head line, the list's `path` lines, and the end line; or a refusal's one line (proposal, lane 5). Reason: the bound guards the client's memory and its share of the 5 s, which every byte costs, and one count is one comparison. The lines are a few hundred bytes beside a 2,590,927-byte `lanes/LANES.md` (Y:4050), so counting them changes no real answer.
- **Its unit: 16·2^20 bytes, 16,777,216** (proposal, lane 5). Reason: the client counts bytes, a buffer's megabyte is usually 2^20 bytes, and the choice moves only lane 2's boundary case, which then has one number. It stays inside Y:4079–4080, which says "16 MB" and names no unit.
- **How the client applies it:** it holds at most 16,777,216 bytes of an answer. An answer longer than that is over 16 MB, so `unavailable` (Y:4076–4079): the client stops at the first byte past the bound, or sooner, at a record line whose `<size>` would carry the answer past it. It also stops at its wait, whichever comes first (Y:4079–4080). A large `--all` answer past the bound is `unavailable`, and the call reads directly (Y:4076–4079; lane 1's U5 review, M1).

### 13.7 Every malformed case, each `unavailable`

Y:4076–4079 makes "a mismatch or a malformed answer (a bad header, a byte count that does not match, a digest that does not match, a file it did not ask for, an answer over 16 MB, or one not complete within the 300 ms share)" `unavailable`: the client discards all of it and reads directly. Under this format, these are the cases the client meets, each under Y's words (proposal, lane 5, for the cases and their mapping, which lane 1's U5 review passes; the rows for sets and the `list` line follow lane 1's M1, and are marked so):

| The client meets | Y's words |
|---|---|
| no byte before the end of the stream, or a reset | not complete |
| a first line that is none of the head line, a `refuse db` line and the three `unavailable` lines | a bad header |
| a head line whose `<n>` has a sign, a leading zero before another digit, or a byte that is not a digit, or whose `<generation>` is not 36 lowercase characters in the uuid's groups | a bad header |
| a `refuse db` line whose `<cause>` is not one of the four, or whose field count does not match its cause: four for `unreachable`, `auth` and `switch-files`, six for `schema`; or a field out of its form: a `<sqlstate>` that is neither five of `[0-9A-Z]` nor `-`, a `schema` line whose SQLSTATE field is not `-`, or a number with a sign, a leading zero before another digit, or a byte that is not a digit (lane 1's U5 review, M2 and 1(b)) | a bad header |
| in any line but a body, a byte before its `\n` that is outside 0x20 to 0x7E, a `\r` among them | a bad header |
| any byte after the `\n` of a `refuse db` or `unavailable` line | a malformed answer |
| a record line that is not `file`, `absent` or `ambiguous`, or a field out of its form: a `<sha256>` that is not 64 lowercase hex, or a `<size>` with a sign, a leading zero before another digit, or a byte that is not a digit (lane 1's U5 re-check agrees with this narrowing, #165 6752336451) | a bad header |
| a record whose `<path>` is not the name asked at its place; for a log, one that the name asked does not match case-insensitively, by T113's one function, which folds A–Z only (CLI46:673; lane 1's U5 review, 1(a)); or an `ambiguous` record for a name that is not a log | a file it did not ask for |
| at a `set` line's place, a line that is not `set <prefix> <count>`, or a `<count>` out of its form; at the `list` line's place, a line that is not `list <count>`, or a `<count>` out of its form | a bad header |
| at a `set` line's place, a `set` line whose prefix is not the one asked there, byte for byte (lane 1's U5 review, M1) | a file it did not ask for |
| inside a set, an `absent` or `ambiguous` record; a member whose `<path>` is outside the set's prefix, is not a render-set path (Y:1145–1146), is out of bytewise order, or repeats the member before it (lane 1's U5 review, M1; lane 1's U5 re-check agrees with the narrowing to `absent` or `ambiguous`, #165 6752336451) | a file it did not ask for |
| inside the list, a line that is none of `path <path>`, `end`, `set …` and `list …` (lane 1's U5 re-check, N-b) | a bad header |
| a `path` line whose path is not a render-set path, or is `lanes/STORE`, or is out of bytewise order, or repeats the line before it (lane 1's U5 review, M1) | a file it did not ask for |
| a record or a `path` line where the next item, or `end`, belongs: more records than a set's `<count>`, more `path` lines than the list's, or any after the last item (lane 1's U5 review, M1) | a file it did not ask for |
| a count whose records end early: `end`, another item's `set` or `list` line, or the end of the stream, before a set's last member or the list's last `path` line (lane 1's U5 review, M1); `end` before the last item; or the end of the stream before `end` | not complete |
| a body cut short by the end of the stream | not complete |
| a byte after a body that is not `\n` | a byte count that does not match |
| a body whose sha256 is not its record's `<sha256>` | a digest that does not match |
| any byte after `end\n` | a malformed answer |
| more than 16,777,216 bytes, or a `<size>` that would carry the answer past them | an answer over 16 MB |
| an answer not whole, to the end of its stream, within the client's wait: 300 ms, or SVC-6's timer plus 100 ms (section 6.1) | not complete within the 300 ms share |

- **What the client does at each** (Y:4079; Y:4153–4156): it discards all of the answer, reads directly, and writes `.direct`, as for an `unavailable` line. It writes no cache file from an answer until the whole answer has passed every check above (proposal, lane 5). Reason: a fault found at the last record must leave no file written from the first, so that Y:4079's "discards all of it" holds on disk too.
- **Before any byte,** section 5.3's other cases are `unavailable` as well: no socket, a dead one, a failed directory or length check, an owner file of another namespace or boot, and a server of another uid.

### 13.8 The client's read, in order (proposal, lane 5)

1. Check the path's length and the directory, and read the owner file (sections 2.1 to 2.4).
2. Connect. `ENOENT` or `ECONNREFUSED` is unavailable, and the client starts a service under section 8.1's rule, whatever the owner file says. On a socket that answers, an owner file of another pid namespace or boot is unavailable, and the client starts none (lane 3's ruling, #165 6746655077, OP-8; section 2.4). An owner file from another install: the client starts one (section 8.1), and still sends its request, which the old service answers `unavailable restarting` before it exits (Y:3915–3918; lane 1's U5 re-check, N-f, #165 6752336451). Then check the server's uid by `SO_PEERCRED`, or `LOCAL_PEERCRED` on macOS (section 3).
3. Send the whole request at once.
4. Read until the end of the stream, the 16 MB bound, or the end of the wait, whichever comes first. The wait is one deadline on a monotonic clock, set at the `connect()` call, so it covers the connect, the send and the read together (Y:4182; Y:4393). Under `--for guard` the deadline is the earlier of the `connect()` call plus `w_s` and `T0 + 3.75 s`, since an answer read whole past that line is too late anyway (Y:4185; section 6.1). That cap is T507's client half, under lane 1's R07 gate (lane 1's U5 review, N4).
5. Check the whole answer (13.4 to 13.7).
6. Only then write the cache family at `n`, `.head` last (CLI45:675), or, for a refusal, exit as the direct read would (section 5.2).

Reason: one deadline is Y's "connect and answer together" (Y:4182), and checking before writing keeps 13.7's rule that no cache file is written from an answer that fails.

### 13.9 Which forms ask the socket, and what each sends

- **Form 2, the guard's prefetch,** names all it reads: `file lanes/LANES.md`, `file lanes/aliases.tsv`, and `file lanes/log/<window>.md` where `--lane=<window>` is given and in the class (CLI46:666, CLI46:670). It never names a set or the list. So the guard's whole path fits the format.
- **Form 4** never asks the socket (B7; CLI45:747).
- **Form 1, the readers' prefetch** (lane 1's answer, #165 6746620707, OP-5; CLI47:665; lane 3's ruling, #165 6747048305, ERR-31). One request names every member of `--files <set>`, or of `--all`, each by the render-set names it covers, on these lines (proposal, lane 5, for the lines, in lane 1's bytes, U5 M1):

  | Member | Its request line |
  |---|---|
  | `register` | `file lanes/LANES.md` |
  | `aliases` | `file lanes/aliases.tsv` |
  | `log:<key>` | `file lanes/log/<key>.md` |
  | `archive` | `set lanes/archive/` |
  | `paths` | `list` |
  | `--all`'s logs | `set lanes/log/`, which the client filters to the class (13.4) |

  - `--all` is `register`, `paths`, `archive`, `aliases` and the logs (CLI47:665), so it sends five lines.
  - One form-1 call reads one snapshot (Y:4890), so it never takes some members through the socket and others directly. The service answers the whole request, and an `unavailable` sends the whole call to the direct read.
- **Form 3, the readers' second read** (lane 1's answer, #165 6746620707, OP-7; CLI46:730–733, with CLI47:730 and CLI47:732). It asks the service first, as every reader's `register read` does (Y:3843–3845; Y:5443), and reads `.direct` as forms 1 and 2 do (C6). It rests on lane 3's OP-2 erratum, ERR-26, the generation in the answer (6746620707).
  - **What it sends:** one request naming every member the family holds, by `.head`, and every member that `--files` and `--log-key=` name, as file names only with no `n` (CLI47:730; Y:4611; B7). Each goes on form 1's line for it, above, so a cached set member goes as its set, not file by file (lane 1's U5 review, M1); and a cached `log:<key>`, each `absent=<key>` row and each `--log-key=<key>` go as `file lanes/log/<key>.md` (proposal, lane 5, for the lines).
  - **How it compares:** the answer's head line gives `n′` and the generation, and its records give the digests at `n′`. They take the place of the direct read's in the rules of CLI46:730–733, with CLI47:730's and CLI47:732's set rule. So form 3's `changed=` lines come from the answer's digests at `n′`, and no answer carries a journal entry; draft 2's journal-derived lines are gone (lane 1's U5 review, M1).
    - a `.head` `file=` row is changed where the answer's record for its path differs from it: another `<sha256>`; a `file` record for a row with `0` and `-` (CLI47:699); or, for a row with `1`, no record for its path: an `absent` record, a `file` record that resolves the same key to another path, since the cached path matches that key too, and two paths present at `n′` that match one key would have been answered `ambiguous`, or a set whose members lack it;
    - a path in a set's answer and not among the `file=` rows of that cached set member, or such a row whose path the set's answer lacks, was created or removed under that member (CLI47:730, CLI47:732; lane 1's U5 review, S3);
    - a path in the list and not in the cached `.paths`, or in the cached `.paths` and not in the list, was created or removed;
    - an `absent=<key>` whose record is now a `file` record now resolves;
    - a `.head` that is missing, or names another `n` or another generation than the answer's head line, rewrites the whole family (CLI46:733).
  - **What it writes:** from the same answer, the members `--files` names, or the whole family, then `.head`, as CLI46:731–732 say. One request is one snapshot at `n′` (Y:4890), so the direct read and the service give the same lines and the same family (CLI47:730).
  - **An `ambiguous` record** (lane 1's U5 review, 1(a), in place of draft 3's proposal that form 3 discard the answer and read directly; narrowed by lane 1's U5 re-check, N-g, #165 6752336451, as lane 3 accepted it, #165 6752360829):
    - form 3 exits 69 only for a key the call was asked for, by `--files` or `--log-key=`, as C11 requires of it, and as the direct read would (CLI46:673; CLI411:673);
    - a cached member form 3 names only because the family holds it, and that now matches two logs, is dropped from the family: its `.log.<key>` and `.logpath.<key>` are removed, so the first read that asks for it ends 69 (CLI411:673);
    - lane 3's reason: a read should not fail on a member it never asked for, and the ambiguity still surfaces at the first read that does ask (#165 6752360829). Round B9 records it in `research.md`, as a decision or as an erratum where a Y line needs one.
    - Forms 1 and 2 write neither `.log.<key>` nor `.logpath.<key>` (13.4).
  - **A `--log-key=` key the request cannot carry** (lane 1's U5 review, M1): a key with any byte outside 0x21 to 0x7E, such as a space, a control byte or a byte above 0x7E, makes the whole call read directly (13.3); a key holding `/` or `,` goes on its `file` line. Reason: one call reads one snapshot (Y:4890), and the direct read takes any key (CLI46:669, CLI46:671). Like a request over the bound, it is the client's own decision, so it writes no `.direct`, by lane 1's N5's reason: the service is not at fault. Lane 1 takes this (lane 1's U5 re-check, #165 6752336451), and CLI411:688 says so: `.direct` is written by "the first fallback in the invocation; never a direct read the client chooses, for a request over the bound or a form-3 key the request cannot carry, which is no fallback".

## 14. Changes

### 14.1 Changes from draft 1

- **The status line:** draft 2. Draft 1 is `51fbd17b`.
- **Section 13, new:** the bytes on the socket, lane 5's proposal for lane 1's U5 review, with each of OP-3's items:
  - one request per connection;
  - the request: `read 1`, a `file <path>` line for each name, and `end`; ASCII; at most 65,536 bytes; awaited for at most the client's wait, `w_s`, from the accept;
  - the head line, `text <n> <generation>`;
  - each file's record, `file <path> <sha256> <size>`, then the body and one `\n`; `absent <path>` for a file absent at `n`;
  - the end, the line `end` and the close;
  - `refuse db <cause> <sqlstate>`, with `-` where there is no SQLSTATE, and the three `unavailable` lines;
  - 16 MB as every byte the client reads for one answer, 16·2^20 bytes;
  - each malformed case, under Y:4076–4079's words.
- **OP-2 and OP-3:** proposed in section 13. OP-2's question to lane 3, on Y:4064–4066, stays.
- **OP-6:** the one reading the format needs is proposed in section 13.4, with the `ambiguous` record, pending lane 1's answer. The point stays open.
- **OP-5 and OP-7:** the format stays neutral (section 13.9). OP-5 now says plainly that a set through the socket is more than file names: a spec question for lane 3 and Brett.
- **OP-4, OP-11, OP-13 and OP-15:** unchanged, but for one line each: lane 1 is answering them.
- **The basis** is B3 `a138b1b1`. Section 0 says that B3's `design.md` differs from Y's at five lines that neither draft cites, adds draft 4.5 as `CLI45:<n>`, the same lines as `CLI44:<n>`, and says where B3's `research.md`, `spec.md` and `tasks.md` differ from the files draft 1 read.
- **Pointers to section 13** in sections 1, 4, 5.1, 5.2, 5.3, 6.3 and 11.1. The header's line on what the contract fixes now names section 13's proposals as the one place it adds values.
- **Numbering:** the open points move from section 13 to section 15, and keep their numbers, OP-1 to OP-15.

### 14.2 Changes from draft 2

- **The status line:** draft 3. Draft 2 is `7d462a5e`. Section 14 now holds both lists.
- **The basis** (section 0): lane 1's contract draft 4.6 is cited as `CLI46:<n>`, for its eight changed lines and draft 3's other new cites, with every other `CLI44:` and `CLI45:` cite kept; the three posts folded in have their rows; and errata are named by the `Y:` lines they correct, since the analysis numbers none of T511's.
- **Lane 3's rulings, #165 6746215510:**
  - OP-1: Y:3892's "overrides with 2" is an erratum, and reads "sets to `c` (SVC-6)" (sections 7 and 12);
  - OP-2: Y:4064–4066's head carries the generation, as an erratum; the bytes stay section 13.4's (sections 5.1 and 13.4);
  - OP-9: `dir-check` is never stamped; `serve` exits 2 naming the directory, and `status` reports the failure itself (sections 2.2, 8.6 and 9);
  - OP-14: a value under the floor is ignored and the default kept, never `config-refused` (section 10).
- **Lane 3's ruling, #165 6746655077:**
  - OP-8, correcting 6746215510's line: an owner file from another pid namespace or boot, on a socket that answers, is unavailable, and the client starts none; `ENOENT` or `ECONNREFUSED` still starts one; the install change stays as draft 1 has it (sections 2.4, 5.3, 8.1 and 13.8);
  - OP-15's bounds: 1000 ms and 10 s, the renderer's row, with an 11 s timer (section 7).
- **Lane 1's answers, #165 6746620707, with draft 4.6:**
  - OP-4: the four cause words, the client's first stderr line on the 69, `switch-files` for a rollback's declaration, and "definitive in this request" (sections 5.2, 13.5 and 13.7);
  - OP-5: sets by the render-set names they cover, encoded in section 13 as lane 5's proposal; the spec question stays open (sections 4, 13.3, 13.4 and 13.9);
  - OP-6: the service resolves a log name by T113's one function, and `ambiguous` stays its own record (sections 4, 13.4 and 13.7);
  - OP-7: form 3 asks the service first, reads `.direct`, and compares by the digests at `n′` (sections 1, 11.1, 11.3 and 13.9);
  - OP-11: the fallback line is printed once wherever the client fell back, after the cause line on a 69, and never after a `refuse db` (sections 5.3, 5.4 and 11.2);
  - OP-13: lane 1's review position on `serve --start` is the contract's text, as lane 5's decision, with OP-8's case read under lane 3's correction (section 9; OP-16);
  - OP-15, lane 1's part: one connection, `unavailable` at once during a fetch, and no `SIGINT` to the fetch (sections 5.1, 5.3, 7 and 13.5).
- **Lane 1's two fixes to draft 2** (6746620707), found by their words: the guard's request names its log "with `--lane=`, and only for a name in the class", and the `.direct` clause cites CLI44:688 (section 4).
- **Section 13, the set encoding** (proposal, lane 5): the request lines `set lanes/archive/`, `set lanes/log/` and `listing`; the answer's `set <prefix> <count>` with one `file` record per member, and a listing head line, the word `listing` and a count, with one path per line, each in bytewise order, `0` for an empty one (draft 4 puts lane 1's `list` lines in place of the listing's, section 14.3); the client's filter of `lanes/log/` to the class; a path the lines cannot carry answered `unavailable`; the version kept at 1; form 3's request, and its two fallbacks to the direct read (sections 13.1 to 13.9).
- **`refuse db`'s optional fifth field, after `schema` only** (proposal, lane 5, pending lane 1's U5 review, #165 6746948770): room for the two numbers, whose form lane 1's review fixes (sections 5.2, 13.1, 13.5 and 13.7).
- **The open points** (section 15): OP-1, OP-2, OP-4, OP-6 to OP-9, OP-11 and OP-13 to OP-15 are closed, each with its ruling or answer; OP-3 stays lane 5's proposal under review; OP-5's spec question, OP-10 and OP-12 stay open; OP-16 is new.

### 14.3 Changes from draft 3

- **The status line:** draft 4. Draft 3 is `e4528577`. Section 14 now holds three lists.
- **The basis** (section 0): rows for lane 1's U5 review and its post (#165 6747151224); lane 3's rulings #165 6747048305, 6747313608 and 6747611042; lane 1's T103 (#165 6747233899) and OP-16 confirmation (#165 6747475637), with lane 3's 6747456383; round B4 and B7's `research.md`, for the errata numbers; contract drafts 4.7, cited `CLI47:<n>`, and 4.10, cited `CLI410:<n>`, which the spec PR's head holds; and Y's `operations.md`, cited `Y-ops:<n>`. Draft 4.7's eight changed lines are cited as `CLI47:<n>` wherever sections 1 to 13 cited one of them alone; ranges, and section 15's records, keep their labels.
- **The errata numbers** (round B4, #165 6747379291; B7's `research.md`), each beside the `Y:` lines it corrects: ERR-25 on Y:3892 (sections 7, 12 and 15); ERR-26 on Y:4064–4066 (sections 5.1, 13.4, 13.9 and 15); ERR-27 on Y:3881–3883 (sections 2.4, 8.1 and 15); ERR-28 on Y:3927–3929 (sections 2.2, 8.6 and 15); ERR-29, D9's row for the fetch (sections 7 and 15); ERR-31 on Y:4036–4037 (sections 4, 13.3, 13.9 and 15); and ERR-39 on Y:4970 (section 7). Section 14.2's record keeps draft 3's words.
- **Lane 1's U5 review, #165 6747151224, folded:**
  - **M1, the request:** the log key widened to any non-empty key of 0x21–0x7E, compared as bytes, folding A–Z only, never as a path; a key with any other byte not sent, and that form-3 call read directly; `set lanes/log/` and `set lanes/archive/` only; `list`, with `list <count>` and `path <path>` lines; `bad name` in `register.log`; the new malformed rows; form 3's `changed=` lines from digests, with no journal-derived lines (draft 3 already had none); and all of it in `read 1` (1(c)). Draft 3's client-side filter of `lanes/log/` to the class stays, as CLI47:665 keeps it. Sections 4, 13.1, 13.3, 13.4, 13.6, 13.7 and 13.9.
  - **1(a), `ambiguous`:** form 3 exits 69 at once, forms 1 and 2 write neither file, and T113's lookup folds A–Z only, in one function that `serve` also calls. Sections 4, 13.4, 13.7 and 13.9.
  - **M2, `refuse db`:** only the four cause words; `refuse db schema - <readers_from> <version>`, six fields; every other cause four fields; any other word or field count malformed; the request's own transaction reads the switch and checks the window with T113's function, answering `refuse db switch-files -` or `refuse db schema - <readers_from> <version>`; `auth` and `unreachable` only where this request met them; and the client's second stderr line for `schema`, as CLI47:767 has it. Sections 5.1, 5.2, 5.4, 13.1, 13.5 and 13.7.
  - **M3, the fallback line:** draft 3's OP-11 fold already covers D2:140, D2:149–150 and D2:279 (sections 5.3, 5.4 and 11.2), and D2:150's row, `unavailable` then a direct read that failed, with the fallback line, is already section 5.4's last row. Draft 4 adds the `schema` numbers' line to that row's stderr, and names the row's source.
  - **S1, `SIGINT`:** only the request whose query `psql` is running is cancelled by `SIGINT`, with Y:4013–4016's 100 ms kill rule; a request that expires while queued is answered `unavailable slow` and dropped from the queue, with no `SIGINT`; a request that arrives during the background fetch is answered `unavailable` at once, as OP-15 already has it. Sections 5.3, 6.2 and 13.5.
  - **The NITs:** N1, `0` for `<n>`, as for `<size>`, whose malformed row now says "a leading zero before another digit" too (13.4 and 13.7); N2, the guard's log only with `--lane=` and only in the class, already in draft 3 (sections 4 and 13.9); N3, at most once a `register read` (6.1); N4, the guard's deadline capped at `T0 + 3.75 s` (6.1 and 13.8); N5, a request over the bound not sent, and no `.direct` (11.3 and 13.3); N7, Y:3902's "answers" is the accepted `connect()` (13.3); N8, the server's TCP keepalives (section 7); N9, `.paths` from forms 1 and 3, already in draft 3 (13.2).
  - **T117's OP-9 lines** (D2:63, D2:236 and D2:241): draft 3's sections 2.2 and 8.6 already say that `dir-check` is never stamped and that `status` reports the failure itself, and its section 8.6 says the cause is closed. Draft 4 adds only ERR-28.
  - **S2 and N6 are withdrawn** (#165 6747151224). Draft 3 kept draft 2's reason for the version line (D2:364), and draft 4 keeps it, adding that `read 1` also guards the case where the owner file names another install (13.3).
- **Where lane 1's text replaced draft 3's,** by the rule that lane 1's bytes replace lane 5's, since lane 1 writes `register read`'s contract (`register-cli.md`'s §3.6) and its direct path (T113), which T512's socket client shares, and reviews that client under U5 (draft 5's wording, by lane 1's U5 re-check, N-d, #165 6752336451):
  1. **The log key:** draft 3's "any bytes of 0x21 to 0x7E but `/`, at least one" is now any non-empty key of 0x21–0x7E, `/` and `,` included, compared as bytes, folding A–Z only, never as a path (M1; 13.3).
  2. **A key the request cannot carry:** draft 3's "a key holding `/`, a space or any byte outside 0x21 to 0x7E" is now a key with any byte outside 0x21–0x7E (M1; 13.3 and 13.9).
  3. **The listing:** draft 3's request line `listing`, and its answer, a listing head line with a count and bare path lines, are now `list`, `list <count>` and `path <path>` lines (M1; 13.1, 13.3, 13.4, 13.6, 13.7 and 13.9).
  4. **`refuse db`:** draft 3's four fields with an optional fifth after `schema`, and draft 2's grammar of any `[a-z0-9-]` word, are now four fields for `unreachable`, `auth` and `switch-files`, and six for `schema`, `refuse db schema - <readers_from> <version>`, with any other word or count malformed (M2 and 1(b); 5.2, 13.1, 13.5 and 13.7).
  5. **The client's lines for `schema`:** draft 3's "until then the client prints CLI46:767's line" is now CLI47:767's cause line and the numbers' line after it (1(b); 5.2, 5.4 and 13.5).
  6. **An `ambiguous` record in form 3:** draft 3's "form 3 discards the answer and reads directly" is now "form 3 exits 69 at once" (1(a); 13.4 and 13.9).
  7. **Malformed rows:** a `set` line with another prefix, which draft 3 called "a bad header", and a listing line out of order or not a render-set path, which draft 3 called "a malformed answer", are now each "a file it did not ask for", the latter as a `path` line; and lane 1's rows for repeats, surplus records and counts that end early join them (M1; 13.7).
  8. **`<n>`:** draft 3's "no leading zero" is now `0` or no leading zero (N1; 13.4 and 13.7).
  9. **A reader's wait:** draft 3's "at most once a read process" is now "at most once a `register read`, and never again in an invocation once `.direct` is written" (N3; 6.1).
- **Lane 5's one new proposal,** for lane 1's U5 re-check: a form-3 call with a `--log-key=` key the request cannot carry reads directly by the client's own decision, and so writes no `.direct`, by the reason lane 1's N5 gives for a request over the bound: the service is not at fault (sections 11.3 and 13.9).
- **Lane 3's rulings:**
  - **Sets and the listing** (#165 6747048305): OP-5's spec question is closed, with ERR-31's words, cited at Y:4036–4037. Sections 4, 13.3, 13.9 and 15.
  - **`schema`'s two numbers** (#165 6747313608, item 3): confirmed; Y:3797–3799's "only" bounds message text. Sections 5.2 and 13.5.
  - **Register paths** (#165 6747611042, (a); ERR-39): `serve` maps its URL with T103's `pg_url_env(url, what)` (#165 6747233899) after dropping every inherited `PG` variable, as contract draft 4.10 states it (CLI410:214). Section 7.
- **OP-16, closed** (#165 6747475637, with lane 3's 6747456383): section 9's exit 0 is where it launched `serve`, or where a failure stamp younger than 60 s stands (Y:3934–3936); its OP-8 bullet says the case is never met, and records lane 1's withdrawn clause. Sections 9 and 15.
- **Contract draft 4.10:** its :214 is cited in section 7. Its :195, one resolver for every `lanes-index` call on the mode and SQL paths, adds nothing here, since this contract does not say how the client finds `lanes-index`. Its §3.5 lines are not this contract's.
- **The open points** (section 15): OP-5 and OP-16 are closed; OP-3 now waits for lane 1's U5 re-check; OP-10 and OP-12 stay open; no point is new.

### 14.4 Changes from draft 4

- **The status line:** draft 5. Draft 4 is `735b4e22`. Section 14 now holds four lists.
- **The basis** (section 0): rows for lane 1's U5 re-check of draft 4 and its post (#165 6752336451), lane 3's ruling on it (#165 6752360829), and lane 1's block v4.10, cited `L1:<n>` for its U5 gate; and a bullet on lane 1's contract draft 4.11, which is not posted, so it is cited as "draft 4.11, to come", beside the draft 4.10 line it will change, never by a digest or a line of its own. "Whose rulings" and "Errata" name the new posts, and round B9's ERR-39 list.
- **Lane 1's U5 re-check, #165 6752336451, folded:**
  - **S-a, a cached set member's rows:** the client writes one `file=` row for every path the set's answer names, its text cached or not, and a member it drops keeps its row, so section 13.9's test for a path created under a cached set member stands as written (13.4; OP-7). Draft 4.11, to come, says the same at CLI410:703.
  - **N-a:** OP-15's draft 4 line no longer names a request queued behind the fetch, since a request that arrives during the fetch is answered `unavailable` at once (OP-15).
  - **N-b:** inside the list, a bad header is a line that is none of `path <path>`, `end`, `set …` and `list …`, as the set row was narrowed (13.7).
  - **N-c:** a set's and the list's `<count>` is `0`, or a number with no leading zero (13.4).
  - **N-d:** lane 1 writes `register read`'s contract (`register-cli.md`'s §3.6) and its direct path (T113), which T512's socket client shares, and reviews that client under U5; the rule that lane 1's bytes replace lane 5's stands (section 13's opening, and 14.3).
  - **N-e:** the write-through names T302's cache API (section 5.1), and `serve` asks T105's mode function (section 8.5), in the U5 gate's words (L1:457, L1:459).
  - **N-f:** the client's read, step 2, names an owner file from another install: the client starts one and still sends its request, which the old service answers `unavailable restarting` before it exits (Y:3915–3918; 13.8).
  - **N-g, as lane 3 accepted it** (#165 6752360829): form 3 exits 69 only for a key the call was asked for, by `--files` or `--log-key=`; a cached member that turns ambiguous is dropped from the family, and its `.log.<key>` and `.logpath.<key>` files are removed, so the first read that asks for it ends 69 (13.4 and 13.9; OP-6). Draft 4.11, to come, says it at CLI410:673.
- **`PGAPPNAME`** (lane 1's U5 re-check, F1; lane 3's ruling, #165 6752360829): the service's `psql` also gets `PGAPPNAME`, set to `lanes-index`, as T103's `_env` sets it on every path (`lanes-index:1756` at T105 `6928ffb`; CLI410:216; `config.md:110`). Round B9's ERR-39 lists it beside the URL's mapping, `PGPASSFILE`, `PGCONNECT_TIMEOUT` and `PGOPTIONS` (sections 0 and 7).
- **What lane 1 took or agreed to** (#165 6752336451):
  - lane 5's proposal that a form-3 call with a key the request cannot carry writes no `.direct` is taken, and its "proposal" mark is gone; draft 4.11, to come, says it at CLI410:688 (11.3 and 13.9);
  - both narrowings, §13.7's `<size>` row and its set row, are right. Neither row carried a "proposal" mark of its own, and each now records lane 1's agreement (13.7).
- **OP-16's withdrawn clause** is named once, in the answer's posted words, "or lane 3's OP-8 case" (#165 6746620707, its :25), and draft 4's other wording of it is dropped (section 9; OP-16).
- **Where lane 1's text replaced draft 4's,** by the same rule:
  1. **A set member's rows:** draft 4's "for each member whose text it caches … one row per file the set member holds at `n`" is now "one `file=` row for every path the set's answer names, its text cached or not"; with it, draft 4's "it writes no file and no row for it", for a member outside the class, is now "it writes no file for it, but writes its `file=` row" (S-a; 13.4).
  2. **The list's bad header:** draft 4's "a line that is not `path <path>`" is now "a line that is none of `path <path>`, `end`, `set …` and `list …`" (N-b; 13.7).
  3. **`<count>`:** draft 4's "with no sign and no leading zero" is now "with no sign: `0`, or a number with no leading zero" (N-c; 13.4).
  4. **Whose bytes, and why:** draft 4's "lane 1 builds the client (T105, T113)" is now N-d's wording (section 13's opening, and 14.3).
  5. **Form 3 on an `ambiguous` record:** draft 4's "form 3 exits 69 at once" is now "form 3 exits 69 only for a key the call was asked for", with the drop rule for a cached member (N-g; 13.4 and 13.9).
  6. **The client's read, step 2:** lane 1's sentence for an owner file from another install is added (N-f; 13.8).
  7. **OP-15's draft 4 line:** its request queued behind the fetch is gone (N-a; OP-15).
  8. **The gate's names:** T302's cache API and T105's mode function, in the gate's words (N-e; 5.1 and 8.5).
- **The open points** (section 15): OP-3 is closed by lane 1's U5 re-check; OP-12 records lane 1's answer and stays open for lane 2's view; OP-10 stays open for lanes 2 and 3; OP-6, OP-7, OP-12, OP-15 and OP-16 gain a "Draft 5" line; no point is new.

### 14.5 Changes from draft 5

- **The status line:** draft 6. Draft 5 is `b21199e2`. Section 14 now holds five lists.
- **The basis** (section 0): rows for lane 1's contract draft 4.11, cited `CLI411:<n>` (`60b397f9…`), lane 1's post sharing it (#165 6752661856), lane 3's acceptance of its point 1 (#165 6752697506), and round B8 `270236bb`, the spec PR's head, whose `research.md` holds ERR-25 to ERR-29, ERR-31 and ERR-39 at the numbers draft 5 uses. B7's row now says it was the head when drafts 4 and 5 were written, and the `Y:<n>` bullet names B8's `design.md`. The bullet on "draft 4.11, to come" now says that draft 4.11 is shared, and cited as `CLI411:<n>`. "Whose rulings" names the two new posts.
- **Draft 4.11's lines, cited directly,** each checked against draft 4.11's line before it was cited:
  - CLI411:673, N-g: form 3 exits 69 only for a key the call was asked for, and drops a cached member that turns ambiguous (13.9);
  - CLI411:688: a direct read the client chooses writes no `.direct` (11.3 and 13.9);
  - CLI411:703, S-a: a set member gives one row per file it holds at `n`, its text cached or not (13.4);
  - CLI411:680, `.paths` never holds `lanes/STORE`, beside section 13.4's list, where draft 5 cited no line for it.
- **Where draft 4.11's words replaced draft 5's,** by the rule that lane 1's bytes replace lane 5's:
  1. **S-a's direct-read side** (13.4): draft 5's "which draft 4.11, to come, says at CLI410:703", with CLI47:703, is now draft 4.11's own words, a set member "gives one row per file it holds at `n`, its text cached or not" (CLI411:703).
  2. **A dropped member's row** (13.4): draft 5's "a member the client drops, outside the class or one of two names that differ only by case, keeps its row, and form 3 does not find it created at every call" is now "a log that `--all` drops from the class, or one of two that differ only by case (C11), keeps its row, so form 3 never reads it as created" (CLI411:703).
  3. **The drop rule** (13.9): draft 5's "a cached member that turns ambiguous, which the call names only because the family holds it, is dropped from the family, and its `.log.<key>` and `.logpath.<key>` files are removed" is now "a cached member form 3 names only because the family holds it, and that now matches two logs, is dropped from the family: its `.log.<key>` and `.logpath.<key>` are removed" (CLI411:673). Draft 4.11's "it" is written "form 3".
  4. **What draft 4.11 says of `.direct`** (13.9): draft 5's "a direct read the client chooses is no fallback, since the service was never asked", given there as draft 4.11's words, is now `.direct`'s row in draft 4.11: written by "the first fallback in the invocation; never a direct read the client chooses, for a request over the bound or a form-3 key the request cannot carry, which is no fallback" (CLI411:688).
  - Section 13.9's first `ambiguous` bullet already had draft 4.11's words, and section 11.3's sentence agrees with CLI411:688, so only their cites change. Lane 3's reason in section 13.9 keeps lane 3's words (#165 6752360829).
- **A `psql` older than 15** (CLI411:234; lane 3, #165 6752697506): it is refused with 69, naming its version, as FR-012 requires, in place of Y:1417's fallback, and B9 numbers the erratum. This contract says nothing of `psql`'s version or of Y:1417's fallback, so nothing is added but section 0's rows.
- **`VERBOSITY`** (lane 1's post, #165 6752661856, point 2): section 7 already said that the service's argv is its own, not the one-shot argv of the register commands. Its cite now adds that lane 1's §2.2 gives every register call `VERBOSITY=verbose` (CLI410:206), and that only the service's child runs `VERBOSITY=sqlstate` (Y:3971–3973). No flag changes.
- **ERR-39** (sections 0 and 7): round B8's ERR-39 now names `PGOPTIONS` with the call's bounds (Y:3503), and round B9 adds `PGAPPNAME` (#165 6752360829), where draft 5 gave the whole list to B9.
- **History kept:** section 14.4, and the "Draft 5" lines of OP-6 and OP-7, keep "draft 4.11, to come", since they record what draft 5 read. OP-6 and OP-7 gain a "Draft 6" line.
- **The open points** (section 15): OP-10 and OP-12 stay open; none closes, and none is new.

## 15. Open points

For lane 1, the U5 reviewer, and lane 3, unless a point names another lane. Each names its sources. Where the contract must take one reading to be whole, it says which, and the point stays open. Draft 3 opens each point ruled or answered with "closed: <by whom, which comment>, see §…", and keeps each point's earlier text as its record. Draft 4 does the same for OP-5 and OP-16, adds a "Draft 4" line where a point's record gains something, and gives each erratum its number. Draft 5 does the same for OP-3, and adds a "Draft 5" line where a point's record gains something. Draft 6 adds a "Draft 6" line where a point's record gains something.

Still open after draft 6: OP-10, for lanes 2 and 3; and OP-12, for lane 2's view, since lane 1 has answered it (#165 6752336451). OP-3 is closed by lane 1's U5 re-check of draft 4 (#165 6752336451). No point is new in draft 6.

### OP-1. SVC-4 item 2 still gives the service's connect as 2 s

- **Closed:** lane 3's ruling, #165 6746215510, OP-1, see §7 and §12. Y:3892's "which the service overrides with 2" reads "which the service, as every register path, sets to `c` (SVC-6)", as an erratum in `research.md` Part 1 in B4: ERR-25.
- Y:3892 (W:3857): "`_env` also defaults `PGCONNECT_TIMEOUT` to 5, which the service overrides with 2."
- Against it: text 13, at Y:3923–3925; SVC-6, Y:3974–3975; D9's row, Y:3519; Y:4970; and Y:5877, "`PGCONNECT_TIMEOUT` is set to `c`". Text 13 replaced W:3888–3890, not W:3857.
- This contract: `c` for each of the `N_a` addresses (section 7), as the task line has it.
- For lane 3: an erratum on Y:3892, in `research.md`, or in `design.md` before the archive.

### OP-2. Where the generation sits in the answer

- **Closed:** lane 3's ruling, #165 6746215510, OP-2, see §5.1 and §13.4. Y:4064–4066's head reads "`text <n>` with the register generation that transaction read" (Y:4833), as an erratum in B4: ERR-26. The bytes stay section 13.4's head line, `text <n> <generation>`, lane 5's proposal under lane 1's U5 review (OP-3).
- **Draft 4:** lane 1's U5 review passes the head line, and asks only that `<n>` may be `0` (N1, #165 6747151224; §13.4).
- Y:4064–4066 gives the answer as "`text <n>`, then for each file its path, its byte count, its digest and its bytes", with no generation.
- Y:4833 gives `register read` "the `n` and the register `generation` it read, in the same snapshot". `.head` carries `generation=` (CLI44:695), form 4 checks it (CLI44:745), and the offer's `act` carries it (L5:364).
- This contract: every text answer carries the generation read in its own transaction (section 5.1). Its place in the bytes is OP-3's.
- Draft 2 proposes that place: the head line's third field, `text <n> <generation>` (section 13.4), for lane 1's U5 review.
- For lane 1 to confirm, and lane 3 to say whether Y:4064–4066 takes an erratum.

### OP-3. The bytes on the socket

- **Closed:** lane 1's U5 re-check of draft 4, #165 6752336451, see §13 and §14.4. U5 passes for T511, and OP-3 closes: every item of lane 1's U5 review of draft 2 is folded faithfully, all thirteen items of the U5 gate pass for T511, and draft 4 agrees with contract draft 4.10, ERR-31 and ERR-39. The re-check has no MUST, one SHOULD, S-a, and seven NITs, N-a to N-g, which draft 5 folds, N-g as lane 3 accepted it (#165 6752360829). Lane 1 takes lane 5's proposal that a form-3 call with a key the request cannot carry writes no `.direct`, and agrees with both narrowings of §13.7's rows. T512's and T513's parts of the gate are checked when those come.
- Y fixes what a request and an answer contain, not how they are encoded. Not fixed:
  - the request's form, and how it ends;
  - one request per connection, which Y implies ("connect and answer together", Y:4182; "from the `connect()` call to the last byte", Y:4393) but does not state;
  - the answer's head line, with `n` and the generation (OP-2);
  - each file's fields, their separators and their order;
  - how a file absent at `n` is marked, which `.head`'s `file=` and `absent=` rows (CLI44:699–700) and `.aliases`' `# no lane aliases` (Y:4150) rest on;
  - how an answer ends;
  - the bytes of `refuse db <cause>` with its SQLSTATE, and of `unavailable`, `unavailable slow` and `unavailable restarting`;
  - what the 16 MB counts (the whole answer, or the files' bytes), and whether it is 16·2^20 or 16·10^6 bytes;
  - how long the service waits for a connected client's request.
- For lane 5's next draft of this contract, before T512 starts, under lane 1's review (U5).
- Draft 2 proposes each item, for lane 1's U5 review: the request, one per connection, its end, its size limit and the service's wait for it, in section 13.3; the head line, each file's record, the `absent` mark and the end, in section 13.4; the `refuse db` and `unavailable` lines, in section 13.5; what the 16 MB counts, and its unit, in section 13.6; and each malformed case, in section 13.7. The cause words inside `refuse db` stay OP-4's.
- **Draft 3: still lane 5's proposal, under lane 1's U5 review.** Lane 1 calls the encoding lane 5's (#165 6746620707, OP-4 and OP-5). Draft 3 adds the set and listing lines and records (sections 13.3 and 13.4), names OP-4's four cause words (13.5), and gives `refuse db` an optional fifth field after `schema`, pending lane 1's U5 review (13.5; #165 6746948770).
- **Draft 4: lane 1's U5 review is folded in, and the point waits for lane 1's U5 re-check.** The review (#165 6747151224) passes the frame, and draft 4 takes its three MUSTs, S1 and the NITs, with lane 1's bytes in place of lane 5's wherever both gave them (§13; §14.3): M1's key rule, `set` and `list` lines and `bad name`; M2's four cause words and `schema`'s six fields; and M3's fallback line. One new proposal of lane 5's goes with it: a form-3 call with a `--log-key=` key the request cannot carry writes no `.direct`, by N5's reason (§13.9). The re-check is lane 1's.

### OP-4. `refuse db`'s causes

- **Closed:** lane 1's answer, #165 6746620707, OP-4 (CLI46:653, CLI46:767), see §5.2 and §13.5. The causes are `unreachable`, `auth`, `switch-files` and `schema`; under `--for guard` the first stderr line on the 69 is `lanes-index: refuse db <cause> <SQLSTATE>`, with `-` where there is none; a rollback's declaration counts only on `switch-files`; and a cause the request itself did not see is answered `unavailable`, which needs no ruling.
- **Draft 4:** `schema` carries the two numbers, `refuse db schema - <readers_from> <version>`, and the client's next stderr line names them with Y:2338's remedy (lane 1's U5 review, M2 and 1(b), #165 6747151224; CLI47:767). Lane 3 confirms it (#165 6747313608), see §5.2 and §13.5.
- Y names the definitive failures (Y:4082–4085), but only two cause words, `auth` and `unreachable` (Y:3931–3932): none for the switch not `primary`, or a refused schema version.
- The client needs the switch's word to tell "the switch at `files`", since a rollback's declaration counts only where the read found the switch at `files` (Y:4449–4453).
- It also needs a line for the 69's first stderr line, "the cause the guard quotes" (CLI44:767), while the service sends no `psql` message text (Y:3797–3799).
- And the connect runs only in the background, and a request before it connects is `unavailable` (Y:4014–4016). So it is not said when "the connection refused" or "an authentication error" is "definitive in this request" (Y:4082–4084), rather than a past background attempt's outcome, which svc:23's "never answer from what it read before" may exclude.
- For lane 1 and lane 3.

### OP-5. Sets the client cannot name

- **Closed:** lane 1's answer, #165 6746620707, OP-5 (CLI46:665), and lane 3's ruling on the spec question, #165 6747048305, see §4, §13.3 and §13.9. Lane 1's answer: each member goes in one request by the render-set names it covers, `archive` as the set `lanes/archive/`, `--all`'s logs as the set `lanes/log/`, which the client filters to the class, and `paths` as the render set's listing at `n`; a set names files only. Lane 3's ruling: a reader's request may name the files it wants singly or by the contract's member sets, or ask for the render set's listing at `n`. B5 records it as ERR-31, which reads Y:4036–4037's "The request names the files it wants" as "the files it wants, singly or by the contract's member sets, or the render set's listing at `n`", and still nothing else, citing Y:4036–4037 as lane 1 corrected it (#165 6747151224). Contract draft 4.7 states it (CLI47:665). The bytes are lane 1's (lane 1's U5 review, M1): `set lanes/archive/`, `set lanes/log/` and `list`.
- **Draft 3's record, the spec question, for lane 3 and Brett:** whether a reader's request may name a set or the listing, when Y:4036–4037 says the request names the files it wants "and nothing else" (Y:4611; B7). Lane 1 has asked lane 3 the same (#165 6746948770). If the answer is no, form 1's sets read directly (§13.9).
- The request names files (Y:4036–4037; svc:59). Form 1's `--all` (every log at `n`), its `archive` member (`lanes/archive/*`) and its `paths` member (the render set's paths present at `n`) are sets whose members the client cannot know before the read (CLI44:665, CLI44:680–681).
- How they are asked of the service, under "file names only", is not said.
- For lane 1 and lane 3.
- Draft 2's format stayed neutral on it (section 13.9), pending lane 1's answer. Said plainly then: asking the service for one of these sets is more than file names, so it is a spec question for lane 3 and Brett, not a matter of the format. Draft 3 keeps that question open, above.

### OP-6. A log named in another case

- **Closed:** lane 1's answer, #165 6746620707, OP-6 (CLI46:673), see §13.4 and §13.7. The service resolves the name by T113's one function, which `serve` reuses; the answer names the resolved path, marks the name absent, or marks two matches. Draft 2's choice between an `ambiguous` record and answering the whole request `unavailable` is closed in `ambiguous`'s favour.
- Form 2 asks for `lanes/log/<window name>.md` (Y:4142–4144). The direct read writes `.log.<key>` where the key "resolves case-insensitively to a `lanes/log/<File>.md` of the render set", with `.logpath.<key>` the path it resolved to, and neither where two logs differ only by case (CLI44:673, CLI44:683–684; Y:4957).
- Through the service the client must write the same two files, or test 10's byte-identity can break through `parse_log_stream`'s `unreadable:` warnings, which name `.logpath`'s path (Y:4957; svc:86).
- Whether the service resolves the name, and how its answer names the path it resolved to, is not said.
- For lane 1.
- Draft 2 proposed the one reading its format needs (section 13.4): the service resolves the name case-insensitively at `n`, the `file` record names the resolved path for `.logpath.<key>`, and `ambiguous` marks two logs that differ only by case. Lane 1's answer matches its `file`, `absent` and `ambiguous` records, with T113's one function in place of draft 2's ASCII-only fold.
- **Draft 4:** lane 1 decides that T113's function compares bytes and folds A–Z only, for every key, and takes the `ambiguous` record; form 3 exits 69 at once on it, and forms 1 and 2 write neither file (lane 1's U5 review, 1(a), #165 6747151224; §13.4, §13.9).
- **Draft 5:** form 3 exits 69 only for a key the call was asked for, by `--files` or `--log-key=`; a cached member that turns ambiguous is dropped from the family, and its `.log.<key>` and `.logpath.<key>` files are removed, so the first read that asks for it ends 69 (lane 1's U5 re-check, N-g, #165 6752336451, accepted by lane 3, #165 6752360829; draft 4.11, to come, at CLI410:673; §13.4, §13.9).
- **Draft 6:** contract draft 4.11 states it at CLI411:673 (#165 6752661856), and section 13.9 now takes its words for the drop rule (§14.5).

### OP-7. Form 3 and the socket

- **Closed:** lane 1's answer, #165 6746620707, OP-7 (CLI46:730), see §11.1 and §13.9. Form 3 asks the service first, reads `.direct` as forms 1 and 2 do, and finds the cached paths changed in `(n, n′]` by their digests at `n′`, against `.head`, a cached `.paths` and the `absent=` keys. It rests on lane 3's OP-2 erratum, ERR-26.
- **Draft 4:** form 3 also counts a path created or removed under a cached set member, and `.head` has a `file=` row for every path read at `n`, present or not (CLI47:699, CLI47:703, CLI47:730, CLI47:732; lane 1's U5 review, S3). Its request names a cached set member as its set, and a key with a byte outside 0x21–0x7E makes it read directly (lane 1's U5 review, M1). See §13.9.
- **Draft 5:** a cached set member has one `file=` row for every path its set's answer names, its text cached or not, so a log the client drops is not found created at every call (lane 1's U5 re-check, S-a, #165 6752336451; draft 4.11, to come, at CLI410:703; §13.4). Form 3 exits 69 only for a key it was asked for, and drops a cached member that turns ambiguous (N-g, as lane 3 accepted it, #165 6752360829; §13.9). A key the request cannot carry writes no `.direct`, which lane 1 takes (#165 6752336451; §11.3).
- **Draft 6:** contract draft 4.11 states S-a at CLI411:703, N-g at CLI411:673 and the no-`.direct` rule at CLI411:688 (#165 6752661856), and sections 11.3, 13.4 and 13.9 cite them (§14.5).
- Form 3 reads at the head `n′`, and needs the paths changed in `(n, n′]` (CLI44:726–735; Y:4900). The request carries no `n` (svc:59; B7), so the service cannot answer "changed since `n`".
- Not said: whether form 3 asks the socket, comparing the answer's digests with `.head`'s `file=` rows for every cached member, or always reads directly, as form 4 does. Y:4900's "(SVC-8's rule)" points at Y:4157–4161, which ERR-04 reads as "always direct" for the guard's read only.
- For lane 1 and lane 3.
- Draft 2's format stayed neutral on it (section 13.9), pending lane 1's answer.

### OP-8. An owner file from another namespace: start one or not

- **Closed:** lane 3's ruling, #165 6746655077, OP-8, which corrects 6746215510's OP-8 line and answers lane 1's question on it (6746620707), see §2.4, §8.1 and §13.8. SVC-2 governs: an owner file from another pid namespace or another boot is unavailable, and the client reads directly and starts none; `ENOENT` or `ECONNREFUSED` still starts one, whatever the owner file says. In B4: an erratum, ERR-27, drops "another namespace or" from Y:3882–3883's start list, which keeps the install change that Y:3914–3918's `unavailable restarting` covers; and tasks:1393's "as absent" (B3's `tasks.md:1407`) becomes "as unavailable, and starts none".
- SVC-2: "each container's client refuses the other's service and starts none" (Y:3786–3788).
- SVC-4 item 1: any client that finds "an owner file from another namespace or another install" starts one (Y:3881–3883). T512: such a client "treats the service as absent" (tasks:1393).
- This contract fixes only that such a service is unavailable to the client (section 2.4). Where the lock is shared, a start there finds `register.lock` held, and exits 0 (section 8.3).
- For lane 3.

### OP-9. Where a `dir-check` stamp goes

- **Closed:** lane 3's ruling, #165 6746215510, OP-9, see §2.2, §8.6 and §9. `dir-check` is never stamped: `serve` exits 2 naming the directory, and `lanes-index status` reports the failure itself, with an erratum on Y:3927–3929, ERR-28. Lane 1 reviews the `status` line (R44).
- **Draft 4:** lane 1's U5 review asks that draft 3 fold it where draft 2's lines 63, 236 and 241 stood (T117), and draft 3 does (§2.2, §8.6). Lane 1 checks those `status` lines under R44 (#165 6747151224).
- A directory that fails the check "is never used and never repaired" (Y:3764–3765). But `dir-check` is a stamped cause (Y:3928), and `register.failed` is a file in that directory (Y:3767–3768, Y:3929–3930).
- A client checks the directory before it would read a stamp (Y:3761–3763), so only `status` would read one.
- For lane 3, and for lane 1, whose `status` reports it.

### OP-10. No `psql`

- Y: `serve` stamps `no-psql`, and exits before it listens (Y:3926–3931).
- tp:2028–2030: with no `psql` on its `PATH`, the service "answers `refuse`".
- This contract follows Y (section 8.6).
- For lane 2, whose test it is, and lane 3. **Still open.**
- Lane 5 proposed exit 2 for `serve` on `no-psql` (#165 6746631207), as for `path-too-long`, the directory check and `config-refused`: each is an end before the socket listens (Y:3926–3929). Lane 1's T102 follow-up gives the `no-psql` stamp 2 in `exit-codes.md`, and leaves this point's question open (#165 6746948770): whether the service ever answers `refuse` without `psql` (tp:2028–2030).

### OP-11. The fallback line where the direct read fails

- **Closed:** lane 1's answer, #165 6746620707, OP-11 (CLI46:764, CLI46:767), see §5.3, §5.4 and §11.2. The fallback line is printed once, wherever the service was unavailable and the client then read directly, whether that read succeeded or failed (svc:104, svc:116–121; tp:1846–1849). It never comes after a `refuse db`, and on a 69 it follows the cause line.
- svc:104: with the service unavailable, the hook "SHALL print `guard: direct (service unavailable)` on stderr, with the same exit code".
- CLI44:764 and Y:4342: form 2 prints it only where the service did not answer and the direct read did. CLI44:767 makes the first stderr line on a 69 the cause the guard quotes.
- Drafts 1 and 2 followed CLI44 (section 11.2); draft 3 follows CLI46.
- For lane 1 and lane 3, to confirm that svc:104 reads so.
- **Draft 4:** lane 1's U5 review (M3) asks that draft 3 fold this where draft 2's lines 140, 149–150 and 279 stood, with draft 2's line 150's row for `unavailable` then a direct read that failed; draft 3 does, and §5.4's last row is that row (#165 6747151224).

### OP-12. macOS's peer check

- Y:3794–3796: "Where `SO_PEERCRED` is not available (macOS offers `LOCAL_PEERCRED` instead, untested here), the directory's owner and mode are the whole protection."
- tasks:1392 and the task line: `LOCAL_PEERCRED` on macOS.
- This contract takes `LOCAL_PEERCRED` at both ends (section 3). No source names a macOS test bed for it, and the owner file has no namespace or boot id there (Y:3783).
- For lane 1, and lane 2: whether T512 shows it on macOS, or records it as not shown, with Y's fallback.
- **Draft 5, lane 1's answer** (#165 6752336451):
  - lane 1's tasks need no macOS test bed, and none of lane 1's code branches by platform;
  - the platform branches on the read path are T512's, `LOCAL_PEERCRED`, the 103-byte socket path and the owner file (Y:3794–3796, Y:3774–3776, Y:3783), and T507's, `T0` from `ps -o etime=` (Y:4263–4267);
  - the code leg's `tests.yml:7–8` records Brett's decision of 2026-10-15, as lane 1's post quotes it: "THE SUITE RUNS ON LINUX ONLY. Brett Heap decided on 2026-10-15, for the whole estate, to take the macOS and Windows test jobs out of CI";
  - lane 1 recommends that T512 record `LOCAL_PEERCRED` as not shown, with Y's fallback, "the directory's owner and mode are the whole protection" (Y:3794–3796);
  - a macOS test bed would reverse Brett's recorded decision, so it would need Brett's own word, not a lane's.
- For lane 2's view. **Still open.**

### OP-13. `serve --start`'s exits, and its first step

- **Closed:** lane 5's decision, which adopts lane 1's review position (#165 6746620707, OP-13) as the contract's text, see §9. No socket try of its own; the client's start path; 0 when it launched `serve` or the rules start none; 2 for a refused configuration (Q6(c) included), a mode other than `primary`, or a failed path or directory check; 64 for usage; no stamp of its own. Its OP-8 clause is read under lane 3's later correction, and OP-16 asks lane 1 to confirm that reading.
- **Draft 4:** lane 1 confirms it, and its exit 0 is where it launched `serve`, or where a failure stamp younger than 60 s stands (Y:3934–3936) (#165 6747475637; OP-16, closed; §9).
- R50 is "lean on `serve --start`" (M:717). No source gives its exits, but Q6(c)'s 2 at load (Y:4872).
- Not said: whether it tries the socket before it starts `serve`. Without that, each session start with the service running costs one more Python start, which section 8.3 then ends with exit 0.
- T513's block reads neither (svc:93).
- For lane 1 and lane 3.

### OP-14. R37's floor: refused or ignored

- **Closed:** lane 3's ruling, #165 6746215510, OP-14, see §10. A value under the floor is ignored and the default kept, never `config-refused`, and DEC-15 gains "a value that is not a positive whole number is ignored, and the default kept" in B4.
- R37: "with a floor (refuse a value under 1 s)" (M:603). DEC-15: "with a floor of 1 s".
- T512: "a value that is not a positive whole number is ignored, with the default kept" (tasks:1423).
- This contract follows T512 (section 10).
- For lane 3, to confirm that R37's "refuse" means the value is not taken, and not a `config-refused` exit.

### OP-15. The background whole-file fetch

- **Closed:** lane 1's part, #165 6746620707, OP-15, and its bounds, lane 3's ruling, #165 6746655077, OP-15, see §7. One connection: the fetch runs on the held connection, in its own transaction, between requests; a request that arrives during it is answered `unavailable` at once; a request's `SIGINT` never cancels it. Its bounds are `SET LOCAL lock_timeout = '1000ms'` and `statement_timeout = '10s'`, the renderer's row (Y:3527), with an 11 s timer; on expiry the fetch is dropped, and the next miss retries. B4 records an erratum that adds a D9 row for the fetch: ERR-29.
- **Draft 4:** a request that expires while queued behind another request's query is answered `unavailable slow` with no `SIGINT` (lane 1's U5 review, S1, #165 6747151224; §6.2).
- **Draft 5:** the line above no longer names the fetch. A request that arrives during the fetch is answered `unavailable` at once (§5.3, §7), and the fetch runs between requests, so no request waits behind it (lane 1's U5 re-check, N-a, #165 6752336451).
- On a cache miss the service "fetches the file whole in the background, in a transaction of its own, never on a request's timer" (Y:4048–4050). A whole `lanes/LANES.md` took 1,219 ms at a 150 ms round trip (Y:4050–4053).
- The held connection takes one query at a time (Y:3982–3984), under a 180 ms `statement_timeout` (Y:4017–4020), and D9's table has no row for this fetch (Y:3516–3537).
- Not said: whether the fetch holds the one connection, so that requests behind it answer `unavailable slow`, or runs on a second; and its bounds.
- For lane 1 and lane 3.

### OP-16. `serve --start` and OP-8's case (new in draft 3)

- **Closed:** lane 1's confirmation, #165 6747475637, with lane 3's agreement, #165 6747456383, see §9. Draft 3's reading stands: `serve --start` makes no socket try, and runs the start a client runs on `ENOENT` (Y:4497–4499), so it never meets OP-8's start-none case, which applies only to a socket that answers; a stale owner file from before a reboot never stops the warm start. It exits 0 where it launched `serve`, or where a failure stamp younger than 60 s stands (Y:3934–3936). Lane 1 withdraws the clause of its answer's exit-0 line, "or lane 3's OP-8 case" (#165 6746620707, its :25), which rested on the OP-8 text lane 3 corrected.
- **Draft 5:** the withdrawn clause is named once, in the answer's posted words, "or lane 3's OP-8 case" (6746620707, its :25). Lane 1's U5 re-check says that both wordings name the same clause, which is withdrawn, so nothing depends on which is kept, and that the posted words are the more exact choice (#165 6752336451).
- Lane 1's position on OP-13 counts "lane 3's OP-8 case" among the rules under which `serve --start` starts none, with exit 0 (#165 6746620707). It was posted before lane 3's correction of OP-8 (#165 6746655077), and asked lane 3 what 6746215510's "another install" meant.
- Under the correction, `ENOENT` or `ECONNREFUSED` starts one whatever the owner file says, and only a socket that answers with an owner file from another namespace or boot starts none (§2.4).
- `serve --start` makes no socket try of its own, and runs the start a client runs on `ENOENT` (Y:4497–4499). So this contract reads it as never meeting OP-8's case: it launches `serve`, which exits 0 where another namespace's service holds a shared `register.lock` (§8.3), and binds where the owner file is stale, as after a container restart, which leaves its stale files on the overlay (Y:3910–3911), or a reboot. Its exit is 0 either way (§9).
- The other reading, that `serve --start` reads the owner file and starts none on another namespace or boot, would leave the `SessionStart` block's warm start undone after every reboot or container restart, until a client's `ECONNREFUSED` starts one.
- For lane 1, to confirm, and lane 3 if the correction meant otherwise.
