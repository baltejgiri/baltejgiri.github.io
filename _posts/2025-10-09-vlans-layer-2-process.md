---
layout: post
title: Virtual Local Area Network
date: 2025-10-09
author: 
#categories: [Networking]
tag: [blog, ccna]
---

Before we dive into concept of virtual local area network (VLAN), let's see what is a local area network and why do we need VLANs.

### Local Area Network (LAN)

A Local Area Network in computer networking consists computers, printers, switches, routers, wireless access points in one location. A LAN basically keeps all network devices in single broadcast domain.

### Broadcast Domain

![Single Broadcast Domain](/assets/img/broadcast_domain.png)
> Image showing single broadcast domain.

A broadcast domain consist of group of network devices, when one network device sends a broadcast frame, all other devices get a copy of the same frame. To separate devices in two or more broadcast domains, VLANs are required.

![Two Broadcast Domains](/assets/img/two_broadcast_domains.png)
> Image showing two broadcast domain.

### VLANs 

A Virtual Local Area Network is a mechanism to segment one broadcast domain into two or more areas. Network switches allows segmentation with VLANs technology.

#### Why do we need VLANs?

- To reduce security risks - Frames are broadcasted to all hosts when switch does not know where is the destination. A malicious user on network can read all frames.
- To segment users and departments into different VLANs, helps reducing the broadcast storm.
- To reduce device resources utilization - In an event of broadcast storm, each device connected on single broadcast domain will continues to receive frames results in heavy CPU uses thus devices performance will degrade or sometimes devices will fail to operate.
- To solve problems quickly - If network loop is present, broadcast storm should only be impacting devices in one VLAN.
- To reduce the workload for spanning tree -  Instead of single spanning tree instance processing work in a large broadcast domain, multiple instance of spanning tree are run on switch, one instance no each VLAN.

#### What is a Trunk?

Trunk carries traffic of multiple VLANs between multiple network switches. Trunk uses the IEEE 802.1Q tag to define each frame to its own VLAN.

![Trunk](/assets/img/Trunk_link_between_two_switches.png)
> Image showing two switches connected via a trunk link.
    
##### How does ethernet frame travels from PC1 to PC2 through a trunk port?
    
 1. PC1 sends a untagged frame (standard ethernet frame) to switch 1. Switch 1 adds 802.1q tag to standard frame with VLAN 10 as it leaves the trunk port Gig0/0.

2. Switch 2 receives the frames as 802.1Q (VLAN 10) as the identifier, removes the 802.1q tag before forwarding the frame to PC2.
