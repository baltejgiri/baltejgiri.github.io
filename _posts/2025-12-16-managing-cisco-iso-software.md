---
layout: post
title: Managing Cisco ISO Software
date: 16-12-2025
author:
categories: [Networking,CCNA]
tag: [blog, ccna]
---

# Managing Cisco IOS Software
Cisco IOS software can be managed and administered using the command-line interface, which is the most preferred method. Today's topic will show some of the ways a network engineer manages Cisco IOS software, from resetting a device to factory settings to creating snapshots and copying configuration files to and from a device.

## Resetting device to factory settings
Factory settings on a network device are like a clean slate—the most default state of software on a device. Network devices come with default settings from the factory when they are purchased; they are also factory reset when decommissioned from the active network either due to end-of-hardware-support or because they need to be redeployed for a different purpose. 

Before we dive into how to reset device to factory settings it is important to understand what is a configuration and what are common configurations.

## What is a configuration on device?
Configuration on a network device refers to custom changes made to a network device within the prescribed programming of the software. By default, network devices come with basic settings. Network engineers often need to run network devices with specific rules or settings—these rules and settings can be set using either the web GUI or CLI.

Network device configuration can only be set according to the programming of the actual software. If a new feature or setting is desired, it needs to be programmed into the software.

Cisco router has two type of configuration;

**Running Configuration** configuration that stays in RAM, it can be the routing table, interface states or any custom configuration that has been applied to ISO software but has not saved yet.

**Startup Configuration** configuration that is saved to NVRAM, everytime ```copy running-config startup-config``` or ```write memory``` command is executed ISO software saves custom configuration - new routes added to route table, ip addresses assigned to interfaces etc, etc.

## Device configuration wipe out process
To wipe out the custom configuration from Cisco IOS software, use the following command ```write erase``` or ```erase startup-config``` from exec privilege mode, then run the ```reload``` command.

Example showing how to reset device to factory settings

```bash
Baltej#write erase
Erasing the nvram filesystem will remove all configuration files! Continue? [confirm]
[OK]
Erase of nvram: complete
Baltej#
*Dec 17 03:26:18.752: %SYS-7-NV_BLOCK_INIT: Initialized the geometry of nvram
Baltej#reload
Proceed with reload? [confirm]

*Dec 17 03:26:30.763: %SYS-5-RELOAD: Reload requested by console. Reload Reason: Reload command.
 *Dec 17 03:26:33.794 
Reload requested
!
!Device will reboot/reload with fresh image
!
Router>
````

Cisco switches with IOS software require one extra step to reset the configuration to factory default: the VLAN database. The VLAN database is stored in a separate file.

To see the name of vlan database file, use ```dir``` from exec privilage mode.

```bash
Switch#dir
Directory of flash:/

    2  -rwx    19211264   Jan 1 1970 00:03:54 +00:00  c2960-cx.bin
    3  drwx         512  Jan 17 2003 03:54:56 +00:00  pnp-tech
    6  drwx         512  Jan 17 2003 03:54:24 +00:00  pnp-info
    7  -rwx        4201  Feb 10 2003 06:04:13 +00:00  config.text
    8  -rwx         856   Feb 9 2003 06:47:50 +00:00  vlan.dat
    9  -rwx        5476  Feb 10 2003 06:04:13 +00:00  private-config.text
   11  -rwx        5144  Feb 10 2003 06:04:13 +00:00  multiple-fs
122185728 bytes total (102712832 bytes free)
````

>Disclaimer: Do not run ```delete flash:``` command itself, you will need to re-install ISO software on device.

vlan.dat is under flash:/ directory. To ***delete*** vlan.dat file from flash:/ directory following the steps here.

```bash
Switch#delete flash:/vlan.dat
```
Once again run ```reload``` to take effect and verify device has no custom configuration

Now your router or switch is set to factory settings.

## Creating a snapshot of default configuration

Cisco ISO software allows to create file under it's flash directory. A snapshot of the running configuration can help revert the device's state back to a working condition if a new change does not go as planned. It can also be useful if a network device is being implemented for a temporary purpose.

Rolling back to a default configuration or a snapshot with the most common configuration is helpful.

Following command needs to be run from the exec privilage mode

```bash
Switch#copy running-config flash:clean-slate
Destination filename [clean-slate]? 
2824 bytes copied in 6.982 secs (404 bytes/sec)
Switch#dir 
Directory of flash0:/

    1  drw-           0  Jan 30 2013 00:00:00 +00:00  boot
  264  drw-           0  Oct 14 2013 00:00:00 +00:00  config
  267  -rw-         567  Sep 29 2020 00:00:00 +00:00  eula.txt
  268  -rw-   119857844  Sep 29 2020 00:00:00 +00:00  vios_l2-adventerprisek9-m
  269  -rw-      524288  Dec 10 2025 02:52:14 +00:00  nvram
  270  -rw-         159  Dec 14 2025 17:21:34 +00:00  e1000_bia.txt
  271  -rw-          35  Dec 17 2025 04:06:24 +00:00  pnp-tech-time
  272  -rw-       30202  Dec 17 2025 04:06:28 +00:00  pnp-tech-discovery-summary
  273  -rw-        2824  Dec 17 2025 04:54:44 +00:00  clean-slate

2142715904 bytes total (2017677312 bytes free)
```
First ```copy running-config flash:``` was typed then a name ```clean-slate``` was typed to name the snapshot file name. Command ```dir``` was used to view the clean-slate file.

So far we have created a snapshot of switch with it's default settings, similar method can be used to create snapshot before a major change in case of device configuration needs to be rolled back to the last good known configuration.

## Rolling back to snapshot

The process of rolling back the running config with the specified snapshot file will distroy (delete) the running configuration and swap it with snapshot file.

The bigest advantance, device does not need to be rebooted.

Following example shows the procedure how it works;

```bash
Baltej#configure replace flash:clean-slate
This will apply all necessary additions and deletions
to replace the current running configuration with the
contents of the specified configuration file, which is
assumed to be a complete configuration, not a partial
configuration. Enter Y if you are sure you want to proceed. ? [no]: Y

*Dec 17 05:39:08.087: Rollback:Acquired Configuration lock.
The rollback configlet from the last pass is listed below:
********
!List of Rollback Commands:
line vty 0 4
 no login
end
********


Rollback aborted after 5 passes
Switch#
```

## Exporting configuration from network device to computer

Configuration export from network device can be a easiest method to store file to review or use keep it as backup. Other methods, TFTP, SCP, NMS Tools can be useful but often require a network connection.

Before I show you how this method works it is important to set the terminal lenght to 0. This will make sure a long output from switch interface is shown at once instead having to either hit enter or spacebar.

```bash
Exporting#terminal length 0
```

In this example I have used terminal software [SecureCRT](https://www.vandyke.com/products/securecrt/).

Step 1. Type *show running-config* but do not hit enter yet.
```bash
show running-config
```
Step 2. In SecureCRT click **Transfer** then **Receive ASCII**, navigate and select desired directory on your computer. Type file name and click save 

Step 3. In SecureCRT within the CLI session of network device, hit enter to run ```show running-config``` command.

Step 4. In SecureCRT, Click **Transfer** then uncheck the **Receive ASCII**

Step 5. View the saved file.

## Importing configuration from computer to network device

Importing configuration file is similar to how we exported the config file from network device using SecureCRT but with less steps.

Step 1. The config file format needs to be the same as what we see in the exported text file; however, we need to make adjustments at the beginning of the file.

For example, file needs to look as follows;

```bash
!
enable
!
configuration terminal
!
! Last configuration change at 05:45:42 UTC Wed Dec 17 2025
!
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
no service password-encryption
service compress-config
!lines of text is emitted
```
Step 2. While you're in SecureCRT and on the desired switch CLI. Click **Transfer** then **Send ASCII**, navigate to directory where file is saved, select all file format. Select the text file that needs to be importated then click open.

Step 3. Verify the running configuration on network device, if it looks good save the running config to startup config.

## Summary

Managing Cisco IOS software using quick methods without needing any file transfer protocols. These methods are great while troubleshooting, setting up a new device, or decommissioning an existing device. However, a day to day management of devices is often achieved using TFTP or network management systems provides automated processes. We will discuss these topics in future blogs.
