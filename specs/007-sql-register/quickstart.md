# Quickstart: the SQL register's first steps

Phase 1 of `/speckit.plan`. The operator's and an engineer's first steps, as
`operations.md` §2.2's act 3 and §2.4 give them at Y. This file orders them
and points at their text; it restates no requirement, and the runbook in
`docs/README-lanes.md` (FR-062) is the one that people follow.

**Citations** are spec.md's: `spec:<n>` and `svc:<n>` are lines of the
change's two spec files on this branch, at `ad82c2f4`; `Y:<n>` is a line of
`design.md` and `ops:<n>` of `operations.md`, which are unchanged from Y.

Every step here is an act, on a person's word, never a build task. Nothing
here runs before the codeXfactory prod install exists ("Wait for the
install", #165 6723925474).

## The operator: Brett

1. **The database.** The codeXfactory prod install creates the register's
   CNPG `Cluster`, and `initdb` makes the database, owned by Brett's admin
   (ops:189–190). He records on #165 what the install hands back (ops:179)
   and runs the checks on the cluster (ops:199).
2. **The schema and the roles,** from a host shell, not from a lane
   session (ops:201–202):

   ```sh
   lanes-index bootstrap --admin-service '<service>' --label '<name>'
   ```

   - `<service>` names a libpq service in his own `~/.pg_service.conf`,
     with its password in his own 0600 `~/.pgpass`; never a connection
     string (ops:410).
   - A second run prints `unchanged` for every object (ops:203).
3. **The logins,** for each engineer, human or AI host, and for the dump
   job (ops:206), each through `--admin-service`, as the bootstrap connects (ops:481); the flag's position is left to T110:

   ```sh
   lanes-index login add <engineer>
   lanes-index login add --dump-job
   ```

   Each prints its password-file lines once, to a terminal. The dump job's
   goes into the Key Vault secret its CronJob reads (ops:482).
4. **Each engineer's file,** on that engineer's workstation, in the host's
   read-only mount: `lanes-index.conf` with `mode=index`, `reader_url=`,
   `register_url=`, `register_passfile=` and `register_label=`, and the two
   0600 password files. Each file names only its own engineer's logins
   (ops:207–209); `register_passfile=` and `reader_passfile=` name the two
   files (Y:2326).
5. **The bench images, once:** one rebuild, then a recreate. The rebuild
   carries the re-vendor, the configuration mount and `postgresql-16`, and
   ends every lane in its container, so notice N1 goes out first. Its check
   is `ready`, below (ops:213–216).
6. **The install,** in each host shell: pipx v1.3.0 (ops:218–219).

## An engineer's workstation

1. **Its file reads:** Brett checks it with `lanes-index status` (ops:210).
   `lanes-index` refuses a file that is group- or world-readable, inside a
   git work tree or under `~/.agents/`, or a URL that carries a password
   (ops:211).
2. **Readiness.** The operator lane runs it in every writing environment
   the workstation has, each bench container and each host shell
   (ops:221–222):

   ```sh
   lanes-index ready --expect index
   ```

   Exit 0 everywhere. Any failing item holds act 4 until it passes
   (ops:224–225).
3. **Nothing else changes yet.** With `mode=index` every tool reads and
   writes as before (ops:212). The flip to `mode=primary` is act 4's, on
   Brett's word (ops:278–279).

## If something goes wrong

- **The schema, before the import:** it holds no register data. Brett
  drops `lanes_register` and the roles the bootstrap made, through the same
  admin service (ops:205).
- **A file:** remove the new keys; `mode=index` behaves as today (ops:212).
- **A restore from a dump,** after a loss: the order is research.md's
  ERR-03, lane 3's ruling on test-plan open point 41.
