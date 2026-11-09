# Operations: acts 3 and 4, the rollback and the disaster path

Lane openRepoTools-1's part 2 of its section for `design.md`
(opensoft/openRepoTools#165, the SQL register), folded as a file of its own
beside `design.md`, whose "The schema" section is part 1. It is folded from
section v2,
`/workspace/projects/.openrepotools-private-prep/work-20261102/165-section-v2/section-lane1-v2.md`
(sha256 `dd061272306f23f4b09d8cab0c5096df22a1bc2005bdf3951d3170ca89dab965`),
with `fix-v2.diff` (sha256
`d1e05586cc3a22c6f9c7d33259e045040c16fd5cdedb8ea6740861304e8cb12a`) and
`renames.md` (sha256
`e0087c0ff5f8fd19425df75224617d540391819115614c6a5ad3541d37ff3f33`) beside it,
under lane 3's rulings on v1 (#165 comment 6578020193) and on v2
(6579459518). Lane 1's v2.1 (`fix-v2.1.diff`, sha256
`7b54372637c079371777c396a1cc52c2409641aa9efcfd2865f1f8f15a8098c8`) and v2.2
(`fix-v2.2.diff`, sha256
`57b5a5a091358c819d318e09d024000d592928d06a25eabfbcca0ff8f2c6f10d`) are
applied on top (#165 6579719525 and 6708446448; lane 5 confirmed v2.1 in
6579761607). Lane 1's v2.3 (#165 6708990159; `fix-v2.3.diff`, sha256
`24aea7d90ea22e4cfb03267bf28226e1e79f99e20b886bfb19a642bca62a45a3`, from
v2.2, `section-lane1-v2.2.md`, sha256
`d9908578c48f05772adc59b20f4a5c3ddbe58dc4877920325a5b5c5bcf060b25`) is
applied on top too: its six part-2 hunks give 78 (`EX_CONFIG`) for Q6(c) in
`ready`'s check 5, `set-mode`'s refusal, 2.7's group A and 2.9's row; lane 2's
13 ms median and 16 ms p95 for the `Register-Seq` lookup in 2.5; and the
restore form's abort in 2.3, which replaces the note the fold had put there.
So lane 1's text here is v2.3's (`section-lane1-v2.3.md`, sha256
`fc2609e80fa8b3d6968fda822a1872f2f7765eaa3b0cf1524cfd99a78b826a28`), except
where lane 3's rulings (#165 6710653292 and 6710770304) or its fold change it:
act 4's cutover, step 3, now six push attempts in all (lane 4's row 4, from
its prototype), and act 3's step 9, which points at the phase plan's push URL;
2.3's sentence on the re-import form's abort; 2.6's line on a `LANDING
EXPIRED`'s spelling; and round 1, now RULED. Lane 1's v2.4 (#165 6711221999)
changes only part 1, in `design.md`; lane 3's ruling on its fourth point
(6711233420) adds 2.3's bullet on the lost tail's window after a restore.
Lane 1's v2.5 (#165 6711914507) also changes only part 1; lane 3's ruling on
its import cost, option (a) (6711924759), adds act 4's step 6 bullet on the
import's key check and its time, and a line on the same check at the
disaster path's re-import, in 2.3. Commit L, on lane 3's rulings on the
review of commit J, changes only the places those findings name: a
rollback's declaration records the rollback's head, `k`, in 2.3's step 2 and
2.5 (R2's M1); 2.5's note on the frame's S3 and its lookup form (R1's N1);
`register read`'s 78 beside `lanes-index mode`'s in 2.4 and 2.7 (R1's M1);
answered questions marked so, Q7's 75 and Q28's owner (R1's S2); `b+1` in
2.9 (R1's N3); the round-2 label (R1's N4); and 2.3's lost-tail window,
where S1's client check exits 2 first (commit J's writer's item 3).
Lane 1's v2.6 (#165 6714645316; `fix-v2.6.diff`, sha256
`299f0db5fbec8b2ecb774f87e140377f7fb90fe596c5040002ff4e81e540aa4f`, from
v2.5) has two part-2 hunks, both in 2.3, applied by hand at commit M, with
"(§1.3)" reading "(`design.md` §1.3)": the rollback's fresh fetch and
divergence check right before its transaction, with LR012 inside it, and
what R2's M1 costs a rerun after a failed render (R2's S5; step 3); and the
after-loss cutover's label (R2's N4), as lane 3 ruled, lane 1's option (a).
Commit M also folds lane 5's check of commit L (#165 6714540821) into 2.5
and 2.9: `--rollback` requires `--mirror-seq <k>`, a rollback's declaration
needs no `--until`, and SVC-12's refusal text is lane 5's.
Commit O places lane 5's three texts from its check of commit M (#165
6714928254) in 2.5: the first refusal lets a database that answers pass only
where the switch reads `files` after a rollback, and then only in the
`--rollback` form; the "and with it" bullet passes `<k>` only where the
journal entry at the switch's `set_seq` is a `rollback` and `<k>` is
`set_seq - 1`; and a new refusal turns Brett's word away while the probe reads
the switch at `files` after a rollback, printing the `--rollback` form for him
to run instead.
Commit P gives 2.9's row for `--mirror-seq` and `--rollback` lane 5's own
words (#165 6715126795), adds `register read` beside `lanes-index mode` in
2.9's row for 78 (verifier V1's NIT 4), and adds the sentence above on commit
O (V1's NIT 6).
Commit Q folds lane 1's v2.8 (#165 6715661308; `fix-v2.8.diff`, sha256
`f1aa94a3c45f3052b5c9aaecc9e1044e43b6550475edca901522597c6c16ea0a`, from
v2.7), whose seven part-2 hunks are applied here, with "(§1.3)" reading
"(`design.md` §1.3)": `register_label=` in act 3's step 3, in 2.3's
re-pointing, in `bootstrap --label` and its output, and in 2.9 (V2's S-2);
`cutover`'s label from that key alone, with its two refusals and the residual,
in 2.3's label bullet; the restored switch's label; the rollback's retry after
a 40001, back through its divergence check each time (V2's NIT 3); the rerun's
declaration (V2's NIT 8); and `ready`'s new check, numbered 10 after commit
O's check 9, so that v2.8's "check 9" reads "check 10" here. It also places
lane 5's texts for commit Q (#165 6715361644) in 2.5: a rollback's declaration
counts only where a guard's own read finds the switch at `files` (V2's S-4,
with V1's NIT 5), in the refusal on `--until` and in condition 2; a new
refusal of the `--rollback` form where the probe cannot read the switch; and
the direct read's 0.5 s floor in the budget, now five things (V2's NIT 7).
Commit S folds lane 1's v2.9 (#165 6716180846; `fix-v2.9.diff`, sha256
`b74325b7404549c589f3d1b09f44efc20963aae4785f02ac3bec1b31e805a9d5`, from
v2.8), whose two part-2 hunks are applied here, with "(§1.3)" reading
"(`design.md` §1.3)": in the rollback's step 2, the retry, which takes a
40001, 40P01, 55P03 or LR001 as a lost turn, reads the head afresh as its `k`,
and exits 75 at D7's deadline (V3's NIT 2); in the disaster path's label
bullet, a new label for a restore's replacement, which the re-pointing writes
into `register_label=` and which is not derived from the URL (V3's NIT 4); and
the disaster path's step 7, where `outage clear` now comes before
`ready --expect primary`. It also gives 2.9's row for `--mirror-seq` and
`--rollback` lane 5's words (#165 6716156221), with the refusal where the
probe cannot read the switch and, in either form, at `primary` (V3's NIT 5).
Lane 1's numbering is kept: a cross-reference to part 1 names
`design.md`, as in "`design.md` §1.3".

- **Basis:** the proposal at `0607526d`. A citation of `proposal.md`, `clarifications.md` or `council-review.md` is to the frozen copy, sha256 `b284a0eb2078fd24a48e833685b0073e881fdbe1625a1019d81a55a4aadaa340`, `636fb45369f825dd7f3d763bdb8d8bcb475ef4d76a3f7cf23203b907713ac2ff` and `9fa18c5ed76d88d4f25b3f8eaa37626ad0511985061f9e0649ff49d13c0fce62`.
- **Round 1, held as provisional when lane 1 wrote this, and RULED since** (Brett's confirmation of 2026-11-07, #165 6710653292): Q6 = 1A (the split by seam), Q8 = 2A, Q20 = 3A (`mode=primary` written at act 4), and Q2(a) and (b) = 4A.
- **Round 2, the recommended options, which Brett took** (#165 6710653292): Q4(a) (i), Q4(b) (iii), Q7 (A″), Q21(b) (B), Q22 (a), Q26 (a′), Q28 (i) and Q29 (a). Where a part turns on one of them, it says what changes under each other option.

## 2. The operations

This part covers acts 3 and 4 as a runbook, the rollback and the disaster path, P8's three operator tools, the declared outage of Q26 (a′), P10's notice to the lanes, and R3c's landing path. It works under round 1 as answered, which lane 3 held as provisional (1A, 2A, 3A and 4A; `reviews/lane3-165-design-starts-6576888068.md:3-7`) and which is RULED since (#165 6710653292), and under round 2's recommended options, which Brett took (6710653292): Q4(a) (i), Q4(b) (iii), Q7 (A″), Q21(b) (B), Q22 (a), Q26 (a′), Q28 (i) and Q29 (a) (`proposal.md:1101-1112`). Where a section turns on a round-2 question, it ends with what changes under each other option, and 2.8 indexes them.

Sources: `proposal.md`, `clarifications.md` and `council-review.md` are the frozen copies at `0607526d`. `r3a/`, `r3b/`, `r3c/`, `council-product/` and `product-fold-check/` are lane 1's reports, and `reviews/` holds the saved #165 posts. `code/…` is the code leg at `2a057a6d`. Schema objects carry the proposal's names, or R3a's where the proposal has none, marked "R3a's". Names this part adds are marked **(new)**, and 2.9 lists them all.

### 2.1 Who acts, and the words

- **Brett** gives every word: the window, the freeze, the cutover, the rollback, an outage's declaration and each merge (`proposal.md:135-136`, `:1042`; `r3c/REPLY.md:108`). He does act 3's infrastructure and the per-host flips (`proposal.md:955-966`).
  - A word is a turn he typed, and the post that acts on it records it. An accepted prompt suggestion is not a word (`reviews/lane3-165-design-starts-6576888068.md:3-7`).
- **The operator lane** runs every `lanes-index` command of acts 3 and 4 from its own register checkout, `<W>-lanes/<lane>/`, and never from `W` (`proposal.md:1252-1254`; `r3b/REPORT.md:205`, `:285`).
  - Brett names it.
  - **Its lane (F12):** `import`, `cutover`, `rollback` and the disaster path run with `LANES_LANE=` the operator lane, through that checkout, so each push they make is a lane's write, as Amendment 23's S2 requires ("no lane, no write"). A shell or a `CLAUDE_NO_LANE=1` session that runs one of them names the operator lane the same way.
  - **The operator entries (F2, F12; D24):** a journal entry that touches only `lanes/STORE` is an operator entry, and there are exactly two: the cutover's entry, seq 1 at a first cutover, `m+1` after a re-import in its after-loss form, or `b+1` after a restore (2.3; `design.md` D24), and the rollback's last seq. The renderer never renders one. The command that writes the entry pushes its commit from the operator lane's checkout, with the renderer's identity (D23; under Q21(b) (A), the push credential every writer holds today), and confirms it by Amendment 23's S4 read-back: a fetch of `origin main` whose first-parent history carries the commit, with its `Register-Seq`, and with `lanes/STORE` present after a cutover or absent after a rollback. A push it cannot confirm takes neither branch: the command exits 3 and changes nothing more, and its rerun reads origin back before it acts.
  - **Their transactions (F1, F23):** each operator entry is written in the transaction that changes the switch row, and that transaction commits before any git step, so no operator command holds a transaction across git.
  - R3c gives the readiness probe in each environment to lane openRepoTools-1 (`r3c/REPLY.md:152`).
- **The coordinator** is #165's lead, lane openRepoTools-3 (`proposal.md:3-4`, `:132-134`), unless Brett names another lane.
  - It posts the notices of 2.6 and keeps the runbook's record: one #165 post per step that changes state, quoting the output it rests on.
- **Each lane** drains, resumes, and checks its own records when it is told to.

### 2.2 Acts 3 and 4: the runbook

Each step gives who acts and where, the command, its check, its refusal and its way back. The commands are the proposal's verbs (`lanes-index import --at`, `cutover`, `rollback` and `status`) and the three tools of 2.4. Under Q20 (B), round 1's 3A, act 3 leaves every workstation on `mode=index`, and `mode=primary` is written only at act 4 (`proposal.md:1768-1773`).

#### Before act 3

- Group C of 2.7 and v1.2.0 have landed, and the hosts are reached (`r3c/REPLY.md:148-153`), so the installed `lanes-index` no longer refuses `mode=primary` (`proposal.md:1380-1394`).
  - Act 3's first two steps need only group A's `bootstrap`, so Brett may run them earlier, from a host shell.
- 1A's PostgreSQL 16 for the bench images (`proposal.md:1121-1122`) rides act 3's one rebuild (step 4).

#### What the codeXfactory prod install provides (new)

Brett's ruling (#165 6723925474): the register's database is "CNPG in the install", our own PostgreSQL on the prod AKS cluster in the codeXfactory prod install, which engineers reach without a VPN through a public load balancer limited to their addresses, with TLS by cert-manager, on one node with no HA, created only once that install exists ("Wait for the install"). That install is made by opsXfactory-6's change `add-codexfactory-prod-register-database`: it sits in Opensoft-Tenant's own `codexfactory` folder, with its own Flux sync entries and reconciler, in the namespace `codexfactory-register` (Brett's answers, observed at 14:50:18Z: "Opensoft-Tenant, own Flux entries (Recommended)" and "codexfactory-register (Recommended)", #165 6743050889), and act 3 waits until that change has landed. The install is built in its own repository, under its own gates; #165 states only what the register needs from it, and act 3 starts when each item exists and Brett has recorded item 9 on #165. The CNPG facts are its 1.30 documentation's, and the Keycloak database in the same subscription runs these shapes today.

1. **The operator:** CNPG 1.30 or later, whose watch list names the register's namespace (a `Cluster` in a namespace the operator does not watch is applied and never reconciled, as the Keycloak database's change found), and the Barman Cloud plugin, 0.15.1 or later.
2. **The Cluster:** one `postgresql.cnpg.io/v1` `Cluster`, in a namespace of its own:
   - PostgreSQL 16, `instances: 1` (Brett: one node, no HA);
   - `enableSuperuserAccess: false`;
   - `bootstrap.initdb` with the register's `database`, `lanes_register`, and its `owner`, `lanes_register_admin`, never `lanes_register_owner`, the NOLOGIN owner the bootstrap makes (`design.md` §1.4; opsXfactory-6's names, #165 6751205159), the owner's password a Key Vault secret delivered as a `kubernetes.io/basic-auth` Secret, the type CNPG documents for `bootstrap.initdb.secret` and for a managed role's `passwordSecret`, with the keys `username`, the owner's name, as CNPG requires for `initdb`, and `password`, and labelled `cnpg.io/reload: "true"`; it reaches the cluster by the Keycloak database's route, Key Vault through the Secrets Store CSI driver, though Keycloak's own Secret is `Opaque`;
   - one `managed.roles` entry, for that owner: `login: true`, `createrole: true`, `createdb: true`, `inRoles: [pg_use_reserved_connections]`, and that Secret as its `passwordSecret`, labelled `cnpg.io/reload`. No other managed role: the logins are `lanes-index login`'s (`design.md` §1.4);
   - `postgresql.parameters`: `max_connections` from item 8; `reserved_connections: "2"`; `tcp_keepalives_idle: "60"`, `tcp_keepalives_interval: "10"` and `tcp_keepalives_count: "6"`, so that no connection is silent for the load balancer's 4-minute idle reset, the import's 300 s statement and the service's held connection among them (lane 1's run: a server keepalive within 65 s on a connection whose statement sent nothing for 75 s); `log_connections: "on"` and `log_disconnections: "on"`, so the server's log names each login. CNPG's own `ssl_min_protocol_version` of `TLSv1.3` stands;
   - `postgresql.pg_hba`, ahead of CNPG's default `host all all all scram-sha-256`: `hostnossl all all all reject`, then `hostssl lanes_register +lanes_register_writer,+lanes_register_reader,lanes_indexer,lanes_register_admin all scram-sha-256`;
   - `enablePDB: false`, since CNPG keeps a one-instance cluster's node from draining while its budget stands. A drain, a node's restart or a parameter that needs a restart is then an outage until the instance runs again, which act 3 times;
   - `certificates.serverTLSSecret` and `serverCASecret`: item 4's server certificate, and the CA chain CNPG needs;
   - resources and storage from item 8.
3. **The endpoint:** a `managed.services.additional` entry with `selectorType: rw` and a `serviceTemplate` of `type: LoadBalancer` on port 5432:
   - `loadBalancerSourceRanges`: each engineer's egress address or range, IPv4, which AKS enforces at the network security group and on the node. Brett names them, and a workstation that joins is added before its act 3 steps;
   - `externalTrafficPolicy: Local`, so the server sees, and logs, each workstation's own address;
   - a static public IP, and one DNS name for it, `<endpoint>`, which is `register.codexfactory.opensoft.one` (Brett's answer, observed at 14:50:18Z: "register.codexfactory.opensoft.one, by hand (Recommended)", #165 6743050889), with one A record and no AAAA, so `N_a` is 1. Brett makes that one A record by hand at Spaceship once the install reports the load balancer's static IP;
   - no `Pooler` for the register, of either pool mode (`design.md` §1.4).
4. **The certificate:** a cert-manager `Certificate` for `<endpoint>` and the `-rw` service's in-cluster names, with `usages: [server auth]`, from a cert-manager CA issuer in the register's namespace under a root of the install's own, which no other party can use to sign for `<endpoint>`. That issuer is the install's private CA: lane 1 suggested it, Brett chose it on 2026-11-08 ("Install's private CA (Recommended)", #165 6726078173), and on 2026-11-09 he kept it as the interim CA ("Interim CA now, OpenXPKI later (Recommended)", #165 6746095035). The certificate is renewed before it expires. CNPG receives it as `certificates.serverTLSSecret`, whose `tls.crt` holds the leaf and every intermediate, with `serverCASecret`'s `ca.crt` carrying the CA chain CNPG needs, each Secret labelled `cnpg.io/reload` (one Secret may be both). Its issuer's root is the file each workstation names in `sslrootcert=`. CNPG reloads a renewed certificate without downtime. **Later:** once the prod OpenXPKI (Brett, 2026-11-09, #165 6742844912: "add openXPKI to prod and use that. both QA and Prod need openXPKI") and its route exist, the certificate is re-issued from it, with the same names, usage, renewal and Secrets, by a route the install's plan sets (opsXfactory-6); CNPG reloads it without downtime, and every client's root file, each workstation's and the dump job's, holds both roots from before the re-issue until every client has moved, then drops the interim root, in the order the install's plan sets (opsXfactory-6); the later root is the prod OpenXPKI's, the trust anchor under openxFactory's `trust-anchor` contract. Act 3 does not wait for that switch.
5. **The physical backups:** as the Keycloak database's:
   - an `ObjectStore` (`barmancloud.cnpg.io/v1`) on a blob container in the prod subscription, with `azureCredentials.inheritFromAzureAD: true` through the Cluster's workload identity, and a `retentionPolicy`;
   - the Cluster's `plugins` entry for `barman-cloud.cloudnative-pg.io` with `isWALArchiver: true` and a `serverName` of its own;
   - a daily `ScheduledBackup` with `method: plugin`.
   - WAL is archived at least every `archive_timeout`, CNPG's default 5 minutes.
6. **The daily dump:** a `CronJob` in the register's namespace, once a day (Brett, #165 6722593397). Its workload identity is its own, and may only:
   - read one Key Vault secret, `lanes_register_dump`'s password;
   - write to the dump's blob container in the prod subscription, which has soft delete, and deletes each dump by lifecycle after 30 days, with no immutability (#165 6751452061).
   - It connects in-cluster to the `-rw` service with `verify-full` (act 3's step 1).
7. **The network policy:** ingress to the instance on 5432 from the load balancer's source ranges, the dump CronJob and the operator, and on 8000, the instance manager's status port, from the operator, and from nothing else. The Barman Cloud plugin needs no rule: it never connects to the instance over the network (opsXfactory-6, #165 6751205159).
8. **The server's size,** set once with room, since changing `max_connections` restarts the one instance:
   - `max_connections` ≥ ⌈1.25 × (Σ_w (S_w + 2·L_w) + I + 2) + C_op + 3 + 2⌉, rounded up to a multiple of 10. The inputs:
     - `S_w`: the environments on workstation `w` that run the service, one connection each (SVC-1);
     - `L_w`: the lanes that can run at once on `w`. Each holds at most one register connection of its own, a write's call or a read, and its guard's direct read while its service is down. Lane 4 opens one connection at a time per running writer, bind or direct guard read, and holds each for about 1 to 3 s (#165 6724210587), so a burst of ten writers is ten connections;
     - `I`: the workstations whose indexer syncs, one connection each;
     - 2: the dump CronJob and one operator command;
     - `C_op`: the connections the operator's instance manager and the backup plugin hold, read at act 3 from `pg_stat_activity`;
     - 3: PostgreSQL's `superuser_reserved_connections`;
     - 2: `reserved_connections`, Brett's admin's.
   - For example, 4 engineers with 3 environments and 8 lanes each give 1.25 × (4 × 19 + 4 + 2) = 102.5. With `C_op` at 5 that is 113, so 120.
   - A login that finds no slot is refused at connect, "remaining connection slots are reserved…" (lane 1's run). Each path reads that as a failed connect, `psql`'s exit 2.
   - The instance's memory is at least `shared_buffers` plus `max_connections` times the memory per connection act 3 measures, with room.
     - As a start, the Keycloak database's shape: a 1 GiB limit with `shared_buffers` at 128 MB for 100 connections, raised with `max_connections`.
     - A CPU request of 100m and a limit of 1.
     - Storage of 8 GiB on the cluster's premium class, far above the register's size.
9. **What it hands back,** for Brett to record on #165:
   - `<endpoint>` and its address;
   - the root file: the interim CA's root, the install's own, which item 4's certificate chains to;
   - the owner's name and the Key Vault secret Brett reads its password from;
   - the namespace and the Cluster's name;
   - the source ranges;
   - the times the operator, the plugin and the backups were first seen working.

#### Act 3

1. **The database.** Brett acts, on the host Q22 named on 2026-11-08: CNPG in the codeXfactory prod install, which creates the database ("What the codeXfactory prod install provides", above).
   - **Command:** none of this change's. The codeXfactory prod install creates the register's CNPG `Cluster` on the prod AKS cluster, to the contract above, once that install exists (Q22; Brett, "CNPG in the install" and "Wait for the install", #165 6723925474); act 3 waits until opsXfactory-6's change `add-codexfactory-prod-register-database`, which makes the install (§2.2; #165 6743069107), has landed. `initdb` makes the register's database, owned by its owner, Brett's admin. He records on #165 the contract's item 9, the backups and the daily `pg_dump` (below), and his downtime limit, 4 hours (`proposal.md:1810-1813`; `clarifications.md:69-74`). The conditions are `design.md` §1.3's on the connection and §1.4's on CNPG and on TLS.
   - **The daily `pg_dump` (Q22; Brett's choice, "Job + storage in prod sub (Recommended)", #165 6722593397, which stands, #165 6723925474):** the install's CronJob (the contract's item 6) dumps both schemas, `lanes_register` and #160's `lanes_index` ("Dump both", 6722541787), into a storage container in the prod subscription, with soft delete, and deletion by lifecycle after 30 days, with no immutability (#165 6751452061). The job logs in as `lanes_register_dump`, a member of READER alone (`design.md` §1.4: the login step; M3's GRANT on `lanes_register`'s sequences, and M6's default privilege for `lanes_index`'s), so no unattended job holds the owner's credential or an engineer's. Its identity (lane 1's recommendation, on lane 3's question) is a workload identity of its own, federated to the CronJob's service account. It may read one Key Vault secret, the dump login's password, which `lanes-index login add --dump-job` printed once and Brett stored, and write to the dump's container; the job writes the password into a 0600 password file of its own run. The command is `umask 077 && pg_dump --no-password --schema=lanes_register --schema=lanes_index --format=custom --file=lanes-register-$(date -u +%Y%m%dT%H%MZ).dump`. Its connection comes from its environment, as SVC-2 maps `reader_url=`: `PGHOST`, the `-rw` service's in-cluster name, which the certificate also names; `PGPORT`, `PGDATABASE` and `PGUSER`; `PGSSLMODE=verify-full` and `PGSSLROOTCERT`; and `PGPASSFILE` naming that file. So no connection string is in its arguments, and it never takes the public endpoint. Lane 1 ran that command as `lanes_register_dump` over `verify-full` against a server certificate of the contract's shape. The job then copies the file into the container. `pg_dump` sets its own timeouts to 0, so the dump login's floors do not cut it. The dump's head `b` reads from the file alone: `pg_restore --data-only --schema=lanes_register --table=register_head --file=- <file>`. The dump holds no role, verifier, membership or floor, since those belong to the cluster (lane 1's run); a restore from it needs `--roles-only` and `lanes-index login add` (2.3). Beside the mirror and the physical backups, the dump keeps the journal's history, its write ids, acts and scopes, and #160's index, in a form any PostgreSQL 16 reads. The mirror stays the current copy outside Azure, so the re-import stays the default route. A restore is chosen only where its `b` is newer than `m` (D22).
   - **On CNPG in the install** (Brett, #165 6723925474):
     - **The access:** the contract's endpoint, a public load balancer whose source ranges are the engineers' addresses, with no VPN. Each workstation's egress address, or its ISP's range, is one range, added before that workstation's act 3 steps.
     - **The endpoint:** `<endpoint>`, port 5432, the load balancer in front of the primary; never a Pooler (`design.md` §1.4).
     - **TLS:** CNPG's `ssl_min_protocol_version` of `TLSv1.3`, its default; `sslmode=verify-full` against `<endpoint>`, and `sslrootcert=` the issuer's root file Brett recorded (item 9), the root only. No URL carries `sslcert=` or `sslkey=`.
     - **Who connected:** `log_connections` and `log_disconnections` (the contract's item 2), with each workstation's own address, since the service keeps the client's (`externalTrafficPolicy: Local`). Where the instance's log is kept is the install's.
     - **The admin:** the database's owner, CNPG's `initdb` owner, `lanes_register_admin` (opsXfactory-6's name, #165 6751205159; lane 1, 6751452061), with `LOGIN CREATEROLE CREATEDB`, runs the bootstrap and `lanes-index login` (2.4) through Brett's admin service, reaching `<endpoint>` from his workstation as any engineer's does.
   - **Check:** each environment reaches it within its own `c`, libpq's `connect_timeout`, at each of at most `N_a` addresses, since libpq spends `c` on each address in turn, so a connect takes at most `N_a`·`c` (lane 4's D9, #165 6722839180; `proposal.md:799`; `clarifications.md:278-280`). Act 3 sets both from each workstation's cold connects as it joins: `c` as `max(2, ⌈t_c + 1 s⌉)`, and `N_a` as the distinct addresses `getent ahosts <host>` prints on its `STREAM` lines, since it prints each address once for each socket type. `ready` (step 6) tests them, the connect in check 4 and the count in check 13.
   - **Checks on the cluster, before step 2** (what lane 1's engine cannot show): from a workstation inside the source ranges, `psql` with `verify-full` reaches `<endpoint>`, and from an address outside them the connect times out; `pg_stat_ssl` shows `TLSv1.3`, and `inet_client_addr()` the workstation's own address; the owner has `rolcreaterole` and `rolcreatedb`, not `rolsuper`, and is a member of `pg_use_reserved_connections`; `max_connections`, `reserved_connections` and `tcp_keepalives_idle` read as the contract sets them, and a session left idle for five minutes still answers; `sslmode=disable` is refused; the Cluster's status shows a completed `ScheduledBackup` and a current WAL archive; `pg_stat_activity` at rest gives `C_op`; and the instance's restart is timed, for the outage it makes. A point-in-time restore into a scratch Cluster, whose roles, verifiers, memberships and floors equal the source's by lane 1's query, is run once on Brett's word. Brett gave that word: "Yes, drill at act 3 (Recommended)" (#165 6726078173). Act 3 restores a copy to a chosen moment from the Barman Cloud backups and checks it against the live register, timed against his 4 hours.
   - **Way back:** nothing depends on the database yet.
2. **The schema and the roles.** Brett acts, from a host shell and not from a lane session.
   - **Command:** `lanes-index bootstrap --admin-service '<service>' --label '<name>'` **(new)** (2.4). The admin connection is a libpq service of Brett's own, never a connection string in an argument list (lane 5's choice 11, which lane 3 accepted for every `lanes-index` path, #165 6578521506). `psql` receives it only as `PGSERVICE=<service>` in its environment, never as `service=` or `-d` on a command line (2.4; SVC-2; lane 5, 6579523948).
   - **Check:** a second run prints `unchanged` for every object (`proposal.md:623-624`).
   - **Refusal:** as 2.4 lists. Nothing is left half-made, because Postgres DDL is transactional (`r3a/REPORT.md:327`).
   - **Way back:** the schema holds no register data before the import. Brett drops schema `lanes_register` and the roles the bootstrap made (`design.md` §1.4; R3a's names for two of them, `r3a/REPORT.md:172`, `:281`) through the same admin service.
   - **Then the logins:** Brett runs `lanes-index login add <engineer>` **(new)** (2.4) for each engineer, human or AI host, and `lanes-index login add --dump-job` for the dump job (Brett, "Two logins each", #165 6723413756). A workstation that joins later gets its engineer's logins the same way, before its step 3. **Way back:** the way back above drops these logins with the roles the bootstrap made. **Then the dump's check,** moved from step 1's checks because it needs this step's login and schema (opsXfactory-6, #165 6751205159): the dump CronJob has run once, and `pg_restore -l` on its file lists both schemas.
3. **The configuration, per host.** Brett acts on every engineer's workstation, human or AI host (#165 6722593397): first on Eagle and on Raven, since under Q22 (a) they are configured together (`proposal.md:955-957`, `:1814-1816`), then on each later workstation as it joins. Below, "each configured workstation" is every workstation configured so far.
   - **Command:** place the 0600 password files and `lanes-index.conf` in the host's dedicated read-only mount (Q23 (a), `proposal.md:1836-1839`). The file carries `mode=index`, `reader_url=`, and the REGISTER-WRITER's pair, `register_url=` and `register_passfile=` (`proposal.md:152-153`, which names them "for example"; R3a's spelling, `r3a/REPORT.md:323`), `register_label=` **(new)**, the label step 2's `--label` gave the database those URLs reach, which `cutover` sends as its label (2.3, S-2), and, under Q21(b) (B), `render_key=`, which names the renderer identity's 0600 key file (D23; step 9).
   - **Per engineer:** each workstation's file names its own engineer's two logins and its own two 0600 password files (`design.md` §1.4, "One engineer's file"); no workstation holds another engineer's line.
   - **Check:** `lanes-index status` reads; `ready` (step 6).
   - **Refusal:** `lanes-index` refuses the file when it is group- or world-readable, inside a git work tree or under `~/.agents/`, or when a URL carries a password (`code/lanes-index:60-70`).
   - **Way back:** remove the new keys. `mode=index` behaves as today (`proposal.md:88-90`).
4. **The bench images, once.** Brett acts.
   - **Command:** one rebuild, then a recreate. The rebuild carries the re-vendor (Q24), the configuration mount (Q23) and 1A's `postgresql-16` (`proposal.md:947-950`, `:1853`).
   - **Before it:** a rebuild ends every lane in its container (`council-product/REPORT.md:161`), so notice N1 (2.6) goes out first.
   - **Check:** `ready` (step 6).
   - **Way back:** the previous image. The register is unchanged.
5. **The install elsewhere.** Brett acts, in each host shell.
   - **Command:** pipx v1.2.0, or the root one-liner as a stopgap (`proposal.md:1840-1853`; `r3c/REPLY.md:53-57`).
   - **Way back:** reinstall v1.1.0. On `mode=index` nothing changes.
6. **Readiness, in every writing environment.** The operator lane acts.
   - **Command:** `lanes-index ready --expect index` **(new)** (2.4), run in each environment on P19's list (`clarifications.md:127-133`): each bench container on each host, each host shell, Raven's install and each later workstation's, and the Codex lane's tools, through Brett (`proposal.md:984-986`).
   - **Confirming the list:** this step confirms that list, which is Brett's act (`clarifications.md:131-133`).
   - **Check:** exit 0 everywhere. The coordinator posts each environment's summary line, which carries no secret.
   - **Refusal:** any failing item holds act 4 until it passes.
7. **Stranded records and `W`.**
   - **Command:** each lane publishes or archives its own stranded records (Q4(c) (i), `proposal.md:1320-1324`), and Brett repairs `W` once (`r3b/REPORT.md:319-322`).
   - **Check:** each lane posts that it is done.
8. **The rehearsal and the drill.** The operator lane acts, against a scratch schema, from a scratch clone whose origin is a local bare copy of the workspace repository, so nothing reaches `opensoft/brett-wip`.
   - **Command:** `lanes-index import --at <C> --dry-run` (`r3b/REPORT.md:235`). Then test 13's sequence (`proposal.md:910-912`), with ADV-C10's outage added (`clarifications.md:207-208`): a cutover, writes, an outage of the scratch database with its refusals, a rollback, and a files-mode write.
   - **Check:** each step's oracle as test 13 states it, and the import's three-part proof (act 4's step 6) on the real register.
   - **Way back:** drop the scratch schema and the scratch clone.
9. **The renderer identity, under Q21(b) (B), where the org's plan supports it.** Brett acts.
   - **Command:** Brett creates the identity and prepares a push ruleset with it as the only bypass (`proposal.md:1787-1794`), not enforced yet. Enforced now, the ruleset would refuse every files-mode writer's push, and under Amendment 23's S4.5 each such writer keeps its commit on HEAD (exit 6) and its checkout refuses every later write. Act 4's step 5 enforces it, inside the freeze (F4).
   - **Its path set:** the rendered paths and `lanes/STORE`, not `lanes/**`. Under Q18 (A) a person still commits `lanes/repos.tsv` (`proposal.md:1744-1762`).
   - **The key:** a deploy key in a 0600 file that `render_key=` names (D23), used only in the push's own environment. Where that file lives is the renderer section's (`clarifications.md:118-120`). How a render pushes with it is answered in `design.md`'s phase plan, "The push URL for the deploy key against origin's HTTPS remote": an explicit SSH URL to the repository `origin` names, in the push's own environment, never `git push origin` (D23; S2's push).

#### Act 4, under Q4(a) (i)

**The gate.** The coordinator checks each item and posts the list:

- 007's code PRs, the root bump and v1.2.0 have landed (`r3c/REPLY.md:153`);
- under Q28 (i), Amendment 23's slices 1a, 1b, 2a, 2b and 3 have landed and are installed (`proposal.md:1946-1954`);
- under Q29 (a), the service is on the gate only if the guard, measured without it, is over Brett's budget, 1 s (`proposal.md:1973-1976`), and it is: from Eagle that guard takes about 1.2 to 2.1 s (lane 5, #165 6725361742), so the service, and v1.2.0 with it, is on the gate (lane 3's ruling, #165 6724332510 and 6726078173);
- act 3's steps 6 and 8 have passed.

1. **The window.** Brett names it, and the coordinator posts notice N2 (2.6) on each coordination issue before it opens (`proposal.md:940-941`).
2. **Readiness again.** The operator lane runs `lanes-index ready --expect index` in every environment.
   - **Why again:** the check covers one moment, and a container started after act 3's run escapes it (`proposal.md:618-619`).
   - **Refusal:** any failure stops the window.
3. **The freeze.** Brett gives the word. The coordinator posts N3, and Brett prompts each pane.
   - **What lanes do:** every lane reaches a stopping point and writes its `PAUSED` record. Those writes must land before the import (`r3b/REPORT.md:243-244`; `proposal.md:1269-1271`).
   - **Way back:** the thaw (step 10). Nothing has changed yet.
4. **The quiet check.** The operator lane confirms that the `lanes` tree id on origin is unchanged for 2 minutes, and that no `lanes-edit.sh` or `lane-start` process runs in any container (`r3b/REPORT.md:245`).
   - **Refusal:** if the tree id moves, the 2 minutes start again.
5. **The ruleset on (F4),** where act 3's step 9 prepared one. Brett enforces it once the quiet check has passed: after the drain's last `PAUSED` write, and before the import.
   - **Why here:** no files-mode writer is left to push, so none is stranded (act 3's step 9). From here a stale writer's push is rejected rather than landing as a stray, and the cutover's push and every render pass as the renderer's identity (D23).
   - **Way back:** a window given up before the cutover's push turns the ruleset off before the thaw (step 10).
6. **The import.** The operator lane acts, in its own checkout, with `LANES_LANE=` its name (F12), after a fetch.
   - **Command:** `lanes-index import --at <C>`, where `C` is the tip of `origin/main` (`proposal.md:518-521`; `r3b/REPORT.md:250`). The journal's seq 0 is its baseline (`proposal.md:1259-1262`).
   - **Its transaction (F23):** it builds the entry and proves its bytes before it sends them, then loads them in one short transaction, so it holds none across git or its render. The S3 answers are compared after the load, with the switch still at `files`. `design.md` §1.4 says how.
   - **Check:** the proof's three parts: each rendered path's blob id equals `C`'s, the counts match, and every lane's S3 answers are the same from the files and from SQL (`proposal.md:1257-1259`; `r3b/REPORT.md:222-230`).
   - **Its key check (LR010):** the import derives every key its lines and holds give and checks each, in path and line order. A line whose key is not a scope stops the import in its own transaction, before anything is written, and `lanes-index import` exits 2 naming the path, the line and the key (`design.md` §1.3's "The act"). The check makes an import of today's register take about 25 s instead of 3 s, by lane 1's measurement of 57.7 s against 7.7 s for 42,001 lines, with 18,205 lines under `lanes/` today. That is inside the import's 300 s bound (`design.md`, "The timeouts (D9)"), and act 3's rehearsal (step 8) measures it on the real register first (lane 3's ruling, option (a), #165 6711924759).
   - **Refusal:** any difference, or a `C` that is not an ancestor of `origin/main` (`r3b/REPORT.md:205`). Nothing is set in the switch (R3a's `register_switch`, `r3a/REPORT.md:179-185`).
   - **Way back:** a re-import replaces the whole store in one transaction, which is allowed while the switch is not `primary` (`r3b/REPORT.md:233`).
7. **The cutover.** The operator lane acts, on Brett's cutover word, with `LANES_LANE=` its name (F12).
   - **Command:** `lanes-index cutover --import <C>` (`proposal.md:1254-1257`; `r3b/REPORT.md:239`). In order, it (`r3b/REPORT.md:252-256`):
     1. sets the switch to `primary` and writes seq 1, the operator entry that adds `lanes/STORE`, in one transaction that commits before step 2 (F2, F23);
     2. builds the `lanes/STORE` commit on the tip `T`, only where `T` descends from `C` and `T:lanes` has `C`'s tree id;
     3. rebuilds when handoff commits land meanwhile, six push attempts in all;
     4. pushes fast-forward only, with the renderer's identity, confirms the push by Amendment 23's S4 read-back (2.1; F2, F12), and records it with `record_marker`, which sets the marker `present` (`design.md` §1.3).
   - **The marker:** `lanes/STORE` holds `store=primary`, `database=` the label, `import=` the imported commit, `lanes-tree=` the tree id of `C:lanes` and `utc=` (D21; `proposal.md:588-590`; `r3b/REPORT.md:261`), and no credential. Its commit carries `Register-Seq: 1` (`r3b/REPORT.md:213-219`) at this first cutover; a later cutover's entry takes the seq after its import's, `m+1` after a re-import or `b+1` after a restore (`design.md` D24), and this step's checks read that seq. Seq 1 is an operator entry: the renderer never renders it, and only `cutover` pushes it (F2).
   - **Check:** the read-back, and `lanes-index status` showing seq 1.
   - **Refusal:** any change under `lanes/` since `C` aborts the cutover (`proposal.md:1255-1257`). The abort waits for a read-back that proves the marker absent from origin. Then, in one transaction (`switch_to_files`, `design.md` §1.3), it deletes seq 1, sets the head back to the import's seq and sets the switch back to `files` (F2; `proposal.md:1776-1779`), and step 6 runs again at the new tip.
     - **A push it cannot confirm** takes neither branch (F12): `cutover` exits 3, and its rerun reads origin first. A marker found there completes the cutover, and a marker proven absent takes the abort.
     - Under 3A no `mode=primary` writer exists before the flip, so a switch left at `primary` is harmless until step 8 (`proposal.md:1776-1778`). Nor can a write follow seq 1 before seq 1 is on origin: `commit_write` refuses a write until the marker is `present`, and `set-mode primary` refuses until the fetched `origin/main` carries the marker (2.4), so no render waits behind an operator seq (F2).
   - **Way back:** after the push, the rollback (2.3).
8. **The flip.** Brett acts on Eagle, then on Raven (Q22 (a)), then on each other configured workstation, once per host and from the host, since one configuration on the host's read-only mount serves every environment there (Q23 (a); `proposal.md:620-622`).
   - **Command:** `lanes-index set-mode primary` **(new)** (2.4).
   - **Check:** `lanes-index ready --expect primary` in each environment on each configured workstation.
   - **Meanwhile:** from the marker's push to the last flip, every register write refuses, since a files-mode writer meets the marker (`proposal.md:169-170`; `r3b/REPORT.md:257`).
   - **Way back:** `set-mode index` on each configured workstation, then the rollback.
9. **The smoke test.** The operator lane writes one `log NOTED` and checks its row in SQL, its mirror commit at the seq after the cutover's entry (`Register-Seq: 2` after a first cutover, `m+2` after a re-import, `b+2` after a restore; `design.md` D24), the read-back, and a lag of 0 in `status` (`proposal.md:944-947`, `:1265-1267`; `r3b/REPORT.md:258`).
   - **Refusal:** any miss is a rollback trigger, on Brett's word.
10. **The thaw.** The coordinator posts N4, and Brett prompts each pane to resume (`r3b/REPORT.md:259`).

**Time.** The mechanical steps take 1–2 minutes, and the window 20–45, almost all of it drain and thaw (`r3b/REPORT.md:281`). By the product seat's estimate, not a measurement, Brett's own time is 45–75 minutes (`proposal.md:1281-1284`).

#### How (ii′) differs

P13's (ii′) applies where every environment passes `ready`; elsewhere (i) applies (`proposal.md:1275-1280`; `clarifications.md:43-54`).

- **The order** (`clarifications.md:48-51`): the rehearsal; `ready` in every environment; the marker's push, as the freeze; the import, at the marker commit's parent; the switch; the flip.
- **No drain, and no prompt to each pane.** N3 says instead that writes will refuse for a minute or two.
- **The marker goes first.** Its commit `S` is pushed as a fast-forward of the tip `C`, carrying `Register-Seq: 1`, and the import runs at `C`, its parent. A `lanes-index cutover --marker-first` **(new)** mode runs both halves in that order.
- **The ruleset (F4), as this part reads it here:** on once the marker's push is read back, since from then a writer that knows the marker refuses before it commits. A stale writer's push is then rejected rather than left as a stray.
- **In the window,** a writer that knows the marker refuses with 69 and "this estate's register is SQL; configure `mode=primary`" (`proposal.md:169-170`). Under 3A no `mode=primary` writer exists yet.
- **A stray commit** from a writer that does not know the marker (ADV-C2) is found before the switch. After a fetch, the rendered paths of `origin/main` must still have `C`'s blob and tree ids. `S` adds only `lanes/STORE`, which is not a rendered path, and the cutover already compares the `lanes` tree in the same O(1) way (`r3b/REPORT.md:253`). Where they differ, either:
  - the switch goes ahead, and `lanes-index import --single <sha>` (D2) loads the stray commit as one write, on a person's word (`proposal.md:385-389`, `:1290-1291`); or
  - the cutover aborts with a commit removing the marker, which carries no `Register-Seq`, since no journal exists yet, and the window falls back to (i).
- **What it gives up:** under (i) the drain holds a stale writer for the window too, and under (ii′) nothing does (`reviews/lane2-165-adversary-on-product-6549222590.md:5-8`).
- **What it saves:** by the product seat's estimate, Brett's time falls to 10–20 minutes (`proposal.md:1281-1284`).

**Q4(a)'s other options.**

- **(ii),** R3's marker first, without the readiness precondition (`proposal.md:1271-1272`): as (ii′), in every environment.
- **(iii),** no freeze, relying on abort and re-import: at an 18 s median gap, step 6 aborts in most windows (`proposal.md:579-583`), and R3 advises against it (`proposal.md:1273-1274`).

### 2.3 The rollback, and the disaster path

#### The rollback, under Q4(b) (iii)

Under (iii) a rollback needs the database (`proposal.md:551-557`).

**Triggers** (`r3b/REPORT.md:304-311`):

- the renderer has diverged, and SQL is not trusted;
- `reconcile` finds the mirror is not a full render and cannot repair it;
- a workstation that must write cannot be configured;
- the credential is compromised;
- the measured costs miss their targets;
- Brett's word, for any reason.

R3b's first trigger, the database down past Brett's limit, 4 hours (his ruling of 2026-11-08), leads under (iii) to waiting or to the disaster path below, because a rollback cannot run without the database. So past 4 hours the register goes back to files by the disaster path, a replacement and its after-loss cutover, or by its "Back to files by hand", never by `lanes-index rollback`. Each loses the unrendered tail after `m`, or after `b` on the restore route.

**Steps.**

1. **The word.** Brett gives it, and the coordinator posts N5 (2.6).
2. **The switch.** The operator lane runs `lanes-index rollback` in its own checkout, with `LANES_LANE=` its name (`proposal.md:527-530`; F12).
   - **Before it starts (F1):** `rollback` refuses with 2 while `status` shows `diverged at <sha>`, since a diverged mirror can never be proved current. The divergence is resolved first, on a person's word, by one of S2's two ways out, `lanes-index import --single <sha>` or `lanes-index render --revert <sha>`. Each is one journal entry through S1's commit call while the switch is still `primary`. A stray whose edits no longer apply to SQL's images is reverted.
   - **Right before its transaction (S5):** after that check, `rollback` fetches `origin main` again and looks for a divergence as the renderer recognises one (D16): a commit after the last render commit that changes a render-set path and is not a render commit, or a last `Register-Seq` above the head (N6). It records one it finds with `record_mirror_state` and refuses with 2, as the check before it starts refuses. This catches a stray already on origin but not yet recorded. One being recorded at that moment is caught inside the transaction: `commit_write` reads `mirror_state` `FOR SHARE` and refuses a `rollback` entry while a divergence is recorded, with LR012 **(new)** (`design.md` §1.3), and `rollback` exits 2 the same way. A 40001, 40P01, 55P03 or LR001 on the call sends it back each time: it reads the head afresh as its `k`, runs this check, and makes the call again on that base, within D7's 30 s deadline, as a writer retries. At the deadline it exits 75, having written nothing, with the switch still `primary` (`design.md` §1.3; V3's NIT 2). The retry is refused only where a divergence was recorded meanwhile. A 40001 can also follow a `record_mirror_state` call that recorded only `last_error`, and then the retry commits.
     - **What stays open:** a stray that lands during the rollback itself, after this check, has no journalled way out once the switch is at `files`, since S2's two ways out need `primary`. That is Brett's open point on a stray landing during a rollback (`design.md`'s open points), which this check narrows to those strays (R2's S5).
   - **What it does first:** in one transaction it sets the switch to `files` and writes the journal's last seq, `k+1`, where `k` is the head it found: the operator entry that removes `lanes/STORE` (F1, F2). Every later SQL write then refuses with 69 everywhere, whatever a writer has fetched (`proposal.md:551-554`), and nothing is journalled after the switch.
   - **The declaration:** from that transaction on, no SQL write can commit anywhere, so the rollback's start declares the outage (2.5). Its `mirror-seq` is `k`, the head that transaction found, not the mirror's `m`: `k+1`, the operator entry, is never rendered (R2's M1). `rollback` writes the declaration itself where its configuration directory is writable. From a bench, whose mount is read-only, it prints the `outage declare --rollback --mirror-seq <k>` command, and Brett runs it on each host.
   - **Refusal:** if the database is unreachable, it exits 69 and changes neither the switch nor the marker (`proposal.md:592-594`). The lanes wait, or Brett takes the disaster path.
   - **A rerun** finds the switch at `files` with the marker `removing` and its own entry as the last seq. The run before it may have died before it declared, so the rerun declares first: it writes the declaration where its configuration directory is writable, and otherwise prints `outage declare --rollback --mirror-seq <k>`, with `k = set_seq − 1`, the head the rollback's transaction found. Then it goes on from step 3. Until a declaration is written, guards fail closed: a guard's read that finds the switch at `files` counts as failed (2.5, condition 1), and with no declaration (condition 2) the guard refuses 2, the operator lane's own included, so that rerun runs from a shell or a `CLAUDE_NO_LANE=1` session, as step 3's does. A switch transaction whose outcome is unknown is settled the same way, by reading the switch again (F12).
3. **The render.** `rollback` renders until the mirror carries every seq up to `k`, and skips `k+1`, an operator entry (F2). It proves a full render from SQL equal to the mirror tip by `lanes` tree id, with `lanes/STORE` aside (`r3b/REPORT.md:288-289`). Since a rollback's declaration carries `mirror-seq=k` (R2's M1, as lane 3 folded it at `3c759ffd`), every guard whose ref is behind `k`, the operator lane's own included, refuses 2 until the mirror carries `k`, so a rerun after a failed render runs outside any guarded session: from a shell, or a `CLAUDE_NO_LANE=1` session with `LANES_LANE=` the operator lane (2.5).
   - **The ruleset off (F4):** where act 4 enforced one, Brett turns it off once this proof holds, and before step 4's push. Until the marker goes, a writer that knows the marker still refuses with 69 before it commits, so none is stranded; after it, a files-mode write lands. So the operator lane runs `lanes-index rollback --hold-marker` **(new)**, which stops here, and once Brett has turned the ruleset off, `lanes-index rollback` again, which resumes at step 4. Under Q21(b) (A) one call runs every step.
4. **The marker.** `rollback` pushes one commit removing `lanes/STORE`, with `Register-Seq` `k+1`, the journal's last seq (`r3b/REPORT.md:290`; R3a's `journal.kind` value `'rollback'`, `r3a/REPORT.md:192`).
   - **Its push (F2, F12):** it builds the commit on the tip only where the tip's render set still equals the images at `k`, pushes it with the renderer's identity, confirms it by Amendment 23's S4 read-back (2.1), and records it with `record_marker`, which sets the marker `absent`; a re-import waits for that (`design.md` §1.3). A push it cannot confirm exits 3, and the rerun reads origin back first.
5. **The flips back.** Brett runs `lanes-index set-mode index` on each configured workstation (Q22 (a)). The order does not matter: a workstation still on `mode=primary` meets the switch at `files` and refuses, and a cleared one meets the marker until it is gone, and refuses (`r3b/REPORT.md:291`).
6. **The check.** A files-mode write succeeds, and a workstation not yet cleared refuses (test 13's end, `proposal.md:910-912`). Brett then runs `lanes-index outage clear` (D20) on each configured workstation (2.5).
7. **The notice.** The coordinator posts N6, saying what the files lose (`r3b/REPORT.md:292-297`): the journal, though each seq's commit and trailer stay in git; the mode that last wrote each row; the database clock's order; and SERIALIZABLE claims, so `CLAIM-LOST`, exit 7 and push-order races return.
   - **No write is lost** under (iii): the database is up, and step 3 renders every seq before the marker goes.

**Q4(b)'s other options.**

- **(iv)** keeps Amendment 22's allowance (`proposal.md:558-565`).
  - **The rollback:** step 2 may run with the database unreachable, from a mirror `status` last showed current, as recorded in `status`'s state file (`r3b/REPORT.md:299-300`).
  - **The writes:** such a rollback cannot set the switch, so every `mode=primary` write first checks `lanes/STORE` on origin, by a fresh fetch or `ls-remote`, and a failed check is 69. That is a GitHub round trip on every write (`proposal.md:563-565`).
  - **The loss:** a rollback with the database down loses the unrendered tail, as the disaster path does.
- **(ii)** is (iv)'s allowance with R3b's proof by `Register-Head` and the spools, which must be spools the operator can list (`proposal.md:566-570`, `:1304-1310`). The probes found it leaks (`proposal.md:1312-1318`), so Brett takes that risk.
- **(i)** is the rollback as ratified, and it leaks the same way (`proposal.md:566-568`).

#### The disaster: the database lost for good, under Q22 (a)

Under (iii) this is not a rollback. It is a restore or a re-import, run with every workstation's lanes paused first (`proposal.md:555-557`, `:1296-1299`).

1. **The trigger.** The database has been unreachable past Brett's downtime limit, 4 hours (act 3's step 1), and he says it will not return in time.
   - Until that word, lanes wait. Every write and deciding read refuses with 69, and the guard refuses with 2 (`proposal.md:422-432`).
2. **The declaration.** Brett declares the outage on each configured workstation (2.5; Q22 (a)), so that he can reach his lanes. Every write and every other deciding read still refuses (`proposal.md:1870-1871`). The coordinator posts N7.
3. **The pause.** Brett prompts each pane to stop.
   - A swap writes its handoff file from a mirror read.
   - Its `PAUSED` record refuses with 69, and the lane stays `SWAPPING` until the late path completes it (Q8(b), round 1's 2A, `proposal.md:1577-1584`).
4. **The loss, recorded.** The operator lane runs `lanes-index status`. It cannot reach the database, so it prints `m`, the mirror tip's `Register-Seq`, and `h`, the last head it recorded, or "unknown".

   **Every write that SQL committed after `m` exists only in SQL, because none was rendered** (`proposal.md:1829-1833`; `clarifications.md:71-72`; `reviews/lane1-165-product-q26-narrowed-6549261651.md:12`). With the database lost, a re-import loses all of them, and a restore keeps only those up to the backup's head. The renderer's lag, which `status` shows, bounds the loss. Where no head was recorded the bound is unknown, and only the lanes can say what they wrote.
5. **The old database, retired.** Brett revokes the old roles wherever the old host can still be reached. Step 6 re-points every host before any write resumes (F18).

   ADV-C1's break is a database that comes back with the switch still at `primary` (`clarifications.md:386-389`). A retired database cannot take a write.
6. **The replacement.** The install creates a new CNPG Cluster, to the contract (2.2). Roles belong to the cluster: a physical backup carries them and a database dump does not (lane 1's run). So before a restore from the daily dump, or a re-import, Brett runs `lanes-index bootstrap --roles-only` **(new)** first (2.4; F18); a point-in-time restore needs none. Then, on his word, he takes one of two routes:
   - **Restore,** only where a backup's head `b` is newer than `m` (D22). He restores it, then runs `bootstrap` again, which migrates it forward or prints `unchanged`. The restored switch keeps the lost database's label until the marker's rewrite (below) sets the replacement's, from the re-pointed `register_label=` (S-2). The marker's rewrite renders from `m`. Lost: every seq after `b`. **From the physical backups** (the contract's item 5): the install creates the new Cluster by CNPG's recovery from the Barman Cloud object store, never in place, to the latest WAL or to a `recoveryTarget` time Brett names. Its `bootstrap.recovery` names the register's database and owner, and its backups take a `serverName` of their own, so it never archives over the backups it restores from. With WAL archived at least every 5 minutes and the archiver keeping up, its `b` is within about 5 minutes of the loss. It carries the roles, their SCRAM verifiers, memberships and floors (lane 1's run of a physical copy), so `--roles-only` prints `unchanged` and no password line. Brett then runs `lanes-index login rotate` for each engineer and the dump job, and the re-pointing writes the new lines, so that a workstation not yet re-pointed cannot log in there though `<endpoint>` now reaches it. **From the daily `pg_dump`:** into a new Cluster whose `initdb` made the database and its owner, after `--roles-only`, Brett restores both schemas through his admin service, with `pg_restore --exit-on-error --no-password --dbname=<database> <file>`, then runs `bootstrap`, and `lanes-index login add` for each engineer and the dump job. Lane 1 ran that restore of both schemas as such an owner, with no superuser and the roles in place, on PostgreSQL 16.15: the restored catalogs, rows and `holds` equalled the source's, the privileges compared as sets.
   - **Re-import,** the default (D22), where no backup is newer than `m`. Brett runs `bootstrap`, and after the re-pointing the operator lane runs `lanes-index import --at <M> --after-loss` **(new)**, where `M` is the tip of `origin/main`, which still carries the marker.
     - **The seq:** the import's baseline takes `M`'s newest `Register-Seq` instead of 0 (`design.md` §1.3: the chain's one root sits at `m`, and the head starts there). Seqs on `main` then stay unique, so a claim comment's "register seq `<n>`" (Q19, `proposal.md:1763-1767`) still names one commit.
     - **Lost:** every seq after `m`.
     - **Its time:** the key check of act 4's step 6, about 25 s on today's register instead of 3 s, inside the 300 s bound; a bad key stops the import before anything is written (lane 3's ruling, #165 6711924759).
   - **The re-pointing (F18),** on either route, before any `lanes-index` command reaches the replacement: on each configured workstation (Q22 (a)), from each host, Brett sets `register_url=`, `reader_url=`, their password files and `register_label=` **(new)** to the replacement's, in one edit, with the lines the bootstrap printed. `register_label=` names the replacement: after a re-import, the label its bootstrap wrote; after a restore, the `--label` Brett gave its `bootstrap`, which must differ from the lost database's label that the restored switch still holds (N4, below). The lanes stay paused (step 3). With two logins per engineer (Brett, "Two logins each", #165 6723413756), a replacement restored from the dump, or re-imported, holds no engineer's login until `lanes-index login add` makes each again, and one restored from the physical backups holds every login with its old password until `lanes-index login rotate` changes it; either way each login has a new password, and each workstation's re-pointing writes its own engineer's lines.
   - **The marker, as the cutover's own entry (F18; D24):** the operator lane runs `lanes-index cutover --import <M> --after-loss` **(new)**. The marker's rewrite is not a third operator entry: it is the cutover's entry in its after-loss form, in the cutover's own transaction, at seq `m+1`, so the next write is `m+2`. That transaction sets the switch to `primary` with the replacement's label, a new generation id (F10, F18) and `set_seq` `m+1`, its own seq (lane 3's open point 23), and rewrites `lanes/STORE`'s fields for the replacement. The command then pushes the rewrite as 2.1 says. D22 as first written left the marker as it was, so its next write would have been `m+1`; this part rewrites it, as lane 3 ruled (#165 6578020193), because `ready` and `set-mode` check that `lanes/STORE` names this database, and the replacement's label differs.
     - **The label (N4; V2's S-2):** `cutover`, in every form, takes its label from the configuration's `register_label=`, never from a flag: the entry's `database_label` in the after-loss forms, and `lanes/STORE`'s `database=` in every form. So the label it sends names the database its `register_url=` reaches: a host not yet re-pointed sends the live database's label, and a re-pointed host the replacement's. Where the switch it meets is at `primary`, as on a restored backup, and as on the live database when a host not yet re-pointed runs either form by mistake, the transaction refuses with LR004 unless the entry's label differs from the switch's, and `cutover` exits 2, writing nothing (`design.md` §1.3). A restored backup's switch still holds the lost database's label, so a re-pointed host's label differs and the restore route finishes; a host not yet re-pointed sends the live database's own label, so it is refused. So a restore needs a new label for its replacement, which the re-pointing writes into `register_label=`. The label is not derived from the URL, so the replacement's host and database name may stay the lost database's (V3's NIT 4). Where the switch is at `files`, at a first cutover and after a re-import, it holds the label its bootstrap wrote, the replacement's own after a re-import, so the transaction does not compare the label there. `cutover` itself refuses with 2 there, before it sends anything, unless `register_label=` equals the switch's label: `cutover: register_label=<x> is not the switch's label <y>; set register_url= and register_label= together (S-2)`. So a file whose URL was re-pointed without its label cannot stamp the lost database's label on the replacement. An entry with no label is refused in both forms, and a file with no `register_label=` is refused with 2 before `cutover` connects: `cutover: lanes-index.conf has no register_label= (S-2)`.
       - **The residual:** a file whose `register_label=` was changed without its `register_url=` sends a new label to the live database, where it differs from the switch's, so N4 lets that cutover through. One edit sets both (the re-pointing, above). `ready`'s check 10 (2.4) cannot stand before a restore's after-loss cutover: the restored switch still names the lost label there, so the check fails by design even on a host re-pointed correctly. At step 7 it fails a host re-pointed only in part, once the database it reaches is at `primary`. A new password file narrows it only where the replacement is on a new cluster: the bootstrap made the roles there with new passwords, so a file whose password file was changed with its label, and holds no line for the live database's host, cannot log in there and exits 69. On the same cluster the roles and their passwords are shared, so nothing narrows it there. Step 5's revocation narrows it wherever the old host can still be reached.
     - **The abort of this form,** the re-import's, keeps the label and generation its cutover set. The new generation is what keeps every pending record and printed rerun made against the lost database refusing with LR007, and the next cutover sets both again (lane 3's ruling, #165 6710770304, lane 1's point 2).
     - **After a restore,** the same entry follows the backup's head instead, as `lanes-index cutover --after-loss` with no import, at `b+1`. It first renders every seq the mirror lacks, up to `b`, as the rollback renders.
     - **Its abort,** once a read-back proves the rewrite absent from origin, removes the entry at `b+1` and puts the head back to `b`. It restores what the entry changed: the switch's fields, with the marker `present`, and `lanes/STORE`'s lines (`design.md` §1.3). It does not set `files`. It leaves no seq after `b` and changes nothing else, so the estate stays stopped, with the lanes paused (step 3), until the cutover is run again (lane 3's ruling, 6579459518, point 1).
     - **The lost tail's window:** between the restore and this cutover, and again after its abort, the database carries the lost database's generation, so neither a write id nor an act's base from the lost tail, above `b`, is refused for its generation; an act's base is refused only while it is past the head, where S1's client check at the snapshot exits 2 before the call and LR004 is the backstop (`design.md` §1.3). The window stays as v2.3 left it for write ids, and the pause (step 3) covers it until the cutover's new generation closes it (lane 3's ruling on lane 1's v2.4, #165 6711233420, point 4).
   - **The generation (F10, F18):** every pending record and every printed rerun made against the lost database carries the old generation, and now refuses with 2 rather than re-apply a write the mirror may already hold. Each lane checks the mirror instead (step 7).
7. **The resumption.** `lanes-index outage clear` (D20) on each configured workstation first, since `ready`'s check 7 fails while a declaration is recorded (2.4), as S5 orders it. Then `ready --expect primary` in each environment, and the smoke test; the lanes stay paused until both pass. N8 names `m`, `h` and the loss. Each lane then checks its last records against the mirror, and writes any owed record again, with the database's UTC and the original time in its free text (`r3b/REPORT.md:318`).

**Back to files by hand.** Brett may instead return the estate to files: a commit removing the marker, made and confirmed as 2.1 says, with any ruleset turned off first (F4, F12), then `set-mode index` on each configured workstation. It loses the same tail. It is safe only after step 5, because ADV-C1's leak needs the old database to come back.

**Q22's other options.**

- **(b): Raven is not a writing workstation** (`proposal.md:1814-1815`). Act 3's steps 3 and 5 skip Raven, and so do act 4's flip, the rollback's flips back, the disaster path's re-pointing and every declaration, but `ready` still runs there, to show that its tools refuse under the marker as an unconfigured workstation's do (Q21(a) (A), `proposal.md:1783-1786`).
- **(c): the product seat's option** (`proposal.md:1817-1825`).
  - **The setup:** the database on Eagle; a downtime limit Brett names; Raven upgraded but not configured, which `ready` covers (`clarifications.md:72-74`).
  - **Its recovery:** a rollback from a current mirror, then a re-import. Under (iii) that is the disaster path above without the restore route, with the same loss (`proposal.md:1825-1833`).
  - **Its cost:** it departs from Amendment 22's act 3, so it needs Brett's word.

### 2.4 P8's three operator tools

The proposal leaves the three tools' names to the design (`proposal.md:602-605`). Each is a `lanes-index` subcommand beside `sync`, `reconcile`, `status` and `export` (`code/lanes-index:2690`). Four rules hold for all three:

- **Exits:** `lanes-index`'s own 0, 2 for a refusal, and 64 for usage (`code/lanes-index:72-76`), plus 69 where the database cannot be reached or refuses. The 69 is R3's answer to Q9 (`proposal.md:1610-1611`), and Q9 settles it.
- **Secrets:** none reads a secret from the environment (Amendment 14(c), `clarifications.md:144-146`).
- **Connections:** none takes a connection string in an argument list, its own or `psql`'s. The admin connection is a libpq service name, which `psql` receives only through its environment as `PGSERVICE=<service>`, never as `service=` or `-d` on a command line (SVC-2; lane 5, 6579523948), and every other connection comes from `lanes-index.conf` (lane 5's choice 11, which lane 3 accepted for every `lanes-index` path, #165 6578521506).
- **Bounds:** every database connection sets its bounds when it connects, from D9's table (`design.md`, "The timeouts (D9)"). `ready` and `set-mode` are readers, with a reader's bounds in D9's table (`design.md:3495`): `connect_timeout=`, the workstation's own `c`; `lock_timeout=1000` and `statement_timeout=3000`; and a client timer of `N_a`·`c` + 4 s, never below 6 s (`proposal.md:799-800` has the guard's tighter pair). `bootstrap` is a migration, with its own `lock_timeout`, retried.

#### `lanes-index bootstrap` (new)

- **Inputs:**
  - `--admin-service '<service>'` **(new)**: the admin connection Brett supplies, used once and held by nothing (`proposal.md:607-611`). It names a service in his own `~/.pg_service.conf`, with its password in his own 0600 `~/.pgpass`, so neither the name nor anything else on the command line is a connection string. `lanes-index` drops an inherited `PGSYSCONFDIR`, `PGSERVICEFILE`, `PGPASSFILE` or `PGPASSWORD`, as it drops the last two today (`code/lanes-index:1300-1305`), so only his own `~/.pg_service.conf` defines the service (lane 5, 6579523948) and no secret comes from the environment (Amendment 14(c));
  - `--roles-only` **(new)**: only the roles, for the disaster path's restore, since roles belong to the cluster and no database dump carries them, while a physical backup does (F18; 2.3; lane 1's run);
  - `--label '<name>'`: the database's first label, with no credential, which the bootstrap writes into the switch when it creates it (`proposal.md:166-168`), and prints as the `register_label=` **(new)** line among the `lanes-index.conf` lines it prints. The hosts' files carry it from there (act 3's step 3; 2.3's re-pointing), and every `cutover` takes its label from that key, never from a flag (S-2). The bootstrap does not refuse a `--label` that differs from a host's `register_label=`: it runs through the admin service, and in act 3 and on the disaster path it runs before the hosts' files name the database, so there is no key to compare yet. `ready`'s check 10 and `cutover`'s own checks compare instead. With the switch at `primary`, as on a restored backup, it writes no label (below); the cutover sets the replacement's;
  - the role names: by default `design.md` §1.4's, `lanes_register_owner`, `lanes_register_writer` and `lanes_register_reader` (the last two R3a's, `r3a/REPORT.md:172`, `:281`), and `lanes_indexer` for the indexer, whose `url=` is `code/lanes-index:2607`;
  - `--repository '<owner/name>'` **(new)** and `--lane '<name>'` **(new)**: the workspace repository the register serves, and the lane or person running the bootstrap. With the host's workstation name they fill the switch's `repository`, `set_by_lane` and `set_by_ws` and the `migrations` row's `by_lane` and `by_ws`, which `design.md` §1.2's DDL makes NOT NULL;
  - `--no-passwords`: for roles that authenticate by peer, trust or certificate, which `lanes-index` already allows (`code/lanes-index:1489`).
- **What it does:**
  - creates schema `lanes_register` (R3a's) with its tables, or migrates it forward to `REGISTER_SCHEMA_VERSION` (R3a's, `r3a/REPORT.md:326`; `design.md` §1.5);
  - creates the owner role and the groups REGISTER-WRITER and READER, none of which can log in, and the indexer's login, with their GRANTs (`design.md` §1.4; `r3a/REPORT.md:280-282`); each engineer's logins, and their floors, the writer logins' `idle_in_transaction_session_timeout` (D9) among them, are `lanes-index login`'s (below);
  - confirms that the indexer cannot write the register's tables (`proposal.md:688-690`);
  - writes the switch at `files`, with the marker `absent`, its label, `set_seq` -1 and a new generation id (F10, F18), and the head at -1, and no journal entry: the import writes seq 0, and the cutover sets the switch to `primary` (`design.md` §1.2, `design.md` §1.3);
  - runs each step as one transaction.
- **Output:**
  - one line per object, `created` or `unchanged`;
  - the `lanes-index.conf` lines, with no secret, `register_label=` among them;
  - for the indexer's login, where it creates it, its password-file line, printed once; each engineer's lines are `lanes-index login`'s.
- **The secret:** it generates a role's password only when it creates the role. It refuses when stdout is not a terminal, so no lane transcript, log or pipe ever holds one (P20, `clarifications.md:135-146`). Brett runs it from a shell. It sets each password as a SCRAM-SHA-256 verifier computed on the client, as psql's `\password` does, never as clear text in SQL, since the server's log, which the install keeps, may hold a failed statement whole (`log_min_error_statement`; PostgreSQL's caution under `CREATE ROLE`).
- **Refusals:**
  - 69: the admin connection fails;
  - 2: an argument carries a connection string or a password, such as a `key=value` pair or a URI (lane 5's choice 11);
  - 2: the admin connection may not create roles or schemas, or does not own the database it names (`pg_has_role(current_user, datdba, 'USAGE')` on that database's `pg_database` row), since there its database-level `REVOKE` and `GRANT` only warn (`design.md` §1.4);
  - 2: the schema is at a version newer than this `lanes-index`'s `REGISTER_SCHEMA_VERSION`. An older one is migrated forward, never down (`r3a/REPORT.md:326-327`; `design.md` §1.5);
  - 2: a role holds a privilege outside its scope. The privilege is named and never revoked silently;
  - 2: `store=sqlite` (`proposal.md:162-163`).
- **With the switch at `primary`,** it only verifies, applies a pending migration (`design.md` §1.5), and changes nothing else.
- **Test:** run twice, it leaves one schema and the same GRANTs (`proposal.md:623-624`).

#### `lanes-index ready` (new)

- **Inputs:**
  - `--expect index|primary`: `index` until the flip (Q20 (B));
  - `--release '<version>'`: the release whose digests the installed tools must match;
  - `--env '<name>'`: this environment's label in the report.
- **Checks,** each a read that writes nothing (`proposal.md:612-619`):
  1. the installed tools' digests equal the release's;
  2. `psql` 16 is present;
  3. the configuration is readable, 0600, outside every git work tree and `~/.agents/` (`code/lanes-index:66-68`), and says the expected mode;
  4. each role connects within the workstation's `connect_timeout=`, its own `c` (D9), at each address the endpoint resolves to, and is refused outside its scope. A write tried in a transaction that always rolls back, opened with `BEGIN READ WRITE`, must fail with SQLSTATE 42501, which is this part's concrete form of "refused outside its scope". The `READ WRITE` matters for the READER, whose role default is read-only (`design.md` §1.4): a plain write there fails with 25006 before any privilege is checked;
  5. the installed `lanes-index` no longer carries Q6(c)'s refusal of `mode=primary`: `lanes-index mode` on a `mode=primary` configuration does not exit 78 (`EX_CONFIG`), Q6(c)'s code (F8; agreement test 14, `proposal.md:913-921`);
  6. the workstation's name is set, so a write is never stamped with a container's hostname (`r3b/REPORT.md:192-194`);
  7. no outage declaration is recorded (2.5);
  8. with `--expect primary`: the switch is at `primary` with the marker `present`, and the fetched `origin/main` carries a `lanes/STORE` that names this database.
  9. the installed tools' `REGISTER_SCHEMA_VERSION` lies inside the database's window (`design.md` §1.5): from `writers_from`, or `readers_from` in an environment that only reads, up to `version`, so no write or read there would refuse with LR003 (lane 3's ruling, #165 6578020193).
  10. with the switch at `primary`, whatever `--expect` says: the configuration's `register_label=` **(new)** equals the switch's `database_label`, or it fails with `register_label=<x>, but the switch names <y>`. So a file re-pointed only in part fails before its `set-mode`: its label without its URL, against the live database, and its URL without its label, once the replacement's cutover has set the replacement's label (2.3, S-2).
  11. each role's connection is encrypted and reaches the primary: its URL carries `sslmode=verify-full` and `sslrootcert=`, its own `pg_stat_ssl` row (`WHERE pid = pg_backend_pid()`) shows `ssl` true, and `pg_is_in_recovery()` is false. Any role reads both, and lane 1 read them as all three (`design.md` §1.4, "TLS and the endpoint").
  12. each login's own floors: its row in `pg_roles` (`WHERE rolname = current_user`) carries its group's, the writer login's `lock_timeout=2000ms`, `statement_timeout=5s` and `idle_in_transaction_session_timeout=5s`, and the reader login's `default_transaction_read_only=on`, `lock_timeout=1000ms` and `statement_timeout=1500ms`, or it fails naming the login and the floor. Any login reads its own row (lane 1's run).
  13. the endpoint's address count: for each distinct host its URLs name, `lanes-index` counts the distinct addresses the host resolves to, by the lookup libpq makes for a host name (`getaddrinfo` with `SOCK_STREAM`, any family and no flags; libpq 16's `fe-connect.c`), and compares the count with the workstation's `N_a` (lane 4's D9: its configuration value, default 1). A count above `N_a` fails, naming the host, the count, `N_a` and the addresses, since every timer counts `N_a`·`c` and a larger count leaves a connect no timer holds; `N_a` is then measured again and raised (act 3's step 1). A count at or below `N_a` passes and prints both. It reads only the resolver, never the database, and it counts an address that would fail at once, such as an IPv6 address the host has no route to, so it errs long.
- **Output:** one line per check, `ok` or `FAIL` with the reason. A last line names the environment, the host, the workstation and the tools' version. It prints no secret.
- **Exits:** 0 when every check passed; 2 when any failed, each failure named; 64 for usage. It changes nothing.
- **Limit:** it checks one moment (`proposal.md:618-619`). So act 4 runs it again at the window, and again after any recreate.
- **Test:** it fails on a stale digest, a missing `psql`, an early `mode=primary`, a role with a GRANT outside its scope, an installed `lanes-index` that still refuses `mode=primary` (`proposal.md:624-626`), and tools outside the schema's window.

#### `lanes-index set-mode` (new)

- **Inputs:** `primary` or `index`, and `--config '<path>'`, which defaults to the configured file (`code/lanes-index:62-64`).
- **What it does:** it rewrites only the `mode=` line, adding one where none exists, by writing a temporary file in the same directory and renaming it. The file keeps its 0600 mode (`proposal.md:626-627`).
- **Output:** the path, the old mode and the new one.
- **Refusals, setting `primary`:**
  - 2: the switch is not `primary` with the marker `present`, or the fetched `origin/main` has no `lanes/STORE` naming this database. A flip before the cutover makes every write refuse (Q20 (A), `proposal.md:1772-1773`);
  - 2: the installed `lanes-index` still carries Q6(c)'s refusal, which `lanes-index mode` reports with 78 (`EX_CONFIG`) (F8), as `register read` does. `set-mode` refuses with 2, as every other `lanes-index` command refuses `mode=primary` then, and prints test 14's text (`proposal.md:916-918`);
  - 2: the file is on a read-only mount, so it must be flipped from the host (`proposal.md:621-622`);
  - 2: `store=sqlite`;
  - 69: the database is unreachable.
- **Setting `index`:** it refuses only on a usage error, because the rollback and the disaster path need it with the database down. Where the switch still reads `primary`, it warns that this workstation's writers will now refuse with 69 under the marker (`r3a/REPORT.md:317`).
- **Who:** Brett, once per host where one configuration serves the host (`proposal.md:620-621`).
  - The product seat had `cutover` flip its own host (`council-product/REPORT.md:386`). The proposal makes the flip a command of its own, and so does this part. It is the setter beside `lanes-index mode` (D3), which only prints the mode; the frame names no verb for the flip.

#### `lanes-index login` (new)

Brett's "Two logins each (Recommended)" (#165 6723413756), as `design.md` §1.4's login step and revoke run it. CNPG's declared roles make only the database's owner (`design.md` §1.4, "One engineer's logins").

- **Who and where:** Brett, from a shell on his workstation, through `--admin-service` as the bootstrap connects, under the four rules above (exits, secrets, connections, bounds), with the bootstrap's rules for a secret: it refuses when stdout is not a terminal, and it sets each password as a SCRAM-SHA-256 verifier computed on the client.
- **`add <engineer>`:** makes `lanes_w_<engineer>` and `lanes_r_<engineer>`, each with a password it generates, and prints, once, their two password-file lines and the `register_url=` and `reader_url=` lines for that engineer's file. Run again, it changes nothing but a lost membership, which it puts back. `--dump-job` makes `lanes_register_dump` alone, whose password Brett stores as the Key Vault secret the dump CronJob reads (act 3's step 1).
- **`rotate <engineer>`:** sets new passwords for both logins and prints the new lines; a session already open goes on until it ends. Until that engineer's two files carry the new lines, that workstation's new connections fail at login, 69, so Brett writes them before its lanes resume.
- **`revoke <engineer>`:** for each of the two logins, in order: `NOLOGIN`, the membership revoked, then its sessions ended, which the admin does by holding the login's privileges for that step alone, so it needs no `pg_signal_backend`. It prints each step's result. A write of that engineer's cut mid-commit is in doubt (`psql` exit 2, `design.md` §1.3's signals table), and its lookup cannot reconnect, so the journal answers it by its `write_id`.
- **What a revoked login sees:** a new connection is refused, FATAL 28000; an open session's next statement is refused, 42501; an ended session gets FATAL 57P01 (`design.md` §1.4).
- **Restores:** a database dump holds no login, membership, verifier or floor; a physical backup holds them all (lane 1's run). On the same server they stay. After a point-in-time restore of the Cluster they are there with their old passwords, and `rotate` gives each login a new one before the re-pointing (2.3). On a new Cluster restored from a dump, or re-imported, `bootstrap --roles-only` makes the groups and `login add` each engineer's logins again, with new passwords, which the re-pointing writes (2.3).
- **Refusals:** 2: an engineer name that is not `^[a-z][a-z0-9_]{0,40}$`; 2: an argument that carries a connection string or a password; 69: the admin connection fails.
- **Test:** two engineers' logins write and read; a member made without the step shows none of the floors; a revoked login is refused at connect and in an open session; the dump job's login dumps both schemas.

### 2.5 The declared outage, under Q26 (a′)

**The rule.** The guard answers from the mirror only during an outage Brett declares, on his word or from a rollback's start, with the declaration readable by each guard. Otherwise (b) holds (`proposal.md:1872-1878`; `reviews/lane1-165-product-q26-narrowed-6549261651.md:9`). The declaration cannot be a row in the database that is down (`proposal.md:1877-1878`).

**How it is declared.**

- **By Brett's word:** Brett runs `lanes-index outage declare --until '<utc>' --why "<Brett's words>"` (D20) on each configured workstation (Q22 (a)), from each host, as with `set-mode`.
  - **`mirror-seq` (F11):** it records the newest `Register-Seq` on `origin/main`, read as `status` reads `m`, after a fetch. Where the host cannot fetch, Brett names it with `--mirror-seq <m>` **(new)**, from `status` on a host that can; with neither, it refuses with 2. A rollback's declaration records the rollback's head instead (below).
  - `--until` is required. Brett names how long the outage lasts, in line with Q22's downtime limit; the expiry is his (D20).
  - `--why` records his words verbatim.
- **From a rollback's start:** at its first step, `lanes-index rollback` writes the same record, marked as from a rollback, wherever its configuration directory is writable, with `mirror-seq=<k>`, where `k` is the head its transaction found (2.3's step 2), not the mirror's `m`: the database is up, so `k` is known exactly, and `k+1`, the rollback's own entry, is never rendered (R2's M1). Brett runs `outage declare --rollback --mirror-seq <k>`, as `rollback` prints it, on each other host, and on its own host where it runs in a bench (2.3). A guard whose ref is behind `k` then refuses 2 with SVC-12's text for a mirror below the declaration's seq, which tells the person to fetch it once origin carries `k`, or wait for the outage to end (below). The frame's S3 has the same declaration at a rollback's start (`design.md` S3, "A rollback's start declares one too"): under Q4(b) (iii) the database is up, but a guard that finds the switch at `files` would otherwise refuse every prompt while the rollback still needs a session (`r3a/REPORT.md:319`; `proposal.md:1872-1875`), and lane 3 accepted it for D20 at this section's fold (#165 6578020193).
- **Refusals:**
  - 2: the database answers from this host, unless the switch reads `files` after a rollback, and then only in the `--rollback` form (below). From this host the outage is then not estate-wide, which is lane 2's point (`proposal.md:1886-1891`);
  - 2: no `--until`, on Brett's word only. A rollback's declaration carries no expiry and needs none: it records the rollback's head `k` (R2's M1), and a guard honours it only where its own read finds the switch at `files` (condition 2, below), so it is safe whenever it is cleared: once a cutover sets `primary` again, it opens nothing. The printed `outage declare --rollback --mirror-seq <k>` carries no `--until` (lane 5's check of commit L, #165 6714540821; V2's S-4);
  - 2: `--rollback` without `--mirror-seq <k>`. Typed by hand without it, `outage declare` would record the newest `Register-Seq` on origin, the mirror's `m`, as under Brett's word, and reopen R2's M1 on that host (lane 5, 6714540821);
    - **and with it,** where the database probe (`design.md`, "The timeouts (D9)") reads the switch at `files`, 2 unless the journal entry at the switch's `set_seq` is a `rollback` and `<k>` is `set_seq - 1`, the head before it (lane 5's optional check, which lane 3 takes);
    - **and with it,** where the probe cannot read the switch, 2: no answer within its bounds, or any other end before the read completes. A rollback's declaration counts only where a guard's own read finds the switch at `files` (condition 2, below), so on a host that cannot read it a declaration gains nothing, and would only be left behind (V2's S-4; lane 5);
  - 2: Brett's word while the probe reads the switch at `files` after a rollback. The refusal prints `outage declare --rollback --mirror-seq <k>`, with `k` as `set_seq - 1`, for Brett to run instead, so that no declaration made during a rollback records the mirror's `m` (R2's M1; lane 5, the check of commit M).

**Where it is recorded.** In a file, `register-outage` (D20), beside `lanes-index.conf` in the same directory (`code/lanes-index:62-64`).

- **Its fields,** none of them a secret: the database label it covers, his words verbatim, the UTC, the host and the expiry (D20), which a rollback's declaration does not carry (the refusals, above); `mirror-seq=<m>`, the mirror's `Register-Seq` when it was declared, or the rollback's head `k` in a rollback's declaration, which the guard's client checks (F11; SVC-12; R2's M1); and, for the disaster path's notice, the last head `status` recorded.
- **Its reach:** under Q23 (a), the host's read-only mount carries the file into every bench container on that host, a mount py-bench does not have yet (SVC-12). One write per host reaches every guard there.
- **The audit record:** the coordinator posts the declaration on #165 and on each coordination issue, as N5 or N7. No register write can carry it, since every write refuses during the outage.

**How each guard reads it.** Only a guard on `mode=primary` reads it, through its client, never the service (SVC-12). It answers from the mirror only when SVC-12's three conditions hold, checked in this order, offline:

1. Its database read has failed: the service answered `refuse db …`, or it was unavailable and the direct read then failed (`register read` exit 69). This part also counts a read that finds the switch at `files` as failed, for the rollback's start above.
2. `register-outage` is present beside `lanes-index.conf`, well formed and unexpired, and names this workstation's database label. A declaration marked as from a rollback carries no expiry, so it counts as unexpired until it is cleared, but it counts only where the read in 1 found the switch at `files`; once a cutover sets `primary` again, or in a partition during the rollback, it opens nothing (V2's S-4). The client reads it once, at the prefetch, before the direct read (the budget, below).
3. The mirror it reads, `origin/main` as last fetched (the guard never fetches), carries a `Register-Seq` of at least the declaration's `mirror-seq=<m>` (F11). The client reads the last `Register-Seq` trailer on that ref's first-parent history. SVC-12 has it as form C, one `git log --first-parent -n 200 --format='%H%x00%(trailers:key=Register-Seq,valueonly)'` on that ref: the client takes the first non-empty value, and refuses 2 if none of the 200 carries one (lane 5, 6579523948 and 6579761607; lane 2 timed it at 13 ms median on 34,144 commits). The earlier form, one `git log -1 --first-parent --grep='^Register-Seq: '`, walks the whole history when no render commit is near the tip, and is not used. The bound must come from a `git log` that does not filter with `--grep`, because with `--grep` the limit counts matches and not commits walked. The renderer writes `Register-Seq: <n>` in each render commit's final trailer block, where `%(trailers)` reads it.

It then runs today's parser on the mirror's text and exits 0 or 2 as it gives, with S3's `read: mirror at <sha12>, …` line and `(declared outage <utc>: <why>)` in place of the cause (`proposal.md:444`; D20), where `<utc>` and `<why>` are the declaration's own. Once the marker is gone the files are the truth, so the mirror's answer stays right.

- **A mirror below `m`** refuses 2: "the mirror here is at seq `<s>`, behind the declared outage's seq `<d>`; fetch it once origin carries seq `<d>`, or wait for the outage to end" (SVC-12; lane 5's text, #165 6714540821).
- **A database that answers with the switch at `primary`:** the guard answers from it, and the declaration plays no part.
- **Any other failed condition** refuses 2 with S3's text: the store, the cause, and the two ways out, which are to wait, or to run `lanes-index status` and `rollback` from a shell or a `CLAUDE_NO_LANE=1` session (`proposal.md:429-432`; `code/lanes-edit.sh:648`). A rollback run there names the operator lane with `LANES_LANE=` (2.1; F12).

Every write and every other deciding read still refuses (`proposal.md:1870-1871`). The `SessionStart` block is unchanged.

**The budget** is SVC-8's one budget for the guard's client. Without a declaration, SVC-8 is unchanged apart from one `stat` at the prefetch. Where an unexpired declaration names this database, the client changes five things (lane 5, 6579523948):

- it reads the declaration once, at the prefetch, before the direct read;
- it moves the direct read's cap 0.1 s earlier, to `T0 + 3.65 s`. A database that accepts the connection and then goes silent would otherwise run the direct read to SVC-8's parse start line, `T0 + 3.75 s`, and the mirror path would start after it, where SVC-8 refuses 2;
- it starts the direct read only where at least 0.5 s remains before that cap, and otherwise exits as "too late to verify", so that a read which expires, and so opens the mirror path, has had at least 0.5 s (SVC-8's floor; V2's NIT 7);
- it starts the mirror path by `T0 + 3.75 s`, so the mirror's reads and parse use SVC-8's 0.5 s reserve;
- its `Register-Seq` lookup is bounded, and refuses 2 where it finds no trailer (condition 3).

The 0.1 s, from the cap at `T0 + 3.65 s` to the mirror path's start by `T0 + 3.75 s`, is the `Register-Seq` lookup's share. Lane 2 measured the bounded lookup at 13 ms median and 16 ms p95 (lane 3, 6708646285), so it fits in that share, and the cap and the start stay where they are.

**How it ends.**

- **On Brett's word:** `lanes-index outage clear` (D20) on each configured workstation removes the file.
- **At `until`:** every guard ignores the file, and (b) holds again.
- **At a rollback's end:** its step 6 ends it.
- **When the database answers again:** `status` and `ready` report a declaration still recorded, so it is ended at once.
- A guard whose database answers with the switch at `primary` ignores the file, so a stale declaration matters only where the database is still unreachable from that host.

**What it does not prove.**

- It is Brett's word in place of a check a guard cannot make (`proposal.md:1893-1896`). In a partition another workstation may still write, and a guard may let a session through for a lane that has moved elsewhere (`proposal.md:1886-1893`).
- From a rollback's start under Q4(b) (iii) that cannot happen. The switch at `files` refuses every SQL write, and the marker refuses every files-mode write until it is removed, after which the files are the truth. And the rollback's declaration records its head, 2.3's `k`, so a guard answers from the mirror only where its ref carries every write SQL committed; a guard behind it refuses 2 until a fetch brings the rendered seqs (R2's M1).
- Under a declaration by Brett's word, the mirror may also lag by the unrendered tail. The guard prints that lag, `<k>`. Its read is never older than the mirror was at the declaration (F11); under a rollback's, never older than the rollback's head.

**Q26's other options.**

- **(b): S3 as ratified** (`proposal.md:1879-1881`).
  - **What goes:** there is no declaration and there are no `outage` verbs. The guard refuses with 2 until the database returns or a rollback runs.
  - **Brett's way in:** a shell, or a `CLAUDE_NO_LANE=1` session (`code/lanes-edit.sh:648`).
  - **The disaster path:** its steps 2 and 3 change. Brett cannot prompt a pane, so a pause is moot: no lane can act until the database or its replacement answers.
- **(a): the mirror at any outage** (`proposal.md:1882`). It needs a check each guard can make, such as every writing environment's spool readable from each guard, and nothing provides one (`proposal.md:1896-1899`, `:1904-1912`). This part does not design it.
- **Q11:** (a′) and (a) pair with Q11 (a). Under Q11 (b), a service outage would refuse every prompt while a database outage might not (`proposal.md:1913-1917`).

### 2.6 P10's notice to the lanes

**What the lanes are told.** The manual's "What changes for a lane" (`proposal.md:932-939`), which the runbook posts before the window (`proposal.md:940-943`):

- exit 69, and what to do;
- exit 75, "register busy" (D7): nothing was written, and a retry in seconds is safe (`proposal.md:933-935`); 75 is Brett's answer to Q7, so the busy outcome never exits 69 (#165 6710653292);
- exit 3, a write in doubt (D8): the report names the write id, and a rerun reuses it;
- the guard during an outage, its two ways out, and the declared outage (2.5);
- `commit` and hand edits refused;
- the files as a mirror that lags, and the verbs that give the truth: `who`, `history`, `lanes` and `lanes-index status`;
- the claim comment's seq (Q19);
- the claim loser's 2 instead of 7 (Q12);
- the later `HOLD` or `LANDING` writer's code (S6);
- `LANDING EXPIRED` (F13), which Brett adopted on 2026-11-08 (#165 6721583757): after Rule 6's 30 minutes, a lane releases a stale `LANDING` only on Brett's word, with `append-line --landing-expired`, which copies the holder, the PR and the repository's spelling from that `LANDING` (lane 4's E3). The database finds that `LANDING` by `repo_key` alone (lane 3's ruling, #165 6710770304, lane 1's point 5; `design.md` S6), and refuses with LR005, exit 2, writing nothing, where it is not open, is not more than 30 minutes old by the database's clock, or is keyed another way; its message says which (U7, lane 3's ruling, #165 6722541787). After a rollback to files, `who --landing` lists a released `LANDING` again, marked STALE (lane 4's E1).
- on primary, a `HOLD` or `LANDING` line whose UTC cannot be read is refused with 2 and nothing is written (U3, lane 3's ruling, #165 6722541787): `append-line` refuses it before the lock (lane 4's E3) and the commit refuses it behind that, so a line composed by hand in `append-line` text carries its UTC; files mode writes such a line as today.

**The channels.**

- **Coordination issues:** each project's coordination issue, for every project with a row in the register, dormant ones included (`r3b/REPORT.md:173-191`, `:243`; `council-product/REPORT.md:398`). The coordinator lists those issues before it posts N1.
- **Brett's prompt to each pane,** under Q4(a) (i) (`proposal.md:1269-1271`).
- **The Codex lane,** through Brett (`proposal.md:984-986`; `clarifications.md:133`).

**The notices.**

| Notice | When | What it says |
|---|---|---|
| N1 | before act 3's rebuild | the rebuild ends every lane in its container; act 3 changes nothing about how lanes write (`mode=index`); `ready` will run in each environment |
| N2 | once Brett names the window, before it opens | "What changes for a lane" in full; the window; the freeze's form; the rollback's triggers |
| N3 | at the freeze word | under (i): stop at a stopping point, write `PAUSED`, and write nothing more until N4, since the ruleset goes on after the drain (F4). Under (ii′): writes refuse with 69 for a minute or two |
| N4 | after the smoke test | the marker's commit and seq 1; the smoke test's result; resume; the files now lag |
| N5 | at a rollback's start, or a declared outage | what refuses; what the guard answers; until when |
| N6 | after the rollback | the register is files again, and what returns (2.3) |
| N7 | at the disaster's declaration | the outage; the pause; that the unrendered tail is at risk |
| N8 | at the disaster's resumption | `m`, `h` and the loss; what each lane checks |

**Dormant projects.** A dormant project meets N2 on its issue when it resumes. Its tools must be upgraded before it writes, or the renderer stops at its first files write (`r3b/REPORT.md:323`). Act 4's second `ready` cannot see an environment that is not running.

### 2.7 R3c's landing path, under 1A's split by seam

Every PR lands on Brett's typed word, by merge commit (Q25(a) (i), `proposal.md:1855-1859`; `r3c/REPLY.md:108`). R3c's sequence (`r3c/REPLY.md:110-154`) follows, with its code PRs in the split's three groups (`proposal.md:1368-1394`; `reviews/lane3-165-round1-answered-6576706132.md:4-8`).

1. **The OpenSpec change:** a spec-leg PR by lane 3, after `openspec validate --strict` and `design.md` (`r3c/REPLY.md:110-114`). It needs no root bump.
2. **Speckit 007:** created from an assembly-root clone, with worktrees at `worktrees/007-sql-register/{spec,code}/` (`proposal.md:1044-1048`).
3. **The root `composed.yml` PR:** needed only if CI's refusal switch for the real-engine tests is a workflow variable. It lands before group A's first PR that needs a database (`proposal.md:1150-1152`; `r3c/REPLY.md:119-122`).
4. **workBenches' re-vendor:** drafted now. Its `apply` waits until the code is on the code leg's `main` (`r3c/REPLY.md:63`, `:123`).
5. **Group A, now, on `2a057a6d`:** everything that runs through no write seam (`proposal.md:1368-1373`):
   - `lanes-index`'s schema, journal, switch and roles, and its `register snapshot`, `register commit`, `register read` and `journal` subcommands against the fixture (D2);
   - `import --at`, with its scratch render and proof;
   - `status` and `reconcile`;
   - the renderer as a pure function from the journal to file images, with no git;
   - the service and its read API;
   - the readers' SQL path, at `register_text_checked` (`code/lanes-edit.sh:3597`).

   This part places here too P8's three tools and the `outage` verbs, which write only DDL and the configuration directory, and the guard's read of the declaration, which is a reader.

   Under Q29 (a) the service would be 007's last slice and could trail (`proposal.md:1973-1976`), but its condition is met: from Eagle the guard without the service misses Brett's 1 s (lane 5, #165 6725361742), so the cutover and the v1.2.0 tag wait for the service, which is built beside groups A and B, not last (lane 3's ruling, #165 6724332510 and 6726078173).

   **The refusal of `mode=primary`:** from group A's first PR, `lanes-index mode` and `register read`, in both its forms, refuse `mode=primary` with 78 (`EX_CONFIG`), Q6(c)'s code, never 2 (F8), and test 14's text; the other `lanes-index` commands refuse it with 2, as the configuration parser does; and `lanes-edit.sh` fails closed on it with 69 (`proposal.md:1380-1388`). That is agreement test 14's first state, and `ready` fails any environment in it (`proposal.md:913-921`).
6. **Group B, the write path,** after Amendment 23's #282 (1a) and #307 (1b) are re-homed and land in the code leg: S1's transaction at the commit seam, M4's refusals, S6's pre-check and hold order, and the marker check after S3's fetch (`proposal.md:1374-1376`).

   Who re-homes #282 and #307, and by when, is Q28's second part (`proposal.md:1955-1967`). Its owner is answered: lane openRepoShape-2, Q28's (a) (#165 6710653292). Its two dates are named too, "Confirm both (Recommended)" (Brett, #198 6722244541): slices 1a and 1b as draft PRs by 2026-11-10 and ready by 2026-11-12, and the other four open by 2026-11-12 and ready by 2026-11-17 (`clarifications.md`), so of Q28 group B waits only on those dates.
7. **Group C, then:** the renderer's commit and push, rollback's push, and agreement tests 3, 5 and 6 (`proposal.md:1377-1379`).
   - **Also here:** this part places `cutover`'s push of the marker in group C, for the same reason as rollback's push.
   - **Its wait:** under the private-index form, it need not wait for 2a and 2b (`proposal.md:1377-1379`, `:1934-1938`).
   - **The flip:** its last PR flips test 14's single constant (`proposal.md:914-915`), so the build accepts `mode=primary`.
8. **For each group:**
   - its code PRs are serialized on `lanes-edit.sh` (`r3c/REPLY.md:130`);
   - the spec docs PR goes beside them (`r3c/REPLY.md:131-133`);
   - a draft root PR bumps both legs to the PR heads, so `exact-pins` proves code, spec and root docs together before any leg lands (`r3c/REPLY.md:134-137`). A code PR can pass its own four jobs and still turn `exact-pins` red after it lands (`r3c/REPLY.md:27-31`);
   - the legs land on the proven heads;
   - lane 1 opens the root bump (Q25(b), `proposal.md:1860-1862`; `r3c/REPLY.md:139-141`).

   R3c has one rehearsal. This part proposes one per group, each costing 64 to 87 minutes of `exact-pins` (`proposal.md:1067-1068`).
9. **After group C:**
   1. the release commit proven, the v1.2.0 notes, a TestPyPI rc1, the tag and `release/v1.2` (`r3c/REPLY.md:142-147`);
   2. the hosts reached (Q24, `r3c/REPLY.md:148-151`);
   3. act 3;
   4. act 4, which under Q28 (i) also waits for Amendment 23's 2a, 2b and 3 (`proposal.md:1946-1954`);
   5. the archive PR, and one more bump (`r3c/REPLY.md:154`).

**A correction to R3c.** R3c's line 47 says `commit_publish` appears 46 times in `lanes-edit.sh` at `2a057a6d`, and `commit_push` nowhere. It has the names swapped. A GET of `lanes-edit.sh` at `2a057a6d84e5…` finds `commit_push` 46 times, defined at `code/lanes-edit.sh:2292`, and `commit_publish` 0 times, as `proposal.md:1032-1033` and `r3a/REPORT.md:125-127` say. Amendment 23's act 2 is not in the code leg, and group B waits for it.

**Q28's other options.**

- **(ii): act 4 waits for 1a and 1b only,** with #311 and #308 trailing (`proposal.md:1953-1954`). Act 4 then follows group C directly, and handoff commits may still strand in `W` at the cutover (`proposal.md:1947-1950`).
- **The second part's options, (a) to (c),** change only who re-homes #282 and #307 before group B (`proposal.md:1959-1967`).

### 2.8 The round-2 questions this part turns on

| Question | Assumed here | Where the other options are |
|---|---|---|
| Q4(a) | (i) | 2.2, "How (ii′) differs" |
| Q4(b) | (iii) | 2.3, the rollback |
| Q22 | (a) | 2.3, the disaster path |
| Q26 | (a′) | 2.5 |
| Q28 | (i) | 2.7 |

Four more bear on this part:

- **Q21(b) (B)** (`proposal.md:1787-1794`): act 3's step 9, act 4's step 5, and the rollback's step 3 (F4).
  - **Under (A), deferral:** no ruleset. The renderer's stop and S2's two ways out are the whole answer to a stale writer, found by `status`'s `diverged at <sha>` and by `ready` run again (`clarifications.md:113-118`).
  - **Under (B′):** act 4's gate also waits for the ruleset, or an equal server-side gate (`proposal.md:1791-1792`).
- **Q29 (a)** (`proposal.md:1973-1976`): the service is not on act 4's gate unless the guard, measured without it, misses Brett's budget, 1 s; it does, from Eagle (lane 5, #165 6725361742), so the service is on the gate.
  - **Under (b):** the service is on the gate, and act 3's step 6 adds its start in each environment to `ready`.
- **Q23 (a)** (`proposal.md:1836-1839`): one configuration per host, so one flip and one declaration per host, on each configured workstation under Q22 (a).
  - **Under (b),** `LANES_INDEX_CONFIG` under an existing mount: the flip and the declaration go to that file's directory, still from the host.
  - **Under (c),** provisioning at launch: a container keeps the copy it launched with, so a later flip or declaration does not reach it until it restarts. (c) needs the declaration on a shared mount of its own.
- **Round 1, held as provisional** (`reviews/lane3-165-design-starts-6576888068.md:3-7`), **and RULED since** (#165 6710653292), so the option below was not taken:
  - **Under Q20 (A):** act 3 writes `mode=primary`, and every write refuses with 69 from act 3 to act 4 (`proposal.md:1772-1773`).
  - **What that changes here:** act 4's step 8 disappears, and `set-mode` must drop its refusal to set `primary` before the cutover.

### 2.9 Names this part defines or relies on

| Name | Kind | Defined |
|---|---|---|
| `mode=`, with `index` and `primary` | configuration key | `proposal.md:149-153` |
| `register_url=`, `register_passfile=` | configuration key | `proposal.md:152-153`, which says "for example"; R3a's spelling at `r3a/REPORT.md:323` |
| `register_label=` | configuration key | **(new)**; the label of the database `register_url=` reaches, `cutover`'s one source for its label (S-2) |
| `render_key=` | configuration key | the frame's D23; it names a key file and holds no secret |
| `reader_url=` | configuration key | `proposal.md:149-151`; `code/lanes-index:2609` |
| `lanes-index.conf` | file | `proposal.md:149`; `code/lanes-index:62-64` |
| `lanes/STORE` | file | `proposal.md:166-168`; an operator entry's one path, never rendered (F2) |
| `Register-Seq: <n>` | commit trailer | `proposal.md:304-307` |
| `lanes_register` | schema | `proposal.md:1222` (R3's); `r3a/REPORT.md:172` |
| `register_switch`, with `mode` and `database_label` | table, columns | `r3a/REPORT.md:179-185` (R3a's; the proposal says "the estate switch", `proposal.md:166`) |
| `register_head`, with `seq` | table, column | `r3a/REPORT.md:187` (R3a's; the proposal says "a one-row head", `proposal.md:1226`) |
| `journal`, with `seq` and `kind` (`'import'`, `'write'`, `'rollback'`) | table, columns | `proposal.md:299`; columns `r3a/REPORT.md:189-195` |
| operator entry: the cutover's (`'cutover'`, seq 1, `m+1` after a re-import, or `b+1` after a restore) and the rollback's last seq (`'rollback'`), each touching only `lanes/STORE` | journal entry | **(new)**; F2, D24, with `design.md` §1.2's flag |
| generation id, `register_switch.generation` | switch field | **(new)**; F10, F18 (`design.md` §1.2) |
| `record_marker`; `marker_state` (`absent`, `adding`, `present`, `removing`) | function; switch field | **(new)**; S4's read-back recorded (F12, `design.md` §1.3) |
| `write_id` | column | `proposal.md:253-258` |
| `REGISTER_SCHEMA_VERSION` | constant | `r3a/REPORT.md:326` |
| REGISTER-WRITER, READER, the indexer | role | `proposal.md:683-691` |
| `lanes_register_writer`, `lanes_register_reader` | role | `r3a/REPORT.md:172`, `:281` |
| `lanes_register_owner`, `lanes_indexer` | role | `design.md` §1.4 **(new)** |
| `lanes-index import --at <C>` | verb | `proposal.md:518-521` |
| `--repository`, `--lane` on `bootstrap` | flag | **(new)** |
| `--dry-run` on `import`, into a scratch schema | flag | `r3b/REPORT.md:235`; `proposal.md:1254` |
| `lanes-index cutover --import <C>` | verb | `proposal.md:1254-1257` (R3's) |
| `lanes-index rollback` | verb | `proposal.md:527-530` |
| `lanes-index status`, `lanes-index reconcile` | verb | `proposal.md:395-398` |
| `lanes-index import --single <sha>`, `lanes-index render --revert <sha>` | verb | `proposal.md:385-389`; the frame's S2 (D2, D16) |
| `lanes-index bootstrap` | verb | **(new)** |
| `--admin-service '<service>'`, `--roles-only` on `bootstrap` | flag | **(new)**; lane 5's choice 11 (#165 6578521506), and F18 |
| `~/.pg_service.conf`, `~/.pgpass`, the admin's own | file | libpq's; the bootstrap's admin connection |
| `PGSERVICE=<service>` | environment value | libpq's; in `psql`'s own environment, the only way the admin connection's service reaches `psql`, never as `service=` or `-d` (SVC-2; lane 5, 6579523948) |
| `lanes-index ready` | verb | **(new)** |
| `lanes-index set-mode` | verb | **(new)** |
| `lanes-index login add`, `add --dump-job`, `rotate` and `revoke` | verb | **(new)**; Brett's "Two logins each" (#165 6723413756) |
| CNPG `Cluster`, `ObjectStore`, `ScheduledBackup`; the dump `CronJob`; the endpoint's `LoadBalancer` service; the cert-manager `Certificate`, whose Secret is CNPG's `serverTLSSecret` (item 4) | Kubernetes objects | the codeXfactory prod install's (2.2, "What the codeXfactory prod install provides") |
| `lanes-index outage declare`, `outage clear` | verb | the frame's D20 |
| `--mirror-seq <m>`, `--rollback` on `outage declare` | flag | **(new)**; `--mirror-seq` is F11's; with `--rollback` it is required, as `--mirror-seq <k>`, the rollback's head, and its absence refuses 2; where the database probe reads the switch at `files`, 2 unless the journal entry at the switch's `set_seq` is a `rollback` and `<k>` is `set_seq - 1`; where the probe cannot read the switch, 2; and, in either form, 2 where it reads the switch at `primary` (2.5; lane 5, #165 6714540821, 6714928254, 6715361644 and 6715901155) |
| `mirror-seq=<m>` | `register-outage` field | **(new)**; F11; SVC-12's name |
| "the mirror here is at seq `<s>`, behind the declared outage's seq `<d>`; …" | text | SVC-12, lane 5's section as folded at `1c45123a`, with lane 5's text of #165 6714540821 |
| `rollback --hold-marker` | flag | **(new)**; F4 |
| `cutover --marker-first` | flag | **(new)** |
| `import --after-loss`, `cutover --after-loss` | flag | **(new)** |
| `register-outage` | file | the frame's D20 |
| `read: mirror at <sha12>, …` | text | `proposal.md:444` |
| "this estate's register is SQL; configure `mode=primary`" | text | `proposal.md:169-170` |
| `mode=primary is not available in this build: …` | text | `proposal.md:916-918` |
| `diverged at <sha>` | text | `proposal.md:378` |
| 69 | exit code | `proposal.md:213-217` |
| 2, from the guard | exit code | `proposal.md:216-217` |
| "register busy", exit 75 | exit code | `proposal.md:224-234`; 75 is the frame's D7, suggested at `clarifications.md:360-373`, and `design.md` §1.3 adopts it; Brett's answer to Q7 keeps it (#165 6710653292) |
| 3, a write in doubt | exit code | the frame's D8; from `cutover` and `rollback`, an operator entry's push not confirmed (F12) |
| `store=`, `database=`, `import=`, `lanes-tree=`, `utc=` | `lanes/STORE` fields | the frame's D21 |
| `lanes-index`'s 0, 2 and 64 | exit code | `code/lanes-index:72-76` |
| 78 (`EX_CONFIG`), Q6(c)'s refusal, from `lanes-index mode` and `register read` | exit code | F8: never 2; sysexits' `EX_CONFIG`, as D3 and test 14 have it (lane 3, 6708646285) |
| `CLAUDE_NO_LANE=1` | environment value | `proposal.md:431-432`; `code/lanes-edit.sh:648` |
| `LANES_LANE=` the operator lane | environment value | Amendment 23's S2 ("no lane, no write"); F12 |
| `GUARD_TIMEOUT=5` | constant | `proposal.md:772-773`; `code/openRepoTools:2344` |
| `connect_timeout=2`, `lock_timeout=1000`, `statement_timeout=1500` | connection setting | `proposal.md:799-800`; `connect_timeout` is now each workstation's `c` (D9) |
| `<W>-lanes/<lane>/` | directory | `proposal.md:97`, `:310-311` |
