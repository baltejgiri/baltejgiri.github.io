---
layout: post
title: Autonegotiation on Ethernet Link
date: 2025-08-04
author: 
#categories: [Networking]
tag: [blog, ccna]
---

## What is Autonegotiation?
It's a Layer 1 (physical layer) mechanism for two directly connected links to agree on the best speed automatically.

## Why Autonegotiation is required?
It is required to eliminate any possible confusion. Without autonegotiation, both interfaces on each device must be set to the same speed and duplex. For example, a 100 Mbps interface requires speed of 100 Mbps and duplex settings of full; otherwise, the link may see a duplex or speed mismatch. Additionally, setting speed and duplex on every single interface is not efficient in a large production network.

There is still a valid use case for manually configuring interfaces with 10, 100, or 1000 speeds and full duplex.

## How Autonegotiation works?

Devices send FLPs (Fast Link Pulses) on Ethernet cables when they are connected using Network Interface cards.

### Normal Link Pulse and Fast Link Pulse

Before we dig into FLPs, let's take a look at NLP. NLP stands for Normal Link Pulse—basically short electrical pulses. NLPs are like a heartbeat between two devices.

A NLP uses 10BASE-T Ethernet (2 pairs of copper wires) to check if the link is still alive. It sends short electrical pulses about 100 nanoseconds long every 16 milliseconds when no data is being transmitted.

Once NLP confirms the link is alive, FLP does its work by negotiating the link's speed and duplex.

Unlike NLP, FLP sends bursts of pulses that carry data using a form of binary. For example, it is more of an encoded message. It encodes data saying *"I am alive, I support 10/100 Mbps full-duplex."*

#### Calculation

- One FLP burst = 17 clock positions spaced 62.5 µs apart.
- Each of these positions may or may not contain a pulse.
- Pulses are either on or off at specific position, represents binary data (1s or 0s).

Out of 17 clock positions, 5 are clock pulses, always on and rest of 12 are data bits, which encodes interfaces speed and duplex.

#### Simplified
- NLP = A simple knock on the door: "Are you there?"
- FLP = A knock with Morse code: "Hi, I can talk at 100 Mbps with full-duplex. What about you?"

#### So How Autonegotiation works?

Finally, an Ethernet cable connects two network devices. NLP messages are sent by both devices' NICs (network interface cards), sharing their capabilities of speed and duplex.

If both interfaces agree on the same speed and duplex, the link is established.

An Ethernet link usually starts with 1000Mbps full-duplex, then 100Mbps full-duplex, if first two are not matching then 100Mbps half-duplex or it can further sets to 10 Mbps full-duplex or 10Mbps half-duplex.

So, what do you think is better 100Mbps half-duplex or 10Mbps full-duplex?



