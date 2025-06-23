---
layout: post
title: Cisco Switch Password Security Configuration
date: 2025-06-22
author: Baltej Giri
categories: [Networking]
tag: [blog, ccna]
---

## Cisco Switch Password Security Configuration

Proper password security is essential for protecting your Cisco switch from unauthorized access. Below is a simple checklist and step-by-step guide to configure basic password security on a Cisco switch.

---

### Password Security Checklist

- [x] Set console password
- [x] Set VTY (Telnet/SSH) password
- [x] Set enable (privileged EXEC) password
- [x] Encrypt all passwords
- [x] Set login banners (optional for legal notice)

---

### Step-by-Step Configuration

#### 1. Set Console Password

````plaintext
Switch(config)# line console 0
Switch(config-line)# password YOUR_CONSOLE_PASSWORD
Switch(config-line)# login
Switch(config-line)# exit
````

#### 2. Set VTY (Telnet/SSH) Password

````plaintext
Switch(config)# line vty 0 15
Switch(config-line)# password YOUR_VTY_PASSWORD
Switch(config-line)# login
Switch(config-line)# exit
````

#### 3. Set Enable (Privileged EXEC) Password

````plaintext
Switch(config)# enable secret YOUR_ENABLE_PASSWORD
````

#### 4. Encrypt All Passwords

````plaintext
Switch(config)# service password-encryption
````

#### 5. Set Login Banners (Optional)

````plaintext
Switch(config)# banner motd # Unauthorized access is prohibited. #
````

---

### Verification

To verify the password security configuration, you can use the following commands:

- **Show running configuration**: To view the current configuration and ensure passwords are set.
- **Show version**: To check if the password encryption service is enabled.

```plaintext
Switch# show running-config
Switch# show version
```

---

### Conclusion

By following this checklist and configuration guide, you can enhance the password security of your Cisco switch and protect it from unauthorized access. Regularly review and update your password policies and configurations to maintain a secure network environment.


