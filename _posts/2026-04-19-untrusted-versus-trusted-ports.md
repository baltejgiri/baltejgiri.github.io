---
layout: post
title: "Untrusted versus Trusted Ports"
date: 2026-04-19
author: 
categories: [Networking,]
tag: [blog,ccna,ccnp]
---

## Overview
Networking protocols provide services to achieve a specific goal as per their design. However, an attacker can take benefit of these protocols to destruct the main functionality of the protocols.

Today's topic is DHCP, we know DHCP provides network addresses to the client's and servers upon a dynamic request. DHCP Server uses DORA process in the sequence of DISCOVER, OFFER, RESPONSE AND ACKNOWLEDGE messages to provide network addresses to clients. Client is referred to any device that needs to learn network addresses dynamically and Server is referred to a DHCP Server which provides the network addresses to clients.

- Client sends DISCOVER and REQUEST.
- Server sends OFFER and ACKNOWLEDGE.

There are two more messages that clients uses - DHCP RELEASE and DHCP DECLINE. These messages are only used by client. A client can tell the DHCP server that it no longer needs to use the network address, basically releasing the network address back to DHCP Server with DHCP RELEASE message. The CMD command ```ipconfig /release``` on the Windows system uses the DHCP RELEASE message.

A Client can also send a DHCP DECLINE message to DHCP server stating it does not need the IP address during a DORA process.

Based of who sends which message in DHCP DORA process basically helps why we configure untrusted and trusted ports.

 DHCP messages are broadcasted on a single broadcast domain means anyone connected on the same domain can receive these messages, by default switches has no mechanism to prevents these messages filter on which port they should be received or sent. Thus, we introduce the title of this post **Untrusted versus Trusted** ports often known as **DHCP Snooping**.

## DHCP Snooping

DHCP Snooping analyzes incoming messages on the specified subset of ports in a VLAN and may filter DHCP messages using a simple logic either allow or discard the packet based of the switch port configuration. Basically, configuring the switch port to either untrust or trust any DHCP messages are received.

### Untrusted Port

- DHCP messages received on a untrusted port, normally send by a server will always be discarded. Messages like DHCPOFFER and DHCPACK.
- DHCP messages received on a untrusted port, sent by a DHCP client, may be filtered if they appear to be the part of an attack. Messages like DHCPDISCOVER and DHCPREQUEST.

### Trusted Port

- DHCP messages received on a trusted port will be forwarded; trusted ports do not filter (discard) any DHCP messages.

## When should a switch port be trusted or untrusted?


### Trust ports if
- A DHCP server connected to switch port, so the DHCPOFFER and DHCPACK messages can be sent.
- A trunk port (link between switches), so that DHCPOFFER and DHCPACK messages can reach to the destination client.
- A router interface is connected to switch, so that router can relay the DHCP requests to the DHCP server which maybe on a different network.

### Untrust ports if
- A client is desktop, laptop, printer or a VoIP phone since they do not send DHCPOFFER and DHCPACK messages however, a attacker can connect to these ports and attempt to send DHCPOFFER and DHCPACK.
- Switch ports that are open in public area like conference rooms and physical access to wall port is easily available.
- A port connects to a Wireless Access Point, Clients connected via an AP can try to spoof DHCP server responses if switch ports left trusted for APs.

## DHCP Snooping Binding Table

DHCP Snooping binding table is built dynamically by the switch through a process of "listening in" (snooping) on the 4-step DORA process between a client and a server.

A binding table tracks which (MAC address) is allowed to use which IP address on a specific physical port. Switch creates an entry in the binding table once it sees a successful lease is formed by a client.

A binding table does sounds like a MAC Address table however they both are different from each other. 

- A MAC Address table is build from any frame whereas a binding table is build from DHCP 4-step handshake.

- A MAC Address table only maps MAC Address to VLAN and Physical port whereas a binding table maps the MAC address to IP Address, Lease, Type, VLAN and Interface.

>>To be continued.

 





