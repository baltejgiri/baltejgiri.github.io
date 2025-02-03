---
layout: post
title: Dynamic Routing with OSPF
date: 02-02-2025
author:
categories: [Networking]
tag: [blog, ccna,ccnp,routing]
---
Welcome to my another blogpost, this document will cover routing protocol OSPF (Open Shortest Path First). But before we dive into OSPF specifics, we needs to understand routing and types of routing. A simple OSPF lab will be shown later covering a backbone and non-backbone areas.

## Routing

Routing is the process of selecting and defining paths for data to travel from one network to another. It happens in __Layer 3 (Network Layer)__ of the __OSI model__ and is essential for communication between networks, such as the internet.

## Routing is two types.

- __Static Routing:__ A network routing where routes are manually configured instead of being learned automatically. These routes does not change unless manually updated, making them simple but less flexible. If the routing table remains the same, a static route would be a best choice to direct traffic.
 - __Dynamic Routing:__ A network mechanism where routers automatically exchange information to determine the best path for data packets to reach their destination. Dynamic routing protocols are required when routing table needs automatic updating based on a number of particular factors.

 The following diagram helps to understand the hierarchical view of dynamic routing protocol.

![Hierarchical view of dynamic routing protocol](/assets/2025-02-02-hierarchical_view_of_routing_protocol.png)

## Classifying Routing Protocols
Routing protocols can be classified into different groups according to their characteristics.

- __Purpose:__ Interior Gateway Protocol (IGP) or Exterior Gateway Protocol (EGP)
- __Operation:__ Distance vector protocol, link-state protocol, or path-vector protocol
- __Behavior:__ Classful (legacy) or classless protocol

<mark>OSPF is classified as IGP, link-state, classless protocol.</mark>

__Note:__ Link-state routing protocols are classless by nature.

## Open Shortest Path First

OSPF is a routing protocol used in computer networks to help routers find the best path for sending data. It is mainly used in large enterprise networks. It uses a mathematical algorithm [Dijkstra](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm) to calculate the most efficient path to direct traffic on IP networks.

In an OSPF networks, routers within the same area maintain an identical link-state database that describes the map of the area. Each router in the area builds its link-state database from the link-state advertisements (LSAs) that it receives from all other routers in the same area and the LSA's that itself generates. LSAs contain essential information about neighbor relationships and path costs. Using this database, each router applies the SPF algorithm to compute a shortest-path spanning tree, treating itself as the root, to determine the most efficient routes for forwarding traffic.


OSPF offers several key advantages:

 - Unlike distance-vector protocols like Routing Information Protocol (RIP), OSPF is better suited for large and diverse networks, providing scalability and efficiency. It can quickly recalculate routes in response to changes in network topology, ensuring fast convergence.

- Additionally, OSPF allows an Autonomous System (AS) to be segmented into multiple areas, keeping each area's topology independent. This reduces routing traffic and minimizes the size of the link-state database, improving network performance.

- Another benefit of OSPF is equal-cost multipath (ECMP) routing, which enables multiple paths with the same cost to be used simultaneously. This allows for load balancing by configuring multiple routes with different next-hop addresses in the TCP stack.

## OSPF Route Design
In the following design, five Cisco routers, two OSPF area's are used in this lab to demonstrate the simple OSPF configuration.

![OSPF Route Configuration Design](/assets/2025-02-02-OSPF_lab_design.png)

## OSPF Route Configuration
### Router 1
```py
!
enable
configure terminal
!
hostname r1
interface gig0/0
ip address 10.1.1.1 255.255.255.0
no shutdown
!
exit
!
interface loopback 1
ip address 1.1.1.1 255.255.255.255
!
exit
!
line console 0
logging synchronous
!
exit
!
```

```py 
configure terminal
!
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
 network 11.1.1.0 0.0.0.255 area 0
 network 12.1.1.0 0.0.0.255 area 10
 network 13.1.1.0 0.0.0.255 area 10
!
end
!
copy running-config startup-config
```

### Router 2
```py
enable
configure terminal
!
hostname r2
interface gig0/0
ip address 10.1.1.2 255.255.255.0
no shutdown
!
interface gig0/1
ip address 11.1.1.1 255.255.255.0
no shutdown
!
interface loopback 2
ip address 2.2.2.2 255.255.255.255
no shutdown
!
exit
!
line console 0
logging synchronous
!
exit
```

```py
configure terminal
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
 network 11.1.1.0 0.0.0.255 area 0
 network 12.1.1.0 0.0.0.255 area 10
 network 13.1.1.0 0.0.0.255 area 10
!
end
!
!
copy running-config startup-config
```

### Router 3
```py 
enable
configure terminal
!
hostname r3
interface gig0/0
ip address 11.1.1.2 255.255.255.0
no shutdown
!
interface gig0/1
ip address 12.1.1.1 255.255.255.0
no shutdown
!
interface loopback 3
ip address 3.3.3.3 255.255.255.255
no shutdown
!
exit
!
line console 0
logging synchronous
!
exit
!
```

```py 
configure terminal
!
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
 network 11.1.1.0 0.0.0.255 area 0
 network 12.1.1.0 0.0.0.255 area 10
 network 13.1.1.0 0.0.0.255 area 10
!
end
!
copy running-config startup-config
```

### Router 4
```py
enable
configure terminal
!
hostname r4
interface gig0/0
ip address 12.1.1.2 255.255.255.0
no shutdown
!
interface gig0/1
ip address 13.1.1.1 255.255.255.0
no shutdown
!
interface loopback 4
ip address 4.4.4.4 255.255.255.255
no shutdown
!
exit
!
line console 0
logging synchronous
!
exit
!
```

```py
configure terminal
!
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
 network 11.1.1.0 0.0.0.255 area 0
 network 12.1.1.0 0.0.0.255 area 10
 network 13.1.1.0 0.0.0.255 area 10
end
!
copy running-config startup-config
```

### Router 5
```py
enable
configure terminal
!
hostname r5
interface gig0/0
ip address 13.1.1.2 255.255.255.0
no shutdown
!
interface loopback 5
ip address 5.5.5.5 255.255.255.0
no shutdown
!
exit
!
line console 0
logging synchronous
!
exit
!
```

```py 
configure terminal
!
router ospf 1
 network 10.1.1.0 0.0.0.255 area 0
 network 11.1.1.0 0.0.0.255 area 0
 network 12.1.1.0 0.0.0.255 area 10
 network 13.1.1.0 0.0.0.255 area 10
!
end
!
copy running-config startup-config
```

## OSPF Route Verification

All five router's OSPF route.
```
Router 1
r1#show ip route ospf
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area 
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2
       i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
       ia - IS-IS inter area, * - candidate default, U - per-user static route
       o - ODR, P - periodic downloaded static route, H - NHRP, l - LISP
       a - application route
       + - replicated route, % - next hop override, p - overrides from PfR

Gateway of last resort is not set

      11.0.0.0/24 is subnetted, 1 subnets
O        11.1.1.0 [110/2] via 10.1.1.2, 03:13:50, GigabitEthernet0/0
      12.0.0.0/24 is subnetted, 1 subnets
O IA     12.1.1.0 [110/3] via 10.1.1.2, 03:13:50, GigabitEthernet0/0
      13.0.0.0/24 is subnetted, 1 subnets
O IA     13.1.1.0 [110/4] via 10.1.1.2, 03:13:50, GigabitEthernet0/0
!
!
Router 2
!lines of code is emitted.
      12.0.0.0/24 is subnetted, 1 subnets
O IA     12.1.1.0 [110/2] via 11.1.1.2, 03:25:40, GigabitEthernet0/1
      13.0.0.0/24 is subnetted, 1 subnets
O IA     13.1.1.0 [110/3] via 11.1.1.2, 03:25:30, GigabitEthernet0/1
!
Router 3
!lines of code is emitted
      10.0.0.0/24 is subnetted, 1 subnets
O        10.1.1.0 [110/2] via 11.1.1.1, 03:26:52, GigabitEthernet0/0
      13.0.0.0/24 is subnetted, 1 subnets
O        13.1.1.0 [110/2] via 12.1.1.2, 03:26:52, GigabitEthernet0/1
!
Router 4
!lines of code is emitted
      10.0.0.0/24 is subnetted, 1 subnets
O IA     10.1.1.0 [110/3] via 12.1.1.1, 03:27:26, GigabitEthernet0/0
      11.0.0.0/24 is subnetted, 1 subnets
O IA     11.1.1.0 [110/2] via 12.1.1.1, 03:27:26, GigabitEthernet0/0
!
Router 5
!lines of code is emitted
      10.0.0.0/24 is subnetted, 1 subnets
O IA     10.1.1.0 [110/4] via 13.1.1.1, 03:27:46, GigabitEthernet0/0
      11.0.0.0/24 is subnetted, 1 subnets
O IA     11.1.1.0 [110/3] via 13.1.1.1, 03:27:46, GigabitEthernet0/0
      12.0.0.0/24 is subnetted, 1 subnets
O        12.1.1.0 [110/2] via 13.1.1.1, 03:29:34, GigabitEthernet0/0
```

Router 1 can reach to the Router 5, similarly Router 5 can reach Router 1
```py
r1#ping 13.1.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 13.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 3/4/5 ms
r1#traceroute 13.1.1.1
Type escape sequence to abort.
Tracing the route to 13.1.1.1
VRF info: (vrf in name/id, vrf out name/id)
  1 10.1.1.2 2 msec 2 msec 2 msec
  2 11.1.1.2 3 msec 3 msec 3 msec
  3 12.1.1.2 4 msec 3 msec * 
!
!
!
r5#ping 10.1.1.1
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.1.1.1, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/4/5 ms
r5#traceroute 10.1.1.1
Type escape sequence to abort.
Tracing the route to 10.1.1.1
VRF info: (vrf in name/id, vrf out name/id)
  1 13.1.1.1 2 msec 2 msec 2 msec
  2 12.1.1.1 3 msec 2 msec 2 msec
  3 11.1.1.1 4 msec 4 msec 4 msec
  4 10.1.1.1 6 msec 5 msec * 
  ```
Router 3 is the Area Border Router, it has learned the routes from area 0 and area 10.

```py
r3#show ip ospf database 

            OSPF Router with ID (3.3.3.3) (Process ID 1)

                Router Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum Link count
1.1.1.1         1.1.1.1         452         0x80000007 0x009475 1
2.2.2.2         2.2.2.2         315         0x8000000C 0x0019B6 2
3.3.3.3         3.3.3.3         919         0x8000000A 0x000FE4 1

                Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.2        2.2.2.2         315         0x80000007 0x0020F1
11.1.1.1        2.2.2.2         827         0x80000009 0x007D8A

                Summary Net Link States (Area 0)

Link ID         ADV Router      Age         Seq#       Checksum
12.1.1.0        3.3.3.3         919         0x80000007 0x0079A2
13.1.1.0        3.3.3.3         919         0x80000007 0x0076A3

                Router Link States (Area 10)
          
Link ID         ADV Router      Age         Seq#       Checksum Link count
3.3.3.3         3.3.3.3         919         0x80000008 0x0021D2 1
4.4.4.4         4.4.4.4         1050        0x80000009 0x0009B0 2
5.5.5.5         5.5.5.5         1233        0x80000008 0x00A53C 1

                Net Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum
12.1.1.2        4.4.4.4         1050        0x80000007 0x007285
13.1.1.2        5.5.5.5         1233        0x80000007 0x009B4F

                Summary Net Link States (Area 10)

Link ID         ADV Router      Age         Seq#       Checksum
10.1.1.0        3.3.3.3         919         0x80000007 0x009D7F
11.1.1.0        3.3.3.3         919         0x80000007 0x008696
```
## Summary

This OSPF configuration helps with setting up dynamic routing on the network. The advance OSPF route configuration helps with fine-tune routing such as route summarization for reduced Link-state database size and more.

A detailed blogpost will be published later date including Link-state database, OSPF packet types, OSPF route types, path selection, load balancing, route redistribution and more.

Thank you for reading the blogpost.









