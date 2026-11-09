# Changelog since v5.4.2
- Merge pull request #89 from expaso/ci/release-drafter-labels

Release Drafter: breaking-change makes a major version 
- Release Drafter: breaking-change makes a major, new-feature a minor

The version resolver only knew the labels major/minor/patch, while PRs
carry the labels the PR Labels check requires (breaking-change,
new-feature, bugfix, ...). The draft for the 6.0.0 changes therefore came
out as v5.5.0. Breaking changes also get their own section now.

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Merge pull request #82 from danieldotnl/fix/pg-hba-config-null-options

Fix pg_hba_config writing literal "null" and orphaned rules on clear 
- Merge pull request #88 from expaso/release/6.0.0

Version 6.0.0: PostgreSQL 18, TimescaleDB 2.31, stability fixes 
- yamllint: one space before comments, as Prettier formats them

Same setting as the hassio-addons app repositories.

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Two spaces before the version comment (yamllint)

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Pin the reusable workflows to hassio-addons/workflows v5.0.0

Same setup as the hassio-addons app repositories:
- All reusable workflows pinned to the v5.0.0 commit SHA instead of @main.
- Deploy runs CI itself on a push to main or a release, instead of the
  workflow_run trigger; CI itself runs on pull requests.
- PR Labels uses pull_request instead of pull_request_target.
- Explicit permissions for labels, lock and stale.

Clears the zizmor code scanning findings (unpinned uses, dangerous
triggers, excessive permissions).

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Build the Toolkit with Alpine's Rust instead of rustup

On aarch64, rustup's musl toolchain links binaries with the glibc loader
(/lib/ld-linux-aarch64.so.1), so cargo-pgrx could not run on Alpine
without gcompat. Alpine's own Rust (1.96) links against musl correctly.
cargo-pgrx is installed into /usr/local instead of the cargo cache mount,
and only the crate registry is cached, so no binary from an earlier build
environment ends up in the image.

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Update TimescaleDB documentation links (docs.timescale.com moved to tigerdata.com)

The old links give a 404. Fixed in .README.j2, from which the store README
is generated, so this replaces expaso/hassos-addons#54.

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Base image 21.0.10 (Alpine 3.24), build extensions on Alpine itself

Move to the latest hassio-addons base image. Its Alpine 3.24 ships other
library versions than the Alpine 3.23 TimescaleDB images the extensions
were compiled on: postgis_raster needed libgdal.so.37, Alpine 3.24 has
libgdal.so.39, so it would not load.

The dependency images are now built on alpine:<ALPINE_VERSION> against
Alpine's own postgresql17/18 packages, the same PostgreSQL the add-on runs,
and tagged <version>-alpine<ALPINE_VERSION>. PGXS compiles the LLVM bitcode
with the clang that PostgreSQL itself was built with, which differs per
version on Alpine 3.24 (clang-20 for 17, clang-22 for 18), so the builds
read it from PostgreSQL's Makefile.global. TimescaleDB itself still comes
from the official TimescaleDB images (only libc dependencies).

Also:
- pgvector is built with OPTFLAGS="" instead of its default -march=native,
  so it runs on every CPU of the architecture.
- make -j for the C extensions (PostGIS builds about 2x faster).
- Unused build dependencies removed (boost/openldap for system_stats and
  pgvector, the 32-bit guards in the toolkit build).

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Make CI lint pass: shellcheck findings and deprecated codenotary

CI runs again since the workflow fix, which surfaced existing findings:
- Quote variables, read -r, cd || return, grouped pg_hba.conf writes and
  a split readonly assignment (shellcheck).
- "if $(bashio::config.true 'retry_upgrade')" executed the output of the
  check and only worked by accident; call the check directly.
- Remove the deprecated codenotary fields (app linter).

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Drop timescaledb-extras backfill helpers that block TimescaleDB updates

compress_dimension_slice and decompress_dimension_slice from the (since
removed) timescaledb-extras backfill.sql take a _timescaledb_catalog row
type as argument. ALTER EXTENSION timescaledb UPDATE then fails as soon as
an update rebuilds that catalog table ("cannot drop table
_timescaledb_catalog.dimension_slice because other objects depend on it"),
the database stays on the old version and pg_upgrade fails on the missing
old TimescaleDB library. Found while upgrading a copy of a production
database from 2.22.1 to 2.31.0.

The existing drops of the other timescaledb-extras helpers now live in one
script with these routines, used before pg_upgrade and before the regular
extension update after an add-on update (where the failure was silently
ignored, leaving the extension outdated).

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Version 6.0.0: PostgreSQL 18, TimescaleDB 2.31.0, base image 20.2.0

Builds on the PostgreSQL 18 work of #73.

- PostgreSQL 18 is the default; existing PostgreSQL 17 databases are
  upgraded with pg_upgrade on first start. PostgreSQL 18 enables data
  checksums by default, so the new cluster takes over the checksum setting
  of the old one (pg_upgrade refuses a mismatch).
- PostgreSQL 15 and 16 databases must first be upgraded with add-on 5.x.
- Base image 20.2.0 (Alpine 3.23): the same Alpine release as the
  TimescaleDB images the extensions are compiled on.
- Only aarch64 and amd64: the base image no longer exists for armhf, armv7
  and i386, in line with Home Assistant itself.
- TimescaleDB 2.31.0, Toolkit 1.27.0+p2 (cargo-pgrx 0.18.1), PostGIS 3.6.4,
  pgvector 0.8.7 (#87).
- Dependency images are built on a pinned TimescaleDB image instead of
  latest, tagged with a -ts<version> suffix, for linux/amd64 and linux/arm64.
- Build fixes for Alpine 3.23: clang-21/llvm-21, CMake 4 policy for
  pgAgent, musl upgrade for the dev packages, and copying the banner into
  the resolved s6-overlay directory (rsync no longer follows that symlink).
- The extension upgrade before pg_upgrade dropped the timescaledb-extras
  helpers in the homeassistant database instead of the database it
  upgraded, failing for installs without that database.
- retry_upgrade now restores an aborted PostgreSQL 17 upgrade.
- Banner uses bashio::app.* (bashio::addon.* is deprecated).
- Port description explains to enter only the number (#86).

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Fix CI: use the app workflows with explicit permissions

CI failed to start since March: the reusable workflow requested more
permissions than the wrapper allowed. Use the current app-ci/app-deploy
workflows (addon-* are deprecated wrappers) and declare the permissions
they need, also for release-drafter (contents: write).

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Keep PostgreSQL running when pgAgent or a startup check fails

- pgagent/finish no longer halts the container: S6 restarts only pgAgent
  and PostgreSQL keeps serving (#84). postgres/finish still stops the
  container, as the Supervisor watchdog restarts the add-on.
- Both finish scripts logged every exit as "code-server" (copied from the
  code-server add-on); they now log the real service name.
- pgAgent removes registrations of agents whose connection is gone before
  starting, so a restart after an unclean exit no longer fails with a
  duplicate key on pga_jobagent_pkey.
- "psql ... | grep -q 1" under bashio's errexit+pipefail could report an
  existing database or extension as missing. For CREATE DATABASE this made
  the add-on exit with "database already exists" and take PostgreSQL down.
  The checks now compare the psql -tA result, and a failing CREATE DATABASE
  only logs a warning.

Co-authored-by: Copilot <223556219+Copilot@users.noreply.github.com> 
- Add support for PostgreSQL 18

Agent-Logs-Url: https://github.com/expaso/hassos-addon-timescaledb/sessions/f5938a08-beeb-4286-91bc-c2a1301f2aea

Co-authored-by: expaso <26247804+expaso@users.noreply.github.com> 
- Fix pg_hba_config writing literal "null" and orphaned rules on clear

Two related bugs were preventing pg_hba_config from being usable for any rule
that didn't set every optional field, and also kept malformed rules around
after the user tried to recover by clearing the config.

1. `bashio::config "...options" ""` collapses the empty-string default to the
   literal "null" string (because bashio uses `${2:-null}` internally). The
   word "null" then gets appended as the pg_hba.conf options column, and
   PostgreSQL refuses to load the file with:

     authentication option not in name=value format: null

   Fixed by adding an `optional_config` helper that uses `has_value` and
   returns a true empty string when the field is null/missing. There is no
   PostgreSQL-accepted no-op value for the options column of md5/scram-sha-256
   rules, so users cannot work around this from configuration.

2. Cleanup of previously-written user rules was inside the
   `bashio::config.has_value 'pg_hba_config'` guard. Clearing the config in
   the UI therefore left any prior `null`-poisoned rules in place, and the
   add-on would refuse to start because PostgreSQL could not parse the file.
   Recovery required hand-editing pg_hba.conf.

   Moved the cleanup above the guard so a cleared config is honored, and
   updated init-addon/run to always invoke the script so cleanup runs even
   when both override blocks are empty.

Fixes #81

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com> 
- Fix pg_hba_config writing literal "null" and orphaned rules on clear

Two related bugs were preventing pg_hba_config from being usable for any rule
that didn't set every optional field, and also kept malformed rules around
after the user tried to recover by clearing the config.

1. `bashio::config "...options" ""` collapses the empty-string default to the
   literal "null" string (because bashio uses `${2:-null}` internally). The
   word "null" then gets appended as the pg_hba.conf options column, and
   PostgreSQL refuses to load the file with:

     authentication option not in name=value format: null

   Fixed by adding an `optional_config` helper that uses `has_value` and
   returns a true empty string when the field is null/missing. There is no
   PostgreSQL-accepted no-op value for the options column of md5/scram-sha-256
   rules, so users cannot work around this from configuration.

2. Cleanup of previously-written user rules was inside the
   `bashio::config.has_value 'pg_hba_config'` guard. Clearing the config in
   the UI therefore left any prior `null`-poisoned rules in place, and the
   add-on would refuse to start because PostgreSQL could not parse the file.
   Recovery required hand-editing pg_hba.conf.

   Moved the cleanup above the guard so a cleared config is honored, and
   updated init-addon/run to always invoke the script so cleanup runs even
   when both override blocks are empty.

Fixes #81

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com> 
- Merge pull request #74 from expaso/copilot/fix-timescaledb-startup-error

Fix "No such file or directory" startup failure after HAOS backup restore 
- Fix No such file or directory startup error after HAOS backup restore

Agent-Logs-Url: https://github.com/expaso/hassos-addon-timescaledb/sessions/a0736aa4-a13d-4880-ab6c-ce1087d8216c

Co-authored-by: expaso <26247804+expaso@users.noreply.github.com> 
- Merge pull request #77 from expaso/copilot/fix-pg-hba-config-crash

Fix pg_hba_config crash and duplicate entries on restart 
- Fix pg_hba_config crash and duplicate entries on restart

- Fix arithmetic crash: ((RULE_COUNT++)) and ((PARAM_COUNT++)) return exit
  code 1 when count is 0 under set -e; use $(( count + 1 )) instead
- Fix duplicate pg_hba entries: remove existing "User-defined authentication
  rules" section before re-adding on each restart

Agent-Logs-Url: https://github.com/expaso/hassos-addon-timescaledb/sessions/1e372b8b-c850-470b-9337-862dea73c3b9

Co-authored-by: expaso <26247804+expaso@users.noreply.github.com> 
- Merge pull request #75 from expaso/copilot/add-permissions-fix-to-restore-scripts

Fix: chown/chmod backup file before restore to prevent postgres permission denied 
- Fix: ensure postgres user can read backup file before restore

Agent-Logs-Url: https://github.com/expaso/hassos-addon-timescaledb/sessions/f6e8c36b-e9b7-4fc7-bdfa-fc15ec9ab775

Co-authored-by: expaso <26247804+expaso@users.noreply.github.com> 
- Initial plan 
- Initial plan 
- Initial plan 
- Merge pull request #72 from nsteinmetz/main

Bump versions: timescaledb 2.26.0, postgis 3.6.2, timescaledb-toolkit 1.22 
- chore: bump pgvector-pg17 to 0.8.2 
- chore: bump timescaledb to 2.26.0 
- chore: bump timescaledb 2.25.2, postgis 3.6.2, timescaledb-toolkit 1.22

To summarize everything that was done:

Version upgrades in timescaledb/Dockerfile and project.sh:
•  timescaledb: 2.22.1 → 2.25.2 (pg16 & pg17)
•  postgis: 3.6.0 → 3.6.2 (pg16 & pg17)
•  timescaledb-toolkit: 1.21.0 → 1.22.0 (pg16 & pg17)
•  pgAgent: already at 4.2.3 — no change

Fixes to the toolkit dependency Dockerfiles
(timescaledb-toolkit-pg16/17) to support building the newer toolkit:
•  Replaced Alpine's Rust 1.83 with rustup stable (1.85+) to support
edition2024
•  Updated cargo-pgrx 0.12.8 → 0.16.1 to match pgrx used by toolkit
1.22.0
•  Added pkgconf + OPENSSL_DIR=/usr to fix the OpenSSL linker issue
•  Added RUSTFLAGS="-C target-feature=-crt-static" to enable dlopen for
bindgen/libclang
•  Added --force to cargo install rustfmt since rustup already ships it 
