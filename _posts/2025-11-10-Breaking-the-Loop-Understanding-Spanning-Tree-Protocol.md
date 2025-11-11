---
layout: post
title: Breaking the Loop: Understanding Spanning Tree Protocol
date: 2025-11-10
author: 
#categories: [Networking]
tag: [blog,ccna,ccnp]
---

# Breaking the Loop: Understanding Spanning Tree Protocol
---
## What is Spanning Tree Protocol?

**Spanning Tree Protocol (STP)** is a Layer 2 link-management (IEEE 802.1D bridge) protocol, provides path redundancy while preventing undesirable loops in the network.
 
## Historical Context

**The Spanning Tree Protocol (STP)** was first standardized by the IEEE as part of 802.1D in 1990. Over time, it was improved and eventually replaced by the **Rapid Spanning Tree Protocol (RSTP)**. RSTP was initially released as 802.1w and integrated into the main 802.1D standard in 2004. Spanning tree protocol is an algorithm that was invented by [Radia Perlman](https://en.wikipedia.org/wiki/Radia_Perlman).

Radia Perlman published an article [An algorithm for distributed computation of a spanning tree in an extended LAN by Radia Perlman](https://dl.acm.org/doi/pdf/10.1145/318951.319004) in 1985. In the article, Radia Perlman beautifully described the STP in an artistic way.

![image-2](/assets/image-2.png)

## The STP Algorithm

## Spanning Tree Computation

## Why We Need Spanning Tree Protocol?

To enhance network resilience, LANs are often designed with redundant links between switches i.e. access layer to distribution layer and core layers. However, these redundant paths can create loops, leading to broadcast storms and degraded performance. STP dynamically identifies and disables redundant paths, allowing only one active path at a time. If the active path fails, STP reactivates a backup path, maintaining connectivity and fault tolerance.

---

## What Spanning Three Does?

STP computes a tree structure that spans all switches in a subnet or network. Redundant paths are placed in Blocking or Standby state to prevent frame forwarding. The switched network is then in a loop-free condition. However, if forwarding port fails or becomes disconnected, the spanning-tree algorithm recomputes the spanning-tree topology so that the appropriate blocked links can be reactivated.

STP prevents three common problems in EthernetL LANs:

1. **Broadcast storm:**
        - If you remember LAN switch - a broadcast frame, multicast frame, or unknown unicast frame loops around a LAN indefinitely since network has a loop.
2. **MAC table instability:**
        - Switch puts an entry of learnt MAC address in its table and starts a counter of 300 seconds (by default value) and removes MAC address entry if no other frame arrives from the same source. However, a loop in network floods frames and makes mac address table instable since it can't keep up the with the number of same frames arriving from different source addresses.
3. **Multiple frame transmission:**
        - In a looped network, a unicast frame destined for host C will be received multiple times leading host C to confuse. 

---

## How Spanning Tree Protocol Works

STP/RSTP algorithm creates a spanning three of interfaces that forward frames. The tree structure of forwarding interfaces creates a single path to and from each ethernet link. STP/RSTP process called _spanning-tree algorithm_ (STA), selects the interfaces that should be in forwarding or blocking state.

STP/RSTP has three mechanism whether to put an interface in a forwarding state:

- **Root switch** is elected, all working interfaces on the root switch in a forwarding state.

- **Root cost**, each nonroot switch has a cost to reach to the root switch, interfaces in spanning tree topology has cost based of interfaces speed. The switch port that has lowest cost path is called **root port (RP)**.

- Switches has dual physical link to other switches, link with lowest root cost becomes the **designated switch** and it's port connected to the link is the **designated port (DP)** on the link.

---

## Core Mechanisms

STP mechanism works by creating single, logical, loop-free topology across all connected switches. It's a ***three step process of election*** and port designation, using special messages called Bridge Protocol Data Units (BPDUs).

- Election for Root Bridge
- Election for Root Ports
- Election for Designated Ports

### Spanning-Tree Communication: Bridge Protocol Data Units (BPDUs)

STP operation happens when switches communicate with each other. Data messages are exchanged in the form of bridge protocol data units (BPDUs). A switch sends out BPDU frame out a port, using the unique MAC address of the port itself as a source address. The switch is unaware of the other switches around it, so BPDU frames are sent with destination address of the well-known STP multicast address ```01:80:c2:00:00:00```.

There are two types of BPDUs:

- ***Configuration BPDU,*** used for spanning-tree computation.
- ***Topology Change Notification (TCN) BPDU***, used to announce changes in the network topology.

|**Field Description**                   |**Number of Bytes**|
|----------------------------------------|-------------------|
Protocol ID (always 0)                   | 2
Version                                  | 1
Message Type (Configuration or TCN BPDU) | 1
Flags                                    | 1
Root Bride ID                            | 8
Sender Bridge ID                         | 8
Port ID                                  | 2
Message age (in 256ths of a second)      | 2
Maximum age (in 256ths of a second)      | 2
Hello time (in 256ths of a second)       | 2
Forward delays (in 256ths of a second)   | 2

Notice several key fields in the BPDU are related to bridge (switch) identification, path costs, and the timer values. These all work together so that the network of switches can converge on a common spanning-tree topology and select the same reference points within the network.

### Electing a Root Bridge

A common framework is required for all network switches to agree on a loop-free topology. The reference point is called the ***root bridge***. The term bridge was used even before STP was developed, if you see _bridge_, think _switch_.

Each switch has a unique _bridge_ ID that identifies it to other switches. The bridge ID is 8-byte value consisting of the following fields:

- **Bridge Priority (2 bytes):** The Priority field can have a value of 0 to 65,535 and defaults to 32,768 (or 0x8000) on every Catalyst switch.
- **MAC Address (6 bytes):** A mac address address that is hard-coded and unique, can't be changed. The MAC address used by a switch can come from the Supervisor module, the backplane, or a pool of 1024 addresses that are assigned to every supervisor or backplane, depending on the switch model.

Second part of bridge ID is MAC address, since switchports has their own MAC addresses. MAC addresses referred in the Bridge ID is often the switch's base ethernet address.

Following switch output is from a physical switch, emulated switch operating systems does not have base ethernet address. Bridge ID uses the base ethernet MAC address.

```plaintext
physicalswitch#show version | i Base ethernet      
Base ethernet MAC Address       : 00:6C:BC:5C:BD:80
```

To further explain the topic, Bride priority can be different based of VLAN number, for example, in the following physical switch output from ```show spanning-tree bridge``` command it list the Bridge ID's for each VLAN but only uses the same MAC address on all VLANs since there is only one Base MAC address on a particular switch.

```planetext
physicalswitch#show spanning-tree bridge 

                                                   Hello  Max  Fwd
Vlan                         Bridge ID              Time  Age  Dly  Protocol
---------------- --------------------------------- -----  ---  ---  --------
VLAN0001         32769 (32768,   1) 006c.bc5c.bd80    2    20   15  rstp        
VLAN0099         32867 (32768,  99) 006c.bc5c.bd80    2    20   15  rstp        
VLAN0100         32868 (32768, 100) 006c.bc5c.bd80    2    20   15  rstp        
VLAN0105         32873 (32768, 105) 006c.bc5c.bd80    2    20   15  rstp        
VLAN0110         32878 (32768, 110) 006c.bc5c.bd80    2    20   15  rstp 
```

Output shows VLAN 1 has bridge priority of 32768 + VLAN number of 1, bridge id becomes 32769 whereas VLAN 99 shows the bridge priority of 32768 + VLAN number of 99, bridge id becomes 32867.

**The election of a root bridge**, Switches boots, assumes itself as a root bridge but as soon as other switches are powered on election process of a root bridge starts by _sending out BPDUs with a root bridge ID equal to its own bridge ID and a senders bridge ID that it its owns bridge ID._

![alt text](image-2.png)

Senders bridge ID (local switch's bridge ID) simply tells other switches who is the actual sender of the BPDU message. After a root bridge is decided on, configuration BPDUs are sent only by the root bridge. All other bridges must forward or relay the BPDUs, adding their own sender bridge IDs to the message.

BPDU's messages are analyzed to see if a "better" root bridge is being announced. _A root bridge is considered better if the root bridge ID value is lower than another._ If _two bridge priority values are equal_, the _lower MAC address makes the bridge ID better_.

The root bridge election converges and all switches agree one of them is the root bridge. However, if a new switch is connected to the network with lower bridge ID or bridge ID is equal to current switches but the mac address is lower, all the switches will reconsider and elects the new switch as root bridge.

_Root bridge election is an ongoing process, triggered by root bridge ID changes in the BPDUs every 2 seconds (by default)._

### Electing Root Ports

Once the *Root Bridge* is elected, every other switch *(Non-Root Switch)* must select a single *Root Port (RP)*. The RP is the port that offers the best path back to the Root Bridge and is always put into the Forwarding state.

The "best path" is determined by comparing costs:

- **Root Path Cost:** This is the cumulative cost to reach the Root Bridge. This value is carried inside the BPDU messages and is updated by each switch along the path.

- **Path Cost:** This is the local cost of a specific link or port (e.g., Fast Ethernet link cost is 19). This value is NOT in the BPDU. The receiving switch adds its incoming port's local Path Cost to the received Root Path Cost to calculate its new total cost.

The original IEEE 802.1D formula is:

$$\text{Original STP Cost} = \frac{1000 \text{ Mbps}}{\text{Link Bandwidth (in Mbps)}}$$

For links that are 10Gbps and more are rounded down to 1 (same as 1Gbps)

#### STP Port Cost Table

| Link Bandwidth | Old STP Cost (802.1D) | New STP Cost (IEEE) |
| :--- | :--- | :--- |
| 4 Mbps | 250 | 250 |
| 10 Mbps | 100 | 100 |
| 16 Mbps | 63 | 62 |
| 45 Mbps | 22 | 39 |
| **100 Mbps (Fast Ethernet)** | 10 | **19** |
| 155 Mbps | 6 | 14 |
| 622 Mbps | 2 | 6 |
| **1 Gbps (Gigabit Ethernet)** | 1 | **4** |
| **10 Gbps** | 0 | **2** |

### Electing Designated Ports

To remove the possibility of bridging loops, STP makes a final computation to identify one *designated port* on each network switch. _Switches choose a designated port based on the lowest cumulative root path cost to the root bridge._

For example, a switch always has an idea of it's own root path cost, which it announces in its own BPDUs. If a neighboring switch on a shared LAN segment sends a BPDU announcing a lower root path cost, the neighbor must have the designated port. If a switch learns only of higher root path cost from other BPDUs received on a port, however, it then correctly assumes that its own receiving port is the designated port for the segment. 

STP has a set of progressive states that each port must go through, regardless of the type or identification. These states actively prevents loops from forming and are described in the next section.

In each determination process discussed so far, two or more links might have identical root path costs. This results in a tie condition, unless other factors are considered. Al tie breaker STP decisions are based on the following sequence of four conditions:

1. Lowest root bridge ID
2. Lowest root path cost to the root bridge.
3. Lowest sender bridge ID
4. Lowest sender port ID

![alt text](image-5.png)

Explanation of Election process of Root bridge, root port and designated port.

**Switch A (Root Bridge)**
When Switch A communicates with Switch B, it sends BPDUs out gi1/0/1 advertising a root path cost of 0. Switch B responds with BPDUs advertising a root path cost of 4. Since Switch A has the lower root path cost, gi1/0/1 becomes a designated port. All ports on the root bridge are designated ports by definition.

When Switch A communicates with Switch C, it sends BPDUs out gi1/0/2 advertising a root path cost of 0. Switch C responds with BPDUs advertising a root path cost of 4. Switch A gi1/0/2 becomes a designated port due to its superior root path cost of 0.

**Switch B**
When Switch B communicates with Switch A, it receives BPDUs advertising a root path cost of 0 on gi1/0/1. Switch B calculates its own root path cost as 4 by adding the incoming link cost. Since Switch A offers the best path to the root bridge, Switch B gi1/0/1 becomes the root port.

When Switch B communicates with Switch C across the B-C link, both switches advertise a root path cost of 8 on their respective gi1/0/2 ports. With equal root path costs, the tiebreaker becomes the bridge ID comparison. Switch B has the lower bridge ID (32768:0000.0000.000b), so its gi1/0/2 port becomes the designated port for this connection.

**Switch C**
When Switch C communicates with Switch A, it receives BPDUs advertising a root path cost of 0 on gi1/0/1. Switch C calculates its own root path cost as 4 by adding the incoming link cost. Since Switch A offers the best path to the root bridge, Switch C gi1/0/1 becomes the root port.

When Switch C communicates with Switch B across the B-C link, both switches advertise a root path cost of 8. However, Switch C has the higher bridge ID (32768:0000.0000.000c), causing it to lose the designated port election. Switch C gi1/0/2 becomes neither a root port nor a designated port, forcing it into the blocking state to prevent loops.

---

## STP States
### STP Timers
### Topology Changes
#### Indirect Topology Changes
#### Insignificant Topology Changes

## Types of STP

### Common Spanning Tree

### Per-VLAN Spanning Tree

### Per-VLAN Spanning Tree Plus



#### Convergence Process

### Practical Example
#### Network Topology

## Lab Demonstration

Following command output shows spanning tree protocol.

```plaintext
SW1#show spanning-tree 

VLAN0001
  Spanning tree enabled protocol ieee
  Root ID    Priority    32769
             Address     5000.0001.0000
             This bridge is the root
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    32769  (priority 32768 sys-id-ext 1)
             Address     5000.0001.0000
             Hello Time   2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  15  sec

Interface           Role Sts Cost      Prio.Nbr Type
------------------- ---- --- --------- -------- --------------------------------
Gi0/0               Desg FWD 4         128.1    P2p 
Gi0/1               Desg FWD 4         128.2    P2p 
```
Switch 1 has the lowest bridge ID 32769, a bridge ID is equal to Priority 32769 (a default value on cisco switches and system-id.

### STP Topology Diagram
### Diagram Explanation

#### STP Decision Process
#### Resulting Tree Structure

## Best Practices and Modern Variants

### Rapid Spanning Tree Protocol (RSTP)
### Multiple Spanning Tree Protocol (MSTP)
### Configuration Tips

## Conclusion
