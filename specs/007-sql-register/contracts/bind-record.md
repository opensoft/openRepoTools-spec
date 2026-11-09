# Contract: the bind record, the session's offer file in its bind modes

- Owner: lane openRepoTools-5. Tag: [L5]. Feature 007, `contracts/bind-record.md`, which tasks.md's T501 lands (R50), after the spec PR and before T502. T502, T507 and T510 build on it, lane 4's T412 uses its liveness helper, and lane 2's AT-F6 tests it.
- Written 2026-11-09T16:31Z by lane 5's build writer for T501, on the local branch `lane5/007-T501-bind-record` from `722078c9`.
- What it says: the offer file as the bind's record (section 1), its four modes (section 2), every key with the phase and the process that writes it (section 3), the bound (section 4), the guard's eleven cases in short (section 5), `pid_start_of` and the liveness test (section 6), who reads the record (section 7), its test seams (section 8), where Y differs from W and which rulings this contract follows over Y's text (section 9), and its open points (section 10).
- It states no value its sources do not give. Where they name no writer for a key, section 3 marks lane 5's reading with [read], and section 10 lists it.

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

- **W and Y.** In "Answer 2's detached bind, and the bind as one verb", W:5336–5512 and Y:5387–5563, Y's line is W's plus 51, and the text is W's but for two lines of the bound's sum (section 9). So T501's W:5372–5383 is Y:5423–5434, and its W:5442–5456 is Y:5493–5507. W:5149 is Y:5200, unchanged. This contract cites Y, the branch's change, and follows it.
- **research.md** entries are cited by name: ERR-05 (R05), ERR-10 (R47), ERR-11 (R52), ERR-12 (R53), ERR-14 (R58) and DEC-17 (R48).
- **The pin's lines** cited here were read at `2a057a6d`, to check the lines Y cites.

**The writers**, as this contract names them:
- **the guard**: `guard_run` in a prompt's hook. At the prompt that makes the offer it writes the file through `guard_offer` (pin:10960–10971); at each later prompt its offer-site branch answers from the record (Y:5493).
- **the hook**: `guard_answer`'s choice 2, in the hook whose prompt is the answer `2` (Y:5393). T501's line calls its write of `c`, `n_a` and `w_s_ms` the guard's (tasks:581), and T507 places that write in `guard_answer` (tasks:747).
- **the outer**: `guard-bind <uuid>`, the detached child, "the pid of record" (Y:5396).
- **the body**: `guard-bind --body <uuid>`, which the outer runs under `timeout` (Y:5397–5398), or `LANES_BIND_BODY` in its place under the seam (R48).

## 1. The offer file as the bind's record

- **The record is the offer file itself,** `$CLAUDE_CONFIG_DIR/lanes/offers/<session uuid>` (Y:5423). `guard_offer_file` names it `${CLAUDE_CONFIG_DIR:-$HOME/.claude}/lanes/offers/<uuid>` (pin:10271–10273). It is "keyed by the transcript, read by the guard at the offer site …, and the session's own fact" (Y:5423).
- **Not a new state file:** "The detached bind's record is the existing offer file in new modes, not a new state file", since "it is keyed by the transcript and the guard already reads it" (Y:6057, row 31).
- **On the mount shared across containers:** "The directory is a bind mount shared across containers …, so the record names its host and container, and liveness is judged only where both match" (Y:5423).
- **Its form:** `key=value` lines (Y:5423). `note=` may repeat, one line per act still owed (Y:5432).
- **Who creates, rewrites and removes it:**
  - **Created** by the guard at the offer, `mode=name-drift` with the offer's identity, its directory made by `mkdir -p` (`guard_offer`, pin:10962–10971). That is today's code, and the bind keeps it.
  - **Rewritten** by the hook to `mode=binding`, "by temp file and `mv`" (Y:5393; tasks:602).
  - **Rewritten** by the outer at its start, "with its own `$$` and start time" (Y:5396), and at its end, with the outcome (Y:5486–5488).
  - **Rewritten** by the guard after a failure, to `mode=name-drift` with `bind=k` (Y:5502–5503), and at the stale row (Y:5505).
  - **Removed** by the guard after "MOVED" (Y:5501), and dropped by it where it cannot be read (Y:5507).
  - Y does not say how the outer and the guard rewrite it: open point 2.
- **Its mode bits:** Y gives none for the record or for `offers/`: open point 1.
- **The per-attempt output file,** `<offers>/<uuid>.bind.<k>.out`, `<k>` being the attempt:
  - The outer's stdout and stderr go to it, with its children's (Y:5394, Y:5408).
  - It is 0600: the hook forks under `( umask 077; … )` (Y:5394), and Y:5408 has "stdout and stderr to one 0600 file per attempt" (also Ytp:5970).
  - The record's `out` names it (Y:5431). It is kept after a failure (Y:5487). When it is removed, Y does not say: open point 13.

## 2. Its four modes

| Mode | What it says | Written by | Moves to |
|---|---|---|---|
| `mode=name-drift` | the offer, waiting for an answer: today's mode, and the mode "once the offer is restored" after an attempt (Y:5427) | the guard: at the offer (pin:10963); after a failure, with `bind=k` and the window renamed back to `<from>` (Y:5502); after the third failure, with `bind=3` (Y:5503); at the stale row, with `rc=unknown` (Y:5505) | `mode=binding`, by the hook, at an answer `2` while choice 2 is still offered |
| `mode=binding` | an attempt is running, or has not yet written its outcome | the hook, by temp file and `mv` (Y:5393); the outer rewrites it at its start with the mode unchanged (Y:5396) | `mode=bound` or `mode=bind-failed`, by the outer; `mode=name-drift`, by the guard at the stale row (Y:5505), and for a record from another host or container only after `deadline_epoch` plus 60 s (Y:5506) |
| `mode=bound` | the attempt ended 0: `rc=0`, with a `note=` for each act still owed (Y:5486) | the outer | removed by the guard, which says "MOVED" once (Y:5501) |
| `mode=bind-failed` | the attempt ended non-0: `lane-start`'s code with its `why` (Y:5487), or 124, 137 or the signal's code where its bound or a signal stopped it (Y:5488) | the outer | `mode=name-drift`, by the guard at the next prompt, which renames the window back to `<from>` (Y:5502–5503) |

- **The offer site's branch** takes `binding`, `bound` and `bind-failed`, and `guard_answer` is never called on them (Y:5493; tasks:588). A `mode=name-drift` record goes to `guard_answer`, as today.
- **Outside the bind,** today's file also takes `mode=allow`, choice 1's record (pin:11115), which today's offer site passes by (pin:10651). The bind adds `binding`, `bound` and `bind-failed` to `name-drift` and `allow`.
- **At most three attempts per offer:** "never by itself: a failed bind spends the person's yes; each `2` is one attempt, at most 3 per offer" (Y:5504). After the third failure choice 2 is no longer offered (Y:5503).
- **A bind stopped by its bound or by a signal** ends `mode=bind-failed` by this contract's reading: open point 3.

## 3. Every key, with its phase and its writer

Phase 1 is T502's, with T507's `c`, `n_a` and `w_s_ms`. Phase 2 is T510's (Y:5558–5559; tasks:569, :1215). [read] marks a writer that Y does not name, which is lane 5's reading (open point 3).

| Key | What it holds | Phase, task | Writer |
|---|---|---|---|
| `mode` | `binding`, `bound`, `bind-failed`, or `name-drift` (Y:5427) | 1, T502; `name-drift` is today's | as section 2 gives |
| `from`, `to`, `session`, `uuid`, `window`, `pane` | "the offer's identity, as today's file has it" (Y:5428): the lane the window left, the lane named, the session's name, its uuid, the window's reference and its pane (pin:10964–10970) | today's | the guard, at the offer (`guard_offer`, pin:10963–10970); every rewrite keeps them |
| `bind` | "the attempt, 1 to 3" (Y:5429) | 1, T502 | the hook, which names the attempt's `<k>` at the fork (Y:5394) [read]; the guard keeps it as `bind=k` after a failure (Y:5502–5503) |
| `started_utc` | "the start" (Y:5429); the bound runs from it (Y:5436) | 1, T502 | the outer, at its start [read] |
| `bound_s` | the bound, "computed at the start by the sum" (Y:5429; section 4) | 1, T502; T507 makes the sum read `c`, `n_a` and `w_s_ms` (tasks:616) | the outer: "`guard-bind` computes `bound_s` at the start" (Y:5436) |
| `bounded` | "`yes` where `timeout` exists, else `no`" (Y:5429); "The record says `bounded=no`" (Y:5403) | 1, T502 | the outer, which runs the body with `timeout` or without it [read] |
| `deadline_epoch` | "the start plus `bound_s` plus 5 s, the 5 s being for the outer to write the outcome after `timeout`'s `KILL`" (Y:5429) | 1, T502 | the outer [read] |
| `pid`, `pidstart` | the outer's `$$`, and its start token from `pid_start_of` (Y:5396, Y:5434; section 6) | 1, T502 | the outer, which "rewrites the record with its own `$$` and start time" (Y:5396) |
| `host`, `container` | "`LANES_WORKSTATION` (else the hostname) and `LANES_CONTAINER`" (Y:5430) | 1, T502 | the outer, with `pid` [read] |
| `out` | "the attempt's output file" (Y:5431), `<offers>/<uuid>.bind.<k>.out` | 1, T502 | the hook, which opens it at the fork (Y:5394) [read] |
| `c` | `.head`'s `c=`: "the larger of the two URLs' `connect_timeout=`, not the snapshot's" (CLI44:704; CLI44:296; O15) | 1, T507 | the hook, at `mode=binding`, from that prompt's prefetch `.head` (tasks:747; CLI44:304, :706) |
| `n_a` | `.head`'s `n_a=`, which is `register_addresses=`, default 1 (CLI44:297) | 1, T507 | as `c` |
| `w_s_ms` | `.head`'s `w_s_ms=`: `w_s` in whole milliseconds, 300, or `register_rtt_ms=` + 200 where `register_rtt_ms=` is over 100, and 0 where no service can answer (CLI44:298, :301–302; O15; B2:138–143) | 1, T507 | as `c` |
| `act` | "the guard's base `<act-id>@<n>:<generation>`, from the `n` and `generation` of the prefetch that made the offer" (Y:5431; Y:5679) | 2, T510 | the guard, "at the offer" (tasks:1220) |
| `write_id`, `generation`, `not_after`, `write_lane`, `args_sha256` | the five copies, from "`start-record`'s pending record, the newest whose `verb` is `start-record` and whose `session` is this transcript's uuid: its `write_id`, `generation`, `lane` (as `write_lane`) and `args_sha256`, and the largest `not_after` among its `call` lines" (Y:5431) | 2, T510 | the outer, "when it writes an outcome other than 0", and the guard "at the stale row where the outer left none" (Y:5431). None where that record holds no `call` line or there is none (Y:5431), and none for a record from another host or container (Y:5506; tasks:1223). The record drops them on LR007 `another-write` (Y:5518) |
| `rc` | `0`; `lane-start`'s code; `124`, `137` or the signal's code; `unknown` (Y:5486–5488, Y:5505). Phase 2 adds `3` and `2` from the lookup (Y:5517–5519) and `start-record`'s codes (Y:5541–5544) | 1, T502; phase 2's codes, T510 | the outer; the guard writes `rc=unknown` at the stale row (Y:5505) |
| `why` | "`lane-start`'s last refusal line", or "stopped by its bound" or "stopped by a signal", then "what it wrote is not known" (Y:5487–5488) | 1, T502 | the outer; how it learns the line is open point 6 |
| `note=` | one line per act still owed, "and its command" (Y:5432): "the stamp not written or not pushed …, the cell not ending on this uuid, or the lane left not marked" (Y:5486). In phase 2 the lane left's `set-row-state` 2 or 3 "becomes a `note=`" (tasks:1232) | 1, T502; T510 | the outer, from the body's steps (open point 6). The body writes no key (Y:5396–5398) |
| `ended_utc` | the outcome's time (Y:5432); at the stale row, "the guard's time" (Y:5505) | 1, T502 | the outer; the guard, at the stale row |

## 4. The bound

- **One helper** computes `bound_s` from the record, for the hook's "BINDING" text and for the outer's start, "so the two never differ" (tasks:604, :616).
- **The sum** (Y:5459): `B`, plus one read's `T_r` + `w_s` to spare, plus the 10 s of `-k`, rounded up to 10 s. "That is 437 + 28`N_a`·`c` + 19`w_s` s, rounded up to 10 s: 500 s at `c` = 2 and 530 s at `c` = 3, with or without the service", and 590 s at Eagle's p90, where `c` = 5 and `w_s` = 0.656 s (Y:5459). With two addresses it is 550 s and 610 s, or 560 s and 620 s with the service (Y:5462).
- **In bash 3.2's integers,** from the record's keys (tasks:616; B2:140): `bound_s = (437000 + 28000·n_a·c + 19·w_s_ms + 9999) / 10000 · 10`.
- **The defaults,** where the keys are absent: `c` = 2, `n_a` = 1, `w_s_ms` = 0, which give 500 s (tasks:616; CLI44:304). They apply to every bind before T507 lands, and to every record that lacks the keys (tasks:616, :747).
- **`LANES_BIND_BOUND_S`** overrides the sum, for tests (Y:5429; tasks:616; Ytp:6201). AT-F6 sets `LANES_BIND_BOUND_S=15`, "under which the body runs under `timeout -k 10 5` and `deadline_epoch` is the start plus 20 s" (Ytp:6020–6021). Its range is open point 10.
- **`deadline_epoch`** is the start plus `bound_s` plus 5 s (Y:5429): at the default, 505 s after `started_utc` (Ytp:6044–6045).
- **The body's `timeout`** is `timeout -k 10 "$(( bound_s - 10 ))"` (Y:5397): 490 s at the default (Ytp:5985).
- **`bounded=yes`** where `timeout` exists. **`bounded=no`** where it is missing, as on macOS without coreutils: the body runs unbounded, started under `set -m` so that it leads a group of its own, and the outer's trap sends `kill -TERM -- -<body pid>`, then `KILL` 10 s later if the group is still there (Y:5402–5405; tasks:611).
- **The outer's sweep** (R47; ERR-10): after `timeout` returns 124 or 137, or after the outer's trap fired, the outer sends `TERM` to the group `timeout` led, then `KILL` 10 s later if any member is left, and only then writes the outcome. It never sweeps after the body's own exit, 0 or not (tasks:610; L5:527–528). The sweep ends by `bound_s` at the latest, inside `deadline_epoch` (tasks:610).
- **What the bound is:** "`bound_s` of wall time from `started_utc`, over the child's whole body" (Y:5436). It "never stops a child whose every step stays inside its own bound" (Y:5436). In files mode it "is a judgment" (Y:5483).

## 5. The guard's eleven cases, in short

The check is a branch of its own at the offer site, after 18(h)'s count and before the table, on `mode` in `binding`, `bound` or `bind-failed` (Y:5493; tasks:588). It costs one small file read, `kill -0` and one `/proc` read, about 7 ms (Y:5493). Every text goes to stderr, and the hook string is unchanged (Y:5493; FR-040). The texts are Y's, in short (Y:5497–5507).

| # | Case | Exit | Record after | Text, in short |
|---|---|---|---|---|
| 1 | the answer `2` itself | 2 | `mode=binding` | "BINDING: this window is now named `<to>` … detached (pid `<p>`, bounded at `<bound_s>` s) … send your work again in a minute or so; until it ends, each prompt is answered BINDING IN PROGRESS" |
| 2 | same session; child live, inside the bound | 2 | unchanged | "BINDING IN PROGRESS: … it ends by `<deadline utc>` at the latest. Your prompt was NOT sent; send it again in a few seconds". Where the record says `bounded=no`, the text leaves out "at the latest" |
| 3 | same session; child live past `deadline_epoch` | 2 | unchanged | "THE BIND HAS RUN PAST ITS `<bound_s>` s BOUND and is still running (pid `<p>`, started `<utc>`)". With `bounded=no`: "this host has no `timeout`, so nothing stops it; it is most likely in a `git` call". With `bounded=yes`: "its `timeout` has not ended it; it is likely stuck on the disk or the network". Then what waiting costs and what `kill <p>` costs, and "Your prompt was NOT sent." |
| 4 | another session wanting the same lane | its own table | its own record | it never sees this record. If it answers `2`, on primary `start-record`'s `bind:<to>` expectation refuses whichever bind commits second, with 2; in files mode, `lane-start`'s re-read at `:2614` does |
| 5 | after success, `mode=bound` | 2, once | removed | "MOVED: this window is lane `<to>`, bound … at `<utc>`", one line per owed note, "This prompt was NOT sent". The next prompt exits 0 |
| 6 | after failure, `mode=bind-failed`, attempt `k` below 3 | 2, once | `mode=name-drift`, `bind=k`; the window renamed back to `<from>` | "THE BIND TO LANE `<to>` FAILED (exit `<rc>`): `<why>` … the offer is KEPT: reply `1`, `2` or `3`" |
| 7 | failure on the third attempt | 2 | `mode=name-drift`, `bind=3` | as case 6, and choice 2 is no longer offered |
| 8 | does a failure restart it? | — | — | never by itself: a failed bind spends the person's yes; each `2` is one attempt, at most 3 per offer |
| 9 | stale: `mode=binding`, same host and container; its pid reused, or dead with no process left in the session it led | 2, once | `mode=name-drift`, `rc=unknown`, `ended_utc` the guard's time, and in phase 2 the five copies | "THE BIND STARTED AT `<utc>` … IS NO LONGER RUNNING AND LEFT NO OUTCOME … the next `2` finds out first" |
| 10 | `mode=binding` from another host or container | 2 | unchanged until `deadline_epoch` plus 60 s, then as case 9 but with no copies | "A BIND OF THIS TRANSCRIPT IS RUNNING ON `<host>/<container>` … Your prompt was NOT sent". Where the record says `bounded=no`, the text adds that the child there may still be running, and that on primary `bind:<to>` refuses whichever bind commits second |
| 11 | an unreadable record | 2 | dropped | today's text: "the pending offer at `<f>` could not be read, so it has been dropped rather than answered on a guess. Send your prompt again and the question will be asked afresh." (pin:11093; open point 14) |

- **"Child live"** in cases 2 and 3 is section 6's test, including a dead outer whose session still has a process (Y:5505; svc:160–162).
- **Case 10's 60 s** "covers a `git` call that outlives the body under its own `timeout` (50 s, P1), plus 10 s between the two hosts' clocks". Where `bounded=yes`, the old write's `not_after` has passed by then (Y:5506).
- **Case 6's rename back** fixes today's finding: the window kept `<to>`'s name, so the kept offer was dropped (Y:5509; tasks:625).
- **Case 1's `<bound_s>`** comes from section 4's helper, and the hook makes no register call of its own for answer 2 (tasks:604).
- **"BINDING" and "BINDING IN PROGRESS"** (R58; ERR-14): "BINDING" is answer 2's own text, and "BINDING IN PROGRESS" each prompt's while the bind runs. Y:5389's "binding in progress" for answer 2 is history.

## 6. Liveness, and `pid_start_of`

- **The test** (Y:5434): "the pid is live and its start time equals `pidstart`". It is judged only where the record's `host` and `container` are this guard's (Y:5423). Otherwise case 10 answers.
- **`pid_start_of <pid>`** (Q-L5-6, L5:516–519; R49):
  - **Argument:** one pid.
  - **Output:** the process's start token on stdout, where it exits 0.
  - **Exit 0:** the pid is live, and its token is printed.
  - **Exit 1:** the pid is not live.
  - **Exit 2:** the start cannot be read. Every caller treats 2 as live, never as dead: "A wrong 'dead' would let a claim or a bind be stolen; a wrong 'live' only waits" (R49). R49 is recorded here.
  - **The token:** on Linux, field 22 of `/proc/<pid>/stat`, counted after the last `)`; on macOS, `ps -o lstart= -p <pid>` (L5:518; Y:5434). It is compared as a string, on the same host and container only (L5:518).
- **So the outer is live** where `pid_start_of <pid>` exits 0 and prints the record's `pidstart`, or exits 2. It is not live where it exits 1, or exits 0 with another token, which is a reused pid: "a reused pid and a dead pid both read as not live" (Y:5434).
- **One helper** "serves this record and F10's pending record" (Y:5434), whose claims' owners it tests (Y:5185; tasks:938). T502 defines it, and it lands before lane 4's T412 (L5:519).
- **It never copies `lock_steal_if_dead`'s pid-only test** (Y:5434; tasks:617). That test is `kill -0` on the lock's pid alone (pin:1286–1296), "under which a reused pid looks live until the 10-minute age" (Y:5434).
- **Its cost:** "P5: 200 checks take 1.42 s, about 7 ms each" (Y:5434).
- **The stale row's session test** (Y:5505; tasks:618), on a record of this host and container whose outer is not live:
  - **A reused pid** (exit 0, another token) needs no session test, and the bind is stale at once: "the kernel does not hand a pid out again while a session of that number has members" (Y:5505; Ytp:6069–6071).
  - **A dead pid** (exit 1): "`$detach` made the outer the leader of its own session", and the body, `timeout` and any `git` under its own `timeout` stay in that session after the outer dies (Y:5505). While any process has the record's `pid` as its session id, the bind is still running, the guard answers by cases 2 and 3, and no second bind starts (Y:5505; svc:160–162). Once none has, the bind is stale, case 9 (svc:156–158).
  - **The probe:** `pgrep -s <pid>`, as v3f's case 6 names it, else a scan of field 6 of `/proc/*/stat` where `pgrep` is missing (tasks:618; Y:5505; Ytp:6061).
  - **Where the outer led no session** (`nohup` alone), or the session cannot be read, the row waits for `deadline_epoch` instead (Y:5505). No key says which: open point 8.

## 7. Who reads the record

- **The guard,** at the offer site, at each prompt of the session (Y:5493–5507): `mode`, the identity, `host`, `container`, `pid`, `pidstart`, `bounded`, `bound_s`, `started_utc`, `deadline_epoch`, `bind`, `rc`, `why` and the `note=` lines, for section 5's cases. `guard_answer` reads a `mode=name-drift` record as today (pin:11076–11093). In phase 2, at the stale row, it also reads `start-record`'s pending record for the five copies (Y:5431).
- **The hook's helper,** for case 1's `<bound_s>`: `c`, `n_a` and `w_s_ms`, or `LANES_BIND_BOUND_S` (tasks:604, :616).
- **`guard-bind`:**
  - The outer refuses unless the record says `mode=binding` (Y:5396), and sums `bound_s` from its `c`, `n_a` and `w_s_ms` (CLI44:304; tasks:616).
  - It reads only the offer file before `lane-start`: its read-door class is `none` (Y:5399; tasks:605).
  - In phase 2, T510's child reads `act` for the `set-row-state` on the lane left, with `LANES_EXPECT="row:<from> bind:<from>"` (Y:5398; tasks:1232); the five copies for its lookup (tasks:1224); and `ended_utc`, `c` and `n_a` for its `w₀` wait (tasks:1225).
- **Lane 4's T412** reads not the record but its liveness helper: the pending record's claims are live by `pid_start_of` (tasks:938; Y:5185).
- **Lane 2's AT-F6** (Ytp:5957–6259): twenty-two cases (R53; ERR-12), and four for rows the cases do not reach, which "write the offer file's record directly, with a sleeping process standing in for the child where one is needed" (Ytp:6202–6206). Its names are selected by T502's, T507's and T510's verifies (tasks:584, :708, :1215).

## 8. Test seams

- **`LANES_BIND_BOUND_S`** overrides `bound_s` (section 4).
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

## 10. Open points, for lane 3

1. **The record's mode bits.** Y gives none for the offer file or for `offers/`. It gives 0600 only for the per-attempt output, through the fork's `umask 077` (Y:5394, Y:5408), and "mode 0600, in a 0700 directory" for F10's pending record (Y:5172). Today's `guard_offer` makes the directory with `mkdir -p` and writes the file with `>`, under the hook's own umask, with no `chmod` (pin:10962–10971). The outer runs under the fork's `umask 077`, and the hook and the guard under the hook's. Lane 3 to say what bits the record and `offers/` take.
2. **How the outer and the guard rewrite the record.** Y says "by temp file and `mv`" only for the hook's rewrite to `mode=binding` (Y:5393; tasks:602). It does not say how the outer rewrites the record at its start (Y:5396) and at its outcome (Y:5486–5488), or how the guard does after a failure and at the stale row (Y:5502–5505). A guard that meets a half-written record takes case 11 and drops it (Y:5507), which during a bind would lose the bind's record. Today's file is written in place with `>` (pin:10963–10971, pin:11115). Lane 3 to say whether every rewrite is a temporary file in `offers/` and a rename.
3. **Writers Y does not name** (section 3's [read] rows). Y names the hook for `mode=binding` (Y:5393), the outer for `pid`, `pidstart` and the outcome (Y:5396, Y:5486–5488), and the guard for its rows (Y:5502–5505); the tasks name `guard_answer` for `c`, `n_a` and `w_s_ms` (tasks:747), and the offer for `act` (tasks:1220).
   - Y does not name who writes `bind` and `out`. This contract reads them as the hook's, since the hook names `<k>` and the file at the fork (Y:5394).
   - Nor who writes `started_utc`, `bounded`, `deadline_epoch`, `host` and `container`. This contract reads them as the outer's, since the outer computes `bound_s` at its start (Y:5436) and writes its own pid (Y:5396).
   - Y:5488 gives a bind stopped by its bound or by a signal its `rc` and `why`, but not its mode. This contract reads it as `mode=bind-failed`, as every end but 0 is (spec:318; svc:152–154).
   - Lane 3 to confirm.
4. **The record before the outer runs.** The "BINDING" text prints the child's pid (Y:5497), but the outer writes `pid` and `pidstart` only once it runs (Y:5396), and the hook's fork, `( umask 077; … & ) >/dev/null 2>&1` (Y:5394), leaves the hook no `$!`. Y does not say how the hook learns `<p>`, or what the guard answers for a `mode=binding` record with no `pid` yet, or one whose outer never started (`$detach` or its exec failing, or `guard-bind` refusing). Nor does it say which absent or malformed keys make a bind-mode record unreadable: Ytp:6230–6233 names only "a truncated one".
5. **Which keys a new attempt keeps.** T510's child reads the attempt before's `ended_utc` and five copies, with `c` and `n_a`, for its `w₀` wait and its lookup (tasks:1224–1225; Y:5511). So the hook's rewrite at the next `2` and the outer's at its start must keep them. Y does not say which keys those rewrites keep and which they clear: the attempt before's `rc`, `why`, `note=` lines and `ended_utc`, and the copies once a lookup has settled them.
6. **How the body's facts reach the outer.** The outer writes `why`, "`lane-start`'s last refusal line" (Y:5487), and one `note=` per act still owed (Y:5486). Those facts come from the body's steps: `lane-start`'s refusal and its stamp, the session cell, and the lane left's `set-row-state`, whose 2 or 3 becomes a note (tasks:1232). The body's exit carries only `rc`. Y does not say how the body hands the rest to the outer.
7. **Cases 9 and 10 keep the offer but do not rename the window back.**
   - Case 6 renames the window back to `<from>`, so that the kept offer can be answered (Y:5502, Y:5509).
   - Case 9, and case 10 once it is stale, restore `mode=name-drift` and keep the offer (Y:5505–5506; svc:156–158). AT-F6's case 6 expects "the offer kept", and that "the next `2` binds with no lookup" (Ytp:6065–6068). Neither row says the window is renamed back.
   - The window still has `<to>`'s name from answer 2 (pin:11155). The guard takes the window's lane from its name (pin:10546–10557), and `guard_answer`'s identity test (pin:11108) then drops the kept offer, as Y:5509 finds of today's failure.
   - Case 7 says "as above" of its text only.
   - Lane 3 to say whether cases 7, 9 and 10 rename the window back.
8. **No key says how the outer was detached.** Case 9 waits for `deadline_epoch` where the outer led no session, with `nohup` alone (Y:5505). But no key in Y:5425–5432 says whether `$detach` used `setsid`, python3's `os.setsid` or `nohup` alone (Y:5395), and once the outer is dead the guard cannot ask the process. Without such a key, a session test on a `nohup`-alone outer finds no process in a session of that number, and reads a body that is still running as stale.
9. **A dead outer whose session runs past its deadline.** Y:5505 answers it by cases 2 and 3. Case 3's text says "`kill <p>` stops it" (Y:5499), but `<p>` is the dead outer's, and no outcome will be written. Y gives no text for this case.
10. **The helper's inputs.** Y gives no range for `LANES_BIND_BOUND_S` (Y:5429). The body's `timeout` takes `bound_s` − 10 (Y:5397). At 10 that is 0, which GNU `timeout` takes as no limit: `timeout -k 10 0` ran its command to its own end. Below 10 it is negative, which `timeout` refuses with 125 (coreutils 9.4, run by this writer on 2026-11-09). Nor do Y or CLI44:304 say what the helper does with a `c`, `n_a` or `w_s_ms` that is present but not a whole number: both give the defaults only where the keys are absent.
11. **The signal's code.** A bind stopped by a signal ends with "the signal's code" (Y:5488; tasks:615), which no source gives as a number, and AT-F6's case 15 asserts it (Ytp:6130–6134).
12. **Today's `utc=` line.** Y:5428 gives the identity "as today's file has it (`:10965–10973`)". Those lines also write `utc=`, the offer's time (pin:10968), which Y's key table does not list. Lane 3 to say whether the bind's rewrites keep it.
13. **The output files' lifetime.** Y keeps the output after a failure (Y:5487), and does not say when `<offers>/<uuid>.bind.<k>.out` is removed: with the record after "MOVED", or never.
14. **A citation in Y and W.** Case 11's "today's text (`:11097–11101`)" (Y:5507; W:5456) points into the comment above `guard_answer`'s identity test. At the pin the text is at `:11093`, under its test at `:11091`.
