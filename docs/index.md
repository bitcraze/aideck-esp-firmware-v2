---
title: Home
page_id: home
---

[NanoCockpit](https://github.com/idsia-robotics/nanocockpit) is an application framework for
AI-based applications on the Crazyflie with an AI-deck. It takes care of camera configuration and
acquisition, forwarding the state estimate of the Crazyflie to the GAP8, and streaming camera
images and state to a computer over Wi-Fi.

This repository contains the part of NanoCockpit that runs on the ESP32 (NINA module) of the
AI-deck: it handles the Wi-Fi communication and passes
[CPX](https://www.bitcraze.io/documentation/repository/crazyflie-firmware/master/functional-areas/cpx/)
packets between the GAP8 and the computer. Most of the framework runs on the GAP8, and is found in
the [aideck-gap8-examples repository](https://github.com/bitcraze/aideck-gap8-examples/tree/master/nanocockpit)
together with example applications.

* [Building and flashing](/docs/building-and-flashing.md)
* [Wi-Fi configuration](/docs/wifi-configuration.md)

## Differences to the original ESP32 firmware

This firmware is an alternative to the original
[aideck-esp-firmware](https://github.com/bitcraze/aideck-esp-firmware). Compared to it, this
firmware is made for throughput, to stream camera images at a high frame rate.

It does not have

* CPX routing to and from the Crazyflie (STM32). Only the route GAP8 - ESP32 - computer is
  supported, so there is no CPX console in the Crazyflie client.
* flashing of the GAP8 over the air. The GAP8 is flashed with a JTAG programmer.
* Wi-Fi setup at run time. The Wi-Fi settings are set when the firmware is built.

The GAP8 application and the ESP32 firmware have to match. The NanoCockpit applications need this
firmware, and the other examples in aideck-gap8-examples need the original firmware.

## Credit

This firmware is part of [NanoCockpit](https://github.com/idsia-robotics/nanocockpit), created by
Elia Cereda, Alessandro Giusti and Daniele Palossi at IDSIA (USI-SUPSI) and ETH Zürich. If you use
it in an academic context, please cite their publication:

E. Cereda, A. Giusti, and D. Palossi, "NanoCockpit: Performance-optimized Application Framework for
AI-based Autonomous Nanorobotics", in IEEE Robotics and Automation Practice, 2026.
[IEEE Xplore](https://ieeexplore.ieee.org/document/11494460),
[arXiv](https://arxiv.org/abs/2601.07476)
