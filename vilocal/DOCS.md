# ViLocal Home Assistant App

## Introduction

ViLocal brings Viessmann ViCare thermostats and climate sensors into Home Assistant through MQTT **without requiring any cloud connectivity or Viessmann API subscriptions**.

It works by passively sniffing ZigBee communication between your ViCare devices and coordinator / Vitoconnect, capturing encrypted traffic and publishing device attributes to your local MQTT broker. With optional thermostat control, you can change radiator setpoints and floor heating output from Home Assistant through Ember Router [ember-router](https://github.com/kristian/ember-router).

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
- **Viessmann ViCare Floor Thermostats**
- **Viessmann ViCare Climate Sensors** (temperature/humidity)

**Important:**

By default ViLocal monitors your devices through passive sniffing. Changing radiator thermostat setpoints or floor heating output requires optional control and a paired EmberZNet adapter. See [Thermostat Control](#d-thermostat-control-optional).

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

- `embersniff` uses ember-sniff, best for network sniffers that expose serial/TCP streams
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

You can enable **Publish Unknown Devices** in Advanced Settings to discover radiator thermostats and climate sensors automatically. Add them in the app configuration to give them friendly names in Home Assistant.

For each device, enter the following.

- Serial number as found in your ViCare app or the printed label on the device
- Friendly name (e.g. "Living Room Thermostat")
- Optional Entity ID override (slug only, without domain). If left empty, it is generated from Name using Home Assistant-like slug rules (lowercase, spaces/camel-case converted to `_`, diacritics normalized, e.g. `Gäste WC` -> `gaste_wc`).
- Suggested area (optional)
- For radiator and floor thermostats, you can link a climate sensor by serial number to show its temperature and humidity readings.

Under **Floor Thermostats**, add one entry for each zone you want to see in Home Assistant. Enter the controller's eight-byte serial number and the zone's Zigbee endpoint (1–240). Zones on the same controller share its serial number. Give each zone a distinct name or Entity ID. ViLocal adds the floor controller automatically and shows its **Supply Temperature**.

To find a zone's endpoint, listen to `ViLocal/+/0x0008/0x4004` in the Home Assistant MQTT integration. Use your configured topic prefix in place of `ViLocal` if needed. The last two hex digits of the zone ID give the endpoint. For example, `AB-CD-EF-12-34-56-78-91-0A` is endpoint 10.

#### C. Advanced Settings (Optional)

Most users can keep defaults.

Only change advanced options when you have a clear reason:

- Language applies only to default device and entity names. Choose `en` for English (default) or `de` for German. ViLocal uses your configured names and suggested areas as entered. This setting will be removed when ViLocal can use the Home Assistant instance language automatically.
- Log level: more detail only when troubleshooting
- Buffer format: keep default unless integration requires another format
- Publish unknown devices: enable if you want data from unlisted devices
- Unwrap layers: packet parsing tuning for special setups
- Additional Control CLI Arguments passes flags such as `-v` to ember-router when control is enabled. Use `-gg` for reconnects with a dedicated adapter. In shared mode, the router waits five seconds after reconnecting for each request. Use `--settle-delay` to change the delay in milliseconds.
- Shared Router Keepalive (`advanced.router_keepalive_minutes`) defaults to 30 minutes. In shared mode, the router checks in with Vitoconnect before capture starts and after that interval without a control request. Periodic check-ins briefly pause capture. Set it to `0` to disable keepalive.

#### D. Thermostat Control (Optional)

Control lets you change a radiator thermostat's heating setpoint or a floor thermostat's requested output level from Home Assistant. Control uses ember-router, which operates an adapter with EmberZNet (NCP) firmware as a ZigBee router ("ViCare Repeater") joined to your ViCare network.

**Set-up (required once, outside of this app):**

The adapter used for control must be joined to your ViCare network as a repeater before enabling control. This cannot be done from within the app. On a machine that can reach the adapter (with Node.js, `ember-router` and `zbtk` installed), follow [Enable Thermostat Writes](https://github.com/kristian/ViLocal#5-optional-enable-thermostat-writes) in the ViLocal instructions ("Set Up Ember Router"). In short: generate an install code, derive the link key, `ember-router reset`, add a "ViCare Repeater (Unterputz)" in the ViCare app using the adapter's EUI64 and the install code, then run `ember-router join`. Stop anything else using the adapter while pairing.

Then enable control in the app configuration (**Thermostat Control**). Whether the capture adapter is shared depends on the Dedicated Control Interface / Path setting:

- **Shared adapter** (Dedicated Control Interface / Path empty): The capture adapter is also used for control. Requires Capture Source `embersniff`, and the adapter must be the one paired in the previous step. While a command is sent, capture pauses briefly and may miss the command or the thermostat's response.
- **Dedicated router** (Dedicated Control Interface / Path set, e.g. `tcp://192.168.1.43:6638`): A second adapter is used only for control. Capture can use any Capture Source and keeps running while commands are sent.

In shared mode, Home Assistant shows a requested radiator setpoint right away because capture may miss the thermostat's report while paused. A later captured report can update the display.

With **Enable Control** on, use a Home Assistant automation to turn a radiator's **External Window Open** switch on and off when a window contact sensor changes state. The **Window Open** sensor shows the state reported by the thermostat. See the [ViLocal documentation](https://github.com/kristian/ViLocal/blob/v3.7.0/README.md#external-window-open) for an example automation.

Each floor heating zone shows its **Requested Output Level** and whether its output is active. Turn on **Enable Control** to request a level from 0 to 100%. With a shared adapter, the active state may take until the controller's next report to update. Vitoconnect can replace the requested level, and ViCare still manages temperature targets and schedules. See the [ViLocal floor thermostat documentation](https://github.com/kristian/ViLocal/blob/v3.7.0/README.md#floor-thermostats) for more details.

Both modes use a local socket for binary requests and replies between ViLocal and ember-router.

#### E. Read and Write ZigBee Attributes from Home Assistant

Turn on **Enable Control** and connect the Home Assistant MQTT integration to the same broker as ViLocal. Use **Developer Tools → Actions → MQTT: Publish** to send a request manually, or add an `mqtt.publish` action to an automation or script. ViLocal needs to have observed traffic from the target device to know its current ZigBee network address.

Use a topic in the form `<mqtt.topic>/<device-id>/<cluster>/<attribute>/<read or write>`. Use your device's hyphen-separated EUI or the floor zone ID described below. Replace `ViLocal` in the examples with your configured MQTT topic. Always leave **Retain** off for requests.

For example, this automation action reads the local temperature attribute.

```yaml
- action: mqtt.publish
  data:
    topic: "ViLocal/28-DB-A7-FF-FE-23-C0-0E/0x0201/0x0000/read"
    payload: ""
    retain: false
```

This action writes a heating setpoint of 22 °C. Send a JSON payload in the attribute's ZigBee units. If ViLocal cannot infer the data type, use a JSON object such as `{"type":"0x29","value":2200}`, where `0x29` is a signed 16-bit integer.

```yaml
- action: mqtt.publish
  data:
    topic: "ViLocal/28-DB-A7-FF-FE-23-C0-0E/0x0201/0x0012/write"
    payload: "2200"
    retain: false
```

ViLocal publishes successful reads and acknowledged write values to the attribute's MQTT state topic, `<mqtt.topic>/<device-id>/<cluster>/<attribute>`. Use an MQTT trigger on that topic if an automation needs to react to updates. For manufacturer-specific attributes, include the manufacturer code in the request payload. Details can be found in [ViLocal's MQTT attribute command documentation](https://github.com/kristian/ViLocal/blob/v3.7.0/README.md#read-and-write-zigbee-attributes-over-mqtt).

For floor zones, append the two-digit hexadecimal endpoint to the controller's EUI with a hyphen. For example, endpoint 10 uses `AB-CD-EF-12-34-56-78-91-0A`. Again refer to the [ViLocal floor thermostat MQTT topics](https://github.com/kristian/ViLocal/blob/v3.7.0/README.md#floor-thermostat-mqtt-topics) for more details.

## Device Diagnostics

**Firmware Version** shows the software build reported by a device. Radiator thermostats also show the following diagnostics.

- **Valve Position** shows the estimated valve opening.
- **Target Valve Position** shows the opening the thermostat is aiming for.
- **Lifetime Motor Steps** shows the thermostat's lifetime motor movement counter.
- **Identification Mode** briefly turns on when you tap the thermostat's connection button.
- **Installation Mode** shows when the thermostat is ready to install.
- **Child Lock** shows the thermostat's lock state. With **Enable Control** on, you can also change it.

You can enable **Firmware Version**, **Target Valve Position**, **Lifetime Motor Steps**, **Identification Mode**, **Installation Mode**, and **Child Lock** in Home Assistant. These entities are disabled by default when first discovered.

ViLocal learns the valve's range during everyday heating and ViCare calibration sweeps. It adjusts the range as it sees new positions and reuses the previous range if calibration is inconclusive. ViLocal saves the range through the MQTT broker so it can restore it after a restart. Home Assistant shows **Unavailable** while ViLocal learns the range, while the thermostat calibrates, or when ViLocal is offline. See the [ViLocal valve position documentation](https://github.com/kristian/ViLocal/blob/v3.7.3/README.md#thermostat-valve-positions) for more details.

## FAQ

**Q: Does this work without the internet?**<br>
A: Yes! Everything is 100% local. No cloud connectivity required.

**Q: Is this invasive to my ViCare network?**<br>
A: No. ViLocal only listens passively by default. Only if you enable control, commands are sent to your thermostats through the configured ember-router adapter.

**Q: Can I control my thermostats?**<br>
A: Yes, optionally. Enable and set up thermostat control, see [Thermostat Control](#d-thermostat-control-optional).

**Q: Why do my thermostat valves show as unavailable?**<br>
A: Valve position is shown as **Unavailable** while ViLocal is learning the valve's range, during thermostat calibration, or when ViLocal is offline. You can try moving the valve manually to help ViLocal learn its range. See the [ViLocal valve position documentation](https://github.com/kristian/ViLocal/blob/v3.7.3/README.md#thermostat-valve-positions) for more details.

**Q: What hardware do I need?**<br>
A: A ZigBee sniffing device like SMLIGHT SLZB-06M or Ubisys Wireshark stick. See [tested devices](https://github.com/kristian/zbtk/blob/main/docs/tested-capture-devices.md).

**Q: How do I get the network key?**<br>
A: Follow the instructions in [ViLocal instructions](https://github.com/kristian/ViLocal#set-up--installation).

## Other Documentation

- **[ViLocal Project](https://github.com/kristian/ViLocal)** - Main project repository
- **[ZigBee Toolkit](https://github.com/kristian/zbtk)** - Underlying toolkit for parsing ZigBee traffic
