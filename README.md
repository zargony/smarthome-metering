# Smart Home Energy Metering

A small device for capturing energy consumption in my home by reading energy meters.

**Note:** This is not a general purpose project. It's a customized solution suitable for my requirements and made for my environment. Some links point to German sites, e.g. because of German energy meters used in my home. Still, you might find parts of it useful for yourself. Feel free to discuss ideas, improvements or your experiences with it.

This device uses a [Seeed Studio XIAO ESP32C3](https://wiki.seeedstudio.com/XIAO_ESP32C3_Getting_Started/), but basically any ESP device should do. I started prototyping using a NodeMCU V2 dev kit and switched to the XIAO module because of its external antenna (bad Wifi connectivity in basement).

[Tasmota firmware](https://tasmota.github.io/) captures energy meters readings and submits them to an MQTT broker where they can be picked up by various tools, e.g. [Home Assistant](https://www.home-assistant.io/). I'm also using [Telegraf](https://www.influxdata.com/time-series-platform/telegraf/) to post data to [InfluxDB](https://www.influxdata.com/products/influxdb/) where it's archived and can be visualized by tools like [Grafana](https://grafana.com/).

## Gas

Counting impulses sent by a reed contact at the gas meter.

A [Honeywell IN-Z61](https://process.honeywell.com/us/en/site/elster-instromet-de/produkte/gasmessung/balgengaszahler/in-z61) impulse sensor attached to a [GMT BK-G4](https://www.gmt.de/gasmessung/haushalt/bk-g-4) gas meter. One impulse is sent every 0.01 m³. Impulses are counted and summed up in software. This is a trivial interface and should work with most other gas meters that provide a reed contact.

## Water

**Work in progress** (got no sensor yet). Counting impulses sent by a pulse emitter at the water meter.

An [Ebeling & Sohn ES40-13](https://www.ebelingundsohn.de/unser-es-40-13/) water meter that allows to attach a [Sensus HRI](https://www.xylem.com/de-de/products--services/telemetry-communications--data-transfer/hand-held--reading-devices/hri-sensor/) sensor. [Several variants](https://www.xylem.com/siteassets/brand/sensus/resources/data-sheets/hri-sensor-data-sheet.pdf) exist. HRI-A is a simple pulse emitter while HRI-B has an M-Bus data interface. While HRI-A would be sufficient, they don't seem to be available anymore nowadays. The pulse emitter should be a trivial interface, similar to the gas meter.

## Electrical

Reading serial OBIS information from electrical meter.

The [eBZ DD3](https://www.ebzgmbh.de/produkte/dd3) electrical meter has an infrared Tx/Rx interface that continuously provides information about voltage, current, power and total consumption. Several [DIY IR sensors](https://www.heise.de/tests/Ausprobiert-Guenstiger-IR-Lesekopf-fuer-Smart-Meter-mit-Tastmota-Firmware-7065559.html) (and [schematics](https://wiki.volkszaehler.org/hardware/controllers/ir-schreib-lesekopf)) are available online, e.g. I got a Hichi IR (the simple TTL variant, no Wifi or USB). It works fine with 3.3V and is connected directly to the MCU UART. It continuously receives metering information in OBIS D0 format (D0 is ASCII based, unlike SML).

## Future Ideas

- Improve connectivity by using [Thread](https://www.threadgroup.org/) networking instead of Wifi. The XIAO ESP32C3 module could be replaced by a pin-compatible [XIAO nRF52840](https://wiki.seeedstudio.com/XIAO_BLE/) module, but there's currently no firmware for nRF52 like Tasmota for ESP.
- Input signal [hardware debounce](https://www.digikey.de/en/articles/how-to-implement-hardware-debounce-for-switches-and-relays). Especially needed for reed contacts. It's currently done in software and works fine, but hardware debounce might be a useful option.
- Use drivers for inputs with long wires. Even though some sensors are several meters away, they're connected directly to the MCU. It might be better to put some driver inbetween (could be combined with debouncing).
