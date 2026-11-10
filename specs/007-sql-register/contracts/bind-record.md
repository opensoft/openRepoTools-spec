# Contract: the bind record, the session's offer file in its bind modes

- Owner: lane openRepoTools-5. Tag: [L5]. Feature 007, `contracts/bind-record.md`, which tasks.md's T501 lands (R50), after the spec PR and before T502. T502, T507 and T510 build on it, lane 4's T412 uses its liveness helper, and lane 2's AT-F6 tests it.
- **Status: draft 6,** written 2026-11-09T23:15Z by lane 5's build writer for T501, on the local branch `lane5/007-T501-bind-record`. Draft 1, `c405f3f1`, was written at 16:31Z from `722078c9`. Draft 2, `40a2ff78`, written at 17:26Z, folded in lane 3's rulings on all 14 of draft 1's open points (lane 3's ruling, #165 6746215510, 2026-11-09T17:03Z).
  - Draft 3, `50429140`, written at 21:12Z, folded in lane 3's rulings on draft 2's points 15 and 16 and on the facts file's two removers (lane 3's ruling, #165 6747528875, 2026-11-09T18:00Z), and lane 3's narrowing of point 15 (lane 3's ruling, #165 6747722621, 18:09Z). It also took round B4's numbers for two of draft 1's errata, ERR-23 and ERR-24 (#165 6747379291), each in the section it changes.
  - Draft 4, `34b59791`, written at 21:27Z, folded in lane 3's rulings on draft 3's points 17 and 18, which also confirm draft 3's two readings as lane 3's (lane 3's ruling, #165 6751772589, 2026-11-09T21:20Z), and lane 3's correction of point 18's question 1, which keeps `bind` the attempt and sets the files' index apart from it (lane 3's correction, #165 6751818233, 21:23Z).
  - Draft 5, `4cb3c277`, written at 21:57Z, folded in lane 3's ruling on lane 5's reading of draft 4 (#165 6752026548): the facts file is `out=`'s `<offers>/<uuid>.bind.<k>` stem with `.facts`, the outer, the body and the guard's sweep all find it from `out=`, never from `bind`, and where the body cannot read the record itself, the outer hands it that path (lane 3's ruling, #165 6752064118, 2026-11-09T21:35Z).
  - Draft 6 takes round B8's number for the erratum to Y:5394, ERR-43 (the spec PR's head `270236bb`), and records that B8's `tasks.md`, in T502's block, has this contract govern where T502's text and it differ.
- What it says: the offer file as the bind's record (section 1), its four modes (section 2), every key with the phase and the process that writes it (section 3), the bound (section 4), the guard's eleven cases in short (section 5), `pid_start_of` and the liveness test (section 6), who reads the record (section 7), its test seams (section 8), where Y differs from W and which rulings this contract follows over Y's text (section 9), the changes from drafts 1, 2, 3, 4 and 5, and its open points (section 10).
- It states no value its sources do not give. Draft 1 marked with [read] each writer its sources did not name, as lane 5's reading. Lane 3 confirmed that reading, with its point 4's change (lane 3's ruling, #165 6746215510, point 3), so draft 2 carries no [read] mark.

## 0. Sources, citations and terms

| Key | Input | sha256 | Lines |
|---|---|---|---|
| `Y:<n>` | the change's `design.md` in this branch, commit Y `a6497d00` | `35ca5179…` | 6,745 |
| `W:<n>` | W `bb08d080`'s `design.md`, which T501's line cites | `76d6a4a0…` | 6,636 |
| `Ytp:<n>` | the change's `test-plan.md`, lane 2's v3f | `8702b70a…` | 9,145 |
| `spec:<n>`, `svc:<n>` | the change's `specs/sql-register/spec.md` and `specs/register-service/spec.md` | `d5974830…`, `9da1fa8d…` | 1,113 and 303 |
| `007-spec:<n>` | this feature's `spec.md` | `00334804…` | 1,589 |
| `tasks:<n>` | `tasks.md` at `722078c9` with lane 5's 43 fold edits, which round B2 folds first (#165 6742056462): `tasks.md.lane5-722078c9`. Its lines up to 1384 are numbered as the branch's | `e3c81f8e…` | 1,779 |
| `CLI44:<n>` | lane 1's contract draft 4.4, `contracts-register-cli.v4.4.md` | `8600078b…` | 963 |
| `R<nn>` | lane 3's rulings memo, R01 to R61, `MEMO-007-rulings-20261108.md`, as ruled (#165 6726556972) | `b2184b1a…` | 839 |
| `L5:<n>` | lane 5's block v5, `lane5-tasks-block-v5.md`, keyed to W | `5d427c42…` | 839 |
| `B2:<n>` | lane 5's answers on phase B, `REPORT-007B-lane5-answers-20261109T1344Z.md` | `91494bf3…` | 193 |
| `pin:<n>` | `code/lanes-edit.sh` at `2a057a6d` (blob `d28ed8f2`), which W and Y cite as `:<n>` | `b68d0cba…` | 18,977 |
| lane 3's ruling, #165 6746215510, point `<k>` | lane 3's rulings on draft 1's 14 open points, #165 6746215510 (2026-11-09T17:03Z), whose full text is lane 3's `RULINGS-L5OP-analysis-20261109.md`, cited to spec `a138b1b1` and code `ddc972d` | `6999a644…` | 77 |
| lane 3's ruling, #165 6747528875, point `<k>` | lane 3's rulings on draft 2's points 15 and 16 and on the facts file's two removers (points 6 and 13), #165 6747528875 (2026-11-09T18:00Z), whose full text is lane 3's `RULINGS-L5-T501d2-analysis-20261109.md`, cited to draft 2 as `d2:<n>` | `0f5642c6…` | 52 |
| lane 3's ruling, #165 6747722621 | lane 3's narrowing of point 15, 2026-11-09T18:09Z, upholding lane 4's view of pid `1` (#165 6747648353): lane 5's saved copy of the post, `read-6747722621.md` | `924d636f…` | 14 |
| lane 3's ruling, #165 6751772589, point `<n>` or reading `<n>` | lane 3's rulings on draft 3's points 17 and 18, confirming draft 3's two readings, 2026-11-09T21:20Z: lane 5's saved copy of the post, `read-6751772589.md`. Its answer to point 18's question 1 is replaced by the correction below | `24abc3b3…` | 20 |
| lane 3's correction, #165 6751818233 | lane 3's correction of 6751772589's point 18, question 1, 2026-11-09T21:23Z, answering lane 5's question (#165 6751803196) with its (a): lane 5's saved copy of the post, `read-6751818233.md` | `dbfb5dca…` | 14 |
| lane 3's ruling, #165 6752064118 | lane 3's ruling on how the outer, the body and the guard's sweep find the facts file, 2026-11-09T21:35Z, answering lane 5's reading of draft 4 (#165 6752026548): lane 5's saved copy of the post, `read-6752064118.md` | `75540676…` | 9 |
| `research:<n>` | round B4's `specs/007-sql-register/research.md`, spec `6fe94d17` (#165 6747379291), which numbers lane 3's errata on draft 1's points | `67441862…` | 807 |
| `research:<n>` at B8 | round B8's `specs/007-sql-register/research.md`, the spec PR's head `270236bb`, which numbers lane 3's correction, #165 6751818233, as ERR-43 (research:640–653 at B8) | `c6cafe86…` | 1,157 |

- **W and Y.** In "Answer 2's detached bind, and the bind as one verb", W:5336–5512 and Y:5387–5563, Y's line is W's plus 51, and the text is W's but for two lines of the bound's sum (section 9). So T501's W:5372–5383 is Y:5423–5434, and its W:5442–5456 is Y:5493–5507. W:5149 is Y:5200, unchanged. This contract cites Y, the branch's change, and follows it.
- **research.md** entries are cited by name: ERR-05 (R05), ERR-10 (R47), ERR-11 (R52), ERR-12 (R53), ERR-14 (R58) and DEC-17 (R48). Lane 3's errata on draft 1's points go into `research.md` Part 1 in B4, from ERR-23 on, and Y's frozen text keeps its words. B4 numbers point 7's erratum ERR-23 and point 14's ERR-24 (#165 6747379291; research:359–367 and research:369–375), and this contract cites both by number and by point (lane 3's ruling, #165 6746215510).
- **The pin's lines** cited here were read at `2a057a6d`, to check the lines Y cites.
- **The basis at draft 2:**
  - The spec PR's head is now B3, `a138b1b1`.
  - B3's `design.md` (`ea5fcc52…`, 6,745 lines) differs from Y's only at lines 2324, 2326, 6305, 6390 and 6393, and this contract cites none of them, so every `Y:<n>` here holds at B3. B3's `test-plan.md` and its two `specs/` files are Y's byte for byte (checked by this writer with `diff` and `cmp`).
  - Lane 1's draft 4.5 (`da5a23ce…`, 963 lines) differs from draft 4.4 only at lines 5, 12, 273 and 957, so every `CLI44:<n>` here still holds.
  - Lane 3's analysis cites `tasks.md` at `a138b1b1` (`e7b63983…`, 1,816 lines). This contract gives the line of `tasks:<n>`'s copy that holds the same text: B3's :601, :603, :621 and :1437 are the copy's :596, :598, :616 and :1423.
  - Lane 3's analysis cites `lanes-edit.sh` at code `ddc972d`. This contract gives the pin's line for the same code: its `d:12325` is pin:10968, `d:12465` is pin:11108, and `d:12512` is pin:11155.
- **At draft 3:** B4 `6fe94d17` keeps B3's `design.md` (`ea5fcc52…`), which differs from Y's at the same five lines, so every `Y:<n>` here still holds at B4 (checked by this writer with `diff`).
- **At draft 6:** round B8's `tasks.md` (spec `270236bb`, `cb3ee143…`, 2,500 lines), in T502's block, has this contract govern where T502's text and it differ: "Where this text and T501's contract differ, the contract governs" (tasks:617 at B8).

**The writers**, as this contract names them:
- **the guard**: `guard_run` in a prompt's hook. At the prompt that makes the offer it writes the file through `guard_offer` (pin:10960–10971); at each later prompt its offer-site branch answers from the record (Y:5493).
- **the hook**: `guard_answer`'s choice 2, in the hook whose prompt is the answer `2` (Y:5393). T501's line calls its write of `c`, `n_a` and `w_s_ms` the guard's (tasks:581), and T507 places that write in `guard_answer` (tasks:747).
- **the outer**: `guard-bind <uuid>`, the detached child, "the pid of record" (Y:5396).
- **the body**: `guard-bind --body <uuid>`, which the outer runs under `timeout` (Y:5397–5398), or `LANES_BIND_BODY` in its place under the seam (R48). It writes no key of the record. It hands `why=` and `note=` to the outer in its facts file (section 1; lane 3's ruling, #165 6746215510, point 6).

## 1. The offer file as the bind's record

- **The record is the offer file itself,** `$CLAUDE_CONFIG_DIR/lanes/offers/<session uuid>` (Y:5423). `guard_offer_file` names it `${CLAUDE_CONFIG_DIR:-$HOME/.claude}/lanes/offers/<uuid>` (pin:10271–10273). It is "keyed by the transcript, read by the guard at the offer site …, and the session's own fact" (Y:5423).
- **Not a new state file:** "The detached bind's record is the existing offer file in new modes, not a new state file", since "it is keyed by the transcript and the guard already reads it" (Y:6057, row 31).
- **On the mount shared across containers:** "The directory is a bind mount shared across containers …, so the record names its host and container, and liveness is judged only where both match" (Y:5423).
- **Its form:** `key=value` lines (Y:5423). `note=` may repeat, one line per act still owed (Y:5432).
- **Who creates, rewrites and removes it:**
  - **Created** by the guard at the offer, `mode=name-drift` with the offer's identity, its directory made by `mkdir -p` (`guard_offer`, pin:10962–10971). That is today's code, and the bind keeps it, under `umask 077` (below).
  - **Rewritten** by the hook to `mode=binding`, "by temp file and `mv`" (Y:5393; tasks:602), with the attempt's first keys (section 3).
  - **The hook takes the outer's pid,** the `<p>` its "BINDING" text prints, as `$!` inside Y:5394's fork, returned through a command substitution. `$detach` runs as a bash array, never as a string or through `eval`. Lane 3's probe on Eagle found `$!` equal to the outer's `$$` in all three forms, `setsid`, python3 and `nohup`, and not under `eval` (lane 3's ruling, #165 6746215510, point 4).
  - **Rewritten** by the outer at its start, "with its own `$$` and start time" (Y:5396), and at its end, with the outcome (Y:5486–5488), but only over its own `binding` (lane 3's ruling, #165 6751772589, point 18):
    - Just before the outer writes its outcome, it reads the record again. It writes only where the record is still its own `binding`, with its own `pid` and `pidstart`. Otherwise it leaves the record as it is: case 11 can leave a live outer behind, and by its end the file may hold another offer's record (draft 3's point 18, ruled in section 10).
    - Its `.out` still carries its outcome, for the removers and for a person reading it.
    - The write is still a temporary file and `mv -f` (below). A change between that read and the `mv` is the one window left, and it is milliseconds wide.
  - **Rewritten** by the guard after a failure, to `mode=name-drift` with `bind=k` (Y:5502–5503), and at the stale row (Y:5505).
  - **Every rewrite,** by the hook, the outer or the guard, writes a temporary file in `offers/` and runs `mv -f` (lane 3's ruling, #165 6746215510, point 2).
  - **Removed** by the guard after "MOVED" (Y:5501), and dropped by it where it cannot be read (Y:5507). Removal is `rm` (lane 3's ruling, #165 6746215510, point 2).
- **Its mode bits:** every write is under `umask 077`, so the record is 0600 and a new `offers/` is 0700. An existing `offers/` is not `chmod`'d (lane 3's ruling, #165 6746215510, point 1; after Y:5172's "mode 0600, in a 0700 directory" for F10's pending record, and Y:5394's and Y:5408's fork).
- **The per-attempt output file,** `<offers>/<uuid>.bind.<k>.out`, `<k>` being the files' index, which is apart from the attempt (lane 3's correction, #165 6751818233):
  - **The files' index** is one above the highest index among the uuid's `.bind.*` files, and 1 where there are none. The hook names it at the fork (Y:5394). So a new attempt never reuses a `<k>` whose files are still there, and Y:5394's `>` never meets a live outer's `.out` (lane 3's ruling, #165 6751772589, point 18, as lane 3's correction, #165 6751818233, corrects it).
  - **It is not the attempt.** `bind` stays the attempt count, 1 to 3 per offer (Y:5429), and `bind=k+1` at the next `2` counts attempts only (section 3). The `k` of `bind=k` is the attempt, and the `<k>` of the files' names is the index: two numbers. After a case 11, the next offer's attempts count from 1 again, while their files' index runs on above the leftover files' (lane 3's correction, #165 6751818233).
  - The outer's stdout and stderr go to it, with its children's (Y:5394, Y:5408).
  - It is 0600: the hook forks under `( umask 077; … )` (Y:5394), and Y:5408 has "stdout and stderr to one 0600 file per attempt" (also Ytp:5970).
  - The record's `out` names it (Y:5431): the attempt's own file. The outer and the guard find it there, not by `bind`, and the facts file sits beside it (lane 3's correction, #165 6751818233). It is kept after a failure (Y:5487). The outer, the body and the guard's sweep all find the facts file from `out=` too, never from `bind` (lane 3's ruling, #165 6752064118; below).
- **The per-attempt facts file,** `<offers>/<uuid>.bind.<k>.facts`, 0600, beside the `.out` that the record's `out` names, with the same `<k>`: it is `out=`'s `<offers>/<uuid>.bind.<k>` stem with `.facts` (lane 3's ruling, #165 6746215510, point 6; lane 3's correction, #165 6751818233; lane 3's ruling, #165 6752064118):
  - **The outer, the body and the guard's sweep** all find it from `out=`, never from `bind`. Where the body cannot read the record itself, the outer hands it that path (lane 3's ruling, #165 6752064118).
  - The body creates it, empty, before its first step. A missing file then means the body never ran (lane 3's ruling, #165 6747528875, point 16(a)).
  - The body writes `why=` and `note=` to it.
  - The outer folds them into the record where the body ended on its own, or writes Y:5488's `why` where the body was stopped, and then removes the file.
  - Which end is the body's own, and what the outer writes where the body gave no reason or left no file, are section 3's `why` and `note=` rows (lane 3's ruling, #165 6747528875, point 16).
- **Their lifetime:** the `.bind.*` files and the facts file live as long as the bind. The guard removes them wherever it removes the record or rewrites it to `mode=allow`, so there are at most three per offer (lane 3's ruling, #165 6746215510, point 13; Y:5504), though after a case 11 the uuid may also hold an earlier offer's leftovers until a removal sweeps them. Case 11 is the one exception, below.
  - **The guard's removal also takes any facts file an outer left, as after case 9** (lane 3's ruling, #165 6747528875, points 6 and 13). The two rulings are halves of one rule: the outer removes the facts file it folded in (point 6), and the guard's sweep of `<uuid>.bind.*` (point 13) takes whatever an outer left, as after a `kill -9`, case 9 or case 10.
  - **Case 11** is the only removal that can meet a live outer. So it drops the record but leaves `<uuid>.bind.*` for the next removal to sweep, and never unlinks a live outer's `.out` (lane 3's ruling, #165 6747528875, points 6 and 13).
    - The next offer's files take indexes above the leftovers', so none of its attempts reuses them, and the removers (points 6 and 13) still clear the old files as they do now (lane 3's ruling, #165 6751772589, point 18; lane 3's correction, #165 6751818233).
    - A live outer left behind writes its outcome only over its own `binding` (above; lane 3's ruling, #165 6751772589, point 18).

## 2. Its four modes

| Mode | What it says | Written by | Moves to |
|---|---|---|---|
| `mode=name-drift` | the offer, waiting for an answer: today's mode, and the mode "once the offer is restored" after an attempt (Y:5427) | the guard: at the offer (pin:10963); after a failure, with `bind=k` and the window renamed back to `<from>` (Y:5502); after the third failure, with `bind=3` (Y:5503); at the stale row, with `rc=unknown` (Y:5505). After the third failure and at the stale row the window is also renamed back to `<from>`, but not for a record from another host or container (lane 3's ruling, #165 6746215510, point 7; ERR-23) | `mode=binding`, by the hook, at an answer `2` while choice 2 is still offered |
| `mode=binding` | an attempt is running, or has not yet written its outcome | the hook, by temp file and `mv` (Y:5393), with the attempt's first keys; the outer rewrites it at its start with the mode unchanged (Y:5396) | `mode=bound` or `mode=bind-failed`, by the outer, only where the record is still its own `binding` when it reads it again (section 1; lane 3's ruling, #165 6751772589, point 18); `mode=name-drift`, by the guard at the stale row (Y:5505), and for a record from another host or container only after `deadline_epoch` plus 60 s (Y:5506). A record without a well-formed `pid` and `pidstart`, an 11-digit `pid` included, reaches the stale row once `deadline_epoch` has passed (lane 3's ruling, #165 6746215510, point 4; lane 3's ruling, #165 6747528875, point 15; lane 3's ruling, #165 6751772589, point 17) |
| `mode=bound` | the attempt ended 0: `rc=0`, with a `note=` for each act still owed (Y:5486), and one naming `<out>` where the body left no facts file (lane 3's ruling, #165 6747528875, point 16(d)) | the outer | removed by the guard, which says "MOVED" once (Y:5501) |
| `mode=bind-failed` | the attempt ended non-0: `lane-start`'s code with its `why` (Y:5487), or 124, 137 or the signal's code where its bound or a signal stopped it (Y:5488). Which end was a stop is section 3's `why` row (lane 3's ruling, #165 6747528875, point 16(b)) | the outer | `mode=name-drift`, by the guard at the next prompt, which renames the window back to `<from>` (Y:5502; after the third failure too, by lane 3's ruling, #165 6746215510, point 7, ERR-23) |

- **The offer site's branch** takes `binding`, `bound` and `bind-failed`, and `guard_answer` is never called on them (Y:5493; tasks:588). A `mode=name-drift` record goes to `guard_answer`, as today.
- **Outside the bind,** today's file also takes `mode=allow`, choice 1's record (pin:11115), which today's offer site passes by (pin:10651). The bind adds `binding`, `bound` and `bind-failed` to `name-drift` and `allow`.
- **At most three attempts per offer:** "never by itself: a failed bind spends the person's yes; each `2` is one attempt, at most 3 per offer" (Y:5504). After the third failure choice 2 is no longer offered (Y:5503). The files' index, apart from the attempt, leaves this unchanged (Y:5503–5504; lane 3's correction, #165 6751818233).
- **A bind stopped by its bound or by a signal** ends `mode=bind-failed`, as every end but 0 does (lane 3's ruling, #165 6746215510, point 3; Y:5487–5488; svc:152–154; spec:318).

## 3. Every key, with its phase and its writer

Phase 1 is T502's, with T507's `c`, `n_a` and `w_s_ms`. Phase 2 is T510's (Y:5558–5559; tasks:569, :1215).

- **The writers Y does not name** are lane 5's reading in draft 1, which lane 3 confirmed with its point 4's change (lane 3's ruling, #165 6746215510, point 3):
  - The hook writes `bind`, `out`, `host`, `container` and `detach`, and writes the timing keys first. The outer rewrites the timing keys at its start (Y:5436).
  - The hook's `mode=binding` rewrite writes `host`, `container`, `bound_s`, `bounded`, `started_utc` and `deadline_epoch`, and the outer then rewrites the timing keys and adds `pid` and `pidstart` (lane 3's ruling, #165 6746215510, point 4). This contract reads the timing keys as those four besides `host` and `container`; lane 3's analysis names `started_utc` and `deadline_epoch` among them.
  - Only the outer writes `pid` and `pidstart`.
- **The hook writes `detach=setsid|python3|nohup`,** the form `$detach` took at the fork (Y:5395; lane 3's ruling, #165 6746215510, point 8).

| Key | What it holds | Phase, task | Writer |
|---|---|---|---|
| `mode` | `binding`, `bound`, `bind-failed`, or `name-drift` (Y:5427) | 1, T502; `name-drift` is today's | as section 2 gives |
| `from`, `to`, `session`, `uuid`, `window`, `pane` | "the offer's identity, as today's file has it" (Y:5428): the lane the window left, the lane named, the session's name, its uuid, the window's reference and its pane (pin:10964–10970) | today's | the guard, at the offer (`guard_offer`, pin:10963–10970); every rewrite keeps them |
| `utc` | the offer's time, which `guard_offer` writes (pin:10968) and choice 1's `mode=allow` record rewrites (pin:11120). Y's key table leaves it out, though Y:5428's identity is "as today's file has it". Nothing reads it: today's reads of the file, at pin:10650, pin:11005–11008 and pin:11078–11081, take other keys | today's | the guard, at the offer; every rewrite keeps it, as part of the identity (lane 3's ruling, #165 6746215510, point 12) |
| `bind` | "the attempt, 1 to 3" (Y:5429), per offer. It is not the files' index: the `<k>` that the hook names at the fork (Y:5394) is apart from it (section 1; lane 3's correction, #165 6751818233) | 1, T502 | the hook, which sets `bind=k+1` at the next `2`, counting attempts only (lane 3's ruling, #165 6746215510, points 3 and 5; lane 3's correction, #165 6751818233); the guard keeps it as `bind=k` after a failure (Y:5502–5503). Case 11's check of `bind` is unchanged (section 5) |
| `started_utc` | "the start" (Y:5429); the bound runs from it (Y:5436) | 1, T502 | the hook first, in its `mode=binding` rewrite; the outer rewrites it at its start (lane 3's ruling, #165 6746215510, points 3 and 4) |
| `bound_s` | the bound, "computed at the start by the sum" (Y:5429; section 4) | 1, T502; T507 makes the sum read `c`, `n_a` and `w_s_ms` (tasks:616) | the hook first, in its `mode=binding` rewrite, by section 4's helper (lane 3's ruling, #165 6746215510, point 4; tasks:616); the outer rewrites it at its start: "`guard-bind` computes `bound_s` at the start" (Y:5436) |
| `bounded` | "`yes` where `timeout` exists, else `no`" (Y:5429); "The record says `bounded=no`" (Y:5403) | 1, T502 | the hook first, in its `mode=binding` rewrite; the outer, which runs the body with `timeout` or without it, rewrites it at its start (lane 3's ruling, #165 6746215510, points 3 and 4) |
| `deadline_epoch` | "the start plus `bound_s` plus 5 s, the 5 s being for the outer to write the outcome after `timeout`'s `KILL`" (Y:5429) | 1, T502 | the hook first, in its `mode=binding` rewrite; the outer rewrites it at its start (lane 3's ruling, #165 6746215510, points 3 and 4) |
| `pid`, `pidstart` | the outer's `$$`, and its start token from `pid_start_of` (Y:5396, Y:5434; section 6) | 1, T502 | only the outer, which "rewrites the record with its own `$$` and start time" (Y:5396; lane 3's ruling, #165 6746215510, point 3). The hook's `mode=binding` record has neither until then (section 5). The outer writes `pidstart` only when `pid_start_of $$` exits 0. When it exits 2, the outer writes `pid` alone (lane 3's ruling, #165 6747528875, point 15). A `binding` record without a well-formed `pid` and `pidstart` reads as one with no `pid` (section 5), and so does one where the outer wrote `pid` alone, with `pidstart` empty (lane 3's ruling, #165 6751772589, reading 1) |
| `host`, `container` | "`LANES_WORKSTATION` (else the hostname) and `LANES_CONTAINER`" (Y:5430) | 1, T502 | the hook, in its `mode=binding` rewrite (lane 3's ruling, #165 6746215510, points 3 and 4) |
| `out` | "the attempt's output file" (Y:5431), `<offers>/<uuid>.bind.<k>.out`, `<k>` being the files' index, not the attempt (section 1). It names the attempt's own file: the outer and the guard find it there, and the facts file sits beside it (lane 3's correction, #165 6751818233) | 1, T502 | the hook, which names `<k>` and opens the file at the fork (Y:5394; lane 3's ruling, #165 6746215510, point 3; lane 3's correction, #165 6751818233) |
| `detach` | `setsid`, `python3` or `nohup`: the form `$detach` took at the fork (Y:5395). The guard's session test runs only for `setsid` and `python3` (section 6) | 1, T502 | the hook (lane 3's ruling, #165 6746215510, point 8) |
| `c` | `.head`'s `c=`: "the larger of the two URLs' `connect_timeout=`, not the snapshot's" (CLI44:704; CLI44:296; O15) | 1, T507 | the hook, at `mode=binding`, from that prompt's prefetch `.head` (tasks:747; CLI44:304, :706) |
| `n_a` | `.head`'s `n_a=`, which is `register_addresses=`, default 1 (CLI44:297) | 1, T507 | as `c` |
| `w_s_ms` | `.head`'s `w_s_ms=`: `w_s` in whole milliseconds, 300, or `register_rtt_ms=` + 200 where `register_rtt_ms=` is over 100, and 0 where no service can answer (CLI44:298, :301–302; O15; B2:138–143) | 1, T507 | as `c` |
| `act` | "the guard's base `<act-id>@<n>:<generation>`, from the `n` and `generation` of the prefetch that made the offer" (Y:5431; Y:5679) | 2, T510 | the guard, "at the offer" (tasks:1220) |
| `write_id`, `generation`, `not_after`, `write_lane`, `args_sha256` | the five copies, from "`start-record`'s pending record, the newest whose `verb` is `start-record` and whose `session` is this transcript's uuid: its `write_id`, `generation`, `lane` (as `write_lane`) and `args_sha256`, and the largest `not_after` among its `call` lines" (Y:5431) | 2, T510 | the outer, "when it writes an outcome other than 0", and the guard "at the stale row where the outer left none" (Y:5431). None where that record holds no `call` line or there is none (Y:5431), and none for a record from another host or container (Y:5506; tasks:1223). The record drops them on LR007 `another-write` (Y:5518). A new attempt keeps them, and the outer drops them once its lookup settles (below) |
| `rc` | `0`; `lane-start`'s code; `124`, `137` or the signal's code; `unknown` (Y:5486–5488, Y:5505). The signal's code is `wait`'s status, 128 plus the signal's number: lane 3's probe gave 143 for `TERM`, and 137 where the body ignores `TERM` (lane 3's ruling, #165 6746215510, point 11). Phase 2 adds `3` and `2` from the lookup (Y:5517–5519) and `start-record`'s codes (Y:5541–5544) | 1, T502; phase 2's codes, T510 | the outer; the guard writes `rc=unknown` at the stale row (Y:5505) |
| `why` | "`lane-start`'s last refusal line", or "stopped by its bound" or "stopped by a signal", then "what it wrote is not known" (Y:5487–5488). It says "stopped by a signal" whenever the outer's trap fired (lane 3's ruling, #165 6746215510, point 11). "Stopped by its bound" needs `timeout`'s 124 or 137 at or after `bound_s` − 10 s from `started_utc`, with the trap not fired. Every other end is the body's own (lane 3's ruling, #165 6747528875, point 16(b), which refines point 11: the code alone cannot tell them apart, since a `KILL` from another process inside the bound is 137 too, and a crash is 139). Where the body ended on its own, with a nonzero `rc` and no `why=` line, `why` is "the bind's body ended with exit `<rc>` and gave no reason; what it wrote is not known; its output is `<out>`" (point 16(c)): Y:5487's refusal line may never be printed by a crash, and Y:5488's texts would be false | 1, T502 | the outer: from the body's facts file where the body ended on its own, else Y:5488's text (lane 3's ruling, #165 6746215510, point 6); point 16(c)'s text where the body ended on its own and gave no `why=` line, a missing facts file included. In phase 2 the five copies still follow Y:5431, an outcome other than 0 (lane 3's ruling, #165 6747528875, point 16(c)) |
| `note=` | one line per act still owed, "and its command" (Y:5432): "the stamp not written or not pushed …, the cell not ending on this uuid, or the lane left not marked" (Y:5486). In phase 2 the lane left's `set-row-state` 2 or 3 "becomes a `note=`" (tasks:1232). Where `rc` is 0 and there is no facts file, the record ends `bound` with one `note=` naming `<out>`: a missing file never passes as "nothing owed" (lane 3's ruling, #165 6747528875, point 16(d)) | 1, T502; T510 | the outer, which folds in the body's facts file (lane 3's ruling, #165 6746215510, point 6). The body writes no key of the record (Y:5396–5398) |
| `ended_utc` | the outcome's time (Y:5432); at the stale row, "the guard's time" (Y:5505) | 1, T502 | the outer; the guard, at the stale row |

- **A new attempt** (lane 3's ruling, #165 6746215510, point 5):
  - The hook's rewrite at the next `2` keeps the identity, `utc`, `act`, the five copies and `ended_utc`, and sets `bind=k+1`, which counts attempts only (lane 3's correction, #165 6751818233).
  - It drops `rc`, `why`, the `note=` lines, `pid`, `pidstart`, `out` and the attempt before's timing keys, and writes the new attempt's keys, as at every `2` (above). The new `out` names a file at the next files' index (section 1; lane 3's correction, #165 6751818233).
  - T510's child reads the kept `ended_utc` and copies for its `w₀` wait and its lookup (tasks:1224–1225; Y:5511).
  - The outer drops the copies once its lookup settles (Y:5511, Y:5518). It keeps them on a 3, where no try takes the lock or the lookup gets LR011 (Y:5517), and on LR007 `generation` (Y:5519).

## 4. The bound

- **One helper** computes `bound_s` from the record, for the hook's "BINDING" text and for the outer's start, "so the two never differ" (tasks:604, :616). The hook runs it for its `mode=binding` rewrite, and the outer at its start (lane 3's ruling, #165 6746215510, point 4).
- **The sum** (Y:5459): `B`, plus one read's `T_r` + `w_s` to spare, plus the 10 s of `-k`, rounded up to 10 s. "That is 437 + 28`N_a`·`c` + 19`w_s` s, rounded up to 10 s: 500 s at `c` = 2 and 530 s at `c` = 3, with or without the service", and 590 s at Eagle's p90, where `c` = 5 and `w_s` = 0.656 s (Y:5459). With two addresses it is 550 s and 610 s, or 560 s and 620 s with the service (Y:5462).
- **In bash 3.2's integers,** from the record's keys (tasks:616; B2:140): `bound_s = (437000 + 28000·n_a·c + 19·w_s_ms + 9999) / 10000 · 10`.
- **The defaults,** where the keys are absent: `c` = 2, `n_a` = 1, `w_s_ms` = 0, which give 500 s (tasks:616; CLI44:304). They apply to every bind before T507 lands, and to every record that lacks the keys (tasks:616, :747).
- **Malformed inputs:** a `c`, `n_a` or `w_s_ms` that is not a whole number, a `c` below 2, or an `n_a` below 1 counts as absent, so its default applies (lane 3's ruling, #165 6746215510, point 10; Y:3810–3812, where a `connect_timeout` under 2 is refused).
- **`LANES_BIND_BOUND_S`** overrides the sum, for tests (Y:5429; tasks:616; Ytp:6201). AT-F6 sets `LANES_BIND_BOUND_S=15`, "under which the body runs under `timeout -k 10 5` and `deadline_epoch` is the start plus 20 s" (Ytp:6020–6021).
  - It is honoured only as a whole number of at least 11. Any other value is ignored, and the sum is kept (lane 3's ruling, #165 6746215510, point 10, after tasks:1423's rule for the service's intervals).
  - At 10 the body's `timeout -k 10 0` runs with no limit, and below 10 `timeout` refuses with 125 (coreutils 9.4: both checked by draft 1's writer, and the first by lane 3's analysis).
- **`deadline_epoch`** is the start plus `bound_s` plus 5 s (Y:5429): at the default, 505 s after `started_utc` (Ytp:6044–6045).
- **The body's `timeout`** is `timeout -k 10 "$(( bound_s - 10 ))"` (Y:5397): 490 s at the default (Ytp:5985).
- **`bounded=yes`** where `timeout` exists. **`bounded=no`** where it is missing, as on macOS without coreutils: the body runs unbounded, started under `set -m` so that it leads a group of its own, and the outer's trap sends `kill -TERM -- -<body pid>`, then `KILL` 10 s later if the group is still there (Y:5402–5405; tasks:611).
- **The outer's sweep** (R47; ERR-10): after `timeout` returns 124 or 137, or after the outer's trap fired, the outer sends `TERM` to the group `timeout` led, then `KILL` 10 s later if any member is left, and only then writes the outcome. It never sweeps after the body's own exit, 0 or not (tasks:610; L5:527–528). The sweep ends by `bound_s` at the latest, inside `deadline_epoch` (tasks:610).
  - The sweep keys on `timeout`'s 124 or 137 and on the trap, as tasks:610 gives it, whatever the time. Point 16(b)'s test of the time sorts only the `why` (section 3; lane 3's ruling, #165 6747528875, point 16(b)). A 137 inside the bound is the body's own end only for the `why`: its group is still swept, so nothing it started is left running (lane 3's ruling, #165 6751772589, reading 2).
- **What the bound is:** "`bound_s` of wall time from `started_utc`, over the child's whole body" (Y:5436). It "never stops a child whose every step stays inside its own bound" (Y:5436). In files mode it "is a judgment" (Y:5483).

## 5. The guard's eleven cases, in short

The check is a branch of its own at the offer site, after 18(h)'s count and before the table, on `mode` in `binding`, `bound` or `bind-failed` (Y:5493; tasks:588). It costs one small file read, `kill -0` and one `/proc` read, about 7 ms (Y:5493). Every text goes to stderr, and the hook string is unchanged (Y:5493; FR-040). The texts are Y's, in short (Y:5497–5507).

| # | Case | Exit | Record after | Text, in short |
|---|---|---|---|---|
| 1 | the answer `2` itself | 2 | `mode=binding` | "BINDING: this window is now named `<to>` … detached (pid `<p>`, bounded at `<bound_s>` s) … send your work again in a minute or so; until it ends, each prompt is answered BINDING IN PROGRESS" |
| 2 | same session; child live, inside the bound | 2 | unchanged | "BINDING IN PROGRESS: … it ends by `<deadline utc>` at the latest. Your prompt was NOT sent; send it again in a few seconds". Where the record says `bounded=no`, the text leaves out "at the latest" |
| 3 | same session; child live past `deadline_epoch` | 2 | unchanged | "THE BIND HAS RUN PAST ITS `<bound_s>` s BOUND and is still running (pid `<p>`, started `<utc>`)". With `bounded=no`: "this host has no `timeout`, so nothing stops it; it is most likely in a `git` call". With `bounded=yes`: "its `timeout` has not ended it; it is likely stuck on the disk or the network". Then what waiting costs and what `kill <p>` costs, and "Your prompt was NOT sent." Where the outer is dead and processes of its session are still running, case 3 has its own text: "…its outer (pid `<p>`) has died and processes of its session are still running … `pkill -s <p>` stops them … no outcome will be written" (lane 3's ruling, #165 6746215510, point 9) |
| 4 | another session wanting the same lane | its own table | its own record | it never sees this record. If it answers `2`, on primary `start-record`'s `bind:<to>` expectation refuses whichever bind commits second, with 2; in files mode, `lane-start`'s re-read at `:2614` does |
| 5 | after success, `mode=bound` | 2, once | removed | "MOVED: this window is lane `<to>`, bound to this session (`<uuid>`) at `<ended_utc>`." Here `<uuid>` is this session's, and where `ended_utc` is missing the line says "a time the record does not give" in its place. Then one line per owed note, "  still owed: `<note>`", and "This prompt was NOT sent: it is the one that hears how the bind ended. Send your work again." The next prompt exits 0 |
| 6 | after failure, `mode=bind-failed`, attempt `k` below 3 | 2, once | `mode=name-drift`, `bind=k`; the window renamed back to `<from>` | "THE BIND TO LANE `<to>` FAILED (exit `<rc>`): `<why>` … the offer is KEPT: reply `1`, `2` or `3`" |
| 7 | failure on the third attempt | 2 | `mode=name-drift`, `bind=3`; the window renamed back to `<from>` | as case 6, and choice 2 is no longer offered |
| 8 | does a failure restart it? | — | — | never by itself: a failed bind spends the person's yes; each `2` is one attempt, at most 3 per offer |
| 9 | stale: `mode=binding`, same host and container; its pid reused, or dead with no process left in the session it led | 2, once | `mode=name-drift`, `rc=unknown`, `ended_utc` the guard's time, and in phase 2 the five copies; the window renamed back to `<from>` | "THE BIND STARTED AT `<utc>` … IS NO LONGER RUNNING AND LEFT NO OUTCOME … the next `2` finds out first" |
| 10 | `mode=binding` from another host or container | 2 | unchanged until `deadline_epoch` plus 60 s, then as case 9 but with no copies; case 10 renames nothing | "A BIND OF THIS TRANSCRIPT IS RUNNING ON `<host>/<container>` … Your prompt was NOT sent". Where the record says `bounded=no`, the text adds that the child there may still be running, and that on primary `bind:<to>` refuses whichever bind commits second |
| 11 | an unreadable record: one missing or malforming any of `mode`, `from`, `to`, `bind` (1 to 3, the attempt, which the files' index leaves unchanged: lane 3's correction, #165 6751818233), `host`, `container`, `deadline_epoch` (digits), or `rc` in `bound` or `bind-failed` (lane 3's ruling, #165 6746215510, point 4). A malformed `pid` or `pidstart` does not join this set (lane 3's ruling, #165 6747528875, point 15) | 2 | dropped; `<uuid>.bind.*` left for the next removal to sweep (lane 3's ruling, #165 6747528875, points 6 and 13) | today's text: "the pending offer at `<f>` could not be read, so it has been dropped rather than answered on a guess. Send your prompt again and the question will be asked afresh." (pin:11091–11094, which Y:5507 cites as `:11097–11101`: ERR-24; lane 3's ruling, #165 6746215510, point 14) |

- **"Child live"** in cases 2 and 3 is section 6's test, including a dead outer whose session still has a process (Y:5505; svc:160–162). That session is tested only where the record says `detach=setsid` or `detach=python3` (lane 3's ruling, #165 6746215510, point 8).
- **A `binding` record without a well-formed `pid` and `pidstart`** (with no `pid`, before the outer's first rewrite or where the outer never ran; with `pid` alone, where the outer's `pid_start_of $$` exited 2; or with either malformed) gets case 2's "BINDING IN PROGRESS", with no pid, until `deadline_epoch`. After that it is case 9, with no session test (lane 3's ruling, #165 6746215510, point 4; lane 3's ruling, #165 6747528875, point 15; Y:5505; R49). A record with `pid` alone, and `pidstart` empty, is one of these: in lane 3's words, "That is 6747722621's 'without a well-formed `pid` and `pidstart`'" (lane 3's ruling, #165 6751772589, reading 1).
  - **Well-formed:** `pid` is decimal digits, with no leading 0, above 1, and at most 10 digits (lane 3's ruling, #165 6751772589, point 17). `pidstart` is not empty, and on Linux is all digits, the one token `pid_start_of` prints there (section 6). The guard applies this rule before it calls anything: "above 1" is the record's own rule, not `pid_start_of`'s (lane 3's ruling, #165 6747528875, point 15, as lane 3's ruling, #165 6747722621 narrows it).
  - **The 10-digit cap** is `pid_start_of`'s own (section 6). An 11-digit `pid` is malformed, so it reads as no `pid`: "BINDING IN PROGRESS" until `deadline_epoch`, then case 9. Lane 3's why: without the cap, such a record holds case 3 for ever, since R49 reads `pid_start_of`'s 2 as live; with it, the record ends at the deadline, as every other malformed `pid` does. No real pid comes near 10 digits (lane 3's ruling, #165 6751772589, point 17).
  - **A malformed value is never passed** to `kill`, `pid_start_of`, `pgrep -s` or a text (lane 3's ruling, #165 6747528875, point 15).
  - **Why it waits, and is not dropped by case 11:** dropping the record during a live bind is the wrong "dead" that R49 forbids. Waiting is Y's own fallback: "the row waits for `deadline_epoch` instead" (Y:5505). Case 11 already requires a well-formed `deadline_epoch`, so the wait ends.
  - **Why a bad `pid` is dangerous, not only stuck:** with `pid=0`, `pgrep -s 0` matches the caller's own session, and `kill -0 0` tests the caller's own process group, so the bind would read as live for ever. Case 3's dead-outer text would tell the person to run `pkill -s 0`, which kills their own session (lane 3's ruling, #165 6747528875, point 15).
- **Case 10's 60 s** "covers a `git` call that outlives the body under its own `timeout` (50 s, P1), plus 10 s between the two hosts' clocks". Where `bounded=yes`, the old write's `not_after` has passed by then (Y:5506).
- **Case 6's rename back** fixes today's finding: the window kept `<to>`'s name, so the kept offer was dropped (Y:5509; tasks:625).
- **Cases 7 and 9 rename the window back to `<from>`,** as case 6 does, and case 10 renames nothing (lane 3's ruling, #165 6746215510, point 7; ERR-23, research:359–367; svc:124, svc:156–158; Ytp:6065–6068; 007-spec:490–493). T502's lines for the two, tasks:596 and :598, follow in B4.
- **Case 1's `<bound_s>`** comes from section 4's helper, and the hook makes no register call of its own for answer 2 (tasks:604).
- **"BINDING" and "BINDING IN PROGRESS"** (R58; ERR-14): "BINDING" is answer 2's own text, and "BINDING IN PROGRESS" each prompt's while the bind runs. Y:5389's "binding in progress" for answer 2 is history.

## 6. Liveness, and `pid_start_of`

- **The test** (Y:5434): "the pid is live and its start time equals `pidstart`". It is judged only where the record's `host` and `container` are this guard's (Y:5423). Otherwise case 10 answers.
- **`pid_start_of <pid>`** (Q-L5-6, L5:516–519; R49):
  - **Argument:** one pid.
    - It exits 2 for an argument that is not a well-formed pid in its own sense: empty, `0`, a leading `0`, a non-digit, or more than 10 digits, as T502's part 2 does at `0ebde08`. For `1` it reads `/proc/1`, and the tokens decide (lane 3's ruling, #165 6747722621, which narrows lane 3's ruling, #165 6747528875, point 15).
    - It does not exit 2 for every argument the bind record's rule refuses: "above 1" is the record's own rule, which the guard applies before it calls anything (section 5). The function's contract is to read a real pid's start token, and in a container pid 1 is a real process (lane 3's ruling, #165 6747722621, upholding lane 4's view, #165 6747648353).
    - The bind record's rule takes the function's cap of 10 digits, so the guard never passes it a `pid` too long for it (section 5; lane 3's ruling, #165 6751772589, point 17).
  - **Output:** the process's start token on stdout, where it exits 0.
  - **Exit 0:** the pid is live, and its token is printed.
  - **Exit 1:** the pid is not live.
  - **Exit 2:** the start cannot be read, or the argument is not a well-formed pid (above). Every caller treats 2 as live, never as dead: "A wrong 'dead' would let a claim or a bind be stolen; a wrong 'live' only waits" (R49). R49 is recorded here.
  - **The token:** on Linux, field 22 of `/proc/<pid>/stat`, counted after the last `)`; on macOS, `ps -o lstart= -p <pid>` (L5:518; Y:5434). It is compared as a string, on the same host and container only (L5:518).
- **So the outer is live** where `pid_start_of <pid>` exits 0 and prints the record's `pidstart`, or exits 2. It is not live where it exits 1, or exits 0 with another token, which is a reused pid: "a reused pid and a dead pid both read as not live" (Y:5434).
- **One helper** "serves this record and F10's pending record" (Y:5434), whose claims' owners it tests (Y:5185; tasks:938). T502 defines it, and it lands before lane 4's T412 (L5:519).
- **Lane 4's T412** is a caller that reads any exit other than 0 and 1 as live (#165 6747564649). That is R49's own rule, which lane 3's ruling, #165 6750702819, leaves unchanged.
- **It never copies `lock_steal_if_dead`'s pid-only test** (Y:5434; tasks:617). That test is `kill -0` on the lock's pid alone (pin:1286–1296), "under which a reused pid looks live until the 10-minute age" (Y:5434).
- **Its cost:** "P5: 200 checks take 1.42 s, about 7 ms each" (Y:5434).
- **The stale row's session test** (Y:5505; tasks:618), on a record of this host and container whose outer is not live:
  - **A reused pid** (exit 0, another token) needs no session test, and the bind is stale at once: "the kernel does not hand a pid out again while a session of that number has members" (Y:5505; Ytp:6069–6071).
  - **A dead pid** (exit 1): "`$detach` made the outer the leader of its own session", and the body, `timeout` and any `git` under its own `timeout` stay in that session after the outer dies (Y:5505). While any process has the record's `pid` as its session id, the bind is still running, the guard answers by cases 2 and 3, and no second bind starts (Y:5505; svc:160–162). Past `deadline_epoch`, case 3 then takes its dead-outer text (section 5; lane 3's ruling, #165 6746215510, point 9). Once no process has, the bind is stale, case 9 (svc:156–158).
  - **The session test runs only where the record says `detach=setsid` or `detach=python3`** (lane 3's ruling, #165 6746215510, point 8).
  - **The probe:** `pgrep -s <pid>`, as v3f's case 6 names it, else a scan of field 6 of `/proc/*/stat` where `pgrep` is missing (tasks:618; Y:5505; Ytp:6061).
  - **Where the outer led no session** (`detach=nohup`, or no `detach` key), or the session cannot be read, the row waits for `deadline_epoch` instead (Y:5505; lane 3's ruling, #165 6746215510, point 8). Lane 3's probe found a `nohup` outer in the hook's own session.
- **A `binding` record without a well-formed `pid` and `pidstart`** (section 5) has no liveness test: it reads as running until `deadline_epoch`, and then as stale with no session test. Neither value is passed to `kill`, `pid_start_of` or `pgrep -s` (section 5; lane 3's ruling, #165 6746215510, point 4; lane 3's ruling, #165 6747528875, point 15).

## 7. Who reads the record

- **The guard,** at the offer site, at each prompt of the session (Y:5493–5507): `mode`, the identity, `host`, `container`, `pid`, `pidstart`, `bounded`, `bound_s`, `started_utc`, `deadline_epoch`, `bind`, `rc`, `why` and the `note=` lines, for section 5's cases. It also reads `detach`, for section 6's session test (lane 3's ruling, #165 6746215510, point 8), and finds the attempt's own file by `out`, not by `bind` (lane 3's correction, #165 6751818233). Its sweep finds the facts file from `out=` too, never from `bind` (lane 3's ruling, #165 6752064118). It never reads `utc` (lane 3's ruling, #165 6746215510, point 12). `guard_answer` reads a `mode=name-drift` record as today (pin:11076–11093). In phase 2, at the stale row, it also reads `start-record`'s pending record for the five copies (Y:5431).
- **The hook's helper,** for case 1's `<bound_s>` and its `mode=binding` rewrite: `c`, `n_a` and `w_s_ms`, or `LANES_BIND_BOUND_S` (tasks:604, :616; lane 3's ruling, #165 6746215510, point 4).
- **`guard-bind`:**
  - The outer refuses unless the record says `mode=binding` (Y:5396), and sums `bound_s` from its `c`, `n_a` and `w_s_ms` (CLI44:304; tasks:616).
  - It reads only the offer file before `lane-start`: its read-door class is `none` (Y:5399; tasks:605).
  - At its end it reads the body's facts file, folds it into the record and removes it (lane 3's ruling, #165 6746215510, point 6). It finds its own `.out` by the record's `out`, and the facts file beside it (lane 3's correction, #165 6751818233), from `out=` too, never from `bind` (lane 3's ruling, #165 6752064118).
  - Just before it writes its outcome, it reads the record again, and writes only where the record is still its own `binding`, with its own `pid` and `pidstart` (section 1; lane 3's ruling, #165 6751772589, point 18).
  - In phase 2, T510's child reads `act` for the `set-row-state` on the lane left, with `LANES_EXPECT="row:<from> bind:<from>"` (Y:5398; tasks:1232); the five copies for its lookup (tasks:1224); and `ended_utc`, `c` and `n_a` for its `w₀` wait (tasks:1225).
  - The body finds its facts file from the record's `out=`, never from `bind`. Where it cannot read the record itself, the outer hands it that path (lane 3's ruling, #165 6752064118).
- **Lane 4's T412** reads not the record but its liveness helper: the pending record's claims are live by `pid_start_of` (tasks:938; Y:5185).
- **Lane 2's AT-F6** (Ytp:5957–6259): twenty-two cases (R53; ERR-12), and four for rows the cases do not reach, which "write the offer file's record directly, with a sleeping process standing in for the child where one is needed" (Ytp:6202–6206). Its names are selected by T502's, T507's and T510's verifies (tasks:584, :708, :1215). Its case 15 asserts the signal's code as 143 (lane 3's ruling, #165 6746215510, point 11; Ytp:6130–6134).

## 8. Test seams

- **`LANES_BIND_BOUND_S`** overrides `bound_s` (section 4), and is honoured only as a whole number of at least 11 (lane 3's ruling, #165 6746215510, point 10).
- **`LANES_BIND_BODY`** (R48; DEC-17; L5:535–538; tasks:583, :609, :613):
  - Where it is set, the outer runs it, with the uuid, in place of `"$RESOLVED" guard-bind --body`.
  - It is kept for the outer and unset in the body (tasks:622).
  - The body's `set-row-state` goes through `LANES_EDIT` where it is set, else `$RESOLVED`, as `lane-start`'s own writes do.
  - Lane 5 answers lane 2's point 39(b) with it (routed to lanes 5 and 4 by #165 6726110116), and lane 4's note (#165 6726296119) raises nothing on it (tasks:583).

## 9. Notes: Y beside W, and the rulings followed over Y's text

- **Where Y differs from W on a passage used here:** two lines of the bound's sum, W:5407–5408 and Y:5458–5459. Y adds the closed form "437 + 28`N_a`·`c` + 19`w_s` s, rounded up to 10 s", the p90 reading ("`c` = 3 means a p90 round trip under about 276 ms"), the 540 s band, and the 590 s at Eagle's p90 (lane 5's O3, #165 6725361742). This contract follows Y. Every other line of W:5336–5512 is Y:5387–5563 unchanged, and W:5149 is Y:5200.
- **R05 (ERR-05):** `guard-bind` reads no configuration. It sums `bound_s` from the `c`, `n_a` and `w_s_ms` that the hook copies from `.head` into the record (CLI44:304–305). Y:5877's "from the configuration", and Ytp:6193's, are read so.
- **O15** (CLI44:111, :703–704; B2:138–143): the key is `w_s_ms`, in whole milliseconds, since `guard-bind` is bash 3.2. `c` is the larger of the two URLs' `connect_timeout=`, which "only lengthens T510's wait".
- **T510's `C`** for `w₀` is `N_a`·`c` + 6 s, from the record's `c` and `n_a` (tasks:1225; L5:703). Y:5511 states it as 8 s, its value at `c` = 2 with one address.
- **R47 (ERR-10):** the outer sweeps `timeout`'s group (section 4), where Y:5401 has `timeout` send the `KILL`.
- **R48 (DEC-17):** `LANES_BIND_BODY` (section 8).
- **R49:** `pid_start_of`'s exit 2 is live (section 6), recorded here.
- **R52 (ERR-11):** the child also unsets `LANES_WRITE_GENERATION`, the printed rerun's variable (Y:5200), with `LANES_WRITE_ID` (tasks:623). The child's environment is otherwise Y:5410–5415's (tasks:619–624).
- **R53 (ERR-12):** AT-F6 has twenty-two cases.
- **R58 (ERR-14):** "BINDING" for answer 2, and "BINDING IN PROGRESS" while the bind runs.
- **R50:** plan.md lists this file (plan.md:274), and the skeleton's lane 5 block gains it. It lands after the spec PR and before T502 (tasks:569, :571).
- **Lane 3's rulings on draft 1's open points** (#165 6746215510). Y's frozen text keeps its words, and lane 3's errata go into `research.md` Part 1 in B4:
  - **ERR-23** (lane 3's ruling, #165 6746215510, point 7; numbered in B4, #165 6747379291, research:359–367): Y:5503's and Y:5505's "Record after" add "window renamed back to `<from>`", and Y:5506's renames nothing. This contract follows the erratum (sections 2 and 5).
  - **ERR-24, two citation errata** (lane 3's ruling, #165 6746215510, point 14; numbered in B4, #165 6747379291, research:369–375): Y:5507's `:11097–11101` reads `:11091–11094`, and Y:5428's `:10965–10973` reads `:10963–10971`. Both hold at the pin (sections 3 and 5).
  - **Four rulings change what Y's text gives,** and lane 3's analysis names no erratum for them:
    - point 4: Y:5394's fork sends its own output to `/dev/null`, which leaves the hook no `$!`. The ruling returns `$!` from inside the fork through a command substitution, with `$detach` as a bash array (section 1);
    - point 6: the facts file is a second file per attempt, beside Y:5408's "one 0600 file per attempt" (section 1);
    - point 8: `detach` is a key that Y:5425–5432 does not list (section 3);
    - point 9: "Y:5499's text no longer holds once the outer is dead", in lane 3's words, so case 3 takes its own text there (section 5).
  - **The other rulings,** points 1, 2, 3, 5, 10, 11, 12 and 13, fill gaps Y leaves, and change none of its words.
- **Lane 3's rulings on draft 2's points** (#165 6747528875, with #165 6747722621 narrowing point 15). Lane 3 names no erratum for them:
  - point 15: Y gives no rule for a malformed `pid` or `pidstart`. The ruling takes Y:5505's own fallback, the wait for `deadline_epoch` (section 5);
  - point 16: (b) says how Y:5488's "stopped by its bound" is told from the body's own end, refining point 11; (c) gives a `why` where Y:5487's refusal line is missing; (a) and (d) fill gaps (sections 1 and 3);
  - points 6 and 13: the facts file's two removers are one rule, and case 11 leaves `<uuid>.bind.*` (section 1). Y:5507's "dropped" is unchanged.
- **Lane 3's rulings on draft 3's points** (lane 3's ruling, #165 6751772589, with lane 3's correction, #165 6751818233, replacing its answer to point 18's question 1). Round B8 or B9 records them in `research.md` "as decisions, or as errata where a Y line needs one", and B8 takes the numbers in order (lane 3's ruling, #165 6751772589):
  - **ERR-43, the erratum to Y:5394** (lane 3's correction, #165 6751818233; numbered in round B8, research:640–653 at B8): Y:5394's `<k>` becomes the files' index, one above the highest index among the uuid's `.bind.*` files and 1 where there are none, and `bind` keeps the attempt, 1 to 3 per offer (Y:5429). `out` is already a key of the record (Y:5431), so the erratum names no new key. B8's ERR-43 records the files' index, and says the facts file sits beside the `out` file. It does not say that every reader finds the facts file from `out=`. That rule is lane 3's ruling, not yet numbered in `research.md`: the facts file is `out=`'s `<offers>/<uuid>.bind.<k>` stem with `.facts`, which the outer, the body and the guard's sweep all find from `out=`, never from `bind`, the outer handing the body that path where the body cannot read the record itself (lane 3's ruling, #165 6752064118). The ruling said B8 or B9 records it with the erratum. That rule names no new key either. This contract follows the erratum (sections 1, 3 and 7), and the rule with it (sections 1 and 7);
  - point 17: Y sets no length for a `pid`. The ruling takes `pid_start_of`'s cap of 10 digits into the record's rule (sections 5 and 6);
  - point 18, question 2: Y:5396 and Y:5486–5488 have the outer write its outcome. The ruling has it read the record again first, and write only over its own `binding` (section 1);
  - the two readings: lane 3 confirms both, the record with `pid` alone (section 5) and point 16(b) sorting only the `why` (section 4). Neither changes a Y line.

## Changes from draft 1

Each is lane 3's ruling, #165 6746215510, by point:
- **Section 0:** the basis at B3 `a138b1b1` and lane 1's draft 4.5; lane 3's analysis as a source.
- **Section 1:** `umask 077` (1); every rewrite by a temporary file and `mv -f` (2); the hook's `$!` (4); the facts file (6); the files' lifetime (13).
- **Section 2:** the rename back after the third failure and at the stale row (7); a stopped bind ends `bind-failed` (3).
- **Section 3:** no [read] marks (3); the hook writes the timing keys, `host` and `container` first (4); the rows `utc` (12) and `detach` (8); `rc`'s 128 + N (11); `why` and `note=` from the facts file (6); a new attempt's keys (5).
- **Section 4:** the range of `LANES_BIND_BOUND_S`, and malformed `c`, `n_a` and `w_s_ms` (10).
- **Section 5:** case 3's dead-outer text (9); the renames in cases 7, 9 and 10 (7); a `binding` record with no `pid`, and case 11's set (4); case 11's citation (14).
- **Sections 6 to 8:** the session test only for `setsid` and `python3` (8); a record with no `pid` (4); who reads `detach`, `utc` and the facts file; AT-F6 case 15's 143 (11); the seam's range (10).
- **Sections 9 and 10:** lane 3's errata and the four rulings that change Y's text; the 14 points ruled, and two new ones.

## Changes from draft 2

Each is lane 3's ruling, #165 6747528875, by point, unless another source is named:
- **Header and section 0:** the status at draft 3; lane 3's two rulings and B4's `research.md` as sources; the basis at B4 `6fe94d17`.
- **Section 1:** the body creates its facts file, empty, before its first step (16(a)); the guard's removal also takes any facts file an outer left, and case 11 leaves `<uuid>.bind.*` (6 and 13).
- **Section 2:** a record without a well-formed `pid` and `pidstart` reaches the stale row (15); the `note=` naming `<out>` (16(d)); which end was a stop (16(b)).
- **Section 3:** the outer writes `pidstart` only when `pid_start_of $$` exits 0 (15); "stopped by its bound" needs the time as well as the code (16(b)); the `why` for a body that gave no reason (16(c)); the `note=` where `rc` is 0 and no facts file is left (16(d)).
- **Section 4:** the sweep keeps tasks:610's trigger, as this contract reads point 16(b).
- **Section 5:** a `binding` record without a well-formed `pid` and `pidstart`, what well-formed means, and why it waits (15, with lane 3's ruling, #165 6747722621); case 11's set leaves out `pid` and `pidstart` (15), and case 11 leaves `<uuid>.bind.*` (6 and 13); ERR-24 named in case 11's row, and ERR-23's lines in B4's `research.md` (#165 6747379291).
- **Section 6:** `pid_start_of`'s own refusals, with pid `1` read (lane 3's ruling, #165 6747722621); lane 4's T412 as a caller under R49 (#165 6747564649; #165 6750702819); a record without a well-formed `pid` and `pidstart` (15).
- **Sections 9 and 10:** ERR-23 and ERR-24 by number; how draft 2's ruled points stand beside Y; points 15 and 16 ruled, and two new ones.

## Changes from draft 3

Each is lane 3's ruling, #165 6751772589, by point or reading, unless lane 3's correction, #165 6751818233, is named:
- **Header and section 0:** the status at draft 4; lane 3's ruling and its correction as sources.
- **Section 1:** the files' index apart from the attempt, and the record's `out` naming the attempt's own file, with the facts file beside it (18, as #165 6751818233 corrects it); an earlier offer's leftovers after a case 11, and the next offer's files above them (18); the outer reads the record again before it writes its outcome, its `.out` still carries the outcome, and the one window left (18, question 2).
- **Section 2:** the outer's outcome only over its own `binding` (18, question 2); an 11-digit `pid` reaches the stale row (17); the three attempts per offer unchanged by the files' index (#165 6751818233).
- **Section 3:** `bind` the attempt, apart from the files' index, and `bind=k+1` counting attempts only; `out` naming the attempt's own file; a new attempt's `out` at the next files' index (#165 6751818233); a record with `pid` alone reads as one with no `pid` (reading 1).
- **Section 4:** point 16(b) sorts only the `why`, and the sweep keeps tasks:610's trigger whatever the time (reading 2).
- **Section 5:** a well-formed `pid` has at most 10 digits, with lane 3's why (17); the record with `pid` alone (reading 1); case 11's check of `bind` unchanged (#165 6751818233).
- **Sections 6 and 7:** the record's rule takes `pid_start_of`'s cap (17); the guard and the outer find the attempt's file by `out` (#165 6751818233); the outer's read before its outcome (18, question 2).
- **Sections 9 and 10:** the erratum to Y:5394, its number pending in B8 or B9 (#165 6751818233); how draft 3's ruled points stand beside Y; points 17 and 18 ruled, and the readings confirmed.

## Changes from draft 4

Each is lane 3's ruling, #165 6752064118, on lane 5's reading of draft 4 (#165 6752026548):
- **Header and section 0:** the status at draft 5; lane 3's ruling as a source.
- **Section 1:** the facts file's path, `out=`'s `<offers>/<uuid>.bind.<k>` stem with `.facts`; the outer, the body and the guard's sweep all find it from `out=`, never from `bind`; where the body cannot read the record itself, the outer hands it that path.
- **Section 7:** the guard's sweep, the outer and the body find the facts file from `out=`, never from `bind`, and the outer hands the body that path where the body cannot read the record itself.
- **Sections 9 and 10:** B8 or B9 records the rule with the erratum to Y:5394, and it names no new key; no point is left open at draft 5.

## Changes from draft 5

Each is round B8's, the spec PR's head `270236bb`, unless another source is named:
- **Header and section 0:** the status at draft 6; B8's `research.md` as the source of ERR-43, beside B4's row; B8's `tasks.md`, in T502's block, has this contract govern where T502's text and it differ (tasks:617 at B8).
- **Section 9:** the erratum to Y:5394 is ERR-43 (research:640–653 at B8; lane 3's correction, #165 6751818233); ERR-43 records the files' index and says the facts file sits beside the `out` file; the rule that every reader finds the facts file from `out=` is lane 3's ruling, #165 6752064118, not yet numbered in `research.md`.
- **Section 10:** point 18, question 1's erratum is ERR-43; no point is left open at draft 6.

## 10. Open points, for lane 3

**Draft 1's points 1 to 14 are ruled by lane 3 (6746215510); see sections 1 to 9:**

| Point | Ruled | Lands in |
|---|---|---|
| 1 | `umask 077`: the record 0600, a new `offers/` 0700, an existing one not `chmod`'d | section 1 |
| 2 | every rewrite a temporary file in `offers/` and `mv -f` | section 1 |
| 3 | the writers confirmed, with point 4's change; a bind stopped by its bound or a signal ends `bind-failed` | sections 2 and 3 |
| 4 | the hook's `$!`; its `binding` rewrite's keys; a `binding` record with no `pid`; case 11's set | sections 1 to 7 |
| 5 | the keys a new attempt keeps and drops | section 3 |
| 6 | the body's facts file | sections 0, 1, 3 and 7 |
| 7 | cases 7 and 9 rename back; case 10 renames nothing (ERR-23) | sections 2, 5 and 9 |
| 8 | `detach`, and the session test only for `setsid` and `python3` | sections 3, 5, 6 and 7 |
| 9 | case 3's text for a dead outer whose session is live | sections 5 and 6 |
| 10 | the bound's inputs | sections 4 and 8 |
| 11 | `rc` is `wait`'s 128 + N; AT-F6 case 15 asserts 143 | sections 3 and 7 |
| 12 | every rewrite keeps `utc=` | sections 3 and 7 |
| 13 | the `.bind.*` files and the facts file live as long as the bind | section 1 |
| 14 | the errata's citations (ERR-24) | sections 5 and 9 |

**Draft 2's points 15 and 16 are ruled by lane 3 (6747528875, with 6747722621 narrowing point 15); see sections 1 to 6:**

| Point | Ruled | Lands in |
|---|---|---|
| 15 | a malformed `pid` or `pidstart` does not join case 11, and counts as a missing `pid`; what well-formed means; the outer's `pidstart` only on exit 0; `pid_start_of`'s own refusals, with pid `1` read (6747722621) | sections 2, 3, 5 and 6 |
| 16 | the body's empty facts file first; "stopped by its bound" by code and time; the `why` for a body that gave no reason; the `note=` where `rc` is 0 and no facts file is left | sections 1 to 4 |
| 6 and 13 | the facts file's two removers as one rule; case 11 leaves `<uuid>.bind.*` | sections 1 and 5 |

**Draft 3's points 17 and 18, and its two readings, are ruled by lane 3 (6751772589, with 6751818233 replacing its answer to point 18's question 1); see sections 1 to 9:**

| Point | Ruled | Lands in |
|---|---|---|
| 17 | (a): the record's rule takes `pid_start_of`'s cap, at most 10 digits; an 11-digit `pid` is malformed and reads as no `pid`, "BINDING IN PROGRESS" until `deadline_epoch`, then case 9 | sections 2, 5, 6 and 9 |
| 18, question 1 | no attempt reuses a `<k>` whose files are still there: the files' index is one above the highest among the uuid's `.bind.*` files, apart from the attempt; `bind` stays the attempt, 1 to 3 per offer; `out` names the attempt's own file, with the facts file beside it; an erratum to Y:5394, ERR-43 in B8 (6751818233, correcting 6751772589) | sections 1, 2, 3, 5, 7 and 9 |
| 18, question 2 | yes: just before its outcome the outer reads the record again, and writes only over its own `binding`; its `.out` still carries the outcome; the temporary file and `mv` leave one window, milliseconds wide (6751772589) | sections 1, 2, 7 and 9 |
| readings 1 and 2 | confirmed: a record with `pid` alone reads as one with no `pid`; point 16(b) sorts only the `why`, and the sweep keeps tasks:610's trigger | sections 3, 4, 5 and 9 |

No point is left open at draft 6.
