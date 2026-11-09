# Research: 007-sql-register

Phase 0 of `/speckit.plan`, keyed to commit Y (`a6497d00`) of the change
`add-sql-register`, which the spec branch merges at `066ad8f1`. The change is
frozen at Y but for its two spec files, which lane 3's rulings since change
on this branch (ERR-03; DEC-05). This file records what the rulings since
the freeze decide for 007, and the errors found in the frozen text.

- **Part 1, the errata** (ERR-01 to ERR-22; ERR-19 is now DEC-21): a line
  at Y that 007 reads otherwise. The frozen text keeps its words; the code
  and the manual follow the erratum.
- **Part 2, the decisions** (DEC-01 to DEC-21): a choice the change leaves
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
spec and the service spec on this branch, at `ce3fad83`; `Y:<n>` is
`design.md`, `ops:<n>` `operations.md` and `clar:<n>` `clarifications.md`,
which are unchanged from Y. `Y-spec:<n>` and `Y-svc:<n>` are the two spec
files' lines at Y, used only where an entry quotes Y's words that the
branch has since changed. A line of commit X above X:3955 is Y's line
less one, since Y rewrote only SVC-5's X:3951–3955 as Y:3951–3956; lane 3's
memo cites X, and each X line is mapped to Y here by its words. Lane 1's
contract is its draft 4.1 (`contracts/register-cli.md`, `df40136e…`,
#165 6739202842) and its block is v4.2 (`cad0fa93…`, #165 6739714248),
both keyed to Y.

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
  (Recommended)" (#165 6727094958). That answer was an accepted prompt
  suggestion in lane 1's session, not Brett's choice, so nothing here rests
  on it.
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
  point 41, 2026-11-09. The admin creates the register's database first, as
  his own act, through his maintenance connection. Then every `lanes-index`
  step of a restore runs through one admin service, which names the
  register's database: `lanes-index` never picks a database. The order:
  1. the admin creates the database;
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
    ops §2.3's step 6 (ops:371), which runs `--roles-only` first with no
    database made, and ops §2.4 (ops:410; ops:486), which give the admin
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
  and exits 0 (spec:626; Y:4492–4494). So it is in the mirror class with no
  register call.
- **Whose:** lane 3's ruling R39 (#165 6726556972), on lane 5's open point 2.
- **Source:** Y:4895; Y:4492–4494; spec:626.

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

### ERR-18. D1's item 5 keeps the physical backups 30 days

- **The erratum:** the install contract's item 5 names the `ObjectStore`'s
  `retentionPolicy` without a value (ops:155). It is 30 days, as the daily
  dump's container keeps 30 days.
- **Whose:** lane 3's ruling, #165 6726078173, which Y records (Y:624;
  clar:505–506); recorded here by lane 3's ruling #165 6727022920. Lane 1's
  dialog of 21:01Z (6727094958) also answered it, with `enablePDB: false`
  and the dump route's (a), but that answer was an accepted prompt
  suggestion in lane 1's session, not Brett's choice, and nothing rests on
  it.
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

### ERR-21. The design's "too late" refusal is the prefetch's (B4)

- **The erratum:** three passages of the design read as a refusal for any
  late read of the guard's:
  - Y:4185, the parse's start line: "a read, through the service or direct,
    that completes after it is a refusal";
  - Y:4266–4271, the clamp, at the direct read's cap of 3.75 s, or 3.65 s
    under a declaration;
  - Y:4344, the exit-mapping row that maps a late read to 2.

  Each holds for form 2, the prefetch, only. Form 4, the guard's second
  read, has B4's ends: 0 with the one log's files written, and every other
  end a log that could not be read, which the guard notes and does not
  refuse for. Its cut is `T0` + 3.75 s whatever a declaration says, since it
  reads no declaration (B5).
- **Whose:** lane 3's ruling B4, with B5, #165 6738474549, on lane 5's §3.6
  review (6738431117).
- **Source:** Y:4185; Y:4266–4271; Y:4344.

### ERR-22. A run with no snapshot clock waits on the record's clock (R26, point 36)

- **The erratum:** Y:5236 says "A run with no snapshot clock calls at once".
  Under DEC-12, a takeover whose own first snapshot failed waits on the
  record's clock instead; "calls at once" stays for a record from another
  host. It was part of DEC-12.
- **Whose:** lane 3's ruling R26 (#165 6726556972), taking lane 4's answer to
  lane 2's point 36 (6726296119).
- **Source:** Y:5236.

## Part 2: decisions

### DEC-01. The service is built beside groups A and B, and the v1.2.0 tag waits for it (R35)

- **The decision:** `lanes-index serve` is built beside groups A and B, not
  last. The cutover and the v1.2.0 tag wait for it, since from Eagle the
  guard without the service takes about 1.2 to 2.1 s, which misses Brett's
  1 s. No service pull request lands before the spec PR.
- **Whose: Brett's.** His words, verbatim, typed in lane 3's session at about
  2026-11-08T20:33Z: "4A, 5A, 1 let them stand, 3A", as lane 3's #165
  comment 6726766577 quotes them. Option 4A, as lane 3 asked it in text:
  "Build it beside groups A and B, and the tag waits for it (recommended).
  X already says this." It changes his Q29 (a) answer, "the service is
  built last" (clar:473), by his own word. Lane 3 had ruled the move first
  (6724332510, 6726078173), and named it on the spec PR's list as a change
  to his answer.
- **Source:** svc:289; Y:4826; Y:3951–3956; clar:524–525.

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
  (Y-svc:188), and in the sql spec's Y-spec:87 and Y-spec:574. Two commits
  on this branch after `aa190a7f`, on lane 3's ruling B4:
  - scope each of those refusals to the guard's prefetch, its first read;
  - add that the second read, started at or past `T0` + 3.75 s whatever a
    declaration says (B5), exits as "too late to verify", and that it fails
    open on every end but 0, in B4's words: every other end is a log that
    could not be read, which the guard notes and does not refuse for;
  - name the prefetch's direct read in the declaration's sentence;
  - scope the scenarios "A guard that starts too late" and "A read held
    behind a lock" to the prefetch, and add "A second read that starts too
    late".

  No spec delta is left. ERR-21 records the design's three passages that
  still read as a refusal for any late read.

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

- **The decision:** on LR004, `register commit` re-reads each path's base
  from SQL, bypassing the image cache, and resends once only where that base
  equals the scratch tree's. Otherwise it exits 69, naming the image cache.
  The retry's second call is read by the commit's exit table in full, so 69
  comes only when both calls ended definitively.
- **Whose:** lane 3's ruling R02 (#165 6726556972), taking lane 1's form (its
  O2), with lane 4's CO-4.
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
  `lane-worktrees`, where that checkout does not exist. In files and `index`
  mode it routes as today, and carries the answer across 1a's `exec` as
  `LANES__MODE`, with the `LANES__PID` check. `reg_open` takes the same
  answer. Lane 4's T409 adds the carry, so slice 1a does not change.
- **Whose:** lane 3's ruling R17 (#165 6726556972), on lane 4's Q-L3-2; the
  carry's place, lane 3's #165 6726831985.
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
  240 s idle reset. The starter's reduced environment keeps both where they
  are set.
- **Whose:** lane 3's ruling R37 (#165 6726556972), taking lane 5's proposal
  (its open points 24 and 25).
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
  imports come first.
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

## Part 3: the choices the design leaves to the plan

Lane 3 took PC-1 to PC-16 as plan.md proposed them, in its 007 call (#165
6724332510); each lane could object in its block, and none did. Each row is
lane 3's ruling.

| # | Choice | Taken | Source at Y |
| --- | --- | --- | --- |
| PC-1 | The image cache's form | Question 13's: content-addressed objects, per-seq manifests, a forward-only head under a short `fcntl.flock`, collection after 10 minutes, and the service writing through | Y:4960 |
| PC-2 | The pipe pair | not in phase 1; decided at phase 2 (DEC-18) | Y:5868 |
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
