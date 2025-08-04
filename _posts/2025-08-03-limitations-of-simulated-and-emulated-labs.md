---
layout: post
title: Limitations of Simulated and Emulated Labs
date: 2025-08-03
author: 
categories: [Networking]
tag: [blog, ccna]
---
# Limitations of Simulated and Emulated Labs

Following lab and output of switch’s CLI (command line interface) shows two multilayer switches in Cisco packet tracer version 8.2.2 does not have command/functionality to change duplex settings. However, sub-command speed can be used to set 10, 100, 1000 or auto.

## WS-3650-24PS Series switches

A simulation of Cisco Catalyst 3650, 24 port switch. It runs on “CAT3K_CAA-UNIVERSALK9” image using software version 16.3.2

![Packet Tracer C3650 Switch Lab ](/assets/2025-08-03-limitations-of-simulated-and-emulated-labs_3650_switch_lab.png)

```bash
3650-SW-1#show int gig1/0/1
GigabitEthernet1/0/1 is up, line protocol is up (connected)
Hardware is Lance, address is 0010.1128.5d01 (bia 0010.1128.5d01)
MTU 1500 bytes, BW 1000000 Kbit, DLY 1000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
Keepalive set (10 sec)
Full-duplex, 1000Mb/s
input flow-control is off, output flow-control is off
ARP type: ARPA, ARP Timeout 04:00:00
Last input 00:00:08, output 00:00:05, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue :0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
956 packets input, 193351 bytes, 0 no buffer
Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
0 watchdog, 0 multicast, 0 pause input
0 input packets with dribble condition detected
2357 packets output, 263570 bytes, 0 underruns
0 output errors, 0 collisions, 10 interface resets
0 babbles, 0 late collision, 0 deferred
0 lost carrier, 0 no carrier
0 output buffer failures, 0 output buffers swapped out
```

```bash
3650-SW-1#show int gig1/0/1 status
Port 	Name 		Status 		Vlan 	Duplex 		Speed 		Type
Gig1/0/1 		    connected 	1 	auto 		auto 		10/100BaseTX
```

```bash
3650-SW-1(config)#int gig1/0/1
3650-SW-1(config-if)#shutdown 

3650-SW-1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet1/0/1, changed state to administratively down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet1/0/1, changed state to down

3650-SW-1(config-if)#speed 100

3650-SW-1(config-if)#?
arp 		Set arp type (arpa, probe, snap) or timeout
bandwidth 	Set bandwidth informational parameter
cdp 		Global CDP configuration subcommands
channel-group 	Etherchannel/port bundling configuration
channel-protocol 	Select the channel protocol (LACP, PAgP)
delay 		Specify interface throughput delay
description 	Interface specific description
exit 		Exit from interface configuration mode
hold-queue 	Set hold queue depth
ip Interface 	Internet Protocol config commands
ipv6 		IPv6 interface subcommands
mdix 		Set Media Dependent Interface with Crossover
no 		Negate a command or set its defaults
power 		Power configuration
rep 		Resilient Ethernet Protocol characteristics
service-policy 	Configure QoS Service Policy
shutdown 	Shutdown the selected interface
spanning-tree 	Spanning Tree Subsystem
speed 		Configure speed operation.
storm-control 	storm configuration
switchport 	Set switching mode characteristics
tx-ring-limit 	Configure PA level transmit ring limit
3650-SW-1(config-if)#no shutdown

3650-SW-1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet1/0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet1/0/1, changed state to up

3650-SW-1(config-if)#end
3650-SW-1#
%SYS-5-CONFIG_I: Configured from console by console
```
The example above shows that, when using the question mark for help, the available syntax only shows speed, but not duplex.

After setting the interface speed to 100 Mbps, the output reflects this:

```bash
3650-SW-1#show int gig1/0/1
GigabitEthernet1/0/1 is up, line protocol is up (connected)
Hardware is Lance, address is 0010.1128.5d01 (bia 0010.1128.5d01)
MTU 1500 bytes, BW 100000 Kbit, DLY 1000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
Keepalive set (10 sec)
Full-duplex, 100Mb/s
input flow-control is off, output flow-control is off
ARP type: ARPA, ARP Timeout 04:00:00
Last input 00:00:08, output 00:00:05, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue :0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
956 packets input, 193351 bytes, 0 no buffer
Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
0 watchdog, 0 multicast, 0 pause input
0 input packets with dribble condition detected
2357 packets output, 263570 bytes, 0 underruns
```

```bash
3650-SW-1#show int gig1/0/1 status
Port 		Name 	Status 		Vlan 	Duplex		Speed 		Type
Gig1/0/1 		connected 	1 	auto 		a-100 		10/100BaseTX
```

## WS-3560-24PS Series switches

This is another simulated switch, the Cisco Catalyst 3560 24-port model. It runs the “C3560-ADVIPSERVICESK” image with software version 12.2.

![Packet Tracer C3650 Switch Lab ](/assets/2025-08-03-limitations-of-simulated-and-emulated-labs_3560_switch_lab.png)

Before setting the links speed to 100Mb/s it shows the speeds of link between Switch 1 and Switch 2 is 1000Mb/s.

```bash
3560-SW-1#show int gig0/1
GigabitEthernet0/1 is up, line protocol is up (connected)
Hardware is Lance, address is 0001.4258.a019 (bia 0001.4258.a019)
MTU 1500 bytes, BW 1000000 Kbit, DLY 1000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
Keepalive set (10 sec)
Full-duplex, 1000Mb/s
input flow-control is off, output flow-control is off
ARP type: ARPA, ARP Timeout 04:00:00
Last input 00:00:08, output 00:00:05, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue :0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
956 packets input, 193351 bytes, 0 no buffer
Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
0 watchdog, 0 multicast, 0 pause input
0 input packets with dribble condition detected
2357 packets output, 263570 bytes, 0 underruns
0 output errors, 0 collisions, 10 interface resets
0 babbles, 0 late collision, 0 deferred
0 lost carrier, 0 no carrier
0 output buffer failures, 0 output buffers swapped out
```

```bash
3560-SW-1#show int gig0/1 status
Port 		Name 	Status 		Vlan 	Duplex 		Speed 		Type
Gig0/1 			connected 	1	 auto 		auto 		10/100BaseTX
```

The output showing in “show int gig0/1” command shows link is Full-Duplex and running at 1000Mb/s before it is set to 100Mb/s on Switch 1.

```bash
3560-SW-1(config)#int gig0/1
3560-SW-1(config-if)#shutdown

3560-SW-1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to administratively down

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to down

3560-SW-1(config-if)#speed 100
3560-SW-1(config-if)#no shut

3560-SW-1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up

3560-SW-1(config-if)#end
3560-SW-1#
%SYS-5-CONFIG_I: Configured from console by console
```

```bash
3560-SW-1#show int gig0/1
GigabitEthernet0/1 is up, line protocol is up (connected)
Hardware is Lance, address is 0001.4258.a019 (bia 0001.4258.a019)
MTU 1500 bytes, BW 100000 Kbit, DLY 1000 usec,
reliability 255/255, txload 1/255, rxload 1/255
Encapsulation ARPA, loopback not set
Keepalive set (10 sec)
Full-duplex, 100Mb/s
input flow-control is off, output flow-control is off
ARP type: ARPA, ARP Timeout 04:00:00
Last input 00:00:08, output 00:00:05, output hang never
Last clearing of "show interface" counters never
Input queue: 0/75/0/0 (size/max/drops/flushes); Total output drops: 0
Queueing strategy: fifo
Output queue :0/40 (size/max)
5 minute input rate 0 bits/sec, 0 packets/sec
5 minute output rate 0 bits/sec, 0 packets/sec
956 packets input, 193351 bytes, 0 no buffer
Received 956 broadcasts, 0 runts, 0 giants, 0 throttles
0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
0 watchdog, 0 multicast, 0 pause input
0 input packets with dribble condition detected
2357 packets output, 263570 bytes, 0 underruns
0 output errors, 0 collisions, 10 interface resets
0 babbles, 0 late collision, 0 deferred
0 lost carrier, 0 no carrier
0 output buffer failures, 0 output buffers swapped out
```

```bash
3560-SW-1#show int gig0/1 status
Port 	Name 		Status 		Vlan 		Duplex 	Speed 	Type
Gig0/1 		connected 	1 		auto 	a-100 	10/100BaseTX
```

## Summary

The output above demonstrates that in Cisco Packet Tracer:

- The Catalyst 3650 running IOS version 16.3.2, and the 3560 running version 12.2, do not support the duplex command in interface configuration mode.

- Only the speed command is available to manually set interface speeds.

To fully understand and test duplex and speed configurations, physical switches are recommended.

Additionally, in Cisco Modeling Labs (CML), the duplex command is available, but the speed command does not always function as expected. CML uses a Linux-based emulation image running IOS version 17.15.1.
