# NINA CPX bridge

This is a minimal implementation of the CPX protocol for the ESP32, which allows communication with the drone over Wi-Fi.

## Build instructions

This project was developed using ESP-IDF 5.3.3, if you don't want to install it you can download a docker container [here](https://hub.docker.com/r/bitcraze/aideck-esp).
Start a bash console inside your docker:
```sh
docker run --rm -it \
    -v "${PWD}:/module/data/" \
    -P \
    --device-cgroup-rule="c 189:* rmw" \
    --device-cgroup-rule="c 188:* rmw" \
    -v /dev/bus:/dev/bus:ro \
    -v /dev/serial:/dev/serial:ro \
    bitcraze/aideck-esp:latest \
    /bin/bash -c "source /esp/esp-idf/export.sh; \
        cd /module/data/; \
        bash"
```
build the ESP application
```shell
idf.py clean; idf.py menuconfig; idf.py all
```

It is possible to set the Wi-Fi configuration inside Menuconfig. The supported options are:
* Station mode (STA, preferred): the ESP32 connects to an existing Wi-Fi network for which you provide SSID and password in menuconfig (default SSID: `AI-deck-streamer`, password: `changeme`)
* Access Point mode (AP): the ESP32 creates its own Wi-Fi network for which you provide SSID and WPA2 password in menuconfig (default SSID: `crazyflie`, default password: empty, i.e. an open network)

In addition, `menuconfig` provides the option to configure the [mDNS](https://en.wikipedia.org/wiki/Multicast_DNS) hostname of the drone, which allows a computer to connect without knowning its IP. The default hostname is `aideck.local`.

Flash the code over JTAG:
```shell
openocd -f interface/ftdi/olimex-arm-usb-tiny-h.cfg -f board/esp-wroom-32.cfg -c "adapter_khz 20000"  -c 'program_esp build/partition_table/partition-table.bin 0x8000 verify' -c 'program_esp build/bootloader/bootloader.bin 0x1000 verify' -c 'program_esp build/aideck_cpx_streamer.bin 0x10000 verify reset exit'
```

## Limitations

- Only the GAP<=>ESP32<=>Host route is supported in this version, other routes supported by the [official CPX implementation](https://github.com/bitcraze/aideck-esp-firmware) (e.g., with STM32) are out of scope for this project.
- Dynamic Wi-Fi configuration is not supported, the desired configuration must be set at build time with `make menuconfig`. AP mode supports open (configure an empty password) and WPA2 networks, STA supports open (configure an empty password) and password-protected Wi-Fi networks, but not enterprise networks (i.e. username/password authentication).
