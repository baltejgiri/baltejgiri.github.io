---
layout: post
title: Autonegotiation on Ethernet Link
date: 2025-08-04
author: 
#categories: []
tag: [blog]
---

## What is Autonegotiation?
It's a Layer 1 (physical layer) mechanism for two directly connected links to agree on the best speed automatically.

## Why Autonegotiation is required?
It is required to eliminate any possible confession, without autonegotiation both interfaces on each devices required to be set on common speed and duplex. For example, a 100Mbps interfaces requires to have speed 100Mbps and duplex settings of full otherwise link may see duplex or speed mismatch. Additionally, setting speed and duplex on every single interface is not efficient in a large production network.

There is still a good use case of manually configuring interfaces with 10, 100 or 1000 speeds and full duplex.

## How Autonegotiation works?

Devices sends FLPs (Fast Link Pules) on Ethernet cables when they are connected using Network Interface cards.

### Normal Link Pulse and Fast Link Pulse

Before we dig into FLPs let's take a look at NLP. NLP stand for Normal Link Pulse basically a short electrical pulses. NLPs are like heartbeat between two devices.

A NLP uses the 10BASE-T Ethernet (2 pairs of copper wires) to check if the link is still alive, it sends short electrical pulses about 100nanosecounds long every 16 milliseconds when no data is being transmitted.

Once NLP confirms link is alive, FLP does it work by negotiating link's speed and duplex.

Unlike NLP, FLP sends burst of pulses that carry data using a form of binary. For example, it is more of an encoded heartbeats, it encodes data saying *I am alive, I support 10/100 Mbps full-duplex."*

#### Calculation

- One FLP burst = 17 clock positions spaced 62.5 µs apart.
- Each of these positions may or may not contain a pulse.
- Pulses are either on or off at specific position, represents binary data (1s or 0s).

Out of 17 clock positions, 5 are clock pulses, always on and rest of 12 are data bits, which encodes interfaces speed and duplex.

#### Simplified
- NLP = A simple knock no the door. "Are you there?"
- FLP = A knock with Morse code: "Hi, I can talk at 100Mbps with full-duplex. What about you?"

#### So How Autonegotiation works?

Finally, An Ethernet cable connects two network devices, NLP messages are sent by both network devices NIC (chips), sharing their capabilities of speed and duplex.

If both interfaces agrees to same speed and duplex link is established.

An Ethernet link usually starts with 1000Mbps full-duplex, then 100Mbps full-duplex, if first two are not matching then 100Mbps half-duplex or it can further sets to 10 Mbps full-duplex or 10Mbps half-duplex.

So, what do you think is better 100Mbps half-duplex or 10Mbps full-duplex?



