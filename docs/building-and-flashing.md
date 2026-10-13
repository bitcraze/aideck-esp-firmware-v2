---
title: Building and flashing
page_id: building-and-flashing
sort_order: 1
---

You can use a released binary, or build the firmware yourself. You need to build it yourself if
you want to change the [Wi-Fi configuration](/docs/wifi-configuration.md). The firmware can be
flashed through the Crazyflie or with a JTAG programmer.

## Flashing through the Crazyflie

Newer Crazyflie release packages include this firmware in place of the original ESP32 firmware. If
you upgrade the firmware of a Crazyflie that has an AI-deck mounted with such a release, for
example from the Crazyflie client, this firmware is flashed to the ESP32 automatically.

To flash only this firmware, you need the application binary. That is either

* `aideck_esp_nanocockpit.bin`, downloaded from the
  [releases page](https://github.com/bitcraze/aideck-esp-firmware-v2/releases), or
* `build/aideck_cpx_streamer.bin`, if you [built the firmware yourself](#building)

Mount the AI-deck on the Crazyflie, turn the Crazyflie on and flash the binary over the Crazyradio.

With [cfcli](https://github.com/bitcraze/cfcli):

```bash
cfcli bootload flash --bin bcAI:esp-fw=[binary]
```

Or with cfloader:

```bash
cfloader flash [binary] deck-bcAI:esp-fw -w [CRAZYFLIE_URI]
```

An example for the CRAZYFLIE_URI is `radio://0/80/2M/E7E7E7E7E7`. Flashing takes about two minutes.

Only the application is written. The bootloader and partition table that are already on the ESP32
are kept, also if the deck has the original firmware on it.

## Building

The firmware is built with ESP-IDF 5.3.3, which is included in the `bitcraze/aideck-esp:0.1.0`
Docker image. From the root of the repository, start a console in the container:

```bash
docker run --rm -it \
    -v "${PWD}:/module/data/" \
    --device-cgroup-rule="c 189:* rmw" \
    --device-cgroup-rule="c 188:* rmw" \
    -v /dev/bus:/dev/bus:ro \
    -v /dev/serial:/dev/serial:ro \
    bitcraze/aideck-esp:0.1.0 \
    /bin/bash -c "source /esp/esp-idf/export.sh; cd /module/data/; bash"
```

In the container, optionally change the configuration and then build:

```bash
idf.py menuconfig
idf.py build
```

The result is found in the `build` directory.

## Flashing with a JTAG programmer

Connect the programmer to the ESP32 JTAG connector of the AI-deck, see the
[JTAG programmer documentation](https://www.bitcraze.io/documentation/repository/aideck-gap8-examples/master/development/jtag-programmer/).
The command below is for the Olimex ARM-USB-TINY-H and is run in the same container as the build:

```bash
openocd -f interface/ftdi/olimex-arm-usb-tiny-h.cfg -f board/esp-wroom-32.cfg -c "adapter_khz 20000" \
    -c 'program_esp build/partition_table/partition-table.bin 0x8000 verify' \
    -c 'program_esp build/bootloader/bootloader.bin 0x1000 verify' \
    -c 'program_esp build/aideck_cpx_streamer.bin 0x10000 verify reset exit'
```

This writes the partition table, the bootloader and the application.

**Note:** USB in Docker is only supported on Linux.
