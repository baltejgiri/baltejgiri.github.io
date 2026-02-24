---
layout: post
title: "Time Protocol - Let's Sync it to Reference Clock"
date: 2026-02-24
author: 
categories: [Networking]
tag: [blog,ccna,ccnp]
---

## Overview
Network Time Protocol (NTP) is to synchronize network device clock to a reference clock. A reference clock provides accurate time similar to an atomic clock or a GPS clock. Reference clocks are stratum 0 within the NTP hierarchy.

Network Engineers review device logs to troubleshoot an issue in the network. For example, a user reports their network was inconsistency at 13:10 on December 26, 2025. Device logs are reviewed to corelate the timestamps to confirm if a device had network events.

## Device Clock

All devices (routers, switches, PC and so on) have an internal clock. In Cisco IOS, device time can be viewed by ```show clock``` command.

```bash
R1#show clock
*00:16:00.857 UTC Sat Dec 26 2025
```
The default time zone is UTC (Coordinated Universal Time). The ```show clock detail``` command revels the time source.

```bash
R1#show clock detail
*00:16:00.857 UTC Sat Dec 26 2025
Time source is hardware calendar
```
The asterisk means, time is not considered authoritative. Next line, **Time source is hardware calendar** means the hardware calendar is the default time source. The internal clock of a device is unreliable source time.

## Show logging command

Following logs demonstrate ```Interface Ethernet0/0``` administratively going down then brought up again within 14 seconds. Logs shows here are called **Syslog** each section from date, time, sequence, utility, description and more are defined in it's own section.

```bash
rtr01#show logging        
Jan  1 00:05:04.249: %LINK-5-CHANGED: Interface Ethernet0/0, changed state to administratively down
Jan  1 00:05:05.249: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to down
Jan  1 00:05:06.685: %DHCPD-7-NO_LEASE: DHCP lease assignment failure, client 5254.00d8.d5e9 reason NO POOL
Jan  1 00:05:17.836: %LINK-3-UPDOWN: Interface Ethernet0/0, changed state to up
Jan  1 00:05:18.837: %LINEPROTO-5-UPDOWN: Line protocol on Interface Ethernet0/0, changed state to up
Jan  1 00:05:20.053: %SYS-5-CONFIG_I: Configured from console by console
Jan  1 00:05:22.861: %DHCP-6-ADDRESS_ASSIGN: Interface Ethernet0/0 assigned DHCP address 192.168.255.44, mask 255.255.255.0, hostname rtr01
```

```bash
rtr01#show clock
00:08:28.925 UTC Fri Jan 1 1993
!
rtr01#show clock detail 
00:12:40.862 UTC Fri Jan 1 1993
Time source is user configuration
```

The ```show clock detail``` confirms the displayed clock is hardware calendar.

## Manual Time configuration

Devices can be manually configured using ```clock set``` command the using question mark to view the next parameters required.

```bash
rtr01#clock set ?
  hh:mm:ss  Current Time

rtr01#clock set 21:49:00 ?
  <1-31>  Day of the month
  MONTH   Month of the year

rtr01#clock set 21:49:00 23 ?
  MONTH  Month of the year

rtr01#clock set 21:49:00 23 feb ?
  <1993-2035>  Year

rtr01#clock set 21:49:00 23 feb 2026
rtr01#
Feb 23 21:49:00.000: %SYS-6-CLOCKUPDATE: System clock has been updated from 00:22:37 UTC Fri Jan 1 1993 to 21:49:00 UTC Mon Feb 23 2026, configured from console by console.
rtr01#
rtr01#show clock detail 
21:49:08.729 UTC Mon Feb 23 2026
Time source is user configuration
rtr01#
```

Hardware calendar also knowns are built-in clock is the default time-source, the hardware clock and software clock are separate and can be configured manually.


## Hardware Clock (Calendar) Configuration
Hardware clock can also be manually configured with the ```calendar set``` command.

```bash
rtr01#calendar ?
  set  Set the time and date

rtr01#calendar set ?
  hh:mm:ss  Current Time

rtr01#calendar set 22:00:00 ?
  <1-31>  Day of the month
  MONTH   Month of the year

rtr01#calendar set 22:00:00 23 ?
  MONTH  Month of the year

rtr01#calendar set 22:00:00 23 feb ?
  <1993-2035>  Year

rtr01#calendar set 22:00:00 23 feb 2026 ?
  <cr>  <cr>

rtr01#calendar set 22:00:00 23 feb 2026 
rtr01#
```

Hardware and software clocks are set, however they are out of sync.

```bash
rtr01#show calendar 
06:02:47 UTC Tue Feb 24 2026
!
rtr01#show clock 
22:05:50.734 UTC Mon Feb 23 2026
```
Hardware and software clocks needs to be synchronized with each other. Use the following commands to sync the calendar to clock's time and clock to the calendar's time.

In our example above, hardware calendar shows the time in future whereas the software clock shows the accurate time.

We will sync the clock to the calendars time.

```bash
Show a way how to sync the hardware clock to the software clock here including the commands.
```

## Configuring the Time Zone

```bash
rtr01(config)#
rtr01(config)#do show clock 
14:15:24.815 pst Mon Feb 23 2026
rtr01(config)#
rtr01(config)#clock timezone ?      
  WORD  name of time zone

rtr01(config)#clock timezone UTC ?
  <-23 - 23>  Hours offset from UTC

rtr01(config)#clock timezone UTC -8 ?
  <0-59>  Minutes offset from UTC
  <cr>    <cr>

rtr01(config)#clock timezone UTC -8 
rtr01(config)#
Feb 23 22:16:00.827: %SYS-6-CLOCKUPDATE: System clock has been updated from 14:16:00 pst Mon Feb 23 2026 to 14:16:00 UTC Mon Feb 23 2026, configured from console by console.

rtr01(config)#do show clock 
14:16:12.067 UTC Mon Feb 23 2026
rtr01(config)#exit

rtr01#clock set 22:17:20 23 feb 2026
rtr01#
Feb 24 06:17:20.000: %SYS-6-CLOCKUPDATE: System clock has been updated from 14:17:19 UTC Mon Feb 23 2026 to 22:17:20 UTC Mon Feb 23 2026, configured from console by console.
rtr01#show clock 
22:17:26.620 UTC Mon Feb 23 2026
rtr01#show calendar
22:17:34 UTC Mon Feb 23 2026
rtr01#
```

## Daylight Saving Time (Summer Time)

Network device clock needs to be configured to sync with daylight saving time. The following command from ```2 Sunday March 02:00``` defines the start of DST and ```1 Sunday November 02:00``` defines the end of DST.

Number 2 in start of DST define the summer time starts 2nd monday of march, similarly number 1 refers to DST ends on first sunday of november.

```bash
rtr(config)#clock summer-time pst recurring 2 sunday march 02:00 1 sunday november 02:20
```

## Network Time Protocol
We learnt network devices can be setup with clock using a manual method. However, manual configuration on network devices is not scalable. Whereas, NTP allows automatic syncing of time over a network.

Network tool ```nslookup``` demonstrates the microsoft and google's time servers.

```bash
C:\Users>nslookup time.windows.com
Server:  dns.google
Address:  8.8.8.8

Non-authoritative answer:
Name:    twc.trafficmanager.net
Address:  168.61.215.74
Aliases:  time.windows.com


C:\Users>nslookup time.google.com 
Server:  dns.google
Address:  8.8.8.8

Non-authoritative answer:
Name:    time.google.com
Addresses:  2001:4860:4806:c::
          2001:4860:4806::
          2001:4860:4806:8::
          2001:4860:4806:4::
          216.239.35.12
          216.239.35.8
          216.239.35.4
          216.239.35.0
```

NTP clients, network devices like routers, switches, PC and such request the time from NTP servers. A device like router or switch can be a NTP client or and server at the same time.

NTP Accuracy is based on location of NTP server, if NTP server is within the LAN it's accuracy of time withing ~1 millisecond or if the location of NTP server is over a WAN or Internet connection then the accuracy of time will be ~50 milliseconds. The `distance` of an NTP server from the original **reference clock** is called **stratum**. NTP uses UDP port 123 to communicate.

## Reference Clocks
A reference clock is usually a very accurate time device like an atomic clock or a GPS clock. Reference clocks are **stratum 0** within the NTP hierarchy. NTP servers directly connected to reference clocks are called **stratum 1**.

## NTP Hierarchy


## NTP Configuration


## NTP Server Mode


## NTP Symmetric active mode


## NTP Authentication


## NTP Commands
