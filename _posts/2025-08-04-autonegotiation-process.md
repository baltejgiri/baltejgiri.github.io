---
layout: post
title: Autonegotiation and More
date: 2025-08-04
author: 
#categories: []
tag: [blog]
---

STATUS: In progress..

To further expand my last blog [Limitations of Simulated and Emulated Labs](https://github.com/baltejgiri/baltejgiri.github.io/blob/main/_posts/2025-08-03-limitations-of-simulated-and-emulated-labs.md) and going into a rabbit hole, I went back to chapter 7 section Autonegoatiation of CCNA 200-301 2nd edition.

## What is Autonegotiation?
It's a Layer 1 (physical layer) mechanism for two directly connected links to agree on the best speed autometicallty.

## Why Autonegotiation is required?
It is required to elimiate any possible confussion, without autonegotiation both interfaces on each devices requied to be set on common speed and duplex. For example, a 100Mbps interfaces requies to have speed 100Mbps and duplex settings of full otherwise link may see duplex or speed mismatch. Additiaonlly, setting speed and duplex on every single interface is not efficent in a large production network.

There is still a good use case of manually configuring interfaces with 10, 100 or 1000 speeds and full duplex.

## How Autonegotiation works?

Devices sends FLPs (Fast Link Pules) on Ethernet cables when they are connected using Network Interface cards.

### Normal Link Pulse and Fast Link Pulse

Before we dig into FLPs let's take a look at NLP. NLP stand for Normal Link Pulse basically a short electical pulses. NLPs are like heartbeat between two devices.

A NLP uses the 10BASE-T Ethernet (2 pairs of copper wires) to check if the link is still alive, it sends short electical pulses about 100nanosecounds long every 16 miliseconds when no data is being transmitted.

Once NLP confirms link is alive, FLP does it work by negotiating link's speed and duplex.

Unlike NLP, FLP sends brust of pulses that carry data using a form of binary. For example, it is more of an encoded heartbeats, it encodes data saying *I am alive, I support 10/100 Mbps full-duplex."*

#### MATH right?

- One FLP brust = 17 clock positions spaced 62.5 µs apart.
- Each of these positions may or may not contain a pulse.
- Pulses are either on or off at specific position, represents binary data (1s or 0s).

**Out of 17 clock positions, 5 are clock pulses, always on and rest of 12 are data bits, which encodes interfaces speed and duplex.

#### In plain english
- NLP = A simple knock no the door. "Are you there?"
- FLP = A knock with Morse code: "Hi, I can talk at 100Mbps with full-duplex. What about you?"

So How Autonegotiation works?

Finally, A Ethernet cable connects two network devices, NLP messages are sent by both network devices NIC (chips), sharing their capabilties of speed and duplex.

If both interfaces agrees to same speed and duplex link is established.

An Ethernet link usually starts with 1000Mbps full-duplex, then 100Mbps full-duplex, if first two are not matching then 100Mbps half-duplex or it can futher sets to 10 Mbps full-duplex or 10Mbps half-duplex.

What do you think is better 100Mbps half-duplex or 10Mbps full-duplex?


To be continuted...


