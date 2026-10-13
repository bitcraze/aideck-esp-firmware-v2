---
title: Wi-Fi configuration
page_id: wifi-configuration
sort_order: 2
---

The Wi-Fi settings are part of the firmware and are set when it is built. To change them, follow
the [build instructions](/docs/building-and-flashing.md#building). The settings on this page are
found under **Streamer configuration** in `idf.py menuconfig`, which is one of the steps there.
Flash the firmware after building it.

## Default: the AI-deck creates a network

By default, and in the released binary, the AI-deck is an access point. It creates an open network
named `aideck-nanocockpit`. Connect your computer to that network to reach the AI-deck.

Every AI-deck with the default settings creates a network with this same name. If you use more
than one, give each its own name.

The settings are

* **Act as access-point**: on
* **Access-point SSID**: the name of the network, 1 to 31 characters
* **Access-point password**: empty for an open network, otherwise 8 to 63 characters (WPA2)

## Connecting to an existing network

Turn **Act as access-point** off and set **WiFi SSID** and **WiFi Password** under **Credentials
for connecting to another access-point**. Leave the password empty for an open network. Enterprise
networks, where you log in with a user name and a password, are not supported.

`menuconfig` stores the settings in the `sdkconfig` file, which is tracked by git. Take care not
to commit the password of your network.

## Finding the AI-deck on the network

The AI-deck announces itself with [mDNS](https://en.wikipedia.org/wiki/Multicast_DNS), so a
computer can connect to `aideck.local` without knowing the IP address. This works both when the
AI-deck creates the network and when it connects to an existing one. The name is set with
**mDNS Hostname**; use different names if there is more than one AI-deck on the same network.

The AI-deck listens for CPX connections on port 5000.

## UDP

With **Use UDP for transmission** turned on, packets from the AI-deck to the computer are sent
over UDP instead of TCP. Lost packets are then not sent again. It is off by default.
