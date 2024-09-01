![Project Stage][project-stage-shield]
![Maintenance][maintenance-shield]
[![License][license-shield]](https://github.com/expaso/hassos-addon-timescaledb/blob/main/LICENSE)

<a href="https://www.buymeacoffee.com/expaso" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

# Home Assistant Add-on: [PostgreSQL](https://www.postgresql.org/) [TimescaleDB](https://www.timescale.com/)

## [PostgreSql 16.3](https://www.postgresql.org/) & [Postgis 3.4.2](https://postgis.net/) & [TimescaleDB 2.16.1](https://www.timescale.com/) & [TimescaleDB Toolkit 1.18.0](https://github.com/timescale/timescaledb-toolkit) & [pgAgent 4.2.2](https://www.pgadmin.org/docs/pgadmin4/development/pgagent.html)

#### Configuation

Example add-on configuration:

```
 {
    "databases": ["homeassistant"],
    "timescale_enabled": ["homeassistant"],
    "timescaledb": {
      "telemetry": "basic",
      "maxmemory": "512MB",
      "maxcpus": "4"
      },
      "max_connections": 20,
      "system_packages": [],
      "init_commands": []
 }
```

#### Option: `databases`

Sets a list of database-names that will be created for you, once you start the add-on.
You can also create databases on your own ofcourse, using a psql client of your choice.

#### Option: `timescale_enabled`

Sets a list of database-names where the timescale-extentions will be enabled for.
Databases not in this list will act like normal Postgre databases.

#### Option: `timescaledb.telemetry`

Switches the telemetry of TimescaleDb on or off.
Valid options are: 'basic' or 'off'.
See: https://docs.timescale.com/latest/using-timescaledb/telemetry

#### Option: `timescaledb.maxmemory`

Sets the maximum amount of memory that PostgreSQL will claim.
It's important to leave breathing room for other processes on your machine (or raspberry pi), so set these level not too high (say max 50% of your total ram).

Example: `maxmemory="1024MB"`
Or leave empty for accepting auto-tune.

#### Option: `timescaledb.maxcpu`

Sets the maximum number of cores that PostgreSQL will use.
It's important to leave breathing room for other processes on your machine (or raspberry pi), so set these level not too high (say max 75% of your total number of cores).

Example: `maxcpu="2"`
Or leave empty for accepting auto-tune.

See also:
https://docs.timescale.com/latest/getting-started/configuring
for further tuning. Your Postgres.config file is located in the addon's data directory.

#### Option: `max_connections`

Sets the maximum number of connections that PostgreSql will accept.
Setting this higher could lead to more memory usage.

Example: `max_connections=30`

#### Option: `system_packages`

Advanced users only!
A list of extra alpine packages to iunstall during addon-startup.

Example: ['nano']

#### Option: `init_commands`

Advanced users only!
A list of extra commands to run during startup.

To alter something in the postgresql.conf file for example:

Example: ['sed -i -e "/max_connections =/ s/= .*/= 50/" /data/postgres/postgresql.conf']

#### Option: `retry_upgrade`

Advanced users only!
When set, the upgrade from Postgres 14 to 15 could be retryed if it failed mid-flight.
Basically this will try to find the old database-files from Postgres 12, and restore them before trying to upgrade to Postgres 14 again.

!! Please don't set this if you don't know what you are doing or before taking a backup. !!

### Running the container standalone.

In this case, you need to have a working Docker installation on your machine.
pull one of the images for the desired architecture from docker hub:

```
docker pull ghcr.io/expaso/timescaledb/amd64:latest
docker pull ghcr.io/expaso/timescaledb/aarch64:latest
docker pull ghcr.io/expaso/timescaledb/armv7:latest
docker pull ghcr.io/expaso/timescaledb/armhf:latest
docker pull ghcr.io/expaso/timescaledb/i386:latest
```

You can replace latest with the version number you want to use.

Simply start it like this:

```
docker run \
  --rm \
  --name timescaledb \
  --v ${PWD}/timescaledb_addon_data:/data \
  -p 5432:5432 \
  ghcr.io/expaso/timescaledb/amd64:dev
```

This will use ~/timescaledb_addon_data as the data directory for the container, and map the port 5432 to the host.

If you want to start the container as a daemon, simply remove the `--rm` option and add the `-d` option like so:

```
docker run \
  -d \
  --name timescaledb \
  --v ${PWD}/timescaledb_addon_data:/data \
  -p 5432:5432 \
  ghcr.io/expaso/timescaledb/amd64:dev
```

## Usage

You are now ready to start using Postgres with TimescaleDb extenstions enabled!

Seeking a nice web-based client? **Try the pgAdmin4 addon.**

Please do not forget to also map the TCP/IP port in the network-section of the addon to the desired port number.
The default is port `5432`

**Securiy Notice!**

The default username is `postgres` with password `homeassistant`.
Make sure you change this immediately after activating the add-on:

```
ALTER USER user_name WITH PASSWORD 'strongpassword';
```

A default `pg_hba.conf` is created in the data directory with the following content, which allows local peer users and network users with passwords.:

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD
host    all             all             0.0.0.0/0               md5"
local   all             all             0.0.0.0/0               md5"
local   all             all             0.0.0.0/0               peer"
```

Please review this configuration carefully by examine the docs:
https://www.postgresql.org/docs/devel/auth-pg-hba-conf.html

### Now what..

Well.. Dive in!

You can read additional documentation on how you van work with your data and Grafana here:

https://github.com/expaso/hassos-addons/issues/1

## Support

- Got questions?
  [Open an issue here][issues]

- For a general repository issue or add-on ideas? [Open an issue here][repo-issues]

[issues]: https://github.com/expaso/hassos-addon-timescaledb/issues
[repo-issues]: https://github.com/expaso/hassos-addons/issues


## WARNING! THIS IS AN EDGE VERSION!

This Home Assistant Add-ons repository contains edge builds of add-ons.
Edge builds add-ons are based upon the latest development version.

- They may not work at all.
- They might stop working at any time.
- They could have a negative impact on your system.

This repository was created for:

- Anybody willing to test.
- Anybody interested in trying out upcoming add-ons or add-on features.
- Developers.

If you are more interested in stable releases, use the stable repository:

<https://github.com/expaso/hassos-addons>


[project-stage-shield]: https://img.shields.io/badge/project%20stage-production%20ready-brightgreen.svg
[release-shield]: https://img.shields.io/badge/version-324f5d3-blue.svg
[release]: https://github.com/expaso/hassos-addon-timescaledb/tree/324f5d3
[license-shield]: https://img.shields.io/github/license/expaso/hassos-addon-TimescaleDB.svg
[maintenance-shield]: https://img.shields.io/maintenance/yes/2024.svg