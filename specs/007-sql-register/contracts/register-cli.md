# Contract: `lanes-index`'s register commands, as group B calls them, and the readers' forms (feature 007)

**What this is.** `contracts/register-cli.md` for feature 007: the contract between group A (`lanes-index`, lane 1) and group B (the write path in `lanes-edit.sh`, lane 4), and, in §3.6, between group A and the readers, the guard and the renderer. Group B codes against it, and its tests' fake `psql` and stub `lanes-index` stand in for it (FR-017). Lane 1 writes it, lane 4 confirms group B's sections, lane 5 reviews §3.6 (R06), and lane 3 rules (lane 3's post, #165 6724332510).

**Status:** Draft 4.1: draft 4 plus lane 5's check (#165 6739068493). By lane openRepoTools-1, 2026-11-09, and still keyed to commit Y, `a6497d00`. Lane 5 found all 16 items of its review folded in, and took L1-D1. Draft 4.1 takes its two NITs, and lane 1's note on an empty `--lane=` in form 2 ("Changes from draft 4", below).
- Draft 4 (`contracts-register-cli.v4.md`, `5d6ce7ca…`), by lane openRepoTools-1, 2026-11-09, folded in form 4, the guard's second read, and lane 5's review of §3.6, both whole, keyed to commit Y, `a6497d00`.
- **Form 4:** lane 1 picked its argv (#165 6727229519) after lane 3's O17 ruling (#165 6727201960).
- **Lane 5's review** (#165 6738431117) is taken item by item as lane 1 decided it, as the contract's author: B1 to B8, C1 to C14, and O14 to O18. Lane 3 then ruled B4 and B7 (#165 6738474549), which closes O19 and O20. Only O21, lane 4's, stays open.
- **L1-D1,** lane 1's own decision in draft 4: `--lane` and `--log-key` take their value in the `=` form.
- Draft 3, by lane openRepoTools-1, 2026-11-08, took lane 3's rulings of 20:15Z (#165 6726556972: R01, R02, R04, R05, R06, R08 and R09, with R17 and R45 as context) and lane 4's answers 36 and 40 (#165 6726296119, Q-L1-1), and was keyed to commit Y, `a6497d00`, X's child, as lane 3 now keys 007 (#165 6727039049). Draft 4 keeps all of it.
- Draft 1 (`contracts-register-cli.md`, `867ce650…`) is T101's basis (R01). Draft 2 (`contracts-register-cli.v2.md`, `745a4b38…`), draft 3 (`contracts-register-cli.v3.md`, `54807ad0…`) and draft 4 (`contracts-register-cli.v4.md`, `5d6ce7ca…`) stay beside it.
- Its date is 2026-11-09 14:00Z, for group B's sections and for §3.6 alike: lane 1 names the contract's own date as §3.6's (R06).

## 0. Basis, and how to read it

**Keyed to commit Y, `a6497d00`.** Every `Y:<n>` is a line of Y's `design.md`, and every `Y-ops:<n>` a line of Y's `operations.md`. Each is quoted by its opening, and every quote checks against Y. Drafts 1 and 2 were keyed to W `bb08d080`, and drafts 3 and 4 are keyed to Y.
- **Y and X.** Y is the child of X, `97147aa8`. In `design.md` it rewrites only SVC-5's lines 3951 to 3955 of X, as Y:3951–3956. So an X line up to 3950 keeps its number, and one above 3955 is Y's line less one. `operations.md` is byte-identical. Lane 3's rulings and the posts this draft takes cite X; where this draft cites one of their X lines, it gives the Y line beside it.
- **Lane 1's own parts** are re-cited against Y here, and text frozen elsewhere keeps its own citations (lane 3, #165 6727022920).

| Input | sha256 | Lines | Used for |
|---|---|---|---|
| Y `design.md` | `35ca5179d5e9e47d…` | 6,745 | every statement marked [Y] |
| Y `operations.md`, byte-identical to X's | `c94ce7d75212dca5…` | 735 | `Y-ops:<n>`: `lanes-index`'s exits, `ready`'s checks, the server's `pg_hba` |
| Lane 3's post keying 007 to Y, #165 6727039049, and its word on re-citing, #165 6727022920 | — | — | the key |
| Lane 3's rulings, #165 6726556972, and its memo `MEMO-007-rulings-20261108.md` | `b2184b1adae0e060…` (the memo) | 839 (the memo) | the [R…] marks |
| Lane 3's O17 ruling, #165 6727201960, in its saved copy `lane3-165-o17-ruling-6727201960.md` | `e46dfafe4a39f9ae…` | 21 | O17 (§8): the guard's read at `n`, and form 3's scope (§3.6) |
| Lane 1's argv for O17, #165 6727229519, as posted (`post-165-o17-argv-2117.posted.md`) | `0a73fe91dfc2f2a1…` | 23 | form 4 (§3.6) |
| Lane 5's review of §3.6, #165 6738431117, and its full file `REPORT-final-review-36-20261109T1057Z.md`, keyed to draft 3 | `e26a91e63497b23d…` (the file) | 340 (the file) | B1–B8, C1–C14 and O14–O18 (§3.6, §8) |
| Lane 1's decisions on that review, `DECISIONS-lane1-on-lane5-s36-review-1110.md`, with its update at 11:10Z | `ca39616dd60828e9…` | 105 | what draft 4 takes of each item |
| Lane 3's ruling on B4 and B7, #165 6738474549, in its saved copy `lane3-165-b4-b7-ruling-6738474549.md` | `a2c71926948a1783…` | 32 | O19 and O20 (§8); B4 and B7 (§3.6) |
| Y `specs/register-service/spec.md` | `a416df1d1b4ab4a2…` | 293 | spec:59, the service's request (B7; O20) |
| Lane 5's check of draft 4, #165 6739068493, in its saved copy `lane5-165-v4-checked-6739068493.md` | `e0420e813dc3fbf2…` | 36 | draft 4.1's changes |
| `code/lanes-edit.sh` at the root pin `2a057a6d`, git blob `d28ed8f2…` | — | 18,977 | the `code/lanes-edit.sh:<n>` lines in §3.6, as lane 5's review cites them |
| Lane 4's answers 36, 38, 40 and 42, #165 6726296119 | — | — | Q-L1-1, in §3.4 |
| Lane 4's block, `lane4-block-T4xx.md`, in its read-only snapshot `snap-1108-op/` | `b95a1ff9dc3aec1f…` | 381 | `lanes_index_bin` (:91), the tests' copy rule (:92) |
| Lane 5's block v3, `lane5-tasks-block-v3.md` | `238e07c91c08ff39…` | 688 | H1 (:363–375), its open points 3, 4, 5 and 19, Q-L5-2 (:383–386) |
| Lane 5's acceptances, #165 6726077268 | — | — | `lanes_index_bin` (Q-L5-2), `reg_flush` (Q-L5-4) |
| Lane 3's `plan.md` draft | `8375e105cd9e1668…` | 662 | the interfaces between the groups, PC-11 |
| Lane 3's `spec.md` draft | `fb209005bb85306f…` | 1,315 | FR-013 to FR-017 |
| Lane 3's `tasks-skeleton.md` | `43b01027c858d25b…` | 878 | L1-A1, and lane 4's block |
| Lane 4's group B plan draft, under review | `e8f162da220a623a…` | 883 | its [B-proposal]s and Q1–Q7 |

- **Reading the drafts:** lane 3's drafts were read with `cat` from `…/openRepoTools-3/briefs/165-007-draft/`, and its memo from `…/openRepoTools-3/briefs/165-007-rulings/`.
- **Lane 4's files:** read with `cat` from `…/openRepoTools-4/.tmp-coordinator/runs-007/sandbox-RK/group-B-plan.md` (the draft of Q1–Q7) and `…/sandbox-GB/w5/snap-1108-op/lane4-block-T4xx.md`.
- **Lane 5's block:** read from the private prep tree, `work-20261021/lane5/w-007/`.
- **The O17 texts and lane 5's review:** lane 3's two rulings and lane 5's post read with `cat` from their saved copies in the private prep tree, `work-20261031-165/reviews/`; lane 5's full file from `work-20261021/lane5/w-007/review-36/`; lane 1's argv and decisions from `work-20261108/007/`. The pinned `lanes-edit.sh` was read from a copy whose git blob id is `d28ed8f2`.
- **The code:** `code/lanes-index:<n>` is the code leg at the root pin `2a057a6d`, as Y cites it.

**The marks:**
- **[Y]:** Y fixes it, at the line cited.
- **[P]:** Y leaves it open, and this is lane 1's proposal. Lane 4 confirms it for group B, lane 5 reviews it in §3.6, or lane 3 rules.
- **[D]:** lane 1's contract decision, which lane 3 has said is lane 1's to make: R01 for CO-2 and CO-6, R09 for the TLS refusal, and the O17 ruling (#165 6727201960) for form 4's argv.
- **[R<nn>]:** lane 3's ruling `<nn>` (#165 6726556972), taken as ruled.
- **[B]:** lane 4's [B-proposal], taken as it stands.
- **[B′]:** lane 4's [B-proposal], changed by this contract. The change is named.
- **[Q-L1-1]:** lane 4's answers 36 and 40, taken by lane 1 (§3.4).
- **[B<n>], [C<n>]:** lane 5's review item of that id (#165 6738431117), as lane 1 took it. [O14] to [O18] mark the open points that review closed.
- **[L1-D1]:** lane 1's own decision in draft 4, as the contract's author: the `=` form of `--lane` and `--log-key` (§3.6).

## Changes from draft 4, made in draft 4.1

Each change takes a point of lane 5's check of draft 4 (#165 6739068493), or lane 1's note on it.

| Change | Where | Point |
|---|---|---|
| The quote of the guard's second read, "which resolves the log's path case-insensitively in the images at `n`, never in the mirror", runs onto Y:4161, so it is cited as Y:4160–4161, and so are the three other citations of that span | "Changes from draft 3"; §3.6, form 3's "At `n′`" and form 4's "What it reads"; §8, O17 | lane 5's first NIT |
| The note that group B's sections are unchanged now names draft 4's two citation-only edits, in §3.2 and §3.3 | "Changes from draft 3" | lane 5's second NIT |
| An empty `--lane=` ends 64 in form 2 as well, for the record, as it does in form 4; §2.7's 64 row names both. T507 never sends one | §2.7; §3.6, form 2's members | lane 1, after lane 5's note |
| The status line, draft 4's digest kept as history, and lane 5's check as an input | header, §0 | — |

No sentence of the contract says that T113 proves the cut, so nothing there changes: `T0` and the cut are T507's (§3.6, "Whose, and when", and `--pid`).

## Changes from draft 3

Draft 4 folds in form 4, the guard's second read, and lane 5's review of §3.6 (#165 6738431117), each item as lane 1's decisions take it, in one revision. Each row names lane 5's item, or the point it takes.

| Item | Change | Where |
|---|---|---|
| O17, form 4 | The guard's second read is form 4, `register read --for guard --at <n> --pid <pid> --cache <file> --lane=<key>`, at the head the guard prefetched, `n`, never in the mirror, refetching nothing (Y:4160–4161). Form 3 serves every other primitive on primary, at `n′` with the family refetch (Y:4900), and no longer runs under `--for guard`: draft 3's rows and sentences for form 3 under `--for guard` are superseded by form 4. O17 closes (#165 6727201960, 6727229519 and 6738431117) | §2.4, §3.6, §8 |
| B1 | Form 4 takes `--pid <pid>`, the hook's `$$`, from which the client reads `T0` | §3.6 |
| B2 | Form 4 takes `--cache <file>`, as every other form names its cache | §3.6 |
| B3 | Form 4 takes the generation from `.head`, and ends 69 where `.head` is missing or the database's generation differs | §2.7, §3.6 |
| B4 | Form 4's ends are lane 5's text, word for word: 0 with the one log's files written, and every other end, "too late" included, a log that could not be read, which the guard notes and does not refuse. Form 4 writes no `.outage`, never opens the mirror path and reads no declaration. Lane 3 ruled it so (O19) | §2.7, §3.6 |
| B5 | Form 4 has no declaration cap or floor: its cap is `T0` + 3.75 s | §2.4, §3.6 |
| B6 | Form 4's cost is one more `lanes-index` start plus `psql`'s start, connect and query: about 83 ms at a round trip of 0, and about 882 ms at 100 ms (Y:4365–4366), in place of "about 72 ms" | §3.6 |
| B7 | Form 4 always reads directly, never through the service, and spec:59 stands. Lane 3 ruled it so, with a `research.md` erratum on Y:4159 (O20) | §3.6 |
| B8 | Declined: form 4 writes no `.head`, which it reads for B3. Concurrent second reads must not share a writable file | §3.6 |
| C1 | The post-bind refill is form 1, `--files register,aliases`, with a reader's row, never `--for guard`. It is struck from form 2's "Called by" cell, and draft 3's refill bullet is replaced | §3.6 |
| C2 | No reader form returns an arbitrary render-set path. Lane 1 prefers lane 5's option (a) for T401, and O21 opens for lane 4 | §3.6, §8 |
| C3 | Any end of form 3 but 0 is the class's failed read, never files mode | §3.6 |
| C4, C5 | `--files` could not carry a key outside the class, and form 2 had no rule for a window name outside it. Both are settled by O18's `--log-key` and its rule for forms 1 and 2 | §3.6 |
| C6 | `reg_flush` also empties `LANES_REGISTER_CACHE`, and keeps `.direct` | §3.6 |
| C7 | `journal --last` keeps Y's flag name, `--grep <key>`, and matches as git's `-S` pickaxe does: a fixed string, never a regex. With no match it prints no `seq=` line | §3.6 |
| C8 | `journal --last` takes an optional `--at <n>`, the door's `n` | §3.6 |
| C9 | In `guard_run`, the only cache files read are `.register`, `.aliases` and `.log.<key>`; a miss on the log is form 4 | §3.6 |
| C10 | `.paths` holds the render set's paths present at `n` | §3.6 |
| C11 | Where two render-set logs differ from the key only by case, no form writes `.log.<key>`, and forms 3 and 4 exit 69 | §2.7, §3.6 |
| C12 | Draft 3's two-line quote of Y's "direct if the flag file exists" is cited as Y:4159–4160 | §3.6 |
| C13 | Under Q6(c)'s refusal, `render --checkout` and `register snapshot --files` exit 2 at load (Y:4872); `register snapshot --files`' exits are listed | §3.6 |
| C14 | No place counts fewer than four forms: §2.4's guard row is forms 2 and 4, and §3.6 and §6 count four | §2.4, §3.6, §6 |
| O14 | Exit 1 under `--for guard` is taken, with lane 5's text: it cannot be told apart from a crash's 1, and it means files mode for form 2 only; under form 4 it is a log that could not be read. Closed | §2.7, §3.6, §8 |
| O15 | H1 as taken, with lane 5's five changes: `w_s_ms=`, in whole milliseconds; `.outage`'s label on one line; form C on `origin/$LANES_BRANCH`, with `mirror_tip=`; `.head`'s `c` the larger of the two URLs'; S3's outage text named, and the cause on stderr's first line. Closed | §2.5, §3.6, §8 |
| O16 | `.head` carries `w_s`'s figure wherever `serve` exists, whether or not the service answered. Closed | §2.5, §8 |
| O18 | Draft 3's one shared slot for a key outside the class is gone. Such a key goes as `--log-key=<key>` to `.log.%<hex>` and `.logpath.%<hex>`; forms 1 and 2 skip it and refuse nothing; form 4's `--lane=<key>` takes any key. Closed | §3.6, §8 |
| O19, O20 | Opened for lane 3 by lane 1's decisions, and closed by lane 3's ruling (#165 6738474549): B4 word for word, and B7 with spec:59 standing | §8 |
| O21 | New, for lane 4: C2's (a) or (b), for T401 | §8 |
| L1-D1 | `--lane` and `--log-key` take the `=` form, so a key that opens with `-` is never read as a flag. The parser takes everything after the first `=` verbatim | §3.6 |
| Lane 1's addendum | Form 4 takes no `--repo`, since it runs no form C. §2.7's 78 row names the forms it means. The exits table's first row says which head each form reads. Form 4 ends 69 for an `--at <n>` above the head, and as form 2 does in a mode that is not primary | §2.7, §3.6 |
| The record | The status, the key's note, the inputs and the marks | header, §0 |

Unchanged: R09's TLS refusal and its exits, 2 as a refused configuration, 3 on `journal --write-id` and 1 under `--for guard`. Group B's sections, §3.1 to §3.5, are unchanged, but for two citation-only edits: §3.2's Y:3522 and §3.3's Y:1559 now name the header line of the column they cite, Y:3516 and Y:1553.

## Changes from draft 2

Each change is keyed to the ruling or the point it takes.

| Change | Where | Ruling or point |
|---|---|---|
| Keyed to Y, X's child, in place of W. Every citation is a `Y:` or `Y-ops:` line, and every quote is Y's text. Of the lines draft 2 quoted, X changed six from W, and Y keeps X's text in each: the lookup's wait (Y:1468) and the in-doubt row (Y:1569), which now carry `T_L` = `N_a`·`c` + 7 s, 9 s at `c` = 2, D9, for W's "8 s timer"; the argv rule (Y:3491); the lookup's D9 row (Y:3524), where a failover became a restart; one engineer's file (Y:2326); and `c` and `N_a` (Y:5877). Each quote still opens its line, and those that X reworded are re-cut from Y. The pooler is Y:1575's "never through a pooler" (C13). §9 records what X, and so Y, changed from W | everywhere; §0, §9 | the re-key; lane 3, #165 6727039049 and 6727022920 |
| CO-2 and CO-6 are lane 1's contract decisions, marked [D]. Draft 1 is T101's basis, and O1 closes | §2.2, §2.7, §3.3, §3.4, §8 | R01 |
| LR004's reload and retry is ruled as lane 1's form, with CO-4, and O2 closes | §3.3, §8 | R02 |
| `register classify` is contract text: lane 1's T122, `[O]`, in group A, with a parity test against `parse_register_line`. Lane 4's T424 lands after T122. A refused configuration there is 2, as on every command that reads one | §3.5, §7, §8 | R04 |
| Where `c`, `N_a` and `w_s` are read, by key, and which command prints each. `w_s` is new, in `register read`'s `.head`. Y:5877's "from the configuration" (R05's X:5876) is a `research.md` erratum. O9 closes, with lane 5's `bound_s` | §2.4, §2.5, §3.2, §3.6, §8 | R05 |
| `journal --since <n>` does not serve the second read, so `register read --since <n>` joins §3.6. §3.6 is written in full: `register read` in three forms, `--for guard` among them, the cache family, `.head` and `.outage`, `journal --since` and `--last`, `render`, and `register snapshot --files`. O3 closes there | §2.4, §2.7, §3.4, §3.6, §3.7, §6, §8 | R06, with R07 |
| O4, O5, O7, O8 and O10 are ratified | §8 | R08 |
| `load_config` refuses a `register_url=` to a TCP host that is not loopback, and a `reader_url=` there only where `register_url=` is also set, unless the URL carries `sslmode=verify-full` and a root file: 2, as a refused configuration; 3 on `journal --write-id`, under CO-3; 1 under `register read --for guard`. A #160 file, with no `register_url=`, loads as today (T104's case 6) | §2.5, §2.7, §3.4, §3.6 | R09 |
| The mode is asked once per run, and carried across 1a's `exec` as `LANES__MODE` with the `LANES__PID` check. The main shell's variable is `REG_MODE`. No flag is added | §3.1 | R17 and R45, as context |
| O11 is settled: `lanes_index_bin`, `$SCRIPT_DIR/lanes-index` first, then `PATH` | §2.1, §6, §8 | lane 5's acceptance (Q-L5-2, #165 6726077268); lane 4's block :91 |
| A takeover with no snapshot of its own waits on the record's clock, and a record from another host calls at once. The lookup's tries are counted start to start, at least 1 s apart and never before the last ends, in a closed 10 s window | §3.4 | Q-L1-1 (lane 4's 36 and 40; R26) |
| The flush helper is `reg_flush` | §3.6 | lane 5's Q-L5-4 answer |
| Four points for lane 5's review of §3.6: the guard's code for a refused configuration (O14), `w_s` where the service is installed (O16), the guard's second read at `n′` (O17), and a log key outside `check_lane_name`'s class (O18) | §8 | R06 |

## Changes from draft 1, made in draft 2

Each change takes one of lane 4's points (#165 6725230285). Draft 3 keeps each, re-keyed to Y; §8 says where a ruling has since closed one.

| Change | Where | Point |
|---|---|---|
| `register commit` reads only the names §3.3 lists and ignores every other name. "Malformed" means an inconsistency among the listed files, not an extra file | §3.3 | CO-1 |
| A crash of `lanes-index`, or a code outside the contract's set, on `journal --write-id` is 3, the record kept, as on `register commit` | §2.7, §3.4 | CO-2 |
| `journal --write-id` exits 2 only for LR007, plus the one 2 that Y fixes for every command, Q6(c)'s refusal. Any other refused configuration there is 3 | §2.7, §3.4 | CO-3, accepted except where Y:2452 fixes the 2 |
| LR004's retry: the second commit call is read by §3.3's table in full, CO-6 included | §3.3 | CO-4 |
| §2.7's 69 row says that on `register commit` an unreachable database is 3, and 64 is raised before any connection is opened | §2.7 | CO-5 |
| On `register commit`, 2 and 69 come only from a refusal identified by its SQLSTATE under `psql`'s exit 3, or from an error raised before anything was sent. Every other end is 3: `psql`'s exit 1 or 2, a signal, the timer `C`, or an exit 0 whose answer cannot be read. A new row in §3.3's table, and the fake `psql` injects exit 1, a signal and an unreadable exit 0 | §2.2, §2.7, §3.3, §5, §6 | CO-6 |
| The attempt directory is cited as lane 4's `<state>/register-writes/<d>/<run>/<k>/`, and `register commit` reads no id from its path | §3.3 | lane 4's note on the directory's name |
| Under `--at`, `--generation` alone is a form, and so are all five identity flags, of which only `--generation` is checked. `--at` without `--generation` is 64 | §3.2 | lane 4's note on `--at` |
| How `lanes-edit.sh` finds `lanes-index`: recorded as lane 5's to fix, with lane 4's proposal, since the stub's place rests on it | §2.1, §8 O11 | lane 4's M9 |
| `changes.tsv`'s ids are 40 hex, which needs `--no-abbrev` | §3.3 | lane 4's item 6 |
| §8 says which point closed which open item | §8 | — |

Not taken: one part of CO-3. Q6(c)'s refusal of a `mode=primary` configuration stays 2 on `journal --write-id`. Y:2452 "every other `lanes-index` command refuses it with 2, as the configuration" fixes it for every command while the refusal is built in. Nothing is lost: while it is built in, no primary write exists, so no lookup is owed. Lane 4 treats any 2 without `ERROR:  LR007:` as 3 (its T417), so the outcome agrees.

---

## 1. Scope

| Command | Called by | This contract |
|---|---|---|
| `lanes-index mode` | the writer's entry (A's phase-1 arm; group B consumes its answer) | §3.1 |
| `lanes-index register snapshot` | each attempt; `act-begin`; the replay | §3.2 |
| `lanes-index register commit` | each attempt's commit call | §3.3 |
| `lanes-index journal --write-id …` | the in-doubt lookup | §3.4 |
| `lanes-index register classify` | `append-line`'s refusals on primary | §3.5, lane 1's T122 (R04) |
| `lanes-index register read`, `journal --since` and `--last`, `render --images`, `register snapshot --files` | the readers, the guard, the renderer and the image cache, not group B | §3.6 (R06) |
| `lanes-index render --checkout <c>` | the parent's fork after COMMIT | §3.7 for the fork, §3.6 for its answer; group C makes it real |

**What is fixed by Y.** The interfaces are fixed by Y's interfaces table: Y:4831 "| `register snapshot` |", Y:4832 "| `register commit` |", Y:4833 "| `register read` |" and Y:4834 "| `journal` |". Y:4841 "- **Phase 1 to phase 2:** phase 2 changes nothing in `lanes-index` if phase 1 ships the four `register` and `journal` contracts complete".

**What Y leaves open, and this contract fixes:** the wire format: the argv, stdout, stderr and the attempt directory's files, and, for the readers, the cache family's files (§3.6). Lane 4's draft names the same gap ("The design fixes what each interface returns and its exit codes, but not its wire format").

**Out of scope here:**
- the pending record, the claim files and the done mark: lane 4's, Y:5171 "- **Where:** `<state>/register-pending/<d>`" (`contracts/pending-record.md`, lane 4's in T412, R34);
- the exit codes of `lanes-edit.sh` itself (`contracts/exit-codes.md`);
- the configuration file's whole key list (`contracts/config.md`);
- the image cache's internal API (T302, lane 3's U8): this contract fixes only what `register snapshot --tree` puts in the tree.

---

## 2. Rules for every register command

### 2.1 One database client [Y]

- `lanes-index` is the one database client, and `lanes-edit.sh` runs no `psql`: Y:1415 "Under (A″) each attempt is two `psql` calls, made by `lanes-index register snapshot` and `lanes-index register commit` (D2: `lanes-index` is the one database client".
- No command holds a session across calls: Y:2506 "An attempt is two short `psql` calls with the verb between them; no session".
- **[B; settled, O11] Finding `lanes-index`.** Y fixes where `lanes-edit.sh`'s register door sits, Y:4859 "\| `lanes-edit.sh` \| beside `index_open`", but not how it finds `lanes-index`.
  - **One resolver,** `lanes_index_bin`, resolves every `lanes-index` call in `lanes-edit.sh`, `reg_open`'s and the guard's prefetch included: `$SCRIPT_DIR/lanes-index` first, then `PATH`, as `index_open` does (`lanes-edit.sh:3750–3752` at `34799b74`, lane 4's citation).
  - **Lane 4 defines it** (its block, `lane4-block-T4xx.md:91`: "`lanes_index_bin` resolves every `lanes-index` call in `lanes-edit.sh`: `$SCRIPT_DIR/lanes-index` first, then `PATH`").
  - **Lane 5 accepted it** (Q-L5-2, #165 6726077268; its block :383–386). T409 defines it, and T504 lands after T409, or defines it where lane 3 orders T504 first.
  - The stub of §6 goes where it looks first.

### 2.2 The `psql` seam

The fake `psql` stands in at this seam.

- **[P, PC-11] Which `psql`:** `lanes-index` runs the first `psql` on `PATH`, as #160 does. A test injects a fake by putting it first on `PATH`: Y:6113 "- **Fake-psql (FP):** a logging fake first on `PATH`".
- **[Y, with P for the exact list] The argv** carries no connection string and no `-d`. Y:3491 "No `lanes-index` path puts a connection string in an argument list, where `/proc` shows it" and Y:4970 "- **The change:** drop `"-d", self.url`". The argv is exactly:
  `psql --no-psqlrc -X -q -A -t -v ON_ERROR_STOP=1 -v VERBOSITY=verbose -v SHOW_CONTEXT=never`
  - `--no-psqlrc -X -q -v ON_ERROR_STOP=1` is #160's `_run`.
  - `VERBOSITY=verbose` and `SHOW_CONTEXT=never` are Y:2590 "It runs with `VERBOSITY=verbose` and `SHOW_CONTEXT=never`".
  - `-A -t` is [P]: a `SELECT`'s value prints bare, while `COPY` output is unchanged.
- **[Y] The environment.**
  - The URL is mapped to `PGHOST`, `PGPORT`, `PGDATABASE` and `PGUSER`.
  - Its query parameters `sslmode`, `sslrootcert`, `sslcert`, `sslkey`, `target_session_attrs` and `connect_timeout` go to `PGSSLMODE`, `PGSSLROOTCERT`, `PGSSLCERT`, `PGSSLKEY`, `PGTARGETSESSIONATTRS` and `PGCONNECT_TIMEOUT`.
  - Sources: Y:3806 "`PGPORT`, database to `PGDATABASE`, user to `PGUSER`" and Y:4970 "- **The change:** drop".
  - `PGPASSFILE` is the configured password file, and an inherited `PGPASSWORD`, `PGPASSFILE`, `PGSERVICEFILE` or `PGSYSCONFDIR` is dropped (Y:4970).
  - `PGOPTIONS` carries the call's bounds, `-c lock_timeout=<ms> -c statement_timeout=<ms>`, as Y:3503 has it: "Every connection names the primary's own endpoint … and sets its bounds when it connects (`options=-c lock_timeout=… -c statement_timeout=…`; the probes found that form and `PGOPTIONS` both work)".
  - `PGAPPNAME` is `lanes-index`.
- **[Y] stdin** is the one request (§2.3). It is never a file name or `-c`. The entry can be large, and Y:1419 says "read from stdin".
- **[Y] What `psql`'s exits mean to `lanes-index`:**
  - 0: done;
  - 1: `psql`'s own failure;
  - 2: the connection was lost or never made;
  - 3: a statement failed under `ON_ERROR_STOP`.
  - Y:2618 "A `psql` exit 3 is never in doubt", and Y:2620 "transaction back. Every exit 2 of a commit call is treated as in doubt," (Y:2621 "connection failures included").
  - **[D, R01] On `register commit`, every end but 0 and an exit 3 with a SQLSTATE is in doubt** (CO-6, §3.3; lane 1's contract decision). That covers exit 1, exit 2, death by a signal, the timer, and an exit 0 whose answer `lanes-index` cannot read. Y gives the reason for exit 2 at Y:2621, "connection failures included, since telling them apart would mean matching", and it holds for the others once the request was sent.
- **[Y] A fake `psql`** asserts argv, environment, credentials and the SQL's shape, and nothing an engine decides (Y:6113).

### 2.3 One request per call [Y]

Each call sends all its statements as one simple-protocol Query, read from stdin, so a call costs the connect plus one round trip.
- **Sources:**
  - Y:5876 "- **One request per call.** Phase 1's `register` subcommands send each call's statements as one request";
  - Y:1417 "1. **The snapshot read:** one `BEGIN ISOLATION LEVEL REPEATABLE READ READ ONLY` transaction, as #160 reads (`code/lanes-index:1331-1344`), sent as one request";
  - Y:1419 "3. **The commit:** `BEGIN ISOLATION LEVEL SERIALIZABLE; SELECT lanes_register.commit_write(", "…, sent as one request".
- **The snapshot:** #160's statements, joined by psql's `\;`, with #160's `\echo` marker sent as `COPY (SELECT E'\x1eLANES-INDEX-NEXT') TO STDOUT`. This needs `psql` 15 or later, and Y-ops:445, `ready`'s check 2, "2. `psql` 16 is present;", requires `psql` 16. An older `psql` reads the parts as one `COPY` of tagged rows (Y:1417).
- **The commit:** `BEGIN ISOLATION LEVEL SERIALIZABLE \; SELECT lanes_register.commit_write(<n>, '<write_id>', <REGISTER_SCHEMA_VERSION>, '<generation>', '<entry>'::jsonb) \; COMMIT;`.
- **The lookup:** `BEGIN \; SELECT lanes_register.find_write('<id>', '<not_after>', '<generation>', '<lane>', '<verb>', '<args_sha256>') \; ROLLBACK;`.
- **The bound in a joined request:** since PostgreSQL 13, `statement_timeout` applies to each statement of such a request (Y:5876). In the commit and the lookup only one statement does work, so the row's bound is the call's. In the snapshot each part is bounded, and the client timer bounds the whole.
- **[P] Two exceptions,** each a second request made only on a rare path, and each named where it occurs:
  - LR006's file rows (§3.3);
  - D18's or D19's holder line (§3.3).

### 2.4 Bounds and client timers [Y]

**What `c` and `N_a` are.** Each workstation's `c` is `max(2, ⌈t_c + 1 s⌉)`, where `t_c` is the p90 of its cold connects, measured as it joins. Its `N_a` is the number of addresses the endpoint resolves to. Each row's client timer is `N_a`·`c` + its statement bound + 1 s, never below the row's figure. Where each is read, and which command prints it, is §2.5's (R05).
- Y:3503 "Every connection names the primary's own endpoint … A row's client timer is `N_a`·`c`, plus its statement bound, plus 1 s".
- Y:5877 "- **`c` and `N_a`, the connect bound.**"

| Call | `connect_timeout` | `lock_timeout` | `statement_timeout` | The client timer, on the whole `psql` | Y |
|---|---|---|---|---|---|
| `register snapshot`, and `act-begin`'s | `c` | 2000 ms | 5000 ms | `S` = `N_a`·`c` + 6 s: 8 s at `c` = 2 | Y:3522 "\| a writer's snapshot, and `act-begin`'s" |
| `register commit` | `c` | 2000 ms | 5000 ms | `C` = `N_a`·`c` + 6 s: 8 s at `c` = 2 | Y:3523 "\| a writer's commit \|" |
| `journal --write-id` (the lookup), each try | `c` | 2000 ms | 6000 ms: its wait of at most 3 s, then the lock, then 1 s | `T_L` = `N_a`·`c` + 7 s: 9 s at `c` = 2 | Y:3524 "\| the in-doubt lookup, `find_write(<id>" |
| `lanes-index mode` | — | — | — | 2 s, the caller's | Y:3521 "\| the mode query, `lanes-index mode`" |
| a whole write | — | — | — | D7's 30 s before a new attempt; 10 s to reconnect in doubt; `T_write` = 62.5 + 3`N_a`·`c` s | Y:3525 "\| a whole write \|" |
| `register read`, forms 1 and 3; `journal --last` (§3.6) | `c` | 1000 ms | 3000 ms | `T_r` = `N_a`·`c` + 4 s: 6 s at `c` = 2 | Y:3526 "\| a reader \|" |
| `register read --for guard`, forms 2 and 4 (§3.6) | `c` | 1000 ms | 1500 ms | cut to `T0` + 3.75 s; in form 2, `T0` + 3.65 s where an unexpired declaration names this database. Form 4 reads no declaration, so its cut is always `T0` + 3.75 s (B5) | Y:3518 "\| the guard's direct read \|" |
| `journal --since`, `render` (§3.6) | `c` | 1000 ms | 10 s | the larger of 15 s and `N_a`·`c` + 11 s | Y:3527 "\| the renderer's reads, and `reconcile` \|" |

- **[Y] The timer runs inside `lanes-index`.** It is `subprocess.run`'s `timeout` on the whole `psql`, which holds the connect: Y:5002 "- A child normally waits for its own commit call: the call's client timer, `C`, `N_a`·`c` + 6 s with the connect inside it … `code/lanes-index:1314–1317`". `lanes-edit.sh` sets no shorter timer around a register call.
- **[Y] What an expired timer means:**
  - on the snapshot, a failed read, 69;
  - on the commit, in doubt, 3;
  - on a lookup try, a try with no answer, which `journal` makes again (§3.4).
  - Y:1569 "\| `psql` exit 2, or 124 from the client timer, on the commit call \| the outcome is unknown".

### 2.5 The configuration each call reads

All the keys come from `lanes-index.conf`: Y:2323 "- **The file:** `${XDG_CONFIG_HOME:-$HOME/.config}/openRepoTools/lanes-index.conf`, or the file `$LANES_INDEX_CONFIG` names". `lanes-index` stays the one parser, and `lanes-edit.sh` reads no key itself.

| Key | Read by | Y |
|---|---|---|
| `mode=` | `lanes-index mode`, and every register command, to refuse Q6(c) (§3.1) | Y:2421 "\| `mode=`, `register_url=`, `register_passfile=` \|" |
| `register_url=`, carrying `connect_timeout=<c>` among its query parameters; its user is `lanes_w_<engineer>`, and its host the install's DNS name | `register snapshot`, `register commit`, `journal --write-id`; and, as the renderer's, `journal --since` and `render` (§3.6) | Y:3459 "\| REGISTER-WRITER \| `register_url=`, `register_passfile=` \|"; Y:5877 "`c` as the URLs' `connect_timeout=` (SVC-2), default 2"; Y:2326 "Each workstation's `lanes-index.conf` names its own engineer's logins, each as the user of its URL" |
| `register_passfile=`, a 0600 file holding only that login's line | the same | Y:3459; Y:2326 |
| `register_addresses=`, that is `N_a`, default 1, measured at act 3 | every register command, for its timers | Y:5877 "`N_a` as `register_addresses=` in `lanes-index.conf`, default 1"; Y:5912 "- the endpoint's addresses, `getent ahosts <host>`: their count is `N_a`;" |
| `register_schema=`, default `lanes_register` | every register command | Y:2323 "R3a's `register_schema=` (default `lanes_register`) names a rehearsal's scratch schema" |
| `reader_url=`, `reader_passfile=`, with the user `lanes_r_<engineer>` | `register read` and `journal --last` (§3.6); never `register classify` | Y:3460 "\| READER \| `reader_url=`, `reader_passfile=` \|" |
| `register_rtt_ms=` | `register read --prefetch`, for `w_s` (below); and the service's timer | Y:4001 "- **The per-request timer** is 200 ms, or, where the workstation's round" |
| `register_label=` | not these commands: `cutover`'s and the service's | Y:2422 |

- **[Y] `connect_timeout` is `c`, and it is set as `PGCONNECT_TIMEOUT`:** Y:5877 "`PGCONNECT_TIMEOUT` is set to `c`". With none, `c` is 2.
- **[P] A value under 2 is refused** by every register command, as the service refuses it: Y:3810 "`PGCONNECT_TIMEOUT`. `connect_timeout` is the workstation's `c` (D9); a" and Y:3811 "value under 2 is refused, since libpq reads 1 as 2 and 0 as no limit". It is refused with 2, as a refused configuration.
- **[Y] Any query parameter outside the six** of §2.2 is refused (Y:4970: "`load_config` refuses any other query parameter").
- **[Y] The file's own refusals** are 2: a file that group or others can read, one inside a git work tree or under `~/.agents/`, or a URL carrying a password (Y:2323).
- **[D, R09] A TCP connection that is not loopback must verify the server.** `load_config` refuses, with 2 as a refused configuration, a `register_url=` that names a TCP host other than a loopback one, unless the URL carries `sslmode=verify-full` and an `sslrootcert=` that names a file, never `system`. Where `register_url=` is set, it refuses `reader_url=` on the same terms.
  - **Its scope:** `reader_url=` is checked only where `register_url=` is also set. A file with no `register_url=` is #160's, and it loads as today (US1): #160's tests configure `reader_url=postgresql://lanes_reader@db.invalid/qa`, with no TLS. Lane 1's block v3 scopes T104 so (T104's "Its scope" bullet), and T104's `tls_at_load` case 6 tests it: "a #160 file with no `register_url=`, whose `reader_url=` names a TCP host with no TLS: it loads as today".
  - **Loopback** is `localhost`, an address in `127.0.0.0/8`, or `::1`. It stays allowed, and so does a Unix socket's directory as the host, so the fixture's 127.0.0.1 stand-ins keep working. Where a URL names several hosts, each must pass.
  - **The file** must exist and be a regular file when `load_config` reads the configuration.
  - **Whose:** lane 1's added decision for T104 (R09). FR-011 itself is enforced at `ready`'s check 11 and by the server's `hostnossl` reject: Y-ops:454 "11. each role's connection is encrypted and reaches the primary" and Y-ops:144 "- `postgresql.pg_hba`, ahead of CNPG's default". The URL's form is Y's: Y:2324 "carries `sslmode=verify-full` and `sslrootcert=`, since libpq's default, `prefer`, verifies nothing".
  - **Where it is not 2:** on `journal --write-id` it is 3, as every refused configuration there is (CO-3, §3.4); under `register read --for guard` it is 1, since 2 there means "too late" (§3.6, O14).

**[R05] Where `c`, `N_a` and `w_s` are read, and which command prints each.** They come from `lanes-index`, and `lanes-edit.sh` parses no configuration (R05).

| Value | The key it is read from, in `lanes-index.conf` | Printed by |
|---|---|---|
| `c` | the `connect_timeout=` query parameter of the URL the call connects with: `register_url=` for the snapshot, the commit, `journal --write-id`, `journal --since` and `render`; `reader_url=` for `register read` and `journal --last`. Default 2; under 2 refused (above). Y:5877 "`c` as the URLs' `connect_timeout=` (SVC-2), default 2" | `register snapshot`'s `c=`, its URL's (§3.2); `register read --prefetch`'s `.head`, the larger of the two URLs' values [O15], since `bound_s` sums both kinds of call (§3.6) |
| `N_a` | `register_addresses=`, default 1 | `register snapshot`'s `n_a=`; `.head`'s `n_a=` |
| `w_s` | none of its own: computed from `register_rtt_ms=` | `.head`'s `w_s_ms=`, in whole milliseconds (O15) |

- **`w_s` is the client's wait for the service:** Y:5457 "`w_s` is the client's wait for the service (SVC-8): where the service runs, 0.3 s, or the service's timer plus 100 ms where SVC-6 raises that timer".
  - The service's timer is 200 ms, or `register_rtt_ms=` plus 100 ms where that is over 100 (Y:4001). So `w_s` is 0.3 s, or `register_rtt_ms=` + 200 ms where `register_rtt_ms=` is over 100: 0.656 s at Eagle's 456 ms (Y:5459). `.head` prints it as `w_s_ms=`, in whole milliseconds: 300, or 656 at that 456 ms (O15).
  - It is 0 where no service can answer: in `mode index`, and where this `lanes-index` has no `serve`, since phase 1's `register read` has only its direct path (Y:4868).
  - [O16] Where it has `serve`, on primary, `.head` carries the figure whether or not the service answered this read, since the bind that reads it may meet a service started since. `bound_s` is an upper bound, and its rounding to 10 s absorbs the difference at `c` ≤ 3: Y:5459 "`guard-bind` computes `bound_s` from the constants its steps use" gives "500 s at `c` = 2 and 530 s at `c` = 3, with or without the service" (O16, closed by lane 5's review, #165 6738431117; Y:5443).
- **Lane 5's `bound_s`, settled** (O9). The guard copies `.head`'s `c`, `n_a` and `w_s_ms` into the bind record, and `guard-bind` sums `bound_s` from the record, with the defaults `c` = 2, `N_a` = 1 and `w_s` = 0 where they are absent. That is lane 5's T507 and its open point 3 (its block :573–576), as R05 rules. Draft 2's note that lane 5 still owed `bound_s` is closed.
- **The erratum.** Y:5877's "and so do `T_write` and `bound_s`, which `guard-bind` computes from the configuration" is a `research.md` erratum (R05, which cites it as X:5876): `guard-bind` reads no configuration, and computes `bound_s` from the values the guard's read reported.

### 2.6 How a code and its DETAIL token reach the caller

**[Y] The codes come from `psql`'s first stderr line.** Y:2590 "It runs with `VERBOSITY=verbose` and `SHOW_CONTEXT=never` … so `psql`'s first line on stderr is `ERROR:  <SQLSTATE>: <message>`. Every code is read from that one line". Y:1571 "The writer reads only the SQLSTATE from psql's `ERROR:` line (step 3), never the constraint's name".

**[Y] The DETAIL token is one fixed word,** and a caller that branches reads the token, never the message:
- LR005's tokens are `one-live-claim`, `claim-spellings`, `main-held`, `expired-none`, `expired-closed`, `expired-young` and `take-no-utc`. Y:1559 "\| LR005 \| a rule of the register: … the client tells the rules apart by the DETAIL token alone".
- LR007's tokens are `generation`, `another-write` and `act-generation`. Y:1566 "\| LR007 \| the write id belongs to another write: … The client tells them apart by that token alone".

**[P] What `lanes-index` writes to its own stderr,** which is what group B reads:
1. **Line 1,** when the database refused: `psql`'s `ERROR:  <SQLSTATE>: <message>` line, verbatim.
2. **Line 2,** when the error has one: `psql`'s `DETAIL:  <token>` line, verbatim. The `LOCATION:` line `psql` prints under `VERBOSITY=verbose` is dropped.
3. **Then** any line of `lanes-index`'s own, prefixed `lanes-index: `.

**[B′] The same shape for `register snapshot`'s two refusals,** which are client checks rather than server errors (§3.2): `ERROR:  LR007: <message>`, then `DETAIL:  generation` or `DETAIL:  another-write`.
- This changes lane 4's `LR007 <token>: …` for the snapshot (I-3), so that group B parses one shape everywhere. Lane 4 to confirm.

**[Y] Group B decides by the exit code first.** It reads the stderr lines only where it must tell refusals apart within one code:
- which rule under 2;
- LR007's `generation` or `another-write` at the lookup, which decides whether the pending record is kept (Y:5241 "- **LR007, raised by `find_write`:**").

**[Y] Nothing secret reaches stderr.** No URL and no password appears: Y:2323 "No URL or password enters an environment a session inherits", and §2.2's argv rule. A message may quote a register line, as LR005's do.

### 2.7 The exit codes of the register commands [Y]

| Code | Means | Where Y fixes it |
|---|---|---|
| 0 | done: committed, already committed, read, or found or absent; from `register read`, also `mode index` | Y:2601 "\| committed at `n+1` \| 0, `committed <seq>`"; Y:2605 "\| LR006: this write is already in the journal"; Y:4345 "\| mode `index` \|" |
| 2 | a refusal, with nothing written: the register's rules (LR005, LR007, LR009, LR010), the snapshot's generation and write-id checks, a refused configuration (R09's TLS refusal included), and Q6(c) while it is built in, on every command but `mode` and `register read`. On `journal --write-id`, only LR007 and Q6(c)'s refusal (CO-3). On `register read --for guard`, only "too late to verify" (§3.6), which form 2 refuses on and form 4 notes as a log that could not be read (B4; lane 3, #165 6738474549) | Y:2604 "\| LR005, a rule of the register"; Y:2606 "\| LR007: the id belongs to another write"; Y:2452 "every other `lanes-index` command refuses it with 2"; Y:4344 "\| a read completed after the parse's start line" |
| 3 | in doubt, from two commands only. **`register commit`:** every end of its `psql` but 0 and an exit 3 with a SQLSTATE: exit 1, exit 2, a signal, the timer `C`, or an exit 0 whose answer cannot be read (CO-6, [D]); the same for its LR004 retry's call (CO-4). **`journal --write-id`:** LR011; no try took the lock; any other SQLSTATE; a refused configuration other than Q6(c)'s, R09's included (CO-3); a crash or an unknown code (CO-2, [D]) | Y:2615 "\| `psql` exit 2, or the call's client timer expired \| 3"; Y:5245 "- **no try that starts within the window" |
| 69 | **On a read** (`register snapshot`; `register read` in each form; `journal --since` and `--last`; `render --images`): the database is unreachable, or refused in any other way: LR002, LR003, any other SQLSTATE, a failed read; under form 4, also a missing `.head`, a generation that differs, an `--at <n>` above the head (B3), or an `--at <n>` below the generation's first seq, and under forms 3 and 4 two logs that differ from the key only by case (C11). **On `register commit`:** only a refusal identified by its SQLSTATE under `psql`'s exit 3 (LR002, LR003, LR004 after its retry, any other SQLSTATE), or an error raised before anything was sent (a `psql` that cannot be started or is too old). There an unreachable database is 3, never 69 (CO-5, CO-6) | Y:2603 "\| `psql` exit 3 for any other SQLSTATE"; Y:2612–2614 |
| 75 | a lost turn, from `register commit` only: LR001, 40001, 40P01, 55P03, LR008 | Y:2602 "\| the head moved (LR001), 40001, 40P01, or 55P03"; Y:2607 "\| LR008: the attempt reached step 9" |
| 78 | `lanes-index mode`, and `register read` in all four of §3.6's forms, for Q6(c) while it is built in. Y's "both its forms" are the reader's, §3.6's forms 1 and 3, and `--for guard`'s, forms 2 and 4. Under form 4, a 78 is a log that could not be read (B4) | Y:4830 "\| `lanes-index mode` \| 0 with `index` or `primary`; 78 for Q6(c)"; Y:2449 "writes nothing (agreement test 14). `register read`, in both its forms," |
| 64 | usage, raised before any connection is opened, so nothing was sent. Group B never sends a usage error by design, and reads a 64 from the commit as an internal error (CO-5). For the record, an empty `--lane=` in `register read`'s form 2 or form 4 is usage (§3.6) | `lanes-index`'s own (Y-ops:402's exits) |
| 1 | `register read --for guard` only: a refused configuration, R09's included. It cannot be told apart from a crash's 1, and the guard does not branch on it: it reads 1 as any code outside 0, 2, 69 and 78, as files mode with a warning (Y:4347). Files mode applies to form 2, the prefetch, only. Under form 4, a 1 is a log that could not be read (B4) [O14, closed] | Y:4347 "\| any other failure of the mode question or the prefetch \|" |

**No other code.** A crash of `lanes-index` (1, or 128+n), or any code outside this table, is not a contract code. Its caller treats it as:
- on `register commit` and on `journal --write-id`, in doubt, 3, with the pending record kept (O8, CO-2);
- under `register read --for guard`: from form 2, as files mode with a warning, as Y:4347 has it for any other code; from form 4, as a log that could not be read (B4);
- elsewhere, as 69 for a failed read.

**[D, R01] This is lane 1's contract decision,** with CO-6's reading of the commit. Y:2615's in-doubt row covers only `psql`'s exit 2 and the timer, and lane 3 sees no frame obstacle to CO-2 or CO-6 (R01). Lane 4 confirmed it for the commit, and asked for the lookup (CO-2).

### 2.8 Versioning [Y]

- **One constant.** `lanes-index` carries `REGISTER_SCHEMA_VERSION`, and a hygiene test asserts that every copy in a release is one number: Y:2332 "- **The row and the constant.** `schema_version` holds `version`, the last migration applied, and two floors".
- **`lanes-edit.sh` carries none,** since it does not open the register.
- **A window, not equality.** A writer goes on only where `writers_from` ≤ `REGISTER_SCHEMA_VERSION` ≤ `version`:
  - Y:2333 "- **How each client checks it:**";
  - in `commit_write`'s step 2: Y:1419's call passes it, and the step raises LR003 when `p_client > v_ver.version OR p_client < v_ver.writers_from` (Y:1423–1449, the SQL block after Y:1421's "Steps 1 to 5").
- **By default the window is an exact match:** Y:2343 "- **The floors.** By default a migration sets both to its own number: R3a's exact match". A migration may keep the floors and say so.
- **Outside the window, 69,** with a refusal that names both numbers and the remedy: Y:2338 "- **Too old** (below the floor) **or too new** (above `version`): nothing is read or written … 69 for writers"; Y:2613 "\| LR003: the client is outside the schema's window \| 69, naming both numbers".
- **Both calls check it:** `register snapshot` in its read, and `register commit` in `commit_write`'s step 2 (Y:2333). A migration between them moves the head row, so the commit gets 40001 and the next snapshot refuses with 69.
- **[P] The format of the outputs.** Each key=value output (§3.2) opens with `format=1`. A group B that meets another value treats the call as a failed read, 69. This guards only a broken install, since both tools ship in one release.

---

## 3. The commands

### 3.1 `lanes-index mode` [Y]

- **argv:** `lanes-index mode`; no stdin.
- **stdout:** one line, `index` or `primary`. Exit 0.
- **The other exits:**
  - 78, `EX_CONFIG`, with Q6(c)'s fixed text, for a `mode=primary` configuration while Q6(c)'s refusal is built in;
  - 2 for a configuration it refuses.
  - Source: Y:4830 "\| `lanes-index mode` \| 0 with `index` or `primary`; 78 for Q6(c); 2 for a refused configuration"; Y:2441 "Every writer passes the workstation guard".
- **It reads only the configuration:** Y:3521 "\| the mode query, `lanes-index mode`, which reads only the configuration (D3)".
- **The caller's timer is 2 s.** On any failure the verb runs in files mode with a warning, and on 78 the writer exits 69 (Y:2441).
- **[Y] Asked only where a configuration exists,** at `lanes-index`'s path or `$LANES_INDEX_CONFIG` (Y:2441).
- **[R17, R45; context only] One answer per run, and no flag.**
  - **A writer** asks the mode once, before 1a's routing, and carries the answer across 1a's `exec` as `LANES__MODE`, with the `LANES__PID` check (R17, lane 4's).
  - **In the main shell** the answer lives in one variable, `REG_MODE`, set by assignment from the one answer and never read from the environment. Whichever of T409 and T504 lands first defines it (R45, lane 5's).
  - **A reader's and the guard's answer** comes from `register read --prefetch` (§3.6), which answers the mode in the same call: Y:4890 "On a configured workstation that one Python start also answers the mode". There is no second mode query (Y:5121 "There is no second mode query").
  - This contract adds no flag for it.
- **[Y] The stub for group B's tests** is a `lanes-index` that answers `primary`. Real-engine tests run a copy with the constant flipped (FR-017; lane 4's I-12, citing Y:4872–4876).

### 3.2 `lanes-index register snapshot`

**[Y] What it does.** One REPEATABLE READ READ ONLY read, as one request (§2.3). It returns the switch with its marker state and `generation`, the head `n`, the database clock in whole seconds, and the write id's entry where found. It also builds the scratch tree.
- Y:4831 "\| `register snapshot` \| the switch with its marker state and `generation`";
- Y:2509 "1. **The snapshot.** `lanes-index register snapshot`, one `psql` call at";
- Y:4985 "1. **The snapshot and the scratch tree.** `lanes-index register snapshot --tree <dir>` returns the snapshot".

**argv** [B′; lane 4's I-3, Q1]:

```text
lanes-index register snapshot
    [--write-id <uuid> --generation <uuid> --lane <lane> --verb <verb> --args-sha256 <hex>]
    [--write-id new --lane <lane> --verb <verb> --args-sha256 <hex>]
    [--tree <dir> --checkout <lane checkout>]
    [--at <k>]
```

- **The write's identity:**
  - **Inside a write,** with an id known (from `LANES_WRITE_ID`, or from the pending record), all five flags. Then the snapshot checks the id ([Y], Y:4986–4987).
  - **For a new write,** `--write-id new` with the other three. Then it mints the id (§4), checks nothing against the journal, and prints it.
    - [P]: Y:2468 "Each write carries a `write_id`, …" says "it is a new random 128-bit id that `lanes-index` mints", and names no command. The first snapshot is where the generation is first known: Y:5192 "- **Written** after the first snapshot, which supplies the `generation`".
    - This changes lane 4's "All five are absent on a write's first snapshot".
  - **For `act-begin`,** none of them: no write-id check and no mint. Y:5572 "- On primary it makes one `lanes-index register snapshot` call."
  - **[P] `--write-id <uuid>` without `--generation`** is refused 64. A printed rerun always carries `LANES_WRITE_GENERATION`: Y:5200 "- **The printed rerun carries the generation**". What `lanes-edit.sh` does with a `LANES_WRITE_ID` set without `LANES_WRITE_GENERATION` is lane 4's (O4, ratified by R08).
- **`--tree <dir> --checkout <lane checkout>`** [Y, the content; B, the flag]. It builds the scratch tree at `n` in `<dir>`, which must not exist or must be empty: Y:5048 "- **From SQL, the render set at `n`:**" and Y:5051 "- **How it is built.** `--tree` writes these files, `git init`, adds and makes one commit".
  - From SQL: `lanes/LANES.md`, every `lanes/log/<lane>.md`, `lanes/aliases.tsv` and `lanes/archive/*`.
  - From `--checkout`'s `origin/main`: `lanes/repos.tsv` and `lanes/log/README.md`.
  - Then one commit, and `refs/remotes/origin/main` pointing at it.
  - Never a hardlink (Y:5052).
  - Not `lanes/STORE`, and not the handoffs.
  - **[Y] When the id is found, no tree is built:** the run goes to "The tail, once" (Y:4987).
- **`--at <k>`** [Y]: the tree at seq `k`, with `repos.tsv` from that entry's `repos_blob`, for the replay. Y:4831 ("and `--at <k>`, which builds it at seq `k` instead"); Y:5318 "2. `lanes-index register snapshot --tree <dir> --at <s−1>`".
  - [P] Under `--at` the snapshot checks only the generation. It makes no write-id or switch check, since it reads history for a write already found.
  - **The forms under `--at`:** `--generation <g>` alone, or all five identity flags, of which only `--generation` is checked. `--at` without `--generation` is 64. Lane 4 passes all five (its T418).
- **[P] `--files`/`--all` without `--tree`** (Y:4831's "the images of `--files` or `--all` at `n`") is not a group B call. Group B always takes images through `--tree`. Its output form is §3.6's, where O3 closes.

**[Y, the order; P, the version's place] The checks,** each in this order, each ending the call at once:

| # | Check | Result | Y |
|---|---|---|---|
| 1 | `--generation` is not the switch's `generation` | 2; `ERROR:  LR007: …`, `DETAIL:  generation`; whatever the switch reads, and whether or not the journal holds the id | Y:4986 "- **the run's generation,** that of its write id" |
| 2 | the id is in the journal with this run's lane, verb and `args_sha256` | 0, with `found=` (stdout); the attempts end | Y:4987 "- **the write id:** found with this run's lane, verb and arguments, it ends the attempts" |
| 2′ | the id is in the journal for another write | 2; `ERROR:  LR007: …`, `DETAIL:  another-write` | Y:4987 |
| 3 | `REGISTER_SCHEMA_VERSION` outside the window (§2.8) | 69, naming both numbers | Y:2333; [P]: placed after the write id, as `commit_write` checks the version after the id (Y:1419's step order) |
| 4 | the switch is not `primary` with the marker `present` | 69, with Amendment 22's text | Y:4988 "- **the switch:** not `primary` with the marker `present`, 69" |
| — | the act's generation and base | not `lanes-index`'s: `lanes-edit.sh` checks them at each snapshot, with 2 | Y:4989 "- **the act:** its generation and its base, each 2" |

**stdout** [B′]. One `key=value` per line, in this order, LF-ended. Group B parses by key, never by position.

```text
format=1
generation=<uuid>                     the switch's
write_id=<uuid>                       echoed, or minted for --write-id new; absent for act-begin
found=<seq>                           only when check 2 found the id
file=<path>\t<present>\t<sha256>      only with found=: that seq's journal_files rows, sorted by path
switch=<mode>                         the switch's mode (Y: files or primary)
marker=<state>                        absent, adding, present or removing
n=<seq>                               the head
clock=<epoch s>                       the database clock, in whole seconds (D13)
not_after=<epoch s>                   clock + C + 2 s, for the commit call (§3.3) [P]
schema=<version>                      schema_version.version
repos_blob=<40 hex>                   only with --tree: the blob id of the tree's lanes/repos.tsv
c=<s>                                 the connect bound in use, register_url='s [R05]
n_a=<count>                           N_a in use [R05]
```

- **`clock=` is epoch seconds** (lane 4's Q1: yes).
- **[Y] What `clock=` is.** It is the database's clock in whole seconds, and the verb's lines carry it: Y:2511 "database's clock in whole seconds (D13)". `lanes-edit.sh` formats it as the register's UTC.
- **`found=` comes with `file=`.** With `found=`, the `file=` rows are that seq's `journal_files`, read in the same transaction. Y:5300 "**The match.** The entry's change set is its `journal_files` rows: path, present, sha256".
- **The canonical row** is `<path>` TAB `<present>` TAB `<sha256>`:
  - `<present>` is `1` or `0`;
  - `<sha256>` is 64 lowercase hex, or `-` where the path is not present;
  - rows sorted by path, bytewise (`LC_ALL=C`).
  - The `files` digest of Y:5173 ("the sha256 of the sorted `files` list it sent (path, present, sha256)") is the sha256 of exactly these rows, each LF-ended, without the `file=` prefix. The same rule holds in §3.3 and §3.4 [P].
- **[P] `not_after=`.** It is `clock` + `C` + 2 s, which Y:1592 defines ("--                  call's client timer plus 2 s"; the entry's `not_after`: "the snapshot's database clock plus the call's client timer plus 2 s"). `lanes-index` computes it because `C` rests on `c` and `N_a`, which only `lanes-index` parses. Group B writes it into the pending record's `call` line before the call (Y:5173), and into the attempt directory (§3.3).
- **[R05] `c=` and `n_a=`** serve `lanes-edit.sh`'s sums of `T_write` and the seam's wait (Y:5002). `c=` is `register_url=`'s `connect_timeout=`, and `n_a=` is `register_addresses=` (§2.5). Lane 5's `bound_s` takes them, with `w_s`, from `register read`'s `.head` instead (§3.6). `lanes-edit.sh` parses no configuration (R05).

**Exits:**
- 0;
- 2 (checks 1 and 2′, a refused configuration, Q6(c) while built in);
- 69 (checks 3 and 4; a database it cannot reach; any failed read; the timer `S`);
- 64.

**[Y] A 55P03 at the snapshot is 69,** not a lost turn: Y:2627 "**55P03 means different things at the two calls.** … a 55P03 there can only be a lock a migration or other DDL holds: a refused read, 69".

**[Y] The caller's mapping.** Where an earlier commit call of the write was in doubt, the caller makes F3's lookup before it ends with 69 or 2. That is the caller's rule, not `lanes-index`'s: Y:3522, in the column headed "On expiry" at Y:3516: "a failed read: 69, or in doubt where an earlier commit of the same write was".

### 3.3 `lanes-index register commit`

**[Y] What it does.** One SERIALIZABLE call, as one request: `commit_write(n, write_id, REGISTER_SCHEMA_VERSION, generation, entry)`. Y:4832 "\| `register commit` \| the attempt directory's format …; `not_after` (F3); the call `commit_write(n, write_id, REGISTER_SCHEMA_VERSION, generation, entry)` …; exits 0 `committed <seq>`, 75, 69, 2, 3; no `--end-line` or `--hook-pid`". Y:2556 "3. **The commit.** `lanes-index register commit`, one `psql` call, runs".

**argv** [B′; lane 4's I-4]:

```text
lanes-index register commit --write-id <uuid> --generation <uuid> --lane <lane> --verb <verb>
    --args-sha256 <hex> --workstation <name> <attempt dir>
```

- **Lane 4 proposed `register commit <attempt dir>` alone.** This contract adds the identity flags, which are the same as the snapshot's, and `--workstation`:
  - so the fake asserts them on argv, as it does for `journal`;
  - and so the journal's `workstation` is the name `lanes-edit.sh` stamps into the lines (its `LANES_WORKSTATION`, or its own fallback), never a second resolution.
  - Lane 4 to confirm.

**[P] The attempt directory: the files `register commit` reads.** The layout is lane 4's: `<state>/register-writes/<d>/<run>/<k>/`, from the record's digest, a run nonce of 32 lower hex minted at the run's start, and the attempt. All three are known before the first snapshot, which mints a new write's id. `register commit` takes the directory as its argument and reads no id from its path. The seam writes most of it: Y:5061 "3. The seam writes `changes.tsv` (status, path, the base blob at `n`, the new blob, from `git diff --cached --no-renames --raw HEAD`), the new images from the index, and `msg`, holding `$msg` verbatim". `register commit` reads only these:

| File | Format | Becomes |
|---|---|---|
| `snapshot` | §3.2's stdout, verbatim | `n=` is `commit_write`'s base. `repos_blob=` is `journal.repos_blob`. `generation=` and `write_id=` must equal the flags, else 2 |
| `changes.tsv` | one line per staged path: `<status>` TAB `<path>` TAB `<base blob>` TAB `<new blob>`. Status is `A`, `M` or `D`. Each id is 40 hex, so the seam's `git diff --cached --no-renames --raw HEAD` takes `--no-abbrev`. A missing side is 40 zeros | the edits, with `files` below |
| `renames.tsv` | optional: `<from>` TAB `<to>`, one per rename the verb made (`LOG_RENAMED_FROM`, Y:5060). The paths are a `D` and an `A` line in `changes.tsv` | `journal_edits`' `rename` instead of `remove` plus `create`. O5, ratified by R08 |
| `images/<path>` | the new file's bytes, for every `A` and `M` path | the new lines |
| `tree/` | the scratch tree at `n`, as `--tree` built it | each path's base, by its blob in `HEAD`; `lanes/repos.tsv` for D19's alias sets and D18's `repo_spellings` |
| `msg` | the commit message, verbatim | `journal.message` |
| `author` | one line, `Name <email>`, from `<c>`'s `user.name` and `user.email` | `journal.author` (D15; Y:5379 "- **The author identity**") |
| `act` | only inside an act: `id=`, `base=`, `generation=`, and one `expect=<scope>` per scope, possibly none | the entry's `act`, `{id, base, generation, expect}` (Y:1594 "--   act            a write's, made inside one of lane 4's acts") |
| `files` | the canonical rows of §3.2 for every path in `changes.tsv`, as the write leaves it | checked: `lanes-index` recomputes them from `images/` and refuses a difference with 2, sending nothing |
| `not_after` | one line, epoch seconds: the snapshot's `not_after=` | the entry's `not_after` (Y:1592) |

- **Group B sends no structured rows.** `lanes-index` derives the edits from each path's base and new bytes. It derives the structured rows (`register_lines`, `log_lines`, `lanes`, `object_holds`) with its one classifier, and D19's alias sets and D18's `repo_spellings` from `tree/lanes/repos.tsv`, whose blob is `repos_blob`. The entry's shape is Y:1587–1611.
- **Lane 4's Q4:** yes, `lanes-index` derives them, and the client writes none.
- **[P] The seam's path check, again.** `lanes-index` refuses with 2, sending nothing, a path outside the render set, as the seam does (Y:5060 "2. Every staged path must be in this write's pathspecs plus `LOG_RENAMED_FROM`").
- **[P] Only the names above are read** (CO-1). `register commit` ignores every other name in the directory, since Y keeps more there: Y:4999 "3. **The child stops at the seam.**" (its `ready` and the FIFO on fd 5), Y:5005 "- **On a message** it writes `seam.out`", Y:5177 "- Its directory keeps three small files while the line stands", and lane 4's `parent.pid`, buffered output and `lock`.
- **[P] A malformed directory** is refused with 2, "attempt directory: <what>; nothing was sent", where the listed files disagree among themselves:
  - a listed file is missing;
  - an `images/` file has no `A` or `M` line;
  - a `changes.tsv` path is missing from `files`, or a `files` row has no `changes.tsv` line;
  - `files` differs from the rows `lanes-index` recomputes;
  - the flags differ from `snapshot`.
  - As at the seam (Y:5078's "a dirty pathspec in a scratch tree is an internal error, exit 2"), it is an internal error, never sent.
- **[Y] The journal's other fields:** `kind` is `write`, `lane` is `--lane`, `verb` is `--verb`, `args_sha256` is `--args-sha256`, and `act_id` is `act`'s `id` (Y:1587–1589).

**stdout** [Y; B for `already`]:
- 0: `committed <seq>`, one line. Y:2601 "\| committed at `n+1` \| 0, `committed <seq>`".
- 0 on LR006: `already <seq>`, then that seq's `file=` rows, sorted. Y:2605 "\| LR006: this write is already in the journal, in this generation … \| 0, with its seq".
  - The word `already` is [B], answering lane 4's Q2: yes. "The tail, once" must know the commit was not this call's (Y:5307 "\| LR006 at a commit \| whichever matches").
  - The `file=` rows are [P]: the match needs them (Y:5300). `commit_write`'s LR006 rolls back with the seq alone, so `lanes-index` reads them in a second request with the snapshot's bounds. A failure of that read prints `already <seq>` alone and exits 0, and the caller then replays (Y:5307's table).
- 2 on LR005 `main-held`, `one-live-claim` or `claim-spellings` [P]: after stderr's `ERROR:` and `DETAIL:` lines, stdout carries `held-by=<seq>` TAB `<path>` TAB `<raw line>` for the earlier taker in `main_slots`, or for the live claim. It is read in a second request with the snapshot's bounds. Y:1559, in the column headed "The writer" at Y:1553: "exits 2 with the holder or the earlier line". `commit_write` names neither, so `lanes-index` reads it after the refusal. A failure of that read leaves the line out.
- Nothing otherwise.

**stderr:** §2.6.

**[Y] The exits, from the signals table (Y:1555–1569) and the outcomes (Y:2601–2616):**

| The database's answer | Exit | stdout | Nothing written? |
|---|---|---|---|
| committed | 0 | `committed <seq>` | the write |
| LR006 | 0 | `already <seq>`, file rows | the write, by an earlier attempt |
| LR001, 40001, 40P01, 55P03, LR008 | 75 | — | yes |
| LR005 with its token | 2 | `held-by=…` where D18 or D19 | yes |
| LR007 with `generation`, `another-write` or `act-generation` | 2 | — | yes |
| LR009, "expectation changed" | 2 | — (the message carries `expectation: <scope> changed at seq <k> by <lane>/<verb>`) | yes |
| LR010, "key not well formed" | 2 | — | yes; never retried |
| LR002 | 69 | — | yes |
| LR003 | 69 | — | yes |
| LR004, after its one retry (below) | 69 | — | yes |
| `psql` exit 3 with any other SQLSTATE, a 23505 included | 69 | — | yes |
| `psql` exit 1, exit 2, ended by a signal, or the timer `C`; or `psql` exit 0 with an answer `lanes-index` cannot read | 3 | — | unknown |

**[D, R01] Where 2 and 69 come from on `register commit`** (CO-6, lane 1's contract decision). They come only from two sources:
- a refusal identified by its SQLSTATE under `psql`'s exit 3, as the rows above;
- an error raised before anything was sent: a refused configuration, a malformed attempt directory (2), or a `psql` that cannot be started or is too old (69).

Every other end of the call is 3. Y:2620 gives the reason: "transaction back. Every exit 2 of a commit call is treated as in doubt," since `psql`'s stderr is the same before and after a COMMIT that landed. Reported as 69, a call that may have committed would lose its `call` line, and a rerun would not look the write up.

**LR004** [Y, with R02]. Y:1567 "\| LR004 \| the edits do not apply, or the proof differs \| reloads every line once and retries; then 69", and Y:2614 "\| LR004: the edits do not apply, or the proof differs", whose `register commit` cell is "69 after one reload and retry".
- The retry is inside `register commit`, and group B never retries an LR004 (lane 4's Q7: yes).
- **[R02] What the reload means under the scratch tree** (O2 = Q-L3-10, ruled as lane 1's form, with CO-4):
  1. `register commit` re-reads the base of each path in `changes.tsv` at `n` from SQL, whole, bypassing the image cache.
  2. **Where that base equals `tree/`'s,** it recomputes the edits and sends the commit once more, under the same `not_after` and the same `files`. The pending record's `call` line stays true.
  3. **Where the base differs,** the verb ran on a wrong base, so its images are not sent again: 69, naming the image cache.
  4. A second LR004 is 69.
  - **The retry's own call is read by this table in full** (CO-4), CO-6 included:
    - its `psql` exit 1 or 2, a signal, the timer, or an unreadable exit 0 is 3;
    - its LR001, 40001, 40P01, 55P03 or LR008 is 75;
    - so 69 comes out only when both calls ended definitively.
  - An LR008 on the retry is likely when the first call was slow, since both share one `not_after`. That is safe: 75, then a fresh attempt.
  - O2 is closed by R02. Y's row predates the scratch tree, so its "reloads every line once and retries" is read as these four steps.
- **The retry's time.** A retried call takes up to a re-read plus two commit calls, each within `C`. The seam's cap (Y:5001, 120 s) holds it.

**[Y] The caller's map**, Y:5009–5015:
- 0, `committed`: the renderer fork, then `tail=sent`, then `committed <s>` to the child;
- 75: `abandon`, and the `call` line dropped;
- 3: the child kept, and the `call` line kept;
- 2 or 69: `abandon`, and the run's end;
- with any `call` line held, no end but 0 before F3's lookup: Y:5020 "- Where it holds one, no end other than 0 comes before F3's lookup".

### 3.4 `lanes-index journal --write-id …`, the in-doubt lookup

**[Y] What it does.** Y:4834 "\| `journal` \| `--write-id <id> --not-after <n> --generation <g> --lane <lane> --verb <verb> --args-sha256 <h>`, where `<n>` is the largest `not_after` of the write's commit calls". `journal` waits on its own estimate, then calls `find_write`, at READ COMMITTED, in a transaction it rolls back, with D9's lookup bounds and tries. Y:5235 "- **The lookup is lane 1's v2.8 `find_write(<id>, <n>, <g>, <lane>, <verb>, <a>)`.**"

**argv** [Y; B for the clock]:

```text
lanes-index journal --write-id <uuid> --not-after <epoch s> --generation <uuid> --lane <lane>
    --verb <verb> --args-sha256 <hex> [--clock <epoch s> --clock-age <s>]
```

- **`--not-after`** is the largest `not_after` among the pending record's `call` lines (Y:5235).
- **`--clock` and `--clock-age`** [B, answering lane 4's Q3; Q-L1-1 for a takeover]. With them, `journal` waits until its estimate, `--clock` + `--clock-age` + the seconds since its own start, puts `<n>` at most 2 s ahead: Y:5236 "1. **The client waits first,** on its own estimate: the last snapshot's database clock plus `SECONDS` since that snapshot call ended". Which clock a run passes:
  - **A run that made a snapshot** passes its last snapshot's `clock=`, and the whole seconds since that snapshot call ended, by bash's `SECONDS`.
  - **A run that took the record over, with no snapshot of its own** (its snapshot failed or refused), passes the clock of the record's newest `call` line as `--clock`. As `--clock-age` it passes the whole seconds since the record's `updated_utc`, by this host's clock, never below 0. These are lane 4's answers 36 and 40, taken by lane 1 (Q-L1-1); this part is 36, and its text change at Y:5236 (R26's X:5235) is a `research.md` erratum (R26).
    - **Why the estimate can only lag.** The `call` line holds "the snapshot's database clock that its lines carry" (Y:5173). It is written after that snapshot ended and before its call was sent: Y:5174 "- It is written, by temp file and `mv`, before the call is sent." `updated_utc` is no earlier than that write. So the estimate lags the database's clock, within one second's grain as `SECONDS`' does, and a lag only lengthens the wait.
    - **An early estimate costs at most LR011:** 3, with the record kept, as after this host's clock stepped forward. The server checks the clock again under the head row's lock: Y:5238 "3. **The server takes the head row's lock**".
    - **Only on this host:** it applies where the record's `host` is this run's own. A record written on another host carries a clock this host cannot age.
  - **Without them** `journal` calls at once: Y:5236 "A run with no snapshot clock calls at once". That is now a run with no clock of its own and none it can age, as with a record from another host.

**[Y; Q-L1-1 for the spacing] Inside `journal`:**
- the wait (at most `C` + 2 s);
- then the window `R`, 10 s, from the wait's end: Y:5247 "- **The window starts when the client's own wait ends.**";
- tries, each under `T_L`, each starting inside the window: Y:5249 "- Every try starts inside its 10 s";
- a failed connect, a cut by `T_L`, or a 55P03 is tried again: Y:5250 "- **A try that ends without an answer is made again,** at least 1 s after the last, while a new try can still start inside the window";
- LR011 and LR007 are answers, and are not tried again.
- **The spacing, counted start to start.** This is lane 4's answer 40, taken by lane 1 (Q-L1-1); R26 says it "needs no text".
  - Each try starts at least 1 s after the last one started, and never before the last one ended.
  - The window is closed: a try may start at the 10 s mark, as Y's worst case has it, Y:5279 "- The last try starts by `R` after the window's start, and ends by `T_L` after that."
  - Counted from the end instead, a try cut by its timer, `T_L`, 9 s at `c` = 2, could start again only at the window's last instant, and at `c` = 3 never. That would defeat the retry Y names for it: Y:5252 "- a try cut by its timer, `T_L`;".
  - Where `T_L` is over `R`, at `N_a`·`c` of 4 s or more, no try can follow a try cut by its timer inside the window. Its outcome stands: Y:5255 "- **The last try, ended without the lock,** gives the third outcome, 3."
  - The tries run inside `journal`, lane 1's T114, so `lanes-edit.sh` changes nothing for them.

So `journal` takes at most `C` + 2 s + `R` + `T_L`, and `lanes-edit.sh` sets no shorter timer around it.

**stdout** [B]:
- `found <seq>`, then that seq's `file=` rows, sorted (§3.2's canonical form), read in the same transaction (Y:5300);
- or `absent`: the lock was held, and the id is not this write's.

**Exits** [Y; B for the shape]:

| The answer | Exit | stdout | stderr | Y |
|---|---|---|---|---|
| found | 0 | `found <seq>` and file rows | — | Y:5240 "- **found:** 0, and the verb's tail" |
| absent, with the lock held | 0 | `absent` | — | Y:5244 "- **absent, with the lock held:**" |
| LR007 | 2 | — | `ERROR:  LR007: …`, `DETAIL:  generation` or `another-write` | Y:5241 "- **LR007, raised by `find_write`:**" |
| LR011 | 3 | — | `ERROR:  LR011: …` | Y:5246 "- **LR011, "lookup too early,"**" |
| no try in the window took the lock | 3 | — | `lanes-index: journal: no try took the head row's lock within 10 s` | Y:5245 |
| any other SQLSTATE | 3 [P] | — | its `ERROR:` line | — |
| a refused configuration, R09's TLS refusal included | 3 [P] | — | its refusal | — (CO-3; R09) |
| Q6(c)'s refusal of a `mode=primary` configuration, while it is built in | 2 | — | its text | Y:2452 "every other `lanes-index` command refuses it with 2, as the configuration" |
| a crash of `lanes-index`, or a code outside this table | 3, the record kept [D, R01] | — | — | §2.7 (CO-2) |

- **Absent, with the lock held, is exit 0 and `absent`.** The code the write then ends with is the caller's: the one its last attempt earned (Y:5244).
- **[P] Any other SQLSTATE is 3,** since it settles nothing. Y names only LR007 and LR011 among the lookup's errors.
- **[P] Exit 2 means LR007** (CO-3), with its `ERROR:` and `DETAIL:` lines, and the token decides the record's fate (Y:5241). The one other 2 is Q6(c)'s, which Y fixes for every command and which no primary write can precede. Lane 4 reads a 2 without `ERROR:  LR007:` as 3 (T417).
- **The other forms are not group B's.** `journal --since <m>` is the renderer's, and `journal --last --path <log> --grep <key>` is `swapped_lanes`' (Y:4834; Y:4914 "\| `swapped_lanes` \|"). Both are §3.6's, and `journal --since` does not serve the readers' second read (§3.6, R06).

### 3.5 `lanes-index register classify` [R04; T122]

- **Why.** On primary, `append-line` refuses three texts with 2, before the lock:
  - a hand-typed `LANDING EXPIRED`;
  - a take with no readable UTC;
  - a taking line that names no repository.
  - Y names "the one classifier" for them (Y:5116, lane 4's E3), and that classifier is `lanes-index`'s `parse_register_line`. A bash copy would be the second classifier Y:6247–6249 warns of.
- **Whose** [R04]. It is lane 1's task T122, `[O]`, in group A. Lane 4's T424, which calls it, lands after T122.
- **argv:** `lanes-index register classify`. **stdin:** register lines, one per line, LF-ended.
- **stdout:** one line per input line, in order: `<kind>` TAB `<slot_role>` TAB `<repo_key>` TAB `<utc_ts>`, each `-` where `parse_register_line` gives NULL. `<utc_ts>` is epoch seconds.
- **Exits:** 0; 2 for a refused configuration, Q6(c)'s included (Y:2452); 64. It reads the configuration only for `register_schema=`, and makes no database call.
- **The parity test** [R04] feeds the same lines to `parse_register_line`, in process, and to `register classify`, by argv and stdin, and holds the four fields equal on every line. Its lines are the test register's, plus one of each refused text above.

### 3.6 `lanes-index register read`, and the other readers' forms [R06]

**Whose, and when.** These are the readers', the guard's, the renderer's and the image cache's forms. Group B calls none of them.
- Lane 1 writes `register read` in T113, and the journal forms in T114.
- Lane 5 writes the client half of `register read --for guard` in T507, under lane 1's review, after T113 (R07): `T0` from the pid, the clamp, the cap and the floor, the declaration read once, SVC-12's conditions 2 and 3, and the whole-file fetch.
- Lane 5 reviewed this section (R06; #165 6738431117), and draft 4 takes each item as lane 1 decided it. Its date is the contract's own, 2026-11-09 14:00Z, before T113 starts.
- Lane 5's H1 formats (its block :363–375, and its open point 19) are taken here, marked [P], with lane 1's changes named. Lane 5 accepted them as taken, with five changes of its own (O15).

**[R06] Does `journal --since <n>` serve the second read? No.**
- Y gives that form each entry's fields, Y:4834 "Also: `--since <m>` (message, author, commit UTC, edits, digests, `journal.operator`)", and names it the renderer's. The changed paths could be read off each entry's digests. But Y gives the form no new head `n′`, and it writes no cache file.
- Y makes the second read a `register read`, which does both in one snapshot: Y:4900 "On primary, a primitive reached with no prefetched file makes one more `register read`, never a git read (SVC-8's rule); it returns the paths changed in `(n, n′]`, and where any of them is already cached, the whole family is refetched at `n′`".
- So `register read --since <n>` joins this section, as form 3 below. Lane 5's open point 4 asked the same of W:4849, which is Y:4900.

**What Y fixes.** Y:4833 "\| `register read` \| `--prefetch --cache <file>`, `--files` or `--all`, `--for guard`; text at `n` with digests, and the `n` and the register `generation` it read, in the same snapshot; 0, 69, 78 or 2". Phase 1's `register read` has only its direct path: Y:4868 "**The service: stop at its interface.** Phase 1's `register read` is SVC-3's client with only its direct path". The socket, the `.direct` flag and the service's write-through come with `serve`.

**The four forms of `register read`:**

| Form | argv | Called by | D9 row (§2.4) |
|---|---|---|---|
| 1, the readers' prefetch | `register read --prefetch --cache <file> --files <set>`, or `--all` in place of `--files <set>` | `reg_open`, once, in the main shell, before the arm (T504); and the post-bind refill, with `--files register,aliases`, in `guard-bind`'s detached child (C1) | a reader's |
| 2, the guard's prefetch | `register read --for guard --prefetch --cache <file> --pid <pid> --repo <dir> [--lane=<window>]` | `guard_run`, before its first register read (T507) | the guard's direct read |
| 3, the readers' second read | `register read --since <n> --cache <file> --files <set>`, or `--log-key=<key>` in place of or beside `--files <set>` | a read site outside the guard whose cache file is missing: every other primitive on primary (Y:4900) | a reader's |
| 4, the guard's second read | `register read --for guard --at <n> --pid <pid> --cache <file> --lane=<key>` | `lane_log_events` in `guard_run`, where `.log.<key>` is missing or the key is outside the class (Y:4157–4162, Y:4956; C9) | the guard's direct read, cut at `T0` + 3.75 s (B5) |

- **`--cache <file>`** is `$SE_CACHE_FILE`, and each member is a file beside it, `<file>.<member>`. Y:4133's guard call names no `--cache`, but the guard's members are files beside `$SE_CACHE_FILE` too (Y:4149), so the guard passes it [P].
- **`--files <set>`** [P] is a comma-separated list of members: `register`, `paths`, `archive`, `aliases`, and `log:<key>`, which may repeat, for a key in `check_lane_name`'s class only: a key outside it goes as `--log-key=<key>` (O18). **`--all`** is `register`, `paths`, `archive`, `aliases`, and `log:<File>` for every `lanes/log/<File>.md` at `n` whose `<File>` is in `check_lane_name`'s class.
- **Form 2's members** are `register`, `aliases`, and, with `--lane`, `log:<window>`: Y:4142 "- on primary, asks the service (or reads directly) for `lanes/LANES.md`," and the window's log, Y:4143 "`lanes/aliases.tsv` and `lanes/log/<window name>.md`, the lane a window is". `--lane=<window>` is the window's name as the guard has it [P]. Without it, or where the name is outside `check_lane_name`'s class, form 2 fetches no log, and refuses nothing for it (O18). **For the record,** as lane 1 decided: an empty `--lane=` ends 64 in form 2 as well, as it does in form 4 (below). T507 never sends one: with no window name, it leaves `--lane=` out.
- **`--pid <pid>`** [P, H1; B1 for form 4] is the hook's own pid, `$$`, in forms 2 and 4: Y:4263 "**How the client knows `T0`.** The guard passes its own pid (`$$`, the hook". The clamp, the cap and the 0.5 s floor are Y:4267–4284's, in T507.
- **`--repo <dir>`** [P] is the guard's `$LANES_REPO`, where SVC-12's form C runs its one `git log` (below). It is lane 1's addition to H1. Form 2 alone takes it: form 4 runs no form C (B4).
- **A key outside `check_lane_name`'s class** is not a file name. Y:4956 "- **The key** is `$1` as received (SVC-8), used only where `$1` is in `check_lane_name`'s class", and any other name takes the second-read path. [O18, lane 5's design] A key outside the class is passed as its own argument, `--log-key=<key>`, never inside `--files`. Its log is written to `.log.%<hex>` and `.logpath.%<hex>`, where `<hex>` is the key's bytes in lowercase hex. `%` is outside the class, so no class key can name such a file, and no two keys share one. Such a file is rewritten on each read and never counted as cached.
  - Forms 1 and 2 fetch no log for a key outside the class, and refuse nothing for it. A tmux window name may hold `/`.
  - Form 3 takes such a key as `--log-key=<key>`. Form 4's `--lane=<key>` takes any key: it writes `.log.<key>` for a key in the class, and `.log.%<hex>` otherwise.
- **[L1-D1] The `=` form.** `--lane` and `--log-key` take their value in the `=` form, `--lane=<key>` and `--log-key=<key>`, so that a key beginning with `-`, which is outside the class, is never read as a flag. A caller must pass the `=` form, and the parser takes everything after the first `=` verbatim. This is `register read`'s rule: group B's `--lane <lane>` (§3.2 to §3.4) carries the writer's own lane, which is in the class, and is unchanged.
- **[C11] Two logs that differ only by case.** "Where two render-set logs differ from `<key>` only by case, no form writes `.log.<key>`, and forms 3 and 4 exit 69, as `log_path_ci` returns 2 today" (`code/lanes-edit.sh:2991`, `:3003–3006`, `:3522`, pin `2a057a6d`).

**The cache family** [Y, with P for `.paths`' form, `.head` and `.outage`]. Each file is written by temp file and `mv -f`, as Y:4890 has today's caches "written by temp file and `mv -f`", and each holds its text at the read's `n`, in the form its read site already expects. `.head` is written last, so a `.head` names only files already in place [P].

| File | Holds | Written by | Read by | Y |
|---|---|---|---|---|
| `.register` | `lanes/LANES.md`, as `register_text_checked` writes it | forms 1, 2 and 3 | `register_text_checked`, and the guard's register reads | Y:4149 "\| `register_text_checked` (`:3597`) \| `$SE_CACHE_FILE.register`" |
| `.paths` | the render set's paths present at `n`, one per line, sorted bytewise (`LC_ALL=C`) [P] | forms 1 and 3 | `register_published`, `log_path_ci`, `lane_log_exists`, `known_lanes`, `remote_log_events` | Y:4907 "\| `register_published` \| :3639 \| `.paths` (new: the render set's paths at `n`)" |
| `.archive` | `lanes/archive/*`, as `archive_text` prints it today | forms 1 and 3 | `archive_text`, and `retired_identity_hits` through it | Y:4908 "\| `archive_text` \| :3671 \| `.archive` (new)" |
| `.aliases` | `lanes/aliases.tsv`; for an empty or absent table, the one line `# no lane aliases` | forms 1, 2 and 3 | `lane_alias_text` | Y:4150 "\| `lane_alias_text` (`:5367`) \| `$SE_CACHE_FILE.aliases`" |
| `.log.<key>` | the log's text, where `<key>` resolves case-insensitively to a `lanes/log/<File>.md` of the render set; empty where none does; not written where two do (C11) | forms 1, 2, 3 and 4 | `lane_log_events`, before `log_path_ci`; `remote_log_events` | Y:4957 "- **The form.** The log's text, plus a sidecar `.logpath.<lane>`" |
| `.logpath.<key>` | the resolved `lanes/log/<File>.md`; empty where no log resolves | with each `.log.<key>` | `parse_log_stream`'s `unreadable:` warnings | Y:4957 |
| `.log.%<hex>`, `.logpath.%<hex>` | as `.log.<key>` and `.logpath.<key>`, for a key outside the class; `<hex>` is the key's bytes in lowercase hex; never counted as cached | forms 3 and 4, rewritten on each read | as `.log.<key>` and `.logpath.<key>` | — (O18) |
| `.head` | the read's head, generation, `c`, `n_a`, `w_s` and digests (below) [P, H1] | forms 1, 2 and 3; never form 4 (B8) | the guard, for the bind record and for form 4's `--at <n>`; form 3; form 4, for the generation (B3) | — |
| `.outage` | the declaration's verdict (below) [P, H1] | form 2 only, on 69; never form 4 (B4) | the guard | Y:4447 "2. `register-outage` (D20) is present beside `lanes-index.conf`, unexpired," |
| `.direct` | the service-down flag [Y; with `serve`] | the first fallback in the invocation | every later read in it, but form 4, which always reads directly and never reads `.direct` (B7; lane 3, #165 6738474549) | Y:4153 "- **The service-down flag is a file,** `$SE_CACHE_FILE.direct`, written by the" |

**`.head`** [P, H1, with lane 1's additions]:

```text
format=1
n=<seq>                                 the head the family was read at
generation=<uuid>                       the register generation, from the same snapshot
c=<s>                                   §2.5: the larger of the two URLs' connect_timeout=
n_a=<count>                             §2.5: register_addresses=
w_s_ms=<ms>                             §2.5: whole milliseconds (O15)
file=<path>\t<present>\t<sha256>        one per text cached, §3.2's canonical row, sorted by path
absent=<key>                            one per log:<key> that resolved to no log
```

- H1's `n`, `generation`, `c`, `n_a` and `w_s` are taken. `w_s` is printed as `w_s_ms`, in whole milliseconds, since `guard-bind` is bash 3.2 (Y:4163) and sums 19·`w_s` (Y:5459) (O15). Lane 1 adds the `file=` rows, Y:4833's "text at `n` with digests", which form 3 reads to know what is cached. Lane 1 also adds the `absent=` rows, so that a log created since `n` counts as a change to what was cached.
- `c` is the larger of the two URLs' `connect_timeout=`, not the snapshot's. That is right for `bound_s`, an upper bound, and it only lengthens T510's wait (O15).
- **Form 4 writes no `.head`** (B8, declined). `.head` is the prefetch's record of `n` and the generation, and form 4 reads it (B3). Concurrent second reads for different keys must not share a writable file (Y:4127–4129, Y:4890). Form 4's result is its exit and its `.log` file, so the log it writes has no `file=` or `absent=` row in `.head`. The guard passes `.head`'s `n` as form 4's `--at`.
- The guard copies `c`, `n_a` and `w_s_ms` into the bind record (T507), and `guard-bind` sums `bound_s` from the record (§2.5, R05).

**`.outage`** [P, H1; written by T507's client half, R07]. It is written only where form 2's read exits 69, and an unexpired declaration names this database. That is SVC-12's condition 2, read once, at the prefetch, before the direct read (Y:4447–4449). Form 4 writes none, reads no declaration and never opens the mirror path (B4).

```text
format=1
verdict=open | behind | no-seq
label=(declared outage <utc>: <why>)
declared_seq=<m>
mirror_seq=<s>                          with verdict=behind
mirror_tip=<sha>                        the tip form C walked (O15)
```

- `<why>` is carried on one line: `outage declare` refuses a `--why` with a line break (T121), and the client never writes one (O15).
- **`open`:** conditions 2 and 3 hold. The guard parses the mirror's text, with `read: mirror at <sha12>, …` and the label in place of the cause: Y:4468 "Then the guard runs today's parser on the mirror's text and exits 0 or 2 as it".
- **`behind`:** condition 3 fails, and the guard refuses 2 with lane 5's text, "the mirror here is at seq `<s>`, behind the declared outage's seq `<d>`; …" (Y:4472–4474).
- **`no-seq`:** none of form C's 200 commits carries a `Register-Seq`, and the guard refuses 2: Y:4463 "non-empty value, and refuses 2 if none of the 200 carries one". Its text is S3's outage text (Y:4475; O15).
- **No file:** no declaration opens the mirror path, as with a rollback's declaration where the read did not find the switch at `files` (Y:4451–4454). The guard then refuses 2 with S3's outage text, which names the store, the cause and the two ways out (Y:4343, Y:4475; O15).
- **Form C** runs in `--repo`, on `origin/$LANES_BRANCH` as last fetched, the ref every read the guard makes uses (`code/lanes-edit.sh:2999`, `:3538`, `:3612`, `:5395`, pin `2a057a6d`). Y names it `origin/main`: Y:4455 "3. the mirror it reads, `origin/main` as last fetched (the guard never". `mirror_tip=` is the tip form C walked, which the guard's mirror read then uses (O15).

**Form 3, the readers' second read** [P, after H1 and Y:4900]:
- **Whose:** every primitive on primary reached with no prefetched file, outside the guard (Y:4900). The guard's second read is form 4 (below; O17).
- **`--log-key=<key>`** [O18] names one key outside the class. Form 3 writes its log to `.log.%<hex>` and `.logpath.%<hex>` at `n′`, and never counts it as cached.
- **One snapshot** at the register's head `n′`, REPEATABLE READ READ ONLY, as one request (§2.3).
- **It finds the paths changed in `(n, n′]`** from those entries' `journal_files` rows, and prints them after its `mode primary` line: `n=<n′>`, one `changed=<path>` per path, sorted, then `refetched=0` or `refetched=1`.
- **Where nothing cached changed,** it writes the members `--files` names at `n′`, and rewrites `.head` at `n′` with their rows added. The family already cached reads the same at `n′` as at `n`, so one verb still reads one snapshot: Y:4890 "On primary it writes the cache family at one head `n`, so one verb reads one snapshot (R2B S-13)".
- **Where any changed path is cached,** it rewrites the whole family at `n′`: every member in the cache, then the members `--files` names, then `.head`. A changed path is cached where `.head` has its `file=` row. A path created or removed counts against `.paths`, where `.paths` is cached. A log created that an `absent=` key resolves to counts against that key.
- **Where `.head` is missing, or names another `n` or another generation,** it rewrites the whole family in the same way, since what is cached cannot be dated.
- **Its ends** [C3]: any end of form 3 but 0 is the class's failed read, never files mode. Y:4347 and Y:4886 give files mode to the prefetch only.
- **At `n′`** [O17, closed]. A primitive may run long after its prefetch, so form 3 reads at the current head `n′`, and keeps the cached family one snapshot by refetching it there (Y:4900). The guard decides one prompt on one snapshot within its 1 s budget, so its second read stays at the head it prefetched, `n`, as form 4 (Y:4160–4161). Each text holds in its own scope: lane 3's ruling, #165 6727201960.

**Form 4, the guard's second read** [Y, Y:4157–4162, for the read; D, O17, for the argv; B1 to B8 as lane 1 took them; lane 3's ruling on B4 and B7, #165 6738474549]:
- **argv:** `lanes-index register read --for guard --at <n> --pid <pid> --cache <file> --lane=<key>`. It is lane 1's argv (#165 6727229519), with lane 5's B1 and B2 (#165 6738431117) and L1-D1's `=` form. Lane 1's T113 builds it, with a case.
  - **`--at <n>`** is `.head`'s `n=`, which the guard passes. `--at <k>` keeps the one meaning this contract gives it, "at seq `k`", as in `register snapshot --at <k>` (§3.2) and `render --images --at <k>` (below).
  - **`--pid <pid>`** [B1] is the hook's own pid, `$$`, as in form 2. The client reads `T0` from it. Without it, "too late" (Y:4185) cannot be judged, and the timer can run past the hook's `"timeout": 5` (Y:4263, Y:4285–4286, Y:4942, Y:4271).
  - **`--cache <file>`** [B2] is `$SE_CACHE_FILE`, as every other form names its cache.
  - **`--lane=<key>`** is the key as `lane_log_events` received it, `$1`, and it takes any key, in the class or outside it (O18): Y:4956 "- **The key** is `$1` as received (SVC-8)". It may name another lane than the window's, or the window's lane in another case (Y:4157–4159).
  - It takes no `--repo`: it runs no form C (B4).
- **Called by** `lane_log_events` in `guard_run`, where `.log.<key>` is missing, or where the key is outside the class (Y:4956): Y:4157 "- A read whose cache file is missing (a binding that names another lane than the". [C9] In `guard_run`, the only cache files read are `.register`, `.aliases` and `.log.<key>`; a miss on the log is form 4.
- **The generation** [B3]. Form 4 takes `generation=` from `.head`. It ends 69 where `.head` is missing, or where the database's generation differs. §3.2 already refuses `--at` without `--generation`. An `--at <n>` above the database's head also ends 69, as a generation that differs does.
- **What it reads:** [Y] `<key>`'s log path, resolved case-insensitively in the images at seq `n`, never in the mirror: Y:4160–4161 "which resolves the log's path case-insensitively in the images at `n`, never in the mirror". [P] It reads in one snapshot, as one request (§2.3), with the guard's D9 row (§2.4).
- **Always directly, never through the service** [B7; lane 3, #165 6738474549]. The service's request carries only file names: register-service spec:59, and Y:4611 "Ruled: the request carries only file names". So the service cannot be asked for a log at `n`, and spec:59 stands. Y:4159–4160 "makes one more `register read`, direct if the flag file exists" carries lane 3's `research.md` erratum: "direct if the flag file exists" becomes "always direct, at `n`". Phase 1 has no service; this path bites at lane 5's T511 and T512.
- **Its cap** [B5] is `T0` + 3.75 s. It reads no declaration, so it has no declaration cap or floor.
- **What it writes:** that one log's `.log.<key>` and `.logpath.<key>` at `n`, each empty where no log resolves; or, for a key outside the class, `.log.%<hex>` and `.logpath.%<hex>` (O18). Where two logs differ from `<key>` only by case, it writes neither, and ends 69 (C11). It refetches nothing else, and writes no `.head` (B8).
- **Its cost** [B6] is one more `lanes-index` start, plus `psql`'s start, connect and query: about 83 ms at a round trip of 0, and about 882 ms at 100 ms (Y:4365–4366). This replaces Y:4161–4162's "about 72 ms", which counts the `lanes-index` start alone. The cap bounds it.
- **Its ends** [B4, lane 5's text word for word; O19, closed by lane 3, #165 6738474549]: "Form 4: 0 with the one log's files written. Every other end (2 for too late, 69, 78, 1, 64, a crash, or a 0 that wrote no `.log.<key>`) is a log that could not be read. The read site returns non-0, and the guard notes it as today and does not refuse. Form 4 writes no `.outage`, never opens the mirror path, reads no declaration, and caps at `T0` + 3.75 s." The guard's note today is at `code/lanes-edit.sh:9830–9831` and `:10707–10710`, at pin `2a057a6d`.
  - So form 4 fails open on every end but 0. The prefetch, form 2, keeps its "too late" refusal (Y:4269–4271).
  - For a key outside the class, `.log.%<hex>` stands for `.log.<key>`.
  - **For the record,** since the guard reads every end but 0 alike: where the mode is not primary, form 4 ends as form 2 does in that mode. In `mode index` it exits 0 with `mode index` and writes nothing, which is a 0 that wrote no `.log.<key>`. Under Q6(c)'s refusal it exits 78 (§2.7).
  - **For the record,** as lane 1 decided: an empty `--lane=` key ends 64, as any malformed argument does.
  - **For the record,** as lane 1 decided: an `--at <n>` below the generation's first seq ends 69, as one above the head does.
- **stdout:** its `mode` line, and on primary nothing more.

**stdout, in every form.** The first line is `mode index` or `mode primary`, Y:4345's words.
- In `mode index`, the call exits 0, reads nothing and writes nothing. So `reg_open` asks no second mode question (§3.1). Y:4345's reader column, "D3 asks first", holds where a caller already holds the answer: it does not call `register read` in `index` mode [P].
- On primary, forms 1, 2 and 4 print nothing more. Form 3 adds its `n=`, `changed=` and `refetched=` lines.

**stderr:** §2.6's lines. Under `--for guard`, also:
- `guard: direct (service unavailable)`, from form 2 only, where the service did not answer and the direct read did: Y:4342 "\| the service unavailable, the direct read succeeded \|";
- `lanes-index: too late to verify`, with exit 2.

Under `--for guard`, the first stderr line on a 69 is the cause the guard quotes (O15).

**The exits of forms 1 to 3** [Y:4336–4347, with P]. Form 4's ends are its own (above; B4).

| What happened | Forms 1 and 3 | Form 2 | The caller |
|---|---|---|---|
| text read, at `n` in forms 1 and 2 and at `n′` in form 3, through the service or directly | 0 | 0, files cached | parses |
| mode `index` | 0, `mode index` [P] | 0, `mode index` | today's files mode |
| `refuse db <cause>`, a failed direct read, or its timer | 69 | 69, with `.outage` where a declaration applies | a reader: its class's failed read (Y:4892–4898); the guard: SVC-12's check, otherwise 2 |
| too late to verify, Y:4344's row | — | 2 | the guard refuses 2 |
| Q6(c)'s refusal of `mode=primary` | 78 | 78 | the guard refuses 2; `reg_open` by class (T504) |
| a refused configuration, R09's included | 2 | 1 (O14) | forms 1 and 2: files mode with a warning (Y:4347; lane 5's T504); form 3: the class's failed read (C3) |
| usage | 64 | 64 | as a refused configuration |

- **Why 1 under `--for guard`.** There 2 means only "too late" (Y:4344), and Y:4139 makes a refused configuration files mode with a warning: Y:4139 "call (a refused configuration, a crash, exit 127, its timer) is files mode". So it cannot be 2. It cannot be told apart from a crash's 1, and the guard does not branch on it. Files mode applies to form 2, the prefetch, only; under form 4 a 1 is a log that could not be read (B4). O14 is closed (#165 6738431117). The guard reads any code outside 0, 2, 69 and 78 alike: Y:4347 "\| any other failure of the mode question or the prefetch \|".
- **On a SQL estate** files mode then refuses by Q21(a)(A), as the marker check does (Y:4140–4141), so the TLS refusal fails closed.

**The other readers' forms:**

- **`journal --since <m>`** [Y for the fields; P for the form]:
  - **argv:** `lanes-index journal --since <m>`.
  - **What it does:** one REPEATABLE READ READ ONLY snapshot, as REGISTER-WRITER, the renderer's role (Y:3459 "\| REGISTER-WRITER \| `register_url=`, `register_passfile=` \| `lanes-edit.sh`'s writes"), with the renderer's row of D9.
  - **stdout:** `format=1`, `generation=<uuid>`, `n=<head>`, then one `entry=<JSON>` line per entry above seq `<m>`, in seq order. Each object is compact, on one line, with the keys `seq`, `utc` (epoch seconds), `author`, `operator`, `message`, `edits` (`journal_edits`' rows, Y:1598's shape) and `files` (`[path, present, sha256]`, §3.2's canonical values).
  - **Exits:** 0; 69 for a failed read; 2 for a refused configuration, Q6(c)'s included (Y:2452); 64.
  - **Its user** is the renderer, in phase 3 (Y:4834's last column).
- **`journal --last --path <log> --grep <key> [--at <n>]`** [Y for the use and the flag's name, Y:4914; C7 for the match; C8 for `--at`]:
  - **Its user** is `swapped_lanes`' rank: Y:4914 "\| `swapped_lanes` \| :9074 (rank :9087–9091) \| none \| rank = head − `lanes-index journal --last --path <log> --grep <key>` (R2B S-14) \|".
  - **What it does:** one snapshot, as READER, with a reader's row.
  - **stdout:** `format=1`, `n=<head>`, and `seq=<k>`. [C7] "`seq=<k>` is the newest entry at or below `n` whose edits change the number of times `<key>` occurs, as a fixed string, in `<log>`: git's `-S` pickaxe, as today's rank reads it (`code/lanes-edit.sh:9087`, pin `2a057a6d`)." The key is never a regex. Where no entry matches, there is no `seq=` line, and the caller keeps 999999999 (`:9085`). The rank is `n` − `k`, both from one snapshot.
  - **`--at <n>`** [C8], optional. With it, `n` is the door's `n`, and `n=` prints it, so the rank keeps one snapshot per verb (Y:4890). Without it, `n` is the journal's own head, as in draft 3.
  - **Exits:** as `journal --since`.
- **`render --images --at <k> --out <dir>`** [Y for the form; P for the output]: Y:4835 "\| `render` \| `--images --at <k> --out <dir>`, the pure function; the image cache".
  - It writes the render set's images at seq `k` under `<dir>/<path>`. `<dir>` must not exist, or must be empty.
  - It prints `format=1`, `n=<k>`, and one `file=` row per image, in §3.2's canonical form.
  - It runs as REGISTER-WRITER, with the renderer's row. Its exits are 0, 69, 2 and 64.
- **`render --checkout <c>`** [Y for the fork; P for the answer]: the parent's fork, §3.7, Y:5377 "- **The renderer fork.** In the parent, after COMMIT, `( lanes-index render --checkout <c> & )`".
  - In phase 2, on a copy with the constant flipped, it answers "git half not installed" on stderr, and exits 69 [P]. Under Q6(c)'s refusal it exits 2 at load, before any answer (C13): Y:4872 "`load_config` (:248) refuses with 2 for any subcommand but `mode` and `register read`". The detached caller never reads either.
  - Phase 3's `--checkout`, `--until` and `--revert` are group C's (Y:4835's last column), and outside this contract.
- **`register snapshot --files <set>` or `--all`, with `--out <dir>`** [P; O3]: the images of Y:4831's "the images of `--files` or `--all` at `n`".
  - It writes those images at `n` under `<dir>/<path>`. `<set>` is render-set paths, comma-separated.
  - It makes §3.2's checks 3 and 4, with no identity flags. It prints §3.2's keys, and one `image=<path>\t<present>\t<sha256>` row per path.
  - **Exits** [C13]: 0; 2 for a refused configuration, Q6(c)'s refusal included, at load (Y:4872); 69 for checks 3 and 4, a database it cannot reach, or a failed read; 64.
  - It is the image cache's form. Group B takes images through `--tree`, and the readers through `register read`.

**No reader form returns an arbitrary render-set path** [C2]. Under option (a), which lane 1 prefers, T401's `register-text` answers `lanes/LANES.md`, `lanes/aliases.tsv` and `lanes/log/*`, and 2 for any other path. Option (b) would be a reader form, `register read --at <n> --path <p> --cache <file>`. The choice is lane 4's (O21). If lane 4 picks (b), draft 5 adds it.

**The flush helper** is `reg_flush` (lane 5's Q-L5-4 answer, #165 6726077268).
- It drops what Y:4958 lists: Y:4958 "- **The family.** `.log.<lane>` is the first per-lane sibling of `.register`". That is `.register`, `.aliases`, `.archive`, `.paths`, `.log.*` and `.logpath.*`. It also empties `LANES_REGISTER_CACHE`, as Y:4958 says and `code/lanes-edit.sh:11063` does at pin `2a057a6d` (C6).
- [P] It also drops `.head` and `.outage`, which are new. Its `.log.*` and `.logpath.*` take the `.log.%<hex>` names too. It keeps `.direct` (C6).
- **The post-bind refill** [C1] is form 1, `register read --prefetch --cache <file> --files register,aliases`, with a reader's row, never `--for guard`. It follows `reg_flush`: Y:4940 "- **The post-bind flush at `:11064`** drops the whole read family, `.log.*` included; on primary the refill is a second `register read`, never git."
  - It runs in `guard-bind`'s detached child, after `lane-start` (Y:5398), where no hook `T0` exists: Y:5399 "- **`guard-bind` is in the read door's `none` class.**" Under form 2's clamp it would be "too late" every time.
  - Y gives it a reader's timer: Y:5449 "| `guard_bound_check`'s read | `T_r` + `w_s` |".
  - At the pin, `guard_bound_check` reads only the register and aliases (`code/lanes-edit.sh:11063–11065`, `:11180`).
- After `committed`, the child's tail reads take D17's path through the read family that `reg_flush` drops (lane 4's Q11).

**Not settled here:** only O21, C2's (a) or (b), which is lane 4's (§8). Lane 5's review closed O14 to O18 (#165 6738431117), and lane 3's ruling closed O19 and O20 (#165 6738474549).

### 3.7 `lanes-index render --checkout <c>`

- The parent forks it after COMMIT, detached, with stdin and fds 3–9 closed and `LANES_NO_FETCH` removed: Y:5377 "- **The renderer fork.** In the parent, after COMMIT, `( lanes-index render --checkout <c> & )`".
- In phase 2 it answers "git half not installed", and group C makes it real.
- Group B relies only on its argv. A stub records argv and environment.
- Its answer and exits, which the detached caller never reads, are §3.6's.

---

## 4. The write id and idempotence [Y]

- **One id per write, reused by every attempt,** and UNIQUE in the journal: Y:2468 "Each write carries a `write_id`, UNIQUE in the journal, that every attempt of".
- **Where it comes from:**
  - `LANES_WRITE_ID` where the caller set it;
  - a new random 128-bit id that `lanes-index` mints where the caller set `new`, or where no pending record matches;
  - else the pending record's (Y:2468–2473).
  - [P] The mint is `register snapshot --write-id new` (§3.2). It is printed as a lowercase UUID, which `commit_write` takes as `uuid`.
- **Idempotence rests on three checks, in one order,** at the snapshot and under the head row's lock in `commit_write`:
  1. another generation is LR007 `generation`, 2, whatever the switch reads;
  2. this write's own entry is found (0 at the snapshot; LR006, 0 `already`, at the commit);
  3. another write's entry is LR007 `another-write`, 2.
  - Y:2556's step 1, "1. take the head row's lock, and refuse a generation not the switch's with LR007", and Y:1422–1435's SQL.
- **"This write's own"** means the same lane, verb and `args_sha256` (Y:1429–1433).
- **`args_sha256`** is the pending record's digest: Y:5171 "- **Where:** `<state>/register-pending/<d>`, where … `<d>` is the sha256 of `lanes-pending/1 NUL <LANES_LANE> NUL <LANES_SESSION> NUL <verb> NUL <arg1> NUL … <argN>`". `lanes-edit.sh` computes it and passes it. `lanes-index` never recomputes it.
- **No second commit, after the lookup.** Every commit call carries `not_after`, and `commit_write` raises LR008 past it (Y:1466 "9. **The bound (F3):**"). So once `journal` has held the lock with its check passed, no call of this write can still commit. One residual remains: a server clock that steps back (Y:1469 "- **The residual:**").
- **A 23505 on the journal's `write_id` is never reached,** and would be a bug, 69 (Y:1571).

---

## 5. What the caller may assume, and must not

**Group B may assume:**
- **Exit 0 means committed.**
  - From `register commit`, the write is in the journal at the printed seq.
  - From `register snapshot` with `found=`, or `journal` with `found`, it is this write's own entry, in this generation.
- **Exit 2 and 69 from `register commit` mean nothing was written by that call.** CO-6 makes this exact: both come only from a definitive refusal, or from an error raised before anything was sent (§3.3).
- **75 means that call did not commit.** LR008 included, it is "not committed" (Y:5266 "- **LR008 is never 3 or 69").
- **Each call is one request** and opens one connection, which it closes. No session survives the call (§2.1, §2.3).
- **The DETAIL tokens are the fixed words of §2.6,** and the SQLSTATE is the first line's.
- **The client timers are `lanes-index`'s own** (§2.4). A call ends by its timer plus one `lanes-index` start.
- **`not_after` is enforced on the server's clock** (LR008), and the lookup waits on the server's clock (Y:1468 "- **The wait:**").
- **`lanes-index` derives all structured rows and alias sets itself** (§3.3).

**Group B must not assume:**
- **That a non-0 end of the write means nothing was written,** where any commit call of the write was in doubt. It makes F3's lookup first (Y:5020; Y:5226's rule).
- **That `psql`'s or `register commit`'s message text is stable,** or carries a constraint's name. Branch on the exit code, the SQLSTATE and the token only (Y:1571).
- **That the snapshot's `clock=` is the host's time.** It is the database's, and the host's clock decides nothing (D13).
- **That exit 3 means written,** or that a connection failure on a commit means not written (Y:2620).
- **That a 55P03 at the snapshot is a lost turn.** It is a refused read, 69 (Y:2627).
- **That `register commit` retries a lost turn** (LR001, 40001, 40P01, 55P03, LR008). It does not: the attempt loop is `lanes-edit.sh`'s (Y:1551 "**The signals a writer gets.** The retry loop is the writer's").
- **That the image cache is right.** The digests and the head check decide (Y:2509).
- **That a URL or password appears in any argv,** or that it may print one (§2.2).
- **That the stdout keys come in a fixed order,** or that no key is added within `format=1`. It ignores keys it does not know.
- **That `lanes-index mode` asks the database.** It reads only the configuration (Y:3521).

---

## 6. What the fakes must do (FR-017)

**The fake `psql`,** first on `PATH` (§2.2), and the stub `lanes-index` that answers `primary`, stand in for the real ones in group B's tests.

**Where the stub goes:** where `lanes_index_bin` looks first, `$SCRIPT_DIR/lanes-index`, beside a copy of `lanes-edit.sh` with the files it reads from `$SCRIPT_DIR`, `repos.tsv` and `lane-start` (lane 4's block :92; lane 5's block :386) (O11).

**The stub `lanes-index`** implements §3.1 to §3.5 exactly:
- each command's argv, stdout, stderr's first two lines and exits;
- the attempt directory's check (§3.3);
- injectable outcomes: each exit, each LR code with each token, a hang past the timer, and "committed, then exit 3", which is agreement test 12's pair;
- on `journal --write-id`, a crash and an unknown code, each read as 3 (CO-2);
- for lane 5's tests, §3.6's four forms of `register read`: the cache family, the `.log.%<hex>` names included, `.head` and `.outage` as §3.6 writes them, and each exit, the guard's 2 and 1 included, with form 4's ends as B4 reads them.

**The fake `psql`:**
- records argv, environment and stdin;
- asserts §2.2's argv and that no argument holds the URL;
- answers the one request on stdin by its first statement;
- exits as `psql` does (§2.2);
- injects, on the commit call, exit 1, death by a signal, and an exit 0 whose answer cannot be read, each of which `register commit` must report as 3 (CO-6).

**The contract tests** run the same cases against the real `lanes-index` on lane 2's real-engine fixture once group A lands (lane 4's GB-1).

---

## 7. Lane 4's questions Q1–Q7 (group B plan draft, §9), answered

Lane 4's reply closes Q1, Q2, Q3, Q4, Q5 and Q7 against this contract, and lane 3 ruled Q6 (R04).

| Q | Lane 4 asked | Lane 1's answer |
|---|---|---|
| Q1 | `register snapshot`'s flags and stdout; the clock in epoch seconds? | The flags are taken, with one change: a new write passes `--write-id new` with its lane, verb and `args_sha256`, and the snapshot mints the id, since Y says `lanes-index` mints it. `act-begin` passes none. stdout is `key=value`, with `found=` and `file=` rows, plus `format=1`, `not_after=`, `schema=`, `repos_blob=`, `c=` and `n_a=` (§3.2). The clock is in epoch seconds: yes |
| Q2 | LR006 as `already <seq>`? stderr relays the `ERROR:` line and the DETAIL token verbatim? | Yes to both. `already <seq>` is followed by that seq's `file=` rows, so the tail's match needs no other call (§3.3). stderr carries the `ERROR:` line first and the `DETAIL:` line second, verbatim; the snapshot's own LR007 refusals take the same shape (§2.6) |
| Q3 | `journal`'s stdout, exits, and how it learns the estimate | `found <seq>` with rows, or `absent`, exit 0; 2 with LR007 and its token; 3 for LR011 or no lock, and for any other SQLSTATE [P]; in draft 2 also 3 for a refused configuration other than Q6(c)'s and for a crash (CO-2, CO-3). `--clock <epoch> --clock-age <s>` is taken (§3.4). In draft 3 a takeover with no snapshot of its own passes the record's clock (Q-L1-1) |
| Q4 | who writes the attempt directory's specification; is the layout acceptable; does `lanes-index` derive D19's alias sets and D18's spellings? | This contract fixes the files `register commit` reads (§3.3), and lane 4's layout holds the rest. `lanes-index` derives both from `tree/lanes/repos.tsv`. Additions: `renames.tsv` (O5), and the identity on argv |
| Q5 | a `lanes-index state-dir` verb, or a bash mirror with a hygiene test? | The bash mirror with a hygiene test, as lane 4 proposes: `${XDG_STATE_HOME:-$HOME/.local/state}/openRepoTools`, as `state_dir()` has it at `code/lanes-index:173–175` (pin `2a057a6d`). It costs no Python start. Lane 1 adds the hygiene test's `lanes-index` side |
| Q6 | `lanes-index register classify`? | Yes: §3.5, lane 1's T122, in group A, as R04 rules (O6) |
| Q7 | LR004's reload and retry is inside `register commit`, and group B never retries it? | Yes. What the reload re-reads under the scratch tree is §3.3's, as R02 rules (O2) |

---

## 8. Open points, and what closed them

Lane 4's reply (#165 6725230285), lane 3's rulings of 20:15Z (#165 6726556972), lane 3's O17 ruling (#165 6727201960), lane 5's review of §3.6 (#165 6738431117) and lane 3's ruling on B4 and B7 (#165 6738474549) closed or moved each point as the last column says.

| # | Point | For | Lane 1's proposal | What closed or moved it |
|---|---|---|---|---|
| O1 | The wire format as a whole (argv, stdout, stderr, the attempt directory's files), which Y leaves open | lane 4 confirms; lane 3 rules | this contract | **Closed by R01:** draft 1 is T101's basis. Lane 4 confirmed it (its §1 items 1–6 and §2's table), CO-1 to CO-6 are in, and CO-2 and CO-6 are lane 1's contract decisions [D] |
| O2 | LR004's "reloads every line once and retries" (Y:1567, Y:2614) predates the scratch tree. Resending images computed on a wrong base would be wrong | lane 3 | §3.3: retry only where the base re-read from SQL equals the tree's; else 69 naming the cache | **Closed by R02** (O2 = Q-L3-10): lane 1's form, with CO-4 for how the retry's own call is read |
| O3 | `register snapshot --files`/`--all` without `--tree` (Y:4831): where the images go | lane 1, in group A's plan | not a group B call | **Closed in §3.6** (R06): `--out <dir>`, with `image=` rows. Lane 4 confirmed it is not group B's |
| O4 | `LANES_WRITE_ID=<id>` without `LANES_WRITE_GENERATION` | lane 4 | `lanes-index` refuses 64 (§3.2); what `lanes-edit.sh` does before it is lane 4's | **Ratified by R08,** as lane 4's item 1 closed it: `lanes-edit.sh` takes the generation from the record that holds that id, and with none refuses 2 before any call. The 64 is a backstop (its T412) |
| O5 | Renames: `changes.tsv` comes from `--no-renames` (Y:5061), so a rename arrives as `D` plus `A`. `journal_edits` has `rename` (Y:1598 "--   edits          [{ord, op, path, line_no, old_sha256, new_text, to_path}]") | lane 4, then lane 3 | `renames.tsv` from `LOG_RENAMED_FROM`. Without it, `lanes-index` sends `remove` plus `create`, which renumbers the moved file's lines | **Ratified by R08,** as lane 4 accepted it (item 6) |
| O6 | `register classify` is not in Y's interfaces table | lane 3 | add it to group A (§3.5) | **Closed by R04** (O6 = Q-L3-9): lane 1's T122, `[O]`, in group A, with a parity test against `parse_register_line`; T424 lands after T122 |
| O7 | The second requests: LR006's file rows and D18/D19's holder line (§3.3). Each is a read after a refusal, on a rare path, with the snapshot's bounds | lane 4 confirms | as §3.3; a failure of either read leaves out only what it would add | **Ratified by R08,** as lane 4 confirmed it (item 5) |
| O8 | A crash of `lanes-index` (1, or 128+n) on a commit call | lane 4 | treat as in doubt (§2.7) | **Ratified by R08,** as lane 4 confirmed and widened it: to `journal --write-id` by CO-2, and to every `psql` end on the commit by CO-6, both lane 1's decisions (R01) |
| O9 | `not_after=`, `c=` and `n_a=` from the snapshot, so `lanes-edit.sh` parses no configuration | lane 4 and lane 5 | §3.2 | **Closed by R05:** `c`, `N_a` and `w_s` come from `lanes-index` (§2.5). `register read`'s `.head` carries them for lane 5's `bound_s`, through the bind record (T507; lane 5's open point 3). Y:5877's "from the configuration" (R05's X:5876) is a `research.md` erratum |
| O10 | `format=1` on key=value outputs | lane 4 | §2.8 | **Ratified by R08,** as lane 4 confirmed it |
| O11 | How `lanes-edit.sh` finds `lanes-index`, and so where the stub goes (lane 4's M9) | lane 5 (Q-L5-2) | lane 4's resolver; no objection from lane 1 | **Closed:** `lanes_index_bin`, `$SCRIPT_DIR/lanes-index` first, then `PATH`, as lane 4 defines it (its block :91) and lane 5 accepted it (Q-L5-2, #165 6726077268) (§2.1, §6) |
| O12 | The attempt directory's name | lane 4 | — | **Closed by lane 4's note:** `<state>/register-writes/<d>/<run>/<k>/` (§3.3) |
| O13 | `--generation` alone under `--at` | lane 4 | a form; so are the five flags; 64 without it (§3.2) | **Closed by draft 2** |
| O14 | `register read --for guard`'s code for a refused configuration, R09's included. Y gives 2 to "too late" (Y:4344) and makes a refused configuration files mode (Y:4139), so it cannot be 2 | lane 5 | 1 (§3.6) | **Closed by lane 5's review** (#165 6738431117): 1, with lane 5's row in §2.7. It cannot be told apart from a crash's 1, and the guard reads it as files mode with a warning, for form 2 only; under form 4 a 1 is a log that could not be read (B4) |
| O15 | H1's formats: `.head`, `--pid`, `.outage` and `--since` (lane 5's open point 19) | lane 5 | §3.6, with `file=` and `absent=` rows, `--lane`, `--repo`, and `--files`' members added | **Closed by lane 5's review** (#165 6738431117): H1 as taken, with lane 5's five changes: `w_s_ms=`; `.outage`'s label on one line; form C on `origin/$LANES_BRANCH`, with `mirror_tip=`; `.head`'s `c` the larger of the two URLs'; S3's outage text named, and the cause on stderr's first line (§2.5, §3.6) |
| O16 | `w_s` where this install has `serve` but the service did not answer the read. Y:5457 says "where the service runs" | lane 5 | the figure, not 0, since `bound_s` is an upper bound (§2.5) | **Closed by lane 5's review** (#165 6738431117), as written (Y:5443, Y:5457, Y:5459) |
| O17 | The guard's second read: at `n` (Y:4160–4161), or at `n′` with the family refetched where a cached path changed (Y:4900) | lane 3 rules; lane 1 picks the argv; lane 5 confirms | draft 3: at `n′`, as Y:4900 (form 3). Draft 4: form 4, `register read --for guard --at <n> --pid <pid> --cache <file> --lane=<key>` (§3.6) | **Closed** by lane 3's ruling (#165 6727201960), lane 1's argv (#165 6727229519) and lane 5's confirmation, with B1 to B8 (#165 6738431117): each text holds in its own scope. The guard's second read is form 4, at its prefetched head `n`, refetching nothing; form 3, at `n′` with the family refetch, serves every other primitive on primary |
| O18 | A log key outside `check_lane_name`'s class, which Y:4956 sends down the second-read path, and which cannot name a cache file | lane 5 | draft 3: one shared slot, never counted as cached. Draft 4: lane 5's design, `--log-key=<key>`, with `.log.%<hex>` and `.logpath.%<hex>` (§3.6) | **Closed by lane 5's review** (#165 6738431117), which objected to the one shared slot: `--files` is comma-separated, and two concurrent second reads would overwrite it (Y:4127–4129, Y:4890). Forms 1 and 2 skip a key outside the class and refuse nothing; form 4's `--lane=<key>` takes any key |
| O19 | Form 4's "too late": noted as a log that could not be read, as lane 5's B4 has it, or a 2 that refuses the prompt | lane 3 | lane 5's B4 text (§3.6) | **Closed by lane 3's ruling** (#165 6738474549): B4 word for word, "too late" included. Form 4 fails open on every end but 0, and the prefetch, form 2, keeps its "too late" refusal (Y:4269–4271) |
| O20 | Form 4 never goes through the service (B7), against Y:4159–4160's "direct if the flag file exists", since the service's request carries only file names (register-service spec:59; Y:4611) | lane 3 | record the departure in `research.md`, or amend spec:59 | **Closed by lane 3's ruling** (#165 6738474549): form 4 always reads directly, and spec:59 stands. Lane 3's `research.md` erratum on Y:4159 makes its "direct if the flag file exists" read "always direct, at `n`". Lane 3 adds that lane 5 brings the point back if T511 and T512 show that path often running out of budget, and that a request naming `n` would be a spec change, for Brett |
| O21 | C2: no reader form returns an arbitrary render-set path. (a): T401's `register-text` answers `lanes/LANES.md`, `lanes/aliases.tsv` and `lanes/log/*`, and 2 for any other path. (b): a reader form, `register read --at <n> --path <p> --cache <file>` | lane 4, for T401 | (a), lane 1's preference (§3.6) | **Open,** new in draft 4. If lane 4 picks (b), draft 5 adds the form |

---

## 9. What X, and so Y, changed from W, for this contract

X replaced W's Azure texts with CNPG in the codeXfactory prod install, and folded lane 1's CNPG texts. Y, X's child, keeps them, and changes only SVC-5's build order. Neither changed this contract's argv, stdout, stderr or exits.

| Item | In this contract | What X, and so Y, changed from W |
|---|---|---|
| Hosting | §2.5's `register_url=` host | the install's DNS name for the public `LoadBalancer` in front of the Cluster's primary, port 5432 (Y:2324). Never through a pooler: Y:1575 "Every register connection reaches the server directly, at the primary's own endpoint, never through a pooler." This replaced W:1556's session-mode allowance (C13; R51, R60) |
| Logins | §2.5's `register_url=`, `register_passfile=`, `reader_url=` and `reader_passfile=` | `lanes_w_<engineer>` and `lanes_r_<engineer>`, under "Two logins each" (Y:2326, replacing W:2307) |
| TLS | §2.2's `PGSSLMODE` and `PGSSLROOTCERT`; §2.5's TLS refusal | `sslmode=verify-full`, with `sslrootcert=` the issuer's root file, never `system` (Y:2324). FR-011 is enforced at `ready`'s check 11 and by the server's `hostnossl` reject (Y-ops:454, Y-ops:144) |
| The lookup | §2.4, §3.4 | `T_L` = `N_a`·`c` + 7 s, 9 s at `c` = 2, D9, for W's "8 s timer" (Y:1468, Y:1569); the D9 row's failover became a restart (Y:3524; lane 4's X1) |
| `c` | §2.4, §2.5 | `t_c` is the p90 of the cold connects, not the median (Y:5877) |
| Keepalives | none | the server's TCP keepalives, set by the install, not by any URL key (Y:3997) |
| The service | §3.6 | built beside phases 1 and 2, not last (Y:4826). Y carries it into SVC-5: Y:3951 "readers stay exactly as today. The service is built beside groups A and B, not" (Q29; Brett, #165 6726766577) |
