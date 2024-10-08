![Project Stage][project-stage-shield]
![Maintenance][maintenance-shield]
[![License][license-shield]](https://github.com/expaso/hassos-addon-pgadmin4/blob/main/LICENSE)

# Home Assistant Add-on: [pgAdmin 4](https://www.pgadmin.org/)

<a href="https://www.buymeacoffee.com/expaso" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>

## Introduction

pgAdmin is the most popular and feature rich Open Source administration and development platform for PostgreSQL, the most advanced Open Source database in the world.

This Add-on can be used to control any postgreSQL databases on your network, incuding those served the TimescaleDB add-on, which can also install from the same addon-repository.

## Configuation

Example add-on configuration:

```
 {
    "ssl": true,
    "certfile": "fullchain.pem",
    "keyfile": "privkey.pem",
    "system_packages": [],
    "init_commands": [],
    "leave_front_door_open": false
 }
```

### Option: `ssl`

Indicates if the add-on UI will be servd from port 80 or port 443 (using the provided certificates).

### Option: `certfile`

The filename of your SSL Certificate in the `ssl` folder.

### Option: `certfile`

The filename of your Private Key File in the `ssl` folder.

### Option: `system_packages`

Optional extra Alpine packages that will be installed during add-on startup.
**Beware**: Adding a lot of packages could lead to long startup time.

### Option: `init_commands`

Any extra commands that will be run during add-on startup.

### Option: `leave_front_door_open`

Serves the website without protection of home-assistant user authentication.

**CAUTION!!**

Setting this option is a potential security risk and should be avoided whenever possible.
If you don't know what you are doing, just leave it off.

## Support

- Got questions?
  [Open an issue here][issues]

- For a general repository issue or add-on ideas? [Open an issue here][repo-issues]

[issues]: https://github.com/expaso/hassos-addon-pgadmin4/issues
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
[release-shield]: https://img.shields.io/badge/version-43c1114-blue.svg
[release]: https://github.com/expaso/hassos-addon-pgadmin4/tree/43c1114
[license-shield]: https://img.shields.io/github/license/expaso/hassos-addon-pgAdmin4.svg
[maintenance-shield]: https://img.shields.io/maintenance/yes/2024.svg