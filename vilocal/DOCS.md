# ViLocal Home Assistant App

## Introduction

ViLocal brings Viessmann ViCare thermostats and climate sensors into Home Assistant through MQTT **without requiring any cloud connectivity or Viessmann API subscriptions**.

It works by passively sniffing ZigBee communication between your ViCare devices and coordinator / VitoConnect, capturing encrypted traffic and publishing device attributes to your local MQTT broker. Optionally, thermostat setpoints can be changed from Home Assistant by forwarding commands to the ViCare network through Ember Router ([ember-router](https://github.com/kristian/ember-router)).

### Key Features

✅ **Completely Local** - No internet or cloud connectivity required<br>
✅ **Non-Intrusive** - Passive sniffing doesn't interfere with your ViCare network<br>
✅ **Control Optional** - Thermostat control can be enabled optionally<br>
✅ **Zero Startup Cost** - Works with affordable ZigBee sniffing hardware<br>
✅ **MQTT Discovery** - Automatic Home Assistant entity creation<br>
✅ **Configurable** - Simple UI for MQTT settings and device configuration<br>
✅ **Privacy-First** - All data stays on your local network<br>

### Supported ViCare Devices

- **Viessmann ViCare Thermostat Radiator Valves (TRVs)**
- **Viessmann ViCare Climate Sensors** (temperature/humidity)

**Important:**

By default ViLocal only monitors your devices (passive sniffing). Changing thermostat setpoints requires the optional thermostat control, see [Thermostat Control](#d-thermostat-control-optional) and additional set-up of [Ember Router](https://github.com/kristian/ember-router) and your ZigBee hardware / EmberZNet adapter.

## Quick Start

1. **Install sniffing hardware** (e.g. SMLIGHT SLZB-06M or Ubisys Wireshark stick, see [tested devices](https://github.com/kristian/zbtk/blob/main/docs/tested-capture-devices.md))
2. **Capture your ZigBee network key** (see [ViLocal docs](https://github.com/kristian/ViLocal#set-up--installation))
3. **Install this app** from Home Assistant app menu
4. **Configure MQTT** and enter your network key
5. **Add thermostats/climate sensors**
6. **View devices** in Home Assistant

## Installation

### Requirements

- Home Assistant (OS, Container, or Supervised)
- MQTT broker (best use Mosquitto app)
- ZigBee sniffing hardware
- ZigBee network key (captured from your network, see [ViLocal instructions](https://github.com/kristian/ViLocal#set-up--installation))

### Add the Repository

[![Add ViLocal Home Assistant App Repository to your Home Assistant](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2Fkristian%2FViLocal-ha_app)

Alternatively in Home Assistant:

1. Go to **Settings → Apps → Install app**
2. Click the three-dot menu → **Repositories**
3. Add: `https://github.com/kristian/ViLocal-ha_app`
4. Search for "ViLocal" and install the app

### Configure the App via the UI

1. Open Settings -> App -> ViLocal.
2. Open Configuration.
3. Fill in each section below.
4. Save.
5. Start from the Info tab.

#### A. Core Connection Settings

Network key:

- Enter your 32-character ZigBee transport / network key (hex, no spaces)

Data source mode:

- `embersniff`: best for network sniffers that expose serial/TCP streams
- `tcpdump`: best when sniffing from a local interface
- `whsniff`: direct channel-based sniffer output

If you choose `embersniff`:

- Set Interface / Path (example: `tcp://10.0.50.16:6638` or `/dev/ttyUSB0`)
- Set ZigBee channel (11-26)

If you choose `tcpdump`:

- Set Interface / Path to local interface name (example: `enx001fee00295e`)

If you choose `whsniff`:

- Set ZigBee channel (11-26)

Optional for any source in the advanced settings:

- Set Additional Capture CLI Arguments to append raw flags for the selected tool
- Example `tcpdump` args: `-n -vvv -s 0`
- Example `ember-sniff` args: `-v -P 10 -gg`

MQTT broker settings:

- Broker URL (example: mqtt://192.168.1.10:1883)
- Username/password if required
- Topic and discovery prefix can usually stay at defaults

#### B. Device List Settings (Optional, Recommended)

You can run ViLocal without manually adding devices (by setting the "Add Unknown Devices" option in the advanced settings). However, in order for friendly names to appear in Home Assistant, it is recommended to add your thermostats and climate sensors in the configuration.

For each thermostat / climate sensor, provide:

- Serial number as found in your ViCare app or the printed label on the device
- Friendly name (e.g. "Living Room Thermostat")
- Optional Entity ID override (slug only, without domain). If left empty, it is generated from Name using Home Assistant-like slug rules (lowercase, spaces/camel-case converted to `_`, diacritics normalized, e.g. `Gäste WC` -> `gaste_wc`).
- Suggested area (optional)
- For thermostats specifically, you can link a climate sensor serial number (optional). This will result in the HA thermostat using the climate sensor values instead of the built-in values of the thermostat.

#### C. Advanced Settings (Optional)

Most users can keep defaults.

Only change advanced options when you have a clear reason:

- Log level: more detail only when troubleshooting
- Buffer format: keep default unless integration requires another format
- Publish unknown devices: enable if you want data from unlisted devices
- Unwrap layers: packet parsing tuning for special setups
- Additional Control CLI Arguments: raw flags appended to the `ember-router write` command (e.g. `-v`), only used if control is enabled

#### D. Thermostat Control (Optional)

Control lets you change the heating setpoint of a thermostat from Home Assistant. Control is only supported through ember-router, which operates an adapter with EmberZNet (NCP) firmware as a ZigBee router ("ViCare Repeater") joined to your ViCare network.

**Set-up (required once, outside of this app):**

The adapter used for control must be joined to your ViCare network as a repeater before enabling control. This cannot be done from within the app. On a machine that can reach the adapter (with Node.js, `ember-router` and `zbtk` installed), follow [Enable Thermostat Writes](https://github.com/kristian/ViLocal#5-optional-enable-thermostat-writes) in the ViLocal instructions ("Set Up Ember Router"). In short: generate an install code, derive the link key, `ember-router reset`, add a "ViCare Repeater (Unterputz)" in the ViCare app using the adapter's EUI64 and the install code, then run `ember-router join`. Stop anything else using the adapter while pairing.

Then enable control in the app configuration (**Thermostat Control**). Whether the capture adapter is shared depends on the Dedicated Control Interface / Path setting:

- **Shared adapter** (Dedicated Control Interface / Path empty): The capture adapter is also used for control. Requires Capture Source `embersniff`, and the adapter must be the one paired in the previous step. While a command is sent, capture pauses briefly and may miss the command or the thermostat's response.
- **Dedicated router** (Dedicated Control Interface / Path set, e.g. `tcp://192.168.1.43:6638`): A second adapter is used only for control. Capture can use any Capture Source and keeps running while commands are sent.

In shared mode, optimistic climate control is enabled automatically: Home Assistant shows a requested setpoint right away instead of waiting for the thermostat's report, as capture may miss it while paused. A later captured report can still correct the display.

## FAQ

**Q: Does this work without the internet?**<br>
A: Yes! Everything is 100% local. No cloud connectivity required.

**Q: Is this invasive to my ViCare network?**<br>
A: No. ViLocal only listens passively by default. Only if you enable control, commands are sent to your thermostats through the configured ember-router adapter.

**Q: Can I control my thermostats?**<br>
A: Yes, optionally. Enable and set up thermostat control, see [Thermostat Control](#d-thermostat-control-optional).

**Q: What hardware do I need?**<br>
A: A ZigBee sniffing device like SMLIGHT SLZB-06M or Ubisys Wireshark stick. See [tested devices](https://github.com/kristian/zbtk/blob/main/docs/tested-capture-devices.md).

**Q: How do I get the network key?**<br>
A: Follow the instructions in [ViLocal instructions](https://github.com/kristian/ViLocal#set-up--installation).

## Other Documentation

- **[ViLocal Project](https://github.com/kristian/ViLocal)** - Main project repository
- **[ZigBee Toolkit](https://github.com/kristian/zbtk)** - Underlying toolkit for parsing ZigBee traffic
