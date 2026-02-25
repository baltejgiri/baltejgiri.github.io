---
layout: post
title: "Domain Name System - It's a DNS issue"
date: 2026-02-25
author: 
categories: [Networking]
tag: [blog,ccna,ccnp]
---

# The Purpose of DNS

Domain Name System (DNS) translates human-readable domain names into computer friendly IP addresses. Domain names are much easier for humans to use and remembers versus IP addresses. For example, when we browse to cisco.com, computer asks DNS server for the IP address of cisco.com. Devices can learn DNS server address either by DHCP servers or manually configured.

The following command ```ipconfig``` shows the DNS address learned on the PC.

```bash
PS C:\Users> ipconfig /all

Windows IP Configuration

!lines are omitted

Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . :
   Description . . . . . . . . . . . : Realtek USB GbE Family Controller
   Physical Address. . . . . . . . . : 7C-4D-8F-55-00-CC
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   IPv4 Address. . . . . . . . . . . : 192.168.1.54(Preferred)
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Lease Obtained. . . . . . . . . . : February 24, 2026 14:03:53
   Lease Expires . . . . . . . . . . : March 4, 2026 14:03:52
   Default Gateway . . . . . . . . . : 192.168.1.1
   DHCP Server . . . . . . . . . . . : 192.168.1.1
   DNS Servers . . . . . . . . . . . : 8.8.8.8
   NetBIOS over Tcpip. . . . . . . . : Enabled

!lines are omitted
```
The following two commands on the Windows PC confirms IP address of cisco.com. An IP address of domain name can be viewed by network tools ```nslookup``` as well as ```ping```.

```bash
PS C:\Users> nslookup cisco.com
Server:  dns.google
Address:  8.8.8.8

Non-authoritative answer:
Name:    cisco.com
Addresses:  2001:420:1101:1::185
          72.163.4.185

PS C:\Users> ping cisco.com

Pinging cisco.com [72.163.4.185] with 32 bytes of data:
Reply from 72.163.4.185: bytes=32 time=68ms TTL=50
Reply from 72.163.4.185: bytes=32 time=67ms TTL=50
Reply from 72.163.4.185: bytes=32 time=68ms TTL=50
Reply from 72.163.4.185: bytes=32 time=67ms TTL=50

Ping statistics for 72.163.4.185:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 67ms, Maximum = 68ms, Average = 67ms
```


