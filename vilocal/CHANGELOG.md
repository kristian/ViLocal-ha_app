# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.5.0] - 2026-08-30

- Bump ViLocal to 2.4.0, adding an option to the advanced settings to retain the state of all published devices in the MQTT broker, allowing Home Assistant to restore the last known state after a restart

## [1.4.3] - 2026-08-28

- Bump ViLocal to 2.3.1 / zbtk 2.6.2 fixing "Met undefined tag value 31 at choice" error, caused by unknown pakets from floor heating thermostats
- Bump dependency ember-sniff to 1.4.1 fixing an issue with graceful restarts

## [1.4.2] - 2026-08-28

- Bump dependency ember-sniff to 1.4.0

## [1.4.1] - 2026-08-25

- Bump dependency ember-sniff to 1.3.0

## [1.4.0] - 2026-08-03

- Pin ViLocal version to 2.3.0

## [1.3.3] - 2026-07-20

- Bump dependency ember-sniff to 1.2.1

## [1.3.2] - 2026-07-20

- Bump dependency ember-sniff to 1.2.0

## [1.3.1] - 2026-07-03

- Improve install instructions in readme file

## [1.3.0] - 2026-07-01

- Remove custom apparmor.txt profile

## [1.2.2] - 2026-07-01

- Bump to latest version of ember-sniff

## [1.2.1] - 2026-07-01

- Fix AppArmor profile to access DNS

## [1.2.0] - 2026-07-01

- Add need for MQTT service in config.yaml to ensure Home Assistant starts the app after the MQTT broker is ready

## [1.1.4] - 2026-07-01

- Fix AppArmor profile to execute Node

## [1.1.3] - 2026-07-01

- Fix AppArmor profile to access Corepack Cache

## [1.1.2] - 2026-07-01

- Fix AppArmor profile to access Node Modules / Yarn

## [1.1.1] - 2026-07-01

- Fix AppArmor profile to access OpenSSL (required for Node.js)
- Hide MQTT password from logs

## [1.1.0] - 2026-07-01

- Add optional Entity ID override for thermostats and climate sensors in configuration

## [1.0.3] - 2026-07-01

- Fix AppArmor profile

## [1.0.2] - 2026-07-01

- Move CHANGELOG.md file to app root

## [1.0.1] - 2026-07-01

- Fix AppArmor profile
- Improve documentation

## [1.0.0] - 2026-07-01

### Added

- Initial release of ViLocal Home Assistant app
- Full configuration UI for MQTT broker settings
- Support for configurable thermostats and climate sensors
- Automatic MQTT Discovery integration with Home Assistant
- Support for embersniff sniffing source (SMLIGHT/Ubisys dongle)
- Support for tcpdump sniffing source (raw network interface)
- Advanced settings: log level, buffer format, publish unknown devices
- Comprehensive documentation and troubleshooting guide
- Multi-language support (English)
- AppArmor security profile
- USB device passthrough for ZigBee dongles
- Configuration persistence in Home Assistant

### Known Limitations

- Read-only access to devices (monitoring only)
- Thermostat control not yet implemented
- Requires external ZigBee sniffing hardware
- Network key must be manually captured from ZigBee network
- Works only with Viessmann ViCare devices
