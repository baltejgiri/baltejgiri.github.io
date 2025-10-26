---
layout: post
title: Network Device staging
date: 29-06-2025
author:
categories: [Networking]
tag: [blog, ccna]
---
---

# Layer 2 Switch Staging: A Step-by-Step Guide to Expanding Network Capacity

Expanding your network by adding new switches is a common need in growing IT environments. Whether it's to support additional users, wireless access points, or IP phones, a Layer 2 access switch provides the foundation for connecting more endpoints.

But before a switch enters your production environment, it needs to be staged—a critical process that ensures your device is properly configured, tested, and ready for deployment. In this guide, we’ll walk through the end-to-end process of staging a Layer 2 network switch.

## Why Stage a Network Switch?

**Device staging** is the act of preparing a network switch in a controlled setting before it goes live in your network. This process ensures:

- Consistency across deployments  
- Reduced on-site configuration time  
- Early detection of hardware or configuration issues  
- Seamless integration with your production environment  

This is especially important when scaling access-layer connectivity across a growing organization.

## When to Stage a Switch

You should stage a Layer 2 switch when:

- Adding port capacity at the access layer  
- Replacing older or failing hardware  
- Deploying access points or VoIP phones  
- Rolling out standardized branch-site hardware  


## Layer 2 Switch Staging Process

### 1. Unboxing and Inspection

Start with a physical inspection of the switch:

- Verify model, part number, and serial number  
- Check for any visible damage  
- Confirm included accessories: rack mounts, power cables, console cable, and documentation  

If applicable, rack-mount the switch temporarily in your staging area.

### 2. Install Modules and Accessories

Install any required accessories:

- SFP or GBIC modules  
- Stack cables (for stackable switches)  
- Power supplies or RPS units  

Ensure uplink modules are seated properly and stack topology is cabled correctly if using switch stacking.

### 3. Initial Console Access and Setup

Connect to the switch using a console cable. Use terminal software (like PuTTY or Tera Term) to access the CLI and begin the initial configuration.

Configure basic settings to enable remote management:

```bash
hostname Access-Switch-01

interface vlan 99
 description Management VLAN
 ip address 10.1.99.10 255.255.255.0
 no shutdown

ip default-gateway 10.1.99.1
ip domain-name example.local
crypto key generate rsa modulus 2048

username admin privilege 15 secret StrongPassword123

line vty 0 4
 login local
 transport input ssh
````

Test SSH connectivity from your workstation before proceeding.

---

### 4. VLAN Configuration

Define all VLANs that will be used by devices connecting to this switch.

#### Example VLANs:

| VLAN ID | Purpose           |
| ------- | ----------------- |
| 10      | Data              |
| 20      | Voice (IP Phones) |
| 30      | Wireless          |
| 99      | Management        |

```bash
vlan 10
 name Data
vlan 20
 name Voice
vlan 30
 name Wireless
vlan 99
 name Management
```

---

### 5. Port Configuration

Assign switch ports to VLANs based on the connected device types.

#### Access Port Examples:

```bash
interface range gi1/0/1 - 10
 description Data Ports
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast

interface range gi1/0/11 - 15
 description IP Phones
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast

interface range gi1/0/16 - 20
 description Wireless APs
 switchport mode access
 switchport access vlan 30
 spanning-tree portfast
```

#### Trunk Port for AP or Uplink:

If you’re connecting an access point or another switch that requires multiple VLANs:

```bash
interface gi1/0/24
 description Uplink to Core/Distribution or Wireless AP
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,99
```

---

### 6. Enable STP and Security Features

Protect your access layer with features like STP and port security.

```bash
interface range gi1/0/1 - 20
 spanning-tree portfast
 spanning-tree bpduguard enable
```

For basic port security:

```bash
interface range gi1/0/1 - 20
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
 switchport port-security mac-address sticky
```

---

### 7. Verification and Testing

Use the following commands to verify your configurations:

```bash
show vlan brief
show running-config
show interfaces status
show spanning-tree
show mac address-table
```

Confirm:

* VLANs are present and active
* Ports are assigned correctly
* SSH access is available
* Trunk links pass VLANs
* STP is in proper state

---

### 8. Save Configuration and Document

Save the switch configuration:

```bash
write memory
```

Or:

```bash
copy running-config startup-config
```

Document the following:

* Hostname and management IP
* VLAN-to-port mappings
* Device role and location
* Serial number and asset tag

Label the switch and prepare it for deployment.

---

## Deployment Checklist

Before shipping or transporting the device:

* Configuration is saved
* Documentation is complete
* Device is labeled clearly
* Verified connectivity and VLANs
* Uplink and management ports tested

---

## Final Thoughts

Staging a Layer 2 switch helps ensure a smooth transition from the lab to the field. By following a standardized process, you reduce errors, increase speed of deployment, and ensure every switch that enters production is fully operational and secure.

A bit of time invested in staging saves hours of troubleshooting and rework later.

