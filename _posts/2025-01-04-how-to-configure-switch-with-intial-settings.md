---
layout: post
title: Switch's Boot process and Initial device configuration
date: 04-01-2025
author:
categories: [Networking]
tag: [blog, ccna]
---

>> In progress..

This blogpost will describe how does Device(Switch/Router) functions and what initial configurations are done on out of the box switch.

# Topics
- Switch Boot Sequence
- The boot system Command
- LED Indicators on Switch
- Switch Port Configuration
- Device Remote Access
- Basic Router Configuration
- Configuration Verification

## Switch Boot Sequence
A cisco switch goes through five-step boot sequence process after it is powered on:
1. Switch loads a power-on self-test (POST) program stored in ROM. POST checks the CPU subsystem. It tests the CPU, DRAM, and the portion of the flash device that makes up the flash file system.

    > __POST:__ Power-On Self-test refers to the routines that run after a computer system is powered on. These routines are designed to check system resources and identify common hardware errors before the operating system loads.

2. Next, the switch loads the boot loader software. The boot loader is a small program stored in ROM and is run immediately after POST successfully completes.

3. The boot loader performs low-level CPU initialization. It initializes the CPU registers that control where physical memory is mapped, the quantity of memory, and memory speed.

    > __CPU Registers:__ A register is a small, fast storage location within the CPU used to hold data temporarily during processing.

    ![CPU Registers](/assets/cpu-registers.png)

4. The boot loader initializes the __flash file system__ on the system board.
    > __System board:__ Generally referred as motherboard or PCB (printed circuit board). It connects and allows communications between all the major components of the system - CPU, RAM, Storage, Graphic cards, input/output ports and other peripherals.

    ![Cisco Switch board](/assets/cisco-switch-board.jpg)

5. Finally, the boot loader locates and loads a default IOS operating system software image into memory and hands control of the switch over to the IOS.

## The boot system Command
All switches comes with a default image in its flash memory. Often network teams tests the different versions of switch image and decided to use the the image they find is most reliable. Then the image is upload to switch and let switch to use the specified image version when it boots up.

BOOT environment variable is set using the __boot system__ global configuration mode command.

The __show boot__ command shows what the current IOS boot file is set to.
<pre>
<code>
switch#show boot
BOOT path-list      : flash:c2960-lanbasek9-mz.122-50.SE4/c2960-lanbasek9-mz.122-50.SE4.bin
Config file         : flash:/config.text
Private Config file : flash:/private-config.text
Enable Break        : no
Manual Boot         : no
HELPER path-list    : 
Auto upgrade        : yes
Auto upgrade path   : 
NVRAM/Config file
</code>
</pre>

    S1(config)# boot system flash:/c2960-lanbasek9-mz.150-2.SE/c2960-lanbasek9-mz.150-2.SE.bin

Command                          | Definition
---------------------------------|---------------------------
boot system                      | The main command
flash:                           | The storage device
c2960-lanbasek9-mz.150-2.SE      | The path to the file system
c2960-lanbasek9-mz.150-2.SE.bin  | The IOS file name

## LED Indicators on Switch
Switch LED indicators are useful when we are physically inspecting switch status. The following figure shows eight LED indicators on a Cisco Catalyst 2960 switch.

![Switch LED Indicators](/assets/2960-switch-led-indicators.jpg)

> Note: Marking number 7 is pointing to the mode button, mode button needs to be pressed to cycle through LED modes from number 1 to 6. Marking number 8 indicates to the ports numbers, an LED close to switch port can be reelects there.

1. __SYST:__ System LED represents the system power, whether switch is receiving power or not and it's proper functionality. An Amber LED means switch is receiving power but is not functioning properly.

2. __RPS:__ Redundant Power System, if the main power supply fails, the RPS automatically takes over to prevent downtime.

    LED Light                        | Definition
    ---------------------------------|---------------------------
    Green Solid                      | Providing backup power
    Green Blinking                   | RPS connected but unavailable as it's providing power to another device
    No Light                         | RPS is not connected
    Amber Solid                      | RPS is in standby mode or in a fault condition.
    Amber Blinking                   | Internal power supply is the switch has failed, and RPS is providing power.

3. __STAT:__ Port Status LED, This LED helps determining the switch port status. Switches usually have STAT mode selected by default thus we see blinking lights on switch's ports. We can select the mode manually by pressing MODE button.

    LED Light                            | Definition
    -------------------------------------|---------------------------
    No LED                               | No Link
    Solid Green                          | Link is present
    Blinking Green                       | Link has activity, port is receiving and sending data
    Alternating Green-Amber              | Link has fault
    Solid Amber                          | Port is blocked to ensure a loop doesn't exist, stays amber for 30 seconds after it is activated
    Blinking Amber                       | Port is blocked to prevent loop in the forwarding domain

4. __DUPLX:__ Port Duplex LED, LED's that are off in __half-duplex mode__. If the port LED is green, the port is in __full-duplex mode__.

5. __SPEED:__ Port Speed LED, Shows the port speeds with different LED status.

    LED Light                            | Definition
    -------------------------------------|---------------------------
    LED is off                           | Port is operating at 10 Mbps
    LED is Solid Green                   | Port is operating at 100 Mbps
    LED is blinking Green                | Port is operating at 1000 Mbps

6. __PoE:__ Power over Ethernet (POE) Mode LED, if the PoE is supported on switch port, a PoE mode LED will present. There is a LED on mode and LED on ports as well referred in marking 7 and 8.

    LED Light                                | Definition
    -----------------------------------------|---------------------------
    LED is off                               | PoE is off
    LED is blinking amber                    | PoE mode is not selected but at least one of the ports has been denied power or has PoE fault
    LED is green                             | PoE mode is selected and the port LEDs will display colors with different meanings.
    Port LED is off                          | PoE is off
    Port LED is solid green                  | PoE is on
    Port LED is alternating green-amber      | PoE is denied because providing power to the powered device will exceed the switch power capacity.
    LED is amber                             | Port PoE is disabled


    

