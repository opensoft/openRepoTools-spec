# Contract: the register service's socket API

- Owner: lane 5 (R50). Tag: [L5]. Feature 007, `contracts/socket-api.md`, which tasks.md's T511 writes.
- Built on by T512 (`lanes-index serve`, with `register read` as its client) and T513 (the `SessionStart` block's `lanes-index serve --start`). It is the source for lane 3's T341, "the socket API" in the manual's part 2 (tasks:1382).
- Status: draft 1, written by lane openRepoTools-5 on 2026-11-09, under lane 1's review (U5). It lands no earlier than the spec PR, and before T512 (tasks:1378).
- In one line, as the manual will say it: the service answers the register's text at `n`, and the reader decides (svc:286; Y:4093–4100).
- What it fixes: where the socket lives and how both ends check it, who may connect, what a request carries, the three answers, the limits, how the service connects, how it starts and stops, `serve --start`, and the client's side as far as the socket reaches. Section 13 lists what the sources leave open or say two ways. Nothing here adds a value the sources do not give.

## 0. Basis, and how to read it

| Input | sha256 | Lines | Cited as |
|---|---|---|---|
| The branch's `openspec/changes/add-sql-register/design.md` at `722078c9` (lane 3's `007-sql-register`, fix round B), byte-identical to Y's `design.md` as lane 1's draft 4.4 keys it | `35ca5179d5e9e47d…` | 6,745 | `Y:<n>` |
| The branch's `specs/register-service/spec.md` at `722078c9`, newer than Y's (`a416df1d…`, 293 lines): it carries lane 3's B4 | `9da1fa8d0523a82b…` | 303 | `svc:<n>` |
| The branch's `test-plan.md` at `722078c9` | `8702b70a799001f1…` | 9,145 | `tp:<n>` |
| W `bb08d080`'s `design.md`, which the task line's `W:<n>` cites | `76d6a4a030e0ddc4…` | 6,636 | `W:<n>` |
| W's `specs/register-service/spec.md` | `11a6ee5886c11e6f…` | 293 | `Wsvc:<n>` |
| Lane 5's texts for commit X, `TEXTS-lane5-X-20261108T1809Z.md`; text 13 above all | `f29328f28139bffd…` | 264 | text 13 |
| Lane 5's text 8″, `TEXTS-lane5-X-8pp-20261108T1839Z.md` | `e7a34f164124f207…` | 44 | text 8″a, 8″b |
| Lane 1's contract draft 4.4, `contracts-register-cli.v4.4.md` | `8600078b571b36ae…` | 963 | `CLI44:<n>` |
| Lane 3's rulings memo, R01 to R61, `MEMO-007-rulings-20261108.md` | `b2184b1adae0e060…` | 839 | `R<nn>`; `M:<n>` for its lines |
| Lane 3's ruling on B4 and B7, #165 6738474549, as saved in `read-6738474549.md` | `6b798557b61d0cef…` | 31 | B7 |
| Lane 5's block v5, `lane5-tasks-block-v5.md` | `5d427c42b2a2a7bf…` | 839 | `L5:<n>` |
| `tasks.md` at `722078c9` with lane 5's 43 fold edits applied, `tasks.md.lane5-722078c9` | `e3c81f8e19cc3d8c…` | 1,779 | `tasks:<n>` |
| 007's `research.md` at `722078c9` | `80b3a0f1f4fcf7c4…` | 663 | ERR-04, DEC-15 |
| 007's `spec.md` at `722078c9` | `003348040d618a2c…` | 1,589 | FR-057 |

- **The branch governs W.** Where the branch's text for a passage differs from W's, this contract follows the branch. Section 12 lists each such passage it uses. Passages were found by their words; a `W:<n>` is given where the task line cites W.
- **`Y:<n>` is a line of the branch's `design.md`.** That file is byte-identical to Y's, so a `Y:<n>` here is the line lane 1's draft 4.4 cites by the same number.
- **`CLI44:<n>` is also draft 4.2's line.** Draft 4.2 is the feature's `contracts/register-cli.md` (`dea51e43…`), and drafts 4.3 and 4.4 changed text only inside existing lines (CLI44:5).
- **Whose rulings.** R36, R37, R44, R50, R51 and B7 are lane 3's rulings, not Brett's. Brett's own answers appear only where svc or the task line cites them, with their comment.
- **#165** is opensoft/openRepoTools#165, and a number after it is a comment id.
- **Names.** The service is `lanes-index serve`; the documentation and its log call it `lanes-register` (Y:3835–3842). Its client is `lanes-index register read` (Y:3843–3848). "The client" below means `register read`'s socket path, and "the guard" means `lanes-edit.sh guard`.

## 1. What the socket serves, and to whom

- **One service per environment,** a bench container or a host shell (Y:3701; tasks:1390). It connects as its engineer's reader login, `lanes_r_<engineer>`, a member of READER, which the environment's `lanes-index.conf` names (Y:3708–3713).
- **What it answers:** the register's text at the journal's head `n`, read fresh for each request. The reader runs today's parser on that text in its own process, and decides (svc:10; Y:4093–4100). The service answers no question by key (Y:4093–4095).
- **Its clients** are `register read`'s socket path, "every reader's path" (Y:3843–3845), and the guard's prefetch, `register read --for guard --prefetch` (Y:3845–3848, Y:4127–4133). In lane 1's terms these are forms 1 and 2 of `register read` (CLI44:655–660). Form 3 is open (OP-7).
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
- **Never used, never repaired.** A directory that fails the check is not used: "the client reads directly, the service exits 2 naming it, and `lanes-index status` reports it" (Y:3764–3766). Where its stamp would go is OP-9.

### 2.3 The files

- **Five files,** all in `run/`: `register.sock`, `register.lock`, `register.owner`, `register.failed` and `register.log` (Y:3767–3768).
- **The bind:** the service binds under `umask 077`, and the directory's check is what stops another user (Y:3768–3770).
- **Nothing secret in them.** `register.log` and every `refuse db <cause>` carry fixed phrases and the SQLSTATE only, never `psql`'s message text and never a URL (Y:3797–3799). The one label the service may name is `where`, with any userinfo removed (Y:3816–3818).

### 2.4 The owner file, `register.owner`: install, namespace and boot

- **It holds** the service's pid, its start UTC, its install's identity, and on Linux its pid namespace (`readlink /proc/self/ns/pid`) and the boot id (`/proc/sys/kernel/random/boot_id`) (Y:3782–3784). The install's identity is the size, mtime and inode of the service's own `lanes-index` file (Y:3915–3916).
- **Another namespace or boot.** A client whose own pid namespace or `boot_id` differs treats the service as unavailable (Y:3784–3786; tasks:1393). Whether it then starts one is OP-8.
- **Another install.** A client that finds an owner file from another install starts a service (Y:3881–3883). The old service answers its next request `unavailable restarting`, and exits (Y:3915–3918).
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

- **It names the files the client wants, and nothing else** (Y:4036–4037; svc:59; tasks:1416): `lanes/LANES.md`, `lanes/aliases.tsv`, `lanes/log/<lane>.md`, "and the rest of the render set" (Y:4036–4037).
- **The guard's request** names `lanes/LANES.md`, `lanes/aliases.tsv` and `lanes/log/<window name>.md`, the log of the lane the window is normally bound to (Y:4142–4144; CLI44:666).
- **It never carries** a session id, `cwd`, agent id, prompt, window, live record or liveness fact (svc:59, svc:61–63; tp:2045–2047).
- **It never carries `n`,** nor a generation: "the request carries file names only, ... So it cannot be asked for a log at an older `n`, and form 4 never asks it" (tasks:1416; B7). So the guard's second read, at the `n` it prefetched, always reads directly, never asks the socket and never reads `.direct` (B7; CLI44:747). svc:59 stands.
- **B7's condition stands with it:** "if T511's and T512's measurements show that path often running out of budget, lane 5 brings the point back; a request naming `n` would be a spec change, for Brett" (tasks:1384; B7). This contract measures nothing; T512's tests do.
- **Not fixed here:** the request's bytes (OP-3); how a set the client cannot name is asked for (OP-5); a log named in another case (OP-6).

## 5. The answers

The service gives one of three answers to a request, and the client turns each into an exit (section 5.4). "`unavailable` never leaves the client" (Y:4336–4337).

### 5.1 The text at `n`, with digests, `n` and the generation

- **One snapshot per request.** For each request the service runs one transaction at REPEATABLE READ READ ONLY. It reads the journal's head `n`, the requested files' digests at `n`, and, in the same transaction, any journal entries or whole files it needs (Y:4036–4041; tasks:1410). Its statements, from `BEGIN ISOLATION LEVEL REPEATABLE READ READ ONLY` to `COMMIT`, are joined with psql's `\;` and sent as one query, so a request is one round trip (Y:3985–3988).
- **The generation, from the same snapshot.** The answer carries the register generation that transaction read, beside `n`. Y's interfaces table gives `register read` "text at `n` with digests, and the `n` and the register `generation` it read, in the same snapshot" (Y:4833), and the task line asks for "`n` and generation". The client writes them to `.head`'s `n=` and `generation=` (CLI44:694–695). Form 4 takes its generation from `.head` (CLI44:745), and the offer's `act` is built from that `n` and generation (L5:364). SVC-7's own words give the answer's head as `text <n>` alone (Y:4064–4066): see OP-2.
- **Fresh at `n`.** It answers `text <n>` only for that transaction's `n`, so the files of one answer are never from two seqs (Y:4064–4065). It "never answers from images it read before" (Y:4088; svc:23).
- **Where the images come from** (Y:4043–4050):
  - images it holds in memory with the digests at `n` are answered;
  - otherwise it brings them to `n` with the journal entries read in that transaction, by the function the writers' snapshot uses, and checks them against the digests;
  - on a mismatch it takes the file from the per-environment image cache, where the cache holds it at the digest at `n`;
  - otherwise it answers `unavailable`, and fetches the file whole in the background, in a transaction of its own, never on a request's timer (OP-15).
- **The write-through.** What it fetched goes to the per-environment image cache, the one cache the writers' snapshot reads (Y:4054–4062; tasks:1418).
- **Its contents:** `text <n>`, then for each file its path, its byte count, its digest and its bytes (Y:4065–4066). The digest is the database's at `n` (Y:4070–4072): the `sha256` of `journal_files` (Y:2394), which `.head`'s `file=` rows also carry (CLI44:699). The bytes are OP-3's.
- **The client's checks.** It checks each file against the digest in the same answer, which catches a cut or corrupted transfer (Y:4072–4074). Then it writes the cache family at `n`, each file in the form its read site expects, `.head` last (Y:4142–4151; CLI44:675–688), and exits 0 (Y:4341).

### 5.2 `refuse db <cause>`

- **Only where the failure is definitive in this request:** "the connection refused, an authentication error, the switch not `primary`, the schema version refused", with a fixed cause and the SQLSTATE (Y:4082–4085). Two cause words are named, `auth` and `unreachable` (Y:3931–3932). It carries fixed phrases and the SQLSTATE only, never `psql`'s message text (Y:3797–3799).
- **The client refuses exactly as the direct read would** (Y:4085–4086), and makes no direct read after it. `register read` exits 69, in form 2 too (Y:4343; CLI44:775). The guard then makes SVC-12's declared-outage check, and otherwise refuses with 2 (Y:4343; svc:23).
- **Not fixed here:** the words for the switch and the schema version, how the switch at `files` is told apart, which a rollback's declaration needs, and what the client prints as the cause (OP-4).

### 5.3 `unavailable`

- **When the service answers it:**
  - on any timeout (Y:4086–4087);
  - at its per-request timer, as `unavailable slow` (Y:4012–4013);
  - before its `psql` child has connected, after a restart (Y:4014–4016);
  - on a cache miss the image cache cannot fill (Y:4046–4049);
  - when its install has changed, as `unavailable restarting` (Y:3916–3918).
- **What the client counts as `unavailable` too:**
  - no socket (`ENOENT`), or a dead one (`ECONNREFUSED`) (Y:3881–3882);
  - a failed directory or length check (Y:3764–3765, Y:3778–3779);
  - an owner file of another pid namespace or `boot_id` (Y:3784–3786);
  - a server of another uid (Y:3792–3794);
  - a service that accepts a connection and never answers (svc:117);
  - "a bad header, a byte count that does not match, a digest that does not match, a file it did not ask for, an answer over 16 MB, or one not complete within the 300 ms share" (Y:4076–4078).
- **What the client does.** It discards all of the answer and reads directly (Y:4079), within what its budget has left (Y:4087–4088), and writes `.direct` (Y:4153–4154). The guard's client prints the fallback line where that direct read succeeds (section 11.2).

### 5.4 From answer to exit

The client's exits through the socket, from Y's exit mapping (Y:4336–4343) and lane 1's forms (CLI44:769–779):

| What happened | A reader's `register read` | `--for guard`, form 2 | The guard |
|---|---|---|---|
| the text at `n`, through the service | 0 | 0, files cached | parses; decides 0 or 2 |
| `unavailable`, then a direct read that succeeded | 0 | 0, files cached, and `guard: direct (service unavailable)` on stderr | parses; decides 0 or 2 |
| `refuse db <cause>`, or `unavailable` then a direct read that failed | 69 | 69 | SVC-12's declared-outage check; otherwise 2 |

The other rows (too late, mode `index`, Q6(c)'s 78, and any other failure) are not the socket's, and stay as Y:4344–4347 and CLI44:769–782 give them.

## 6. The limits

### 6.1 The client's wait: 300 ms

- **300 ms, connect and answer together,** or the service's timer plus 100 ms where SVC-6 raises that timer (Y:4182; tasks:1412). So it is 300 ms with no `register_rtt_ms=`, or one of 100 or less, and `register_rtt_ms=` plus 200 ms above that (CLI44:301): 350 ms at 150 (tp:1966–1971), and 656 ms at Eagle's p90 of 456 ms (Y:4193–4195).
- **At its end** the client stops, discards what it read, and reads directly: an answer "not complete within the 300 ms share" is `unavailable` (Y:4076–4079). A silent service costs one such wait a prompt, "never one per read", because the first fallback writes `.direct` (Y:4153–4156).
- **It is one share of the guard's 5 s,** spent once (svc:124; Y:4182). The direct read after it has `T0 + 3.75 s − now` (Y:4184), and a read through the service that completes after `T0 + 3.75 s` is too late, as any read is (Y:4185).
- **A reader waits the same** `w_s`, at most once a read process (Y:5443).
- **The guard never waits for a start** (Y:3921–3925; section 8.2).

### 6.2 The service's timer: 200 ms

- **200 ms,** or `register_rtt_ms=` plus 100 ms where `register_rtt_ms=` is over 100 ms; with none, or 0, it is 200 ms (Y:4001–4002, Y:4008; D9's row, Y:3519).
- **`register_rtt_ms=` is a p90:** "the p90 of act 3's `select 1` round trips in one session, taken while the workstation is at work and written when it joins, as `c` and `N_a` are" (Y:4005–4008; text 8″a). Act 3 takes "the median, and the p90, which is `register_rtt_ms=`" (Y:5915; text 8″b). Text 8″ changed only how act 3 measures the input, not the timer's rule (tasks:1408).
- **Inside the client's wait,** so the service answers `unavailable` before the client gives up (Y:4183).
- **The server's bounds** per request, set in `PGOPTIONS`: `lock_timeout` 150 ms and `statement_timeout` 180 ms, so that the server cancels its own query just inside the 200 ms timer (Y:4017–4020, Y:3976).
- **On expiry** the service sends `SIGINT` to `psql`, which cancels the query, and answers `unavailable slow`. If `psql` has not printed the request's marker within 100 ms more, the service kills it; the next request starts a new one in the background, and is answered `unavailable` until it connects. "A reconnect never runs on a request's time" (Y:4012–4016).
- **From the request's arrival.** AT-10 reads `unavailable slow` "no earlier than 200 ms after the request reached it", and no earlier than 250 ms at `register_rtt_ms=150` (tp:1966–1970).
- **Not yet shown:** "the 150, 180, 200 and 100 ms values are the design's, for lane 2's fixture to prove" (Y:4026–4029).

### 6.3 16 MB

- **The client reads at most 16 MB,** and stops at its wait, whichever comes first (Y:4079–4080; tasks:1414).
- **An answer over 16 MB is `unavailable`:** the client discards all of it and reads directly (Y:4076–4079).
- **For scale:** the real `lanes/LANES.md` is 2,590,927 bytes (Y:4050), and a 1.35 MB answer took 2.35 ms median from the connect to the last byte (Y:4066–4068).
- **Not fixed here:** what the 16 MB counts, and in which unit (OP-3).

## 7. The service's connection

- **One long-lived `psql` child on pipes,** logged in as the reader login, run as `psql -X -q -A -t -v ON_ERROR_STOP=0 -v VERBOSITY=sqlstate` (Y:3971–3973). No database driver, and `dependencies = []` stays (Y:3979–3981; svc:34). This is the service's own argv, not the one-shot argv of `lanes-index`'s register commands (CLI44:205–206).
- **Its credential:** `reader_url=` and `reader_passfile=` (Y:3460; CLI44:277), read from the 0600 configuration and its password file (Y:3889–3891).
- **Its connection is in its environment, never its argv.** The URL's host, port, database and user, and its `sslmode`, `sslrootcert`, `sslcert`, `sslkey`, `target_session_attrs` and `connect_timeout`, go to `PGHOST`, `PGPORT`, `PGDATABASE`, `PGUSER`, `PGSSLMODE`, `PGSSLROOTCERT`, `PGSSLCERT`, `PGSSLKEY`, `PGTARGETSESSIONATTRS` and `PGCONNECT_TIMEOUT`; `PGPASSFILE` and `PGOPTIONS` are the service's own (Y:3799–3812). A password in the URL, or any other query parameter, makes `serve` exit with the stamp `config-refused`, and clients read directly (Y:3812–3815).
- **The connect: `c` for each address.** `PGCONNECT_TIMEOUT` is `c` for each of the endpoint's `N_a` addresses (Y:3974–3975; text 13, at Y:3923–3925; D9's row, Y:3519), so one connect may take `N_a`·`c` (Y:3507).
  - `c` is the URL's `connect_timeout=`, the connect bound each workstation writes from its own p90 when it joins; with none it is 2, and a value under 2 is refused (Y:3810–3812, Y:5877).
  - `N_a` is `register_addresses=`, default 1 (Y:5877).
  - On register paths `PGCONNECT_TIMEOUT` is set to `c`, not defaulted, so an inherited value cannot lengthen it (Y:4970).
  - Y:3892 (W:3857) still says the service "overrides with 2": OP-1.
- **In the background only.** The child starts at the service's first request and restarts lazily after any failure (Y:3971–3973). Its connect runs "outside any share, so that no request waits for it" (svc:124; Y:4024–4025).
- **Where it connects:** the primary's port 5432, directly, through the install's load balancer, never through a pooler (Y:3976–3979; text 15; R51).
- **One query at a time,** one round trip a request. The service handles connections concurrently, but each query, ended by an `\echo` of a per-request marker, goes to the one `psql` in turn (Y:3982–3988).
- **The idle check.** While no request comes, the service sends `SELECT 1` on the held connection every 60 s, under the same timer, and a failure restarts `psql` in the background (Y:3993–3995). So the connection is never idle for the 4 minutes after which the load balancer resets a flow (Y:3995–4000; text 16). Section 10 gives the test-only interval.
- **What it never does:** write, render or re-index; read the mirror or the outage declaration (Y:4030–4032); read a pid, a `/proc` entry of a lane, a session record or tmux (Y:4436–4437).

## 8. The start on demand

No supervisor of its own: no systemd and no launchd (svc:41; Y:3872–3876).

### 8.1 Who starts it

- **The `SessionStart` block,** on a primary workstation, after its own answer, without waiting, through `serve --start` (Y:3878–3881; section 9).
- **Any client** that finds no socket (`ENOENT`), a dead one (`ECONNREFUSED`), or an owner file from another install, unless a failure stamp is fresh (Y:3881–3883). Item 1 also names an owner file from another namespace, which SVC-2 contradicts (OP-8).
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

- **`serve` refuses with exit 2 unless the configuration says `primary`** (Y:3948–3949; tasks:1402), with the stamp `config-refused` (Y:3928–3929).
- **Under Q6(c)'s refusal,** `load_config` "refuses with 2 for any subcommand but `mode` and `register read`" (Y:4872), so the refusing build's `serve` exits 2, with no departure from Y (R36; tasks:1403).
- **So it is dormant on every real host** until T328 lifts that refusal. Its behaviour is tested on lane 2's flipped copy of `lanes-index`, L2-0d (tasks:1404; Y:3951–3956).
- **Clients ask the socket only where the mode answer is `primary`** (Y:3948–3950; L5:405).

### 8.6 The failure stamp, `register.failed`

- **What is stamped:** only the causes that make `serve` exit before it listens: `path-too-long`, `dir-check`, `config-refused` (a refused configuration, a mode other than `primary`, or a URL parameter SVC-2 does not map) and `no-psql` (Y:3926–3929).
- **What it holds:** the UTC and that fixed cause, and no other text (Y:3929–3931).
- **What is not stamped:** a database that refuses or does not answer (`auth`, `unreachable`, a refused schema version). The service listens, answers `refuse db <cause>` or `unavailable`, and reconnects in the background (Y:3931–3934).
- **Who reads it:** a client, only after `ENOENT` or `ECONNREFUSED`; a stamp younger than 60 s means it starts nothing and reads directly (Y:3934–3936; tasks:1400). And `lanes-index status`, which reports a stamp and its cause (Y:3939), in T512's lines in `cmd_status` (R44; tasks:1419).
- **When it goes:** the service removes any stamp once it listens, and again at its first successful connect (Y:3936–3938).
- **Two causes are open:** `dir-check` (OP-9) and `no-psql` (OP-10).

## 9. The start-only form: `lanes-index serve --start` (R50)

- **What it does:** it runs the starter of section 8.2 and returns at once, so the bash `SessionStart` block detaches nothing itself (tasks:1401; R50, M:714–720; Y:3892–3893).
- **What it does not do:** ask the service anything, or wait for it (svc:93; Y:4493–4499; tasks:1436).
- **What it runs** is "the start a client runs on `ENOENT`" (Y:4498–4499): the length and directory checks of section 2, the stamp of section 8.6, then the start. A live service is left as it is: the `serve` it starts finds `register.lock` held, and exits 0 (section 8.3).
- **Its caller** is T513's block, on a `mode=primary` workstation, after its own answer, without waiting (svc:93; tasks:1432). The block exits 0 always (svc:93), so it never branches on `serve --start`'s exit; R36 reads the block the same way (M:594).
- **Under Q6(c)'s refusal** it exits 2 at load, as every subcommand but `mode` and `register read` does (Y:4872). Before group C no real host is `primary`, so the block's start never fires there (tasks:1437).
- **Not fixed here:** its other exits, and whether it tries the socket before it starts `serve` (OP-13).

## 10. The two test-only variables (R37)

- **`LANES_REGISTER_SERVE_IDLE_CHECK_S`:** the interval of section 7's `SELECT 1`, default 60 (tasks:1421; Y:3994).
- **`LANES_REGISTER_SERVE_IDLE_STOP_S`:** section 8.4's idle stop, default 900 (tasks:1422; Y:3912).
- **Test-only** (R37; research.md DEC-15). They shorten AT-10's idle-check case and the idle-stop guess (tasks:1428).
- **Honoured only where set.** Each names whole seconds. A value that is not a positive whole number is ignored, and the default kept, so each has a floor of 1 s (tasks:1423; R37, M:603).
- **The idle check's cap:** below 240 s, the load balancer's idle reset. A value of 240 or more is ignored, and 60 kept (tasks:1423; R37; DEC-15; Y:3995–3997). The idle stop has no cap.
- **The starter keeps both** in its reduced environment where they are set, or the service never sees them (tasks:1427; R37; DEC-15).
- **R37 says "refuse" where T512 says "ignored":** OP-14.

## 11. The client's side, as far as the socket fixes it

### 11.1 One client

- **`lanes-index register read` asks the socket first,** and reads directly when the service is unavailable (Y:3843–3845; tasks:1394). "One socket client, one fallback, one set of timeouts" (Y:3847–3848).
- **Phase 1's `register read` has only its direct path.** The socket, the `.direct` flag and the write-through come with `serve` (Y:4868–4870; CLI44:653). Phase 1 fixes what `serve` must also return: "the files at `n` with per-file digests, written by the client into the cache files" (Y:4870).
- **By form** (CLI44:655–662): form 1, the readers' prefetch, asks the socket; form 2, the guard's prefetch, asks it; form 3 is OP-7; form 4 never asks it (B7).

### 11.2 The fallback, and its line

- **The fallback is the direct read** (svc:104; Y:3843–3845). On `mode=primary` it reads the database, never the mirror, except under a declared outage, whose three conditions the client checks, never the service (svc:104; Y:4441–4443).
- **The guard's fallback line,** on stderr, exactly:

  ```text
  guard: direct (service unavailable)
  ```

  `--for guard` sets it (Y:3845–3848). Form 2 prints it, and only where the service did not answer and the direct read did (CLI44:764; Y:4342). See OP-11.
- **Test 10's comparison.** Through the service and through the direct read, the guard's stdout, its stderr less this one line, and its exit code are equal, for `ok`, `refuse` and `offer` (svc:86–90; tp:1912–1916).

### 11.3 The service-down flag, `.direct`

- **A file,** `$SE_CACHE_FILE.direct`, written by the first fallback in the invocation. Every later read in that invocation, inside any subshell, finds it and goes straight to the direct path (Y:4153–4156; tasks:1413).
- **Form 4 never reads it** (B7; CLI44:688). `reg_flush` keeps it (CLI44:815).

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
- **Where text 13 stops.** Text 13 replaced SVC-4 item 6 (W:3888–3890) only. Item 2's "which the service overrides with 2" (W:3857) stands at Y:3892: OP-1.

## 13. Open points

For lane 1, the U5 reviewer, and lane 3, unless a point names another lane. Each names its sources. Where the contract must take one reading to be whole, it says which, and the point stays open.

### OP-1. SVC-4 item 2 still gives the service's connect as 2 s

- Y:3892 (W:3857): "`_env` also defaults `PGCONNECT_TIMEOUT` to 5, which the service overrides with 2."
- Against it: text 13, at Y:3923–3925; SVC-6, Y:3974–3975; D9's row, Y:3519; Y:4970; and Y:5877, "`PGCONNECT_TIMEOUT` is set to `c`". Text 13 replaced W:3888–3890, not W:3857.
- This contract: `c` for each of the `N_a` addresses (section 7), as the task line has it.
- For lane 3: an erratum on Y:3892, in `research.md`, or in `design.md` before the archive.

### OP-2. Where the generation sits in the answer

- Y:4064–4066 gives the answer as "`text <n>`, then for each file its path, its byte count, its digest and its bytes", with no generation.
- Y:4833 gives `register read` "the `n` and the register `generation` it read, in the same snapshot". `.head` carries `generation=` (CLI44:695), form 4 checks it (CLI44:745), and the offer's `act` carries it (L5:364).
- This contract: every text answer carries the generation read in its own transaction (section 5.1). Its place in the bytes is OP-3's.
- For lane 1 to confirm, and lane 3 to say whether Y:4064–4066 takes an erratum.

### OP-3. The bytes on the socket

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

### OP-4. `refuse db`'s causes

- Y names the definitive failures (Y:4082–4085), but only two cause words, `auth` and `unreachable` (Y:3931–3932): none for the switch not `primary`, or a refused schema version.
- The client needs the switch's word to tell "the switch at `files`", since a rollback's declaration counts only where the read found the switch at `files` (Y:4449–4453).
- It also needs a line for the 69's first stderr line, "the cause the guard quotes" (CLI44:767), while the service sends no `psql` message text (Y:3797–3799).
- And the connect runs only in the background, and a request before it connects is `unavailable` (Y:4014–4016). So it is not said when "the connection refused" or "an authentication error" is "definitive in this request" (Y:4082–4084), rather than a past background attempt's outcome, which svc:23's "never answer from what it read before" may exclude.
- For lane 1 and lane 3.

### OP-5. Sets the client cannot name

- The request names files (Y:4036–4037; svc:59). Form 1's `--all` (every log at `n`), its `archive` member (`lanes/archive/*`) and its `paths` member (the render set's paths present at `n`) are sets whose members the client cannot know before the read (CLI44:665, CLI44:680–681).
- How they are asked of the service, under "file names only", is not said.
- For lane 1 and lane 3.

### OP-6. A log named in another case

- Form 2 asks for `lanes/log/<window name>.md` (Y:4142–4144). The direct read writes `.log.<key>` where the key "resolves case-insensitively to a `lanes/log/<File>.md` of the render set", with `.logpath.<key>` the path it resolved to, and neither where two logs differ only by case (CLI44:673, CLI44:683–684; Y:4957).
- Through the service the client must write the same two files, or test 10's byte-identity can break through `parse_log_stream`'s `unreadable:` warnings, which name `.logpath`'s path (Y:4957; svc:86).
- Whether the service resolves the name, and how its answer names the path it resolved to, is not said.
- For lane 1.

### OP-7. Form 3 and the socket

- Form 3 reads at the head `n′`, and needs the paths changed in `(n, n′]` (CLI44:726–735; Y:4900). The request carries no `n` (svc:59; B7), so the service cannot answer "changed since `n`".
- Not said: whether form 3 asks the socket, comparing the answer's digests with `.head`'s `file=` rows for every cached member, or always reads directly, as form 4 does. Y:4900's "(SVC-8's rule)" points at Y:4157–4161, which ERR-04 reads as "always direct" for the guard's read only.
- For lane 1 and lane 3.

### OP-8. An owner file from another namespace: start one or not

- SVC-2: "each container's client refuses the other's service and starts none" (Y:3786–3788).
- SVC-4 item 1: any client that finds "an owner file from another namespace or another install" starts one (Y:3881–3883). T512: such a client "treats the service as absent" (tasks:1393).
- This contract fixes only that such a service is unavailable to the client (section 2.4). Where the lock is shared, a start there finds `register.lock` held, and exits 0 (section 8.3).
- For lane 3.

### OP-9. Where a `dir-check` stamp goes

- A directory that fails the check "is never used and never repaired" (Y:3764–3765). But `dir-check` is a stamped cause (Y:3928), and `register.failed` is a file in that directory (Y:3767–3768, Y:3929–3930).
- A client checks the directory before it would read a stamp (Y:3761–3763), so only `status` would read one.
- For lane 3, and for lane 1, whose `status` reports it.

### OP-10. No `psql`

- Y: `serve` stamps `no-psql`, and exits before it listens (Y:3926–3931).
- tp:2028–2030: with no `psql` on its `PATH`, the service "answers `refuse`".
- This contract follows Y (section 8.6).
- For lane 2, whose test it is, and lane 3.

### OP-11. The fallback line where the direct read fails

- svc:104: with the service unavailable, the hook "SHALL print `guard: direct (service unavailable)` on stderr, with the same exit code".
- CLI44:764 and Y:4342: form 2 prints it only where the service did not answer and the direct read did. CLI44:767 makes the first stderr line on a 69 the cause the guard quotes.
- This contract follows CLI44 (section 11.2).
- For lane 1 and lane 3, to confirm that svc:104 reads so.

### OP-12. macOS's peer check

- Y:3794–3796: "Where `SO_PEERCRED` is not available (macOS offers `LOCAL_PEERCRED` instead, untested here), the directory's owner and mode are the whole protection."
- tasks:1392 and the task line: `LOCAL_PEERCRED` on macOS.
- This contract takes `LOCAL_PEERCRED` at both ends (section 3). No source names a macOS test bed for it, and the owner file has no namespace or boot id there (Y:3783).
- For lane 1, and lane 2: whether T512 shows it on macOS, or records it as not shown, with Y's fallback.

### OP-13. `serve --start`'s exits, and its first step

- R50 is "lean on `serve --start`" (M:717). No source gives its exits, but Q6(c)'s 2 at load (Y:4872).
- Not said: whether it tries the socket before it starts `serve`. Without that, each session start with the service running costs one more Python start, which section 8.3 then ends with exit 0.
- T513's block reads neither (svc:93).
- For lane 1 and lane 3.

### OP-14. R37's floor: refused or ignored

- R37: "with a floor (refuse a value under 1 s)" (M:603). DEC-15: "with a floor of 1 s".
- T512: "a value that is not a positive whole number is ignored, with the default kept" (tasks:1423).
- This contract follows T512 (section 10).
- For lane 3, to confirm that R37's "refuse" means the value is not taken, and not a `config-refused` exit.

### OP-15. The background whole-file fetch

- On a cache miss the service "fetches the file whole in the background, in a transaction of its own, never on a request's timer" (Y:4048–4050). A whole `lanes/LANES.md` took 1,219 ms at a 150 ms round trip (Y:4050–4053).
- The held connection takes one query at a time (Y:3982–3984), under a 180 ms `statement_timeout` (Y:4017–4020), and D9's table has no row for this fetch (Y:3516–3537).
- Not said: whether the fetch holds the one connection, so that requests behind it answer `unavailable slow`, or runs on a second; and its bounds.
- For lane 1 and lane 3.
