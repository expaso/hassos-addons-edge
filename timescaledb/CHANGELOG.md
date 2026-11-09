## Changes

- Release Drafter: breaking-change makes a major version @expaso (#89)
- Fix pg\_hba\_config writing literal "null" and orphaned rules on clear @danieldotnl (#82)

## 💥 Breaking Changes

- Replace postgresql\_config by postgresql\_settings, which works @expaso (#90)
- Version 6.0.0: PostgreSQL 18, TimescaleDB 2.31, stability fixes @expaso (#88)

## 🚀 Features

- scram-sha-256 for new installs, postgres\_password option @expaso (#92)
- Bump versions: timescaledb 2.26.0, postgis 3.6.2, timescaledb-toolkit 1.22 @nsteinmetz (#72)

## 🐛 Bug Fixes

- Rebuild backup and restore: compressed, disk-safe, TimescaleDB-correct @expaso (#91)
- Replace postgresql\_config by postgresql\_settings, which works @expaso (#90)
- Version 6.0.0: PostgreSQL 18, TimescaleDB 2.31, stability fixes @expaso (#88)
- Fix "No such file or directory" startup failure after HAOS backup restore @[copilot-swe-agent[bot]](https://github.com/apps/copilot-swe-agent) (#74)
- Fix pg\_hba\_config crash and duplicate entries on restart @[copilot-swe-agent[bot]](https://github.com/apps/copilot-swe-agent) (#77)
- Fix: chown/chmod backup file before restore to prevent postgres permission denied @[copilot-swe-agent[bot]](https://github.com/apps/copilot-swe-agent) (#75)
