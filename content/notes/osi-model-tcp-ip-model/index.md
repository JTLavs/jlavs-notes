---
title: Networking Models - OSI and TCP/IP
date: "2022-07-27"
description: The OSI model and the TCP/IP model
tags: ["networks"]
---
In the OSI networking model, there are 7 layers:
1. Physical 
2. Data Link
3. Network
4. Transport
5. Session
6. Presentation
7. Application

In the TCP/IP network model there are just 4 layers:
4. Application
3. Transport
2. Internet
1. Network Access

Some of the protocols used in the application, presentation and session layers (Layers 7,6 and 5) are `HTTP`, `DNS`, `DHCP` and `FTP`.

Some of the protocols used in the Transport layer (Layer 4) include `TCP` and `UDP`

Some of the protocols used in the Network layer (Layer 3) include `IP` and `ICMP`.

Some of the protocols used in the Data Link layer (Layer 2) include `Ethernet`.

Some differences between the 2 models are:
- The minimum header size of the TCP/IP model is 20 bytes, the minimum header size of the OSI model is 5 bytes.
- TCP/IP follows a horizontal approach, OSI model follows a vertical approach

