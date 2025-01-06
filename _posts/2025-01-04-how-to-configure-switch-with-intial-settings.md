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
- Boot Process
    - Switch Boot Sequence
    - The boot system Command
    - LED Indicators on Switch
    - Recovering from system crash
- Initial Device Configuration
    - Switch Port Configuration
    - Device Remote Access
    - Remote Access Management
    - Switch Virtual Interface Configuration
    - Basic Router Configuration
    - Configuration Verification

## Boot Process

__The boot loader__ is a program responsible for booting a switch. It runs boot sequence process after switch is powered on.

The boot loader finds the Cisco IOS image on the switch by first looking in a directory that has the same name as the image file (excluding the .bin extension). If boot loader does not find the operating system software image, boot loader software searches each subdirectory before continuing the search in the original directory.

The operating system then initializes the interfaces using Cisco IOS commands found in the operating system configuration file, config.text, stored in the switch flash memory.

> The boot loader commands support initializing flash,  formatting flash, install a new IOS, changing the BOOT environment variable and recovery of a lost or forgotten passwords.

### Switch Boot Sequence
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

### The boot system Command
All switches comes with a default image in its flash memory. Often network teams tests the different versions of switch image and decided to use the the image they find is most reliable. Then the image is upload to switch and let switch to use the specified image version when it boots up.

BOOT environment variable is set using the __boot system__ global configuration mode command.

The __show boot__ command shows what the current IOS boot file is set to.
```markdown
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
```

```markdown
S1(config)# boot system flash:/c2960-lanbasek9-mz.150-2.SE/c2960-lanbasek9-mz.150-2.SE.bin
```

Command                          | Definition
---------------------------------|---------------------------
boot system                      | The main command
flash:                           | The storage device
c2960-lanbasek9-mz.150-2.SE      | The path to the file system
c2960-lanbasek9-mz.150-2.SE.bin  | The IOS file name

### LED Indicators on Switch
Switch LED indicators are useful when we are physically inspecting switch status. The following figure shows eight LED indicators on a Cisco Catalyst 2960 switch.

![Switch LED Indicators](/assets/2960-switch-led-indicators.jpg)

> Note: Marking number 7 is pointing to the mode button, mode button needs to be pressed to cycle through LED modes from number 1 to 6. Marking number 8 indicates to the ports numbers, an LED close to switch port can be reelects there.

1. __SYST:__ System LED represents the system power, whether switch is receiving power or not and it's proper functionality. An Amber LED means switch is receiving power but is not functioning properly.

2. __RPS:__ Redundant Power System, if the main power supply fails, the RPS automatically takes over to prevent downtime.

    LED Light                        | Definition
    ---------------------------------|---------------------------
    Solid green                      | Providing backup power
    Blinking green                   | RPS connected but unavailable as it's providing power to another device
    No Light                         | RPS is not connected
    Solid amber                      | RPS is in standby mode or in a fault condition.
    Blinking amber                   | Internal power supply is the switch has failed, and RPS is providing power.

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

### Recovering from system crash

__Recovering from a System Crash__, If the operating system or system files on a Cisco switch are missing, corrupted, or damaged the switch typically enter into bootloader mode also referred as __ROMmon__ mode instead of booting into the normal operating system.

A console cable is required to access boot loader. The following explains the process:

1. Connect a PC by console cable to the switch console port. Configure terminal emulation software like [Putty](https://www.putty.org/) to connect to the switch.

2. Disconnect the switch power cord.

3. Plug the power cord to switch and within 15 seconds, press and hold the [__Mode__](#led-indicators-on-switch) button while the System LED is still flashing green.

4. Continue pressing the __Mode__ button until the System LED turns briefly amber and ten solid green; then release the __Mode__ button.

5. The boot loader __switch:__ prompt appears in the terminal emulation software on the PC.

> __Note:__ Switch interface mode will show __switch:__, the colon represents switch in it's bootloader mode.

```markdown
switch: set
BOOT=flash:/c2960-lanbasek9-mz.122-55.SE7/c2960-lanbasek9-mz.122-55.SE7.bin
```

__set__ command appears in the terminal emulation software on the PC.

__flash_init__ command to view the current files in flash.

```markdown
switch: flash_init
    Initializing Flash...
    flashfs[0]: 2 files, 1 directories
    flashfs[0]: 0 orphaned files, 0 orphaned directories
    flashfs[0]: Total bytes: 32514048
    flashfs[0]: Bytes used: 11838464
    flashfs[0]: Bytes available: 20675584
    flashfs[0]: flashfs fsck took 10 seconds.
    ...done Initializing Flash.
```

__dir flash:__ command to view the directories and files in the flash.

```markdown 
switch: dir flash:
    Directory of flash:/
        2  -rwx        1798   Sep 2 1993 06:32:59 +00:00  config.text
        3  -rwx        4270   May 7 2019 12:35:12 +00:00  cfg.txt
        4  -rwx    11660773   Mar 1 1993 00:03:45 +00:00  nvram
        5  drwx         512   Mar 1 1993 00:00:12 +00:00  crashinfo
        7  -rwx    11660773   Mar 1 1993 00:24:16 +00:00  c2960-lanbasek9-mz.122-58.SE2.bin
        8  -rwx         796  Sep 26 1993 06:22:29 +00:00  vlan.dat
        9  -rwx        3840   Sep 2 1993 06:33:00 +00:00  private-config.text
    10  -rwx        3096   Sep 2 1993 06:33:00 +00:00  multiple-fs
    27998208 bytes total (4466176 bytes free)
```

Now enter the __BOOT=flash__ command to change the BOOT environment variable path the switch uses to load the new IOS in flash.

```markdown
switch: BOOT=flash:c2960-lanbasek9-mz.150-2.SE8.bin
```

Once again run __set__ command to verify the new BOOT environment variable path.

```markdown
switch: set
BOOT=flash:c2960-lanbasek9-mz.150-2.SE8.bin
```

Final step to load the new IOS type the __boot__ command.

```markdown
switch: boot
```

## Initial Device Configuration

Network devices needs initial configuration like assigning management ip address, VLAN (virtual local area network), a topic we will explore in another post and enable SSH protocol.

Network teams usually installs the stable version of firmware as part of initial device configuration.

### Remote Access Management

A switch must have a switch virtual interface (SVI) configured with an IPv4 address, subnet mask, default gateway or an IPv6 address and a prefix length for IPv6. The SVI is a virtual interface, not a physical port on the switch.

To demonstrate the logical topology, a PC would connect to switch's console port using console cable and access switch's command line interface using terminal emulation software. An Ethernet cable from switch's port will connect to router or layer 3 switch's port.

![logical-view-of-pc-switch-layer3-switch](/assets/logical-view-of-pc-switch-layer3-switch.png)

### Switch Virtual Interface Configuration

All ports are assigned to VLAN 1 by default and switches remote management can be setup on vlan 1. For security reasons, the best practice to use a VLAN other than VLAN 1 for management VLAN.

:lab_coat: **Switch virtual interface configuration LAB** 

We're going to create a new VLAN with VLAN number "99" and name "device-mgmt". Then we will setup an IPv4 and IPv6 address on vlan 99's SVI. Therefore we will setup a default gateway for IPv4 and complete this lab verifying the work. At last, we will make sure the running configuration is saved to startup configuration so that we do not loss the configured commands in the event of switch reboots.

Step 1. Create VLAN, name vlan, configure IPv4 and IPv6 to SVI interface and enable it.

```markdown
switch#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
switch(config)#vlan 99
switch(config-vlan)#
%LINK-5-CHANGED: Interface Vlan99, changed state to up
switch(config-vlan)#name device_mgmt
switch(config-vlan)#exit
switch(config)#interface vlan 99
switch(config-if)#
switch(config-if)#ip address 10.1.99.11 255.255.255.0
switch(config-if)#ipv6 address 2001:db8:acad:99::11/64  
switch(config-if)#no shutdown
switch(config-if)#end
switch#
%SYS-5-CONFIG_I: Configured from console by console  
```

Step 2. Setup default gateway address on switch's global configuration mode.

```markdown
switch#configure terminal
Enter configuration commands, one per line.  End with CNTL/Z.
switch(config)#
switch(config)#ip default-gateway 10.1.99.1
switch(config)#end
switch#
%SYS-5-CONFIG_I: Configured from console by console
```

Step 3. Verify switch's physical and virtual interfaces.

> __Note:__ An IP address applied to the SVI is only for remote management access to the switch; this does not allow the switch to route Layer 3 packets.

```markdown
switch#show ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol 
FastEthernet0/1        unassigned      YES manual up                    up 
FastEthernet0/2        unassigned      YES manual down                  down 
!lines of code is emitted
GigabitEthernet0/2     unassigned      YES manual down                  down 
Vlan1                  unassigned      YES manual administratively down down 
Vlan99                 10.1.99.11      YES manual up                    down
!
switch>show ipv6 interface brief 
Vlan99                 [down/down]
    FE80::C27B:BCFF:FEC4:A9C1
    2001:DB8:ACAD:99::11
!lines of code is emitted
```

