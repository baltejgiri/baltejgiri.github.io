---
layout: post
title: "Dynamic ARP Inspection (DAI) - Trust with Verify"
date: 2027-04-19
categories: [Networking,CCNA]
tag: [blog,ccna-vol2-ch12]
---

# Overview

Dynamic ARP Inspection (DAI) works just like the title says 'Trust with Verify'. 

The core feature of Dynamic ARP Inspection compares incoming ARP messages with two sources of data: the DHCP Snooping binding table and any configured ARP ACLs. If the incoming ARP message does not match the tables in the switch, the switch discards the ARP message.

DAI works hand-in-hand with DHCP Snooping,it's important to have a understanding of DHCP Snooping and how it builds it's DHCP Snooping binding table.

## DAI - The problem it solves

ARP traffic, like many other network protocols, is broadcast-based. As we know, switches takes the broadcast traffic and flood it out in the same broadcast domain, which creates a risk of someone eavesdropping on the broadcast traffic. The key distinction here is not all ARP traffic is broadcasted but when you ARP for something and switches floods the broadcast traffic.

Because of this people with mal-intent use DHCP packets to their benefit so they can plan and execute various attacks on network.

## Diagram

We will use the following diagram to understand how Dynamic APR Inspection works.
![DAI Diagram](/baltejgiri.github.io/assets/img/DHCP_snooping_diagram.png)

## How DIA works

The first method was explained in post [DHCP Snooping ](/baltejgiri.github.io/_posts/2026-04-18-DHCP-Snooping-Trusted-versus-untrusted.md) explains how trusted and untrusted switch interfaces work.

To be completed...





