# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
