---
title: Network Protocols - Ethernet
date: "2022-07-27"
description: The Ethernet Protocol
tags: ["networks"]
---
- Operates on the physical and data link layers of the OSI model and Network Access/Link layer of the TCP/IP model
- Minimum frame size is 64 bytes, maximum is 1518 bytes
- Preamble field not included in frame size
- Any frame less than 64 bytes or greater than 1518 bytes is discarded
- Preamble & SFD (start of frame delimiter) fields are 7 and 1 bytes in size, respectively and are used for synchronisation between devices.
- Destination MAC address field used to identify recipient
- Source MAC address field used to identify NIC where frame originated. Can only be unicast (to one address)
- Type/length field identifies the upper layer protocol encapsulated in the Ethernet frame (2 bytes in size)
- Data field contains encapsulated frame from a higher layer. 46-1500 bytes in in size
- Frame check sequence field used to detect errors (4 bytes)
