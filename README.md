# Enphase Envoy for WeeWX

Poll an Enphase Envoy photovoltaic gateway over local HTTP at
`/api/v1/production`. This driver supplies current `power`, cumulative
`energy_total` and interval `energy`. It is separate from Davis Weather Envoy
hardware and does not use the Enphase cloud API.

Copyright 2017 Matthew Wall. Distributed under [GPLv3](license).

This repository mirrors upstream source. For WeeWX 5, install the separately
repaired catalog archive **0.1.post1** linked below. A branch download
does not include those catalog repairs.

## Install

An existing WeeWX station is required. Replace `/path/to/weewx.conf`
with its configuration file before running the commands.

Use the Python environment and `weectl` belonging to your WeeWX station. The catalog release requires Python >=3.10,<3.15 and WeeWX >=5.5,<6.

```sh
weectl extension install https://github.com/weewx-packages/catalog/releases/download/envoy/v0.1.post1/envoy-0.1.post1.tar.gz --config=/path/to/weewx.conf
weectl station reconfigure --driver=user.envoy --config=/path/to/weewx.conf
```

A downloaded copy of this same archive can be passed instead of the URL.
These commands work with stock WeeWX 5; no package-manager integration is
required.

## Configure local polling

```ini
[Station]
    station_type = Envoy
[Envoy]
    driver = user.envoy
    host = 192.0.2.10
    serial = YOUR_ENVOY_SERIAL
    polling_interval = 300
```

Replace the example address and serial number. The driver requires `serial` in
configuration, but its production request uses only `host`. It implements no
authentication and requires the legacy production endpoint to be accessible.
Token-protected firmware is outside this driver's implementation.

The polling interval must be at least 300 seconds. Restart WeeWX after selecting
the driver. There are no additional Python dependencies beyond WeeWX and the
standard library. Energy/power observations need corresponding archive columns,
unit groups and report configuration; the installer does not create a schema.
The installer adds a `grid_energy` sum accumulator, while the runtime emits
`energy`, so review aggregation for your schema.

Use the installed module for option help:

```sh
PYTHONPATH=/path/to/station/bin python -m user.envoy --help
```

Running it with `--host` polls a real gateway. No gateway was contacted during
this documentation check. See [envoy.py](bin/user/envoy.py) for the original
endpoint research and credits, and
[matthewwall/weewx-envoy](https://github.com/matthewwall/weewx-envoy) for upstream.

<!-- Documentation updated 2026-10-23 by weewx-packages. -->
