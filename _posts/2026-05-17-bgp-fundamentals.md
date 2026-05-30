---
layout: post
title: "BGP Fundamentals"
date: 2026-05-17
categories: [Networking,CCNP]
tag: [blog,encor-ch11]
---

# Overview

BGP stands for **Border Gateway Protocol**. [RFC 1654](https://datatracker.ietf.org/doc/html/rfc1654) defines BGP as an EGP standardizing protocol, it is used for network scalability, flexibility and network stability.

## BGP Fundamentals

An **autonomous system (AS)** is a group of routers owned/controlled by one organization (like an ISP, a company, a university), and internally they typically use IGP protocol like OSPF or EIGRP to move packets around inside that AS.

### AS Routing Policy Consistency

When multiple IGPs or different metrics are deployed within a single autonomous system, the AS must still present a uniform routing policy to all external autonomous systems, Internal routing complexity - such as the use of both OSPF

### BGP as IGP
Yes, BGP can be used as IGP routing protocol to route traffic within an autonomous system. This means we do not need OSPF or EIGRP routing protocols.

## Autonomous System Numbers
An Autonomous System Number (ASN) is a globally unique identifier assigned to an autonomous system, required for exchanging routing information with other autonomous systems over BGP on the public internet.

ASNs were originally 2 bytes in size, allowing for 65,535 numbers. RFC 4893 expanded ASNs to 4 bytes, increasing the pool to 4,294,967,295 numbers.

IANA is responsible for global ASN uniqueness and delegates regional assignments to Regional Internet Registries (RIRs) such as ARIN (North America), RIPE (Europe), and APNIC (Asia-Pacific).

**Public ASN Assignment Requirements:** An organization must demonstrate three things to qualify for a public ASN:

- Ownership of a publicly allocated IP address range
- Internet connectivity through multiple upstream providers
- A unique routing policy that is independent from those providers

**Private ASNs:** Two blocks are reserved for private use and are not routable on the public internet:

2-byte range: 64,512 – 65,535
4-byte range: 4,200,000,000 – 4,294,967,294

## Path Attributes

BGP uses path attributes (PAs) associated with each network path. The PAs provide BGP
with granularity and control of routing policies within BGP.

Every time BGP learns a route to a destination, that route comes with a set of "tags" or "labels" - called Path Attributes. These tags give you precise control to change how traffic flows, allowing you to easily choose the best, safest, or cheapest path for your network.

An perfect example of path attributes would be; PAs are like luggage tags attached to a route, giving BGP the detailed information it needs to steer internet traffic exactly where you want it to go.

To see how this works, think of a BGP router as a massive airport sorting hub. The network destinations are the luggage, and the Path Attributes are the specific pieces of data printed on the tags that dictate how the luggage is handled:

- **The Final Destination** ***(NEXT_HOP)***: This is the airport code (like YLW or YYC) printed in bold. It tells the airport handlers exactly where the bag needs to go next to reach its owner.

- **The Flight Routing History** ***(AS_PATH)***: This is the list of all connecting airports the bag has already traveled through. Airlines look at this history to ensure the bag isn't accidentally sent backward in a loop to an airport it already visited.

- **The Priority Routing** ***(LOCAL_PREF)***: This acts like a bright "VIP / FIRST CLASS" sticker. When multiple conveyor belts are available, the airport handlers look for this tag first to ensure these specific bags are fast-tracked out of the airport ahead of regular luggage.

- **The Hand-off Instructions** ***(MED)***: This is like a note from a neighboring airline saying, "If you are handing this bag over to us, please use Gate 4 instead of Gate 12." It helps smooth out the hand-off between two different flight networks.

By reading these specific pieces of information on the "luggage tag," BGP can instantly decide the smartest, fastest, and most efficient way to route traffic across the globe.

The BGP prefix PAs are classified as follows:

- **The Absolute Essentials (Well-Known Mandatory):** Just like every airline luggage tag must print the destination airport code and flight number so the bag doesn't get lost, BGP routes must include attributes like AS_PATH and NEXT_HOP to find their way. Without them, the system breaks down.

- **The Priority Upgrades (Well-Known Discretionary):** Think of a bright orange "PRIORITY" sticker. Every airport worker in the world knows what it means, but it’s only put on certain bags. In BGP, attributes like LOCAL_PREF act like this priority sticker, telling routers to fast-track certain paths over others.

- **The Inter-line Transfers (Optional Transitive):** If you are switching airlines mid-journey, a "TRANSFER" tag ensures that even if the middle airport's automated sorting machine doesn't understand the tag, it is required to leave it on the bag for the next airline. This is exactly like a BGP COMMUNITY string, which gets passed safely across different networks.

- **The Local Cart Codes (Optional Non-Transitive):** A gate agent scribbles a quick note on your bag that says "Load onto Cart B." This is incredibly useful for the workers at that specific airport, but the moment the bag is loaded onto the plane and flies to a new city, that local note is completely ignored or stripped off. This is how BGP uses attributes like MED to handle local traffic engineering between immediate neighbors.

## Loop Prevention

## Address Families

## Inter-Router Communication

## BGP Session Types

### Internal BGP (iBGP)

### External BGP (eBGP)

## BGP Messages

### OPEN
#### Hold Time

#### BGP identifier

### KEEPALIVE

### UPDATE

### NOTIFICATION

## BGP Neighbor States

### Idle

### Connect

### Active

### OpenSent

### OpenConfirm

### Established