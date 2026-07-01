# ViLocal Home Assistant App

## Introduction

ViLocal brings Viessmann ViCare thermostats and climate sensors into Home Assistant through MQTT **without requiring any cloud connectivity or Viessmann API subscriptions**.

It works by passively sniffing ZigBee communication between your ViCare devices and coordinator / VitoConnect, capturing encrypted traffic and publishing device attributes to your local MQTT broker.

### Key Features

✅ **Completely Local** - No internet or cloud connectivity required<br>
✅ **Non-Intrusive** - Passive sniffing doesn't interfere with your ViCare network<br>
✅ **Zero Startup Cost** - Works with affordable ZigBee sniffing hardware<br>
✅ **MQTT Discovery** - Automatic Home Assistant entity creation<br>
✅ **Configurable** - Simple UI for MQTT settings and device configuration<br>
✅ **Privacy-First** - All data stays on your local network<br>

### Supported ViCare Devices

- **Viessmann ViCare Thermostat Radiator Valves (TRVs)**
- **Viessmann ViCare Climate Sensors** (temperature/humidity)

**Important:**

Currently ViLocal it is **read-only** (monitoring). Thermostat control coming in future releases.

- Today, ViLocal is for monitoring and data collection (read-only)
- Active control of thermostats is not supported (yet)

## Quick Start

1. **Install sniffing hardware** (e.g. SMLIGHT SLZB-06M or Ubisys Wireshark stick, see [tested devices](https://github.com/kristian/zbtk/blob/main/docs/tested-capture-devices.md))
2. **Capture your ZigBee network key** (see [ViLocal docs](https://github.com/kristian/ViLocal#set-up--installation))
3. **Install this app** from Home Assistant add-on store
4. **Configure MQTT** and enter your network key
5. **Add thermostats/climate sensors**
6. **View devices** in Home Assistant

## Installation

### Requirements

- Home Assistant (OS, Container, or Supervised)
- MQTT broker (use Mosquitto add-on)
- ZigBee sniffing hardware
- ZigBee network key (captured from your network, see [ViLocal instructions](https://github.com/kristian/ViLocal#set-up--installation))

### Add the Repository

In Home Assistant:

1. Go to **Settings → Add-ons → Add-on Store**
2. Click the three-dot menu → **Repositories**
3. Add: `https://github.com/kristian/ViLocal-ha_app`
4. Search for "ViLocal" and install the app

Or directly:

```text
https://github.com/kristian/ViLocal-ha_app
```

### Configure the App via the UI

1. Open Settings -> Add-ons -> ViLocal.
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
- Example `ember-sniff` args: `-v -P 10`

MQTT broker settings:

- Broker URL (example: mqtt://192.168.1.10:1883)
- Username/password if required
- Topic and discovery prefix can usually stay at defaults

#### B. Device List Settings (Optional, Recommended)

You can run ViLocal without manually adding devices (by setting the "Add Unknown Devices" option in the advanced settings). However, in order for friendly names to appear in Home Assistant, it is recommended to add your thermostats and climate sensors in the configuration.

For each thermostat / climate sensor, provide:

- Serial number as found in your ViCare app or the printed label on the device
- Friendly name (e.g. "Living Room Thermostat")
- Suggested area (optional)
- For thermostats specifically, you can link a climate sensor serial number (optional). This will result in the HA thermostat using the climate sensor values instead of the built-in values of the thermostat.

#### C. Advanced Settings (Optional)

Most users can keep defaults.

Only change advanced options when you have a clear reason:

- Log level: more detail only when troubleshooting
- Buffer format: keep default unless integration requires another format
- Publish unknown devices: enable if you want data from unlisted devices
- Unwrap layers: packet parsing tuning for special setups

## FAQ

**Q: Does this work without the internet?**<br>
A: Yes! Everything is 100% local. No cloud connectivity required.

**Q: Is this invasive to my ViCare network?**<br>
A: No. ViLocal only listens passively - it doesn't send any commands or interfere with normal operations.

**Q: Can I control my thermostats?**<br>
A: Not yet. Current version is read-only (monitoring). Control is planned for future releases.

**Q: What hardware do I need?**<br>
A: A ZigBee sniffing device like SMLIGHT SLZB-06M or Ubisys Wireshark stick. See [tested devices](https://github.com/kristian/zbtk/blob/main/docs/tested-capture-devices.md).

**Q: How do I get the network key?**<br>
A: Follow the instructions in [ViLocal instructions](https://github.com/kristian/ViLocal#set-up--installation).

## Other Documentation

- **[ViLocal Project](https://github.com/kristian/ViLocal)** - Main project repository
- **[ZigBee Toolkit](https://github.com/kristian/zbtk)** - Underlying toolkit for parsing ZigBee traffic
