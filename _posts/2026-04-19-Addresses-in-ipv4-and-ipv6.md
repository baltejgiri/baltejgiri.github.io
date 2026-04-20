---
layout: post
title: "Multicast Addresses"
date: 2026-04-18
categories: [Networking,CCNA]
tag: [blog,ccna-vol1-ch23]
---

# Overview

IPv4 uses three types of addresses unicast, multicast and broadcast similarly IPv6 also three types of addresses, including unicast and multicast however there is no broadcast addresses instead it uses anycast addresses, which are not available in IPv4.

Functions that are performed by by broadcast addresses in IPv4 are performed by multicast and anycast address in IPv6. Unicast addresses in IPv6 work similarly to how they work in IPv4. A unicast address is also used to send packets to a single device.

## Reference Table for IPv4 Multicast Addresses to IPv6

| Multicast Address               | IPv4              | IPv6              |
|---------------------------------|-------------------|-------------------|
| All Hosts                       | 204.0.0.1         |FF02:0:0:0:0:0:0:1 |
| All Routers                     | 204.0.0.2         |FF02:0:0:0:0:0:0:2 |
| All OSPF Routers                | 204.0.0.5         |FF02:0:0:0:0:0:0:5 |
| All OSPF DRs                    | 204.0.0.6         |FF02:0:0:0:0:0:0:6 |
| All RIP Routers (expect RIPv1)  | 204.0.0.9         |FF02:0:0:0:0:0:0:9 |
| All EIGRP Routers               | 204.0.0.10        |FF02:0:0:0:0:0:0:A |