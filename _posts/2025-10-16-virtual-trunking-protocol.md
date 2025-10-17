---
layout: post
title: Two Layer 2 Cisco Proprietary Protocols - DTP and VTP
date: 2025-10-16
author: 
#categories: [Networking]
tag: [blog, ccna]
---

### Dynamic Trunking Protocol (DTP)

Normally switchports are manually configured using either ```switchport mode access``` or ```switchport mode trunk```. However, DTP allows Cisco switches to negotiate the status of their switchports to be either access ports or trunk ports, without manual configuration. DTP is enabled by default on Cisco switch interfaces. Still, it is recommended to manually configure Cisco switchports due to security purposes as DTP can be exploited by attackers.

#### How does DTP Negotiation Works?

DTP negotiation involves the exchange of DTP frames between two neighboring switch interfaces.

##### DTP Modes and Their Negotiation Behavior

1. Dynamic Auto

    - Passively waits to receive a negotiation message to make itself a trunk, at which point the switch will respond and negotiate whether to use trunking or not. The switch's interface becomes trunk port if the neighboring interface is set to trunk or dynamic desirable mode.

2. Dynamic Desirable
    - Actively initiates trunking by sending negotiation messages to dynamically choose weather to start trunking. The switch's interface becomes trunk port if the neighboring interface is set to trunk or dynamic desirable mode.

3. Trunk Mode
    - The interface is permanently set to trunking mode. It sends DTP frames to try and convert the neighboring switch interface into a trunk as well.

4. Access Mode

    - The port is permanently set to non-trunking (access) mode. It will not form a trunk even if the neighboring switch's interface request it.

5. Nonegotiation Mode

    - Disables DTP completely - the port won't send or be affected by DTP frames.

#### DTP configuration

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

    ```
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


### VLAN Trunking Protocol (VTP)

VTP is another Cisco proprietary layer 2 messaging protocol that allows you to configure VLANs on a  central switch, which then acts as a server that other switches can synchronize to, so you don't have to configure VLANs on every single switch in the network.

VTP can be seen as a way to automate the network administration where a network engineer can create, modify, or delete a VLAN on a single switch (the VTP Server), since the changes from VTP server switch get's propagated to the all other switches in the same domain. However, VTP was not used a lot due to it's biggest disadvantage of wiping network configuration on entire domain if incorrect VTP Server (switch) was plugged in to the same network domain.

#### How VTP Works?

It sends VTP advertisements (messages) across trunk links to other switches within the same VTP domain. The advertisement contains the VLAN configuration information.


    