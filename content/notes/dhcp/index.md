---
title: Network Protocols - DHCP
date: "2022-02-21"
description: The Dynamic Host Configuration Protocol
tags: ["networks"]
---
In networking, the Dynamic Host Configuration Protocol is responsible for allocating IPv4 addresses, subnet masks and other parameters to hosts on a network.

Within your private home network, you will more than likely have an DHCP enabled router. When a new device connects this server is contacted.

The DHCP server will then choose from a range of IP addresses which have been configured (this range can be set within your router settings, but will come preset) and leases it to the host.

For the period of time the host is assigned the IP address is known as the lease period, which is also configurable. After this time elapses, the address is returned to the pool.

To initiate the process, when it connects the client/host sends a `DHCPDISCOVER` message to find a server.

The DHCP server picks up on this and responds with an IPv4 address and subnet mask within a `DHCPOFFER` message. This message also contains the duration of the lease.

If the client likes this IPv4 address it sends back a `DHCPREQUEST` message which identifies the server and the offer it is accepting.

The server responds again with a `DHCPACK` message to finalise the exchange. However if the offer has expired, the server will respond with a `DHCPNACK`.
