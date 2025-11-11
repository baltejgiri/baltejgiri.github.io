---
layout: post
title: Breaking the Loop: Understanding Spanning Tree Protocol
date: 2025-10-1
author: 
#categories: [Networking]
tag: [blog,ccna,ccnp]
---

# Breaking the Loop: Understanding Spanning Tree Protocol

## What is Spanning Tree Protocol?

**Spanning Tree Protocol (STP)** is a Layer 2 network protocol that ensures a loop-free topology in Ethernet networks. To enhance network resilience, LANs are often designed with redundant links between switches i.e. access layer to distribution layer and core layers. However, these redundant paths can create loops, leading to broadcast storms and degraded performance. STP dynamically identifies and disables redundant paths, allowing only one active path at a time. If the active path fails, STP reactivates a backup path, maintaining connectivity and fault tolerance.

### Historical Context

Spanning tree protocol is an algorithm that was invented by [Radia Perlman](https://en.wikipedia.org/wiki/Radia_Perlman).

An article [An algorithm for distributed computation of a spanning tree in an extended LAN by Radia Perlman.](https://dl.acm.org/doi/pdf/10.1145/318951.319004) was published in 1985. Radia Perlman described the STP in an artistic way.

![image-2](/assets/image-2.png)

**The Spanning Tree Protocol (STP)** was first standardized by the IEEE as part of 802.1D in 1990. Over time, it was improved and eventually replaced by the **Rapid Spanning Tree Protocol (RSTP)**. RSTP was initially released as 802.1w and integrated into the main 802.1D standard in 2004.

## Why We Need Spanning Tree Protocol
STP prevents three common problems in EthernetL LANs.
1. **Broadcast storm:**
        - If you remember LAN switch - a broadcast frame, multicast frame, or unknown unicast frame loops around a LAN indefinitely since network has a loop.
2. **MAC table instability:**
        - Switch puts an entry of learnt MAC address in its table and starts a counter of 300 seconds (by default value) and removes MAC address entry if no other frame arrives from the same source. However, a loop in network floods frames and makes mac address table instable since it can't keep up the with the number of same frames arriving from different source addresses.
3. **Multiple frame transmission:**
        - In a looped network, a unicast frame destined for host C will be received multiple times leading host C to confuse. 

## What Spanning Three Does?

**STP/RSTP** prevents loops by placing each interface on switch in either a forwarding state or a blocking state. Interfaces that are in forwarding state, forwards and receives traffic normally. However, interfaces in blocking state do not process any frames expect STP/RSTP messages.

## How Spanning Tree Protocol Works

- Root bridge/switch is elected, all working interfaces on the root switch in a forwarding state.
- Each nonroot switch calculates the least-cost path between itself and the root switch based on STP/RSTP interface costs. The switch port that begins that least-cost path is its *root port (RP)*, and the cost is the switch's **root cost.**
- In a modern Ethernet that uses switches, each physical link connects two devices only. With two switches on a link, the switch with the lowest root cost becomes the designated switch, and its port connected to that link is the **designated port (DP)** on the link.

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
Switch 1 has the lowest bridge ID, a bridge ID is equal to Priority and MAC address.


### Core Mechanisms
#### Bridge Protocol Data Units (BPDUs)
#### Root Bridge Election
#### Path Cost Calculation

### The STP Algorithm
#### Spanning Tree Computation
#### Port States and Transitions
#### Convergence Process

### Practical Example
#### Network Topology

## Lab Demonstration

### STP Topology Diagram
### Diagram Explanation

#### STP Decision Process
#### Resulting Tree Structure

## Best Practices and Modern Variants

### Rapid Spanning Tree Protocol (RSTP)
### Multiple Spanning Tree Protocol (MSTP)
### Configuration Tips

## Conclusion
