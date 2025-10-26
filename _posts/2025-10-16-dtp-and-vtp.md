---
layout: post
title: Understand Dynamic Trunk (DTP) and VLAN Trunk Protocol (VTP)
date: 2025-10-16
author: 
#categories: [Networking]
tag: [blog, ccna,ccnp]
---

## Understand Dynamic Trunking Protocol (DTP)

Normally switchports are manually configured using either ```switchport mode access``` or ```switchport mode trunk```. However, DTP allows Cisco switches to negotiate the status of their switchports to be either access ports or trunk ports, without manual configuration. DTP is enabled by default on Cisco switch interfaces. Still, it is recommended to manually configure Cisco switchports due to security purposes as DTP can be exploited by attackers.

### How does DTP Negotiation Works?

DTP negotiation involves the exchange of DTP frames between two neighboring switch interfaces.

### DTP Modes and Their Negotiation Behavior

#### Dynamic Auto

- A switchport in dynamic auto mode will NOT actively try to form a trunk with other Cisco switches, however it will form a trunk if the switch connected to it is actively trying to form a trunk. It will form a trunk with a switchport in following modes:

```plaintext
switchport mode trunk
switchport mode dynamic desirable
```

#### Dynamic Desirable

- Actively initiates trunking by sending negotiation messages to dynamically choose weather to start trunking. The switch's interface becomes trunk port if the neighboring interface is set to trunk or dynamic desirable mode.

#### Trunk Mode

- The interface is permanently set to trunking mode. It sends DTP frames to try and convert the neighboring switch interface into a trunk as well.

#### Access Mode

- The port is permanently set to non-trunking (access) mode. It will not form a trunk even if the neighboring switch's interface request it.

#### Nonegotiation Mode

- Disables DTP completely - the port won't send or be affected by DTP frames.

- DTP will not form a trunk link with a router, PC, etc. The switchport will be in access mode.

- DTP negotiation on an interface can be disabled using command: ```switchport nonegotiate```

### Dynamic Trunking Protocol Mode Chart

| Administrative Mode | Trunk | Dynamic Desirable | Access | Dynamic Auto |
|---------------------|-------|-------------------|--------|--------------|
| Trunk               | Trunk | Trunk             |   X    | Trunk        |
| Dynamic Desirable   | Trunk | Trunk             | Access | Trunk        |
| Access              |   X   | Access            | Access | Access       |
| Dynamic Auto        | Trunk | Trunk             | Access | Access       |

### DTP configuration

DTP configuration uses two Cisco switches and link is connected between both switches.

```bash
SW2(config-if)#switchport mode ?
  access        Set trunking mode to ACCESS unconditionally
  dot1q-tunnel  set trunking mode to TUNNEL unconditionally
  dynamic       Set trunking mode to dynamically negotiate access or trunk mode
  private-vlan  Set private-vlan mode
  trunk         Set trunking mode to TRUNK unconditionally
!
SW2(config-if)#switchport mode dynamic ?
  auto       Set trunking mode dynamic negotiation parameter to AUTO
  desirable  Set trunking mode dynamic negotiation parameter to DESIRABLE

SW2(config-if)#switchport mode dynamic 
```
The output above shows switchport can be set to ***dynamic auto*** or ***dynamic desirable***.

- A switchport in ***dynamic desirable*** mode will actively try to form a trunk with other Cisco switches. It will form a trunk if connected to another switchport in the following modes:

    ```bash
    switchport mode trunk
    switchport mode dynamic desirable
    switchport mode dynamic auto
    ```

| SW1 | SW2 |
|----------|-----------|
| Switchport mode dynamic desirable | Switchport mode trunk |

Switch 1's interface gig0/0 is set to dynamic desirable whereas switch 2 interface gig0/0 (link to switch 1's gig0/0) is set to forced trunk.

```bash
SW1#show interfaces gig0/0 switchport 
Name: Gi0/0
Switchport: Enabled
Administrative Mode: dynamic desirable
Operational Mode: trunk
!
SW2#show interfaces gig0/0 switchport 
Name: Gi0/0
Switchport: Enabled
Administrative Mode: trunk
Operational Mode: trunk
```

Switch 1's interface gig0/0 operational mode negotiated to trunk mode because switch 2's administrative mode was manually configured to trunk. This happened due to Switch 1 actively sent the DTP Frames and Switch 2 was already configured with Trunk.

## Understand VLAN Trunking Protocol (VTP)

VTP is another Cisco proprietary layer 2 messaging protocol that allows you to configure VLANs on a central switch, which then acts as a server that other switches can synchronize to, so you don't have to configure VLANs on every single switch in the network.

VTP can be seen as a way to automate the network administration where a network engineer can create, modify, or delete a VLAN on a single switch (the VTP Server), since the changes from VTP server switch get's propagated to the all other switches in the same domain. However, VTP was not used a lot due to it's biggest disadvantage of wiping network configuration on entire domain if incorrect VTP Server (switch) was plugged in to the same network domain.

### VTP Messages in detail

VTP packets are transmitted within either Inter-Switch Link (ISL) frames or IEEE 802.1Q (dot1q) frames. These packets use the destination (multicast) MAC address ```01:00:0C:CC:CC:CC``` and include a Logical Link Control (LLC) header with a Subnetwork Access Protocol (SNMP) code of AAAA and a type value of 2003 in the SNAP header. The following two diagrams shows the structure if a VTP packet encapsulated in ISL and 802.1Q:

![VTP Packet Encapsulation in ISL Frame](/assets/img/VTP_Packet_Encapsulation_in_ISL_Frame.png)

> VTP Packet Encapsulation in ISL Frame

![VTP Packet Encapsulation in 802.1Q Frame](/assets/img/VTP_Packet_Encapsulation_in_802.1Q_Frame.png)
> VTP Packet Encapsulation in 802.1Q Frame

VTP packets contain following fields in the header:

- VTP protocol version 1, 2, and 3.
- VTP message types:
    - Summary advertisements
    - Subset advertisement
    - Advertisement requests
    - VTP join messages
- Management domain length
- Management domain name

### Configuration Revision Number

The VTP configuration revision number is a 32-bit value used to track changes to the VLAN configuration. Every VTP device maintains its own revision number, which is included in most VTP packets sent. This number helps receiving device determine if the information is newer than their current configuration. The revision number is ***incremented by one*** with every VLAN change. To ***reset***the revision number on a switch, you must temporarily change the VTP domain name and then revert it to the original name.

### Summary Advertisements

By default, ***VTP summary advertisements*** are sent every ***five minutes*** to announce the domain name and current configuration revision number. Upon receipt, a switch first ***ignores*** the advertisement if the ***VTP domain name*** does not match its own. If the domains name matches, the switch compares the received ***revision number*** to its own. If its local number is lower, it requests an update; otherwise, the packet is ignored.

![Summary Advert Packet Format](/assets/img/Summary_Advert_Packet_Format.png)
> Summary Advert Packet Format

### Subset Advertisement

Whenever a VLAN is modified (added, deleted, or changed) on server switch, the switch's configuration revision number increments, and it immediately broadcasts a ***summary advertisement***. This Summary is followed by one or more ***subset advertisements***. The ***subset advertisements*** carry the actual details, listing the ***VLAN information*** that changed. Id the list of updated VLAN is extensive, the VTP server may need to send ***multiple subset advertisements*** to transmit all the necessary data.

### Advertisement Request

A switch sends a ***VTP advertisement request*** when it needs to immediately synchronize its VLAN configuration. This is necessary in three specific scenarios:

- The switch has been ***reset*** (rebooted).
- The ***VTP domain name*** on the switch has been ***changed***.
- The switch receives a ***summary advertisement*** showing a ***higher configuration revision number*** than its own.

### VTP Modes

You can set a switch to operate in one of four VTP modes, determining its role in managing and distributing VLAN information:

#### Server (Default Mode)
Server mode is the control center for VTP. A switch in this mode can create, modify, and delete VLANs, and it's where you set domain-wide parameters like the VTP version or pruning. Servers actively advertise their VLAN configuration to other switches in the domain and synchronize their own configuration when they receive updates from other servers.

#### Client
Client switches function much like servers in terms of receiving and acting on updates: they synchronize their VLAN configurations based on advertisements they receive. The key difference is that you cannot locally create, change, or delete VLANs on a VTP client switch. Clients simply listen and adopt the configuration provided by a server.

#### Transparent
Transparent switches completely do not participate in the VTP domain. They neither advertise their own local VLAN configuration nor synchronize their configuration based on received VTP advertisements. However, in VTP Version 2, they act as conduits, forwarding any VTP advertisements they receive out their trunk ports to other switches.

#### Off
The Off mode is similar to the Transparent mode in that the switch does not participate in VTP management or synchronization. The critical distinction is that switches in Off mode do not forward VTP advertisements at all, effectively blocking the VTP traffic on that trunk. (In Server, Client, and Transparent modes, VTP advertisements are processed or forwarded once the switch is in the management domain state.)

### DTP Configuration

## Summary

## References

- Cisco Official Cert Guide [CCNA 200-301 Official Cert Guide Library, 2nd Edition](https://www.ciscopress.com/store/ccna-200-301-official-cert-guide-library-9780138221393)
- Video [Jeremy's IT Lab YouTube Videos](https://www.youtube.com/@JeremysITLab)
- Cisco Whitepaper [Understand VLAN Trunking Protocol](https://www.cisco.com/c/en/us/support/docs/lan-switching/vtp/10558-21.html)
