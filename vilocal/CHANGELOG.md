# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [2.3.2] - 2026-11-26

- Bump ViLocal to 3.7.3. Radiator valve position estimates adapt to new readings and reuse the learned range after an inconclusive calibration. [Upstream changes](https://github.com/kristian/ViLocal/compare/v3.7.2...v3.7.3)

## [2.3.1] - 2026-11-26

- Bump ViLocal to 3.7.2 and ember-router to 2.3.1. [Upstream changes](https://github.com/kristian/ViLocal/compare/v3.7.0...v3.7.2)

## [2.3.0] - 2026-11-25

> [!NOTE]
> The app's configuration is now available in German when you use Home Assistant in German. To show default device and entity names in German, choose `de` under Advanced Settings → Language. The default is `en` for English. This setting will be removed when ViLocal can use the Home Assistant instance language automatically.
>
> With Enable Control on, you can use a window contact sensor in a Home Assistant automation to tell a radiator thermostat when the window opens or closes. Child Lock, Identification Mode, and Installation Mode are available as optional entities. They start disabled when first discovered, along with Firmware Version and Lifetime Motor Steps.
>
> Floor heating zones are now linked to their controller in Home Assistant. The controller shows the supply water temperature.

- Bump ViLocal to 3.7.0 and pass the advanced language setting through `LANGUAGE`. [Upstream changelog](https://github.com/kristian/ViLocal/blob/v3.7.0/CHANGELOG.md)
- Add German translations for the app configuration.
- Add the External Window Open switch, Child Lock, and thermostat mode diagnostics. Valve position sensors now also use ViLocal's MQTT availability, and valve range learning ignores installation and cleaning movements.
- Use Home Assistant-compatible nine-byte floor zone IDs in MQTT topics and discover the floor controller with a Supply Temperature sensor.
- Support zone IDs and manufacturer-specific attributes in MQTT read and write requests.

## [2.2.0] - 2026-11-25

> [!NOTE]
> You can now add ViCare floor heating zones in the app's **Floor Thermostats** settings. Each zone shows its requested output level and whether the output is active. Link a ViCare climate sensor to see room temperature and humidity alongside them. With **Enable Control** on, you can also change the requested output from Home Assistant. ViCare still manages room temperature targets and schedules.
>
> ViCare radiator thermostats now show lifetime motor steps and, once ViLocal has seen enough valve movement, current and target valve openings. Home Assistant also shows firmware versions for devices that report them. If you use a shared adapter with keepalive enabled, ViLocal now checks in with your ViCare gateway at startup as well as after periods of inactivity.

- Bump ViLocal to 3.5.0, which includes ZigBee Toolkit 3.1.1. [Upstream changelog](https://github.com/kristian/ViLocal/blob/v3.5.0/CHANGELOG.md)
- Add floor thermostat configuration with one Home Assistant device per controller endpoint.
- Add radiator valve position estimates with learned bounds retained over MQTT, lifetime motor steps, and firmware version reporting for discovered devices.
- Send a power source report from the paired router to Vitoconnect when shared adapter keepalive runs, including at startup before capture begins.

## [2.1.0] - 2026-11-22

> [!NOTE]
> If you share one adapter for monitoring and controlling your thermostats, ViLocal now briefly brings the router back online after 30 minutes without a control request. This helps prevent ViCare from marking it offline. You can change the interval in Advanced Settings or set it to 0 to turn it off.
>
> Thermostats now show **Heat** mode in Home Assistant, the previous "Auto" mode was not the correct status for thermostats. With control enabled, you can also use Home Assistant's **MQTT: Publish** action to read or change individual ZigBee settings of your ViCare devices. The Documentation tab includes examples for both.

- Bump ViLocal to 3.3.1, ember-sniff to 1.5.2, and ember-router to 2.2.0. Set the Home Assistant climate HVAC mode to `heat`.
- Use the local `--router` socket for binary requests and replies with both shared and dedicated control adapters.
- Keep the shared adapter visible to ViCare by resuming the router after 30 minutes of inactivity. Set `advanced.router_keepalive_minutes` to `0` to disable keepalive.
- With control enabled, use Home Assistant's built-in `mqtt.publish` action to request ZigBee attribute reads and writes.

## [2.0.1] - 2026-11-21

- Bump ember-sniff to 1.5.1 fixing issues with graceful restarts and ember-router to 1.3.0 introducing the same graceful restarts, by setting `--graceful` / `-gg` as additional capture / control CLI arguments in the advanced settings.

## [2.0.0] - 2026-11-19

> [!NOTE]
> This major release introduces support for controlling thermostats. In case you are already using an EmberZNet based adapter for capturing ZigBee traffic, the same adapter can be used for controlling your thermostats as well, following the set-up instructions in the documentation. Using a dedicated adapter for control is possible and recommended for more reliable operation. Both operation modes are described in the documentation.

- Bump ViLocal to 3.0.0, enabling support for controlling climate controls for thermostats through ember-router. Bump ember-sniff and add ember-router as dependency. Set-up instructions for ember-router added to the documentation.

## [1.6.0] - 2026-11-19

- Bump ViLocal to 2.5.0, smaller changes to climate controls for thermostats.

## [1.5.2] - 2026-09-02

- Bump ViLocal to 2.4.2, improving logging for multiple PAN-IDs and fixing an issue in regards of FCS / checksum validation of filtered packets.

## [1.5.1] - 2026-09-01

- Bump ViLocal to 2.4.1, adding an option to filter for a specific network / PAN (Personal Area Network) ID in the advanced settings, allowing users to monitor only a specific ZigBee network when multiple networks are present

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
- Support for ember-sniff sniffing source (SMLIGHT/Ubisys dongle)
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
