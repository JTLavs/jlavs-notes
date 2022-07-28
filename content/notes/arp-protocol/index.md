---
title: Network Protocols - ARP
date: "2022-07-27"
description: The Address Resolution Protocol
tags: ["networks"]
---
- This protocol is needed to map IPv4 addresses to MAC addresses
- The device will check and ARP table in RAM memory, if none is found it sends out an ARP request
- If the device is on the same network as its destination, it will search for the IPv4 of the destination
- If the device is on a different network it looks for the IPv4 of the default gateway
- When the request is broadcast to every device on the network, when the device with the matching IPv4 address recieves the request, it responds with its MAC address and this is then populated in the ARP cache.
- ARP entries are removed from the ARP table if they have not been used for a certain period of time and are added on the fly.
