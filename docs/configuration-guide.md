# ⚙️ Configuration Guide

## Overview
This guide provides detailed configuration steps for all network devices in the Enterprise Office Network Simulation project.

---

## Table of Contents
- [IT Office Router Configuration](#it-office-router-configuration)
- [IT Office Switch Configuration](#it-office-switch-configuration)
- [HR Office Switch Configuration](#hr-office-switch-configuration)
- [Wireless LAN Controller (WLC) Configuration](#wireless-lan-controller-wlc-configuration)
- [Access Points Configuration](#access-points-configuration)
- [DHCP Configuration](#dhcp-configuration)
- [DNS Configuration](#dns-configuration)
- [VLAN Configuration](#vlan-configuration)
- [ACL Configuration](#acl-configuration)

---

## IT Office Router Configuration

### Device Information
- **Model**: Cisco 2911
- **Hostname**: IT-Router
- **IP Address**: 192.168.1.1
- **Role**: Default Gateway, DHCP Server, Inter-VLAN Routing

### Basic Configuration

```cisco
enable
configure terminal

! Set hostname
hostname IT-Router

! Configure console password
line console 0
password cisco123
login
logging synchronous
exit

! Configure enable secret
enable secret class123

! Configure SSH access
ip domain-name intercity.local
crypto key generate rsa
1024
ip ssh version 2

line vty 0 4
transport input ssh
login local
exit

! Create local user for SSH
username admin privilege 15 secret admin123
```

### Interface Configuration

```cisco
! GigabitEthernet0/0 - IT Office LAN
interface GigabitEthernet0/0
description Connection to IT-Switch
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

! GigabitEthernet0/1 - HR Office Connection
interface GigabitEthernet0/1
description Connection to HR-Switch
ip address 192.168.1.254 255.255.255.0
no shutdown
exit

! Serial0/3/0 - WAN Connection to Cloud
interface Serial0/3/0
description WAN Link to Cloud Data Center
ip address 10.0.0.1 255.255.255.252
clock rate 64000
no shutdown
exit
```

### DHCP Configuration

```cisco
! Exclude static IP addresses
ip dhcp excluded-address 192.168.1.1 192.168.1.100

! DHCP Pool for dynamic allocation
ip dhcp pool LAN-POOL
network 192.168.1.0 255.255.255.0
default-router 192.168.1.1
dns-server 192.168.1.10
lease 7
exit
```

### Static Routes

```cisco
! Default route to cloud
ip route 0.0.0.0 0.0.0.0 10.0.0.2

! Save configuration
end
write memory
```

---

## IT Office Switch Configuration

### Device Information
- **Model**: Cisco Catalyst 2960
- **Hostname**: IT-Switch
- **IP Address**: 192.168.1.2
- **Role**: Layer 2 Switching, VLAN Management

### Basic Configuration

```cisco
enable
configure terminal

hostname IT-Switch

! Configure management VLAN
interface vlan 1
ip address 192.168.1.2 255.255.255.0
no shutdown
exit

ip default-gateway 192.168.1.1

! Console and VTY configuration
line console 0
password cisco123
login
exit

enable secret class123

! SSH configuration
ip domain-name intercity.local
crypto key generate rsa
1024
ip ssh version 2

line vty 0 15
transport input ssh
login local
exit

username admin privilege 15 secret admin123
```

### VLAN Configuration

```cisco
! Create VLANs
vlan 1
name Default-Management
exit

vlan 2
name HR-Department
exit

vlan 10
name Employee-Wireless
exit

vlan 20
name Guest-Wireless
exit
```

### Port Configuration

```cisco
! Uplink to Router
interface FastEthernet0/1
description Uplink to IT-Router
switchport mode access
switchport access vlan 1
no shutdown
exit

! Ports for IT PCs (F0/2 - F0/22)
interface range FastEthernet0/2-22
description IT Department PCs
switchport mode access
switchport access vlan 1
spanning-tree portfast
no shutdown
exit

! Port for WLC
interface FastEthernet0/23
description Wireless LAN Controller
switchport mode access
switchport access vlan 1
no shutdown
exit

! Port for Access Point
interface FastEthernet0/24
description Lightweight Access Point
switchport mode access
switchport access vlan 1
no shutdown
exit

! Disable unused ports
interface range FastEthernet0/25-28
shutdown
exit

! Save configuration
end
write memory
```

---

## HR Office Switch Configuration

### Device Information
- **Model**: Cisco Catalyst 2960
- **Hostname**: HR-Switch
- **IP Address**: 192.168.1.20
- **Role**: Layer 2 Switching for HR Department

### Basic Configuration

```cisco
enable
configure terminal

hostname HR-Switch

! Configure management VLAN
interface vlan 2
ip address 192.168.1.20 255.255.255.0
no shutdown
exit

ip default-gateway 192.168.1.254

! Security configuration
line console 0
password cisco123
login
exit

enable secret class123

username admin privilege 15 secret admin123
```

### VLAN Configuration

```cisco
! Create VLANs
vlan 2
name HR-Department
exit
```

### Port Configuration

```cisco
! Uplink to IT Router
interface FastEthernet0/1
description Uplink to IT-Router
switchport mode access
switchport access vlan 2
no shutdown
exit

! Ports for HR PCs (F0/2 - F0/22)
interface range FastEthernet0/2-22
description HR Department PCs
switchport mode access
switchport access vlan 2
spanning-tree portfast
no shutdown
exit

! Port for HR Access Point
interface FastEthernet0/23
description HR Lightweight Access Point
switchport mode access
switchport access vlan 2
no shutdown
exit

! Port for HR Printer
interface FastEthernet0/24
description HR Network Printer
switchport mode access
switchport access vlan 2
no shutdown
exit

! Disable unused ports
interface range FastEthernet0/25-28
shutdown
exit

! Save configuration
end
write memory
```

---

## Wireless LAN Controller (WLC) Configuration

### Device Information
- **IP Address**: 192.168.1.3
- **Role**: Centralized wireless management

### Initial Setup (GUI-based)

1. **Access WLC Web Interface**
   - Open browser to: `https://192.168.1.3`
   - Login: `admin` / `admin123`

2. **Configure Management Interface**
   - IP Address: `192.168.1.3`
   - Subnet Mask: `255.255.255.0`
   - Default Gateway: `192.168.1.1`

3. **Create WLANs**

#### Employee SSID
- **SSID**: `Intercity-Employee`
- **Security**: WPA2-Enterprise
- **VLAN ID**: 10
- **Authentication**: RADIUS (if server available)

#### Guest SSID
- **SSID**: `Intercity-Guest`
- **Security**: WPA2-Personal
- **Pre-Shared Key**: `GuestPass2024!`
- **VLAN ID**: 20
- **Client Isolation**: Enabled

4. **Add Access Points**
   - Navigate to Wireless > Access Points
   - APs should auto-discover via CAPWAP
   - Assign APs to appropriate WLANs

---

## Access Points Configuration

### Device Information
- **Type**: Lightweight Access Points (LWAP)
- **Management**: Controlled by WLC via CAPWAP

### Configuration Steps

Access Points in lightweight mode don't require individual configuration. They:

1. **Auto-discover WLC** via DHCP option 43 or DNS
2. **Establish CAPWAP tunnel** to WLC
3. **Download configuration** from controller
4. **Broadcast SSIDs** as defined on WLC

### Verification

```cisco
! On WLC CLI
show ap summary
show wlan summary
show client summary
```

---

## DHCP Configuration

### On IT Router

```cisco
configure terminal

! Exclude static addresses
ip dhcp excluded-address 192.168.1.1 192.168.1.100

! DHCP Pool for wired/wireless clients
ip dhcp pool LAN-POOL
network 192.168.1.0 255.255.255.0
default-router 192.168.1.1
dns-server 192.168.1.10
domain-name intercity.local
lease 7
exit

! Save configuration
end
write memory
```

### Verification

```cisco
show ip dhcp binding
show ip dhcp pool
show ip dhcp server statistics
```

---

## DNS Configuration

### DNS Server Setup
- **IP Address**: 192.168.1.10
- **Type**: Internal DNS Server

### Common DNS Records

| Hostname | IP Address | Record Type |
|----------|-----------|-------------|
| router.intercity.local | 192.168.1.1 | A |
| switch-it.intercity.local | 192.168.1.2 | A |
| wlc.intercity.local | 192.168.1.3 | A |
| dns.intercity.local | 192.168.1.10 | A |
| fileserver.intercity.local | 192.168.1.11 | A |
| switch-hr.intercity.local | 192.168.1.20 | A |
| printer-hr.intercity.local | 192.168.1.81 | A |

---

## VLAN Configuration

### VLAN Summary

| VLAN ID | Name | Subnet | Gateway | Purpose |
|---------|------|--------|---------|---------|
| 1 | Default-Management | 192.168.1.0/24 | 192.168.1.1 | Infrastructure devices |
| 2 | HR-Department | 192.168.1.0/24 | 192.168.1.1 | HR office devices |
| 10 | Employee-Wireless | 192.168.1.0/24 | 192.168.1.1 | Employee wireless clients |
| 20 | Guest-Wireless | 192.168.1.0/24 | 192.168.1.1 | Guest wireless clients |

### Inter-VLAN Routing

Inter-VLAN routing is performed on the IT Router using sub-interfaces (if needed) or through a Layer 3 switch. In this implementation, all VLANs share the same subnet for simplicity, with ACLs providing security.

---

## ACL Configuration

### On IT Router

```cisco
configure terminal

! ACL for Guest VLAN - Internet only
ip access-list extended GUEST-ACL
deny ip 192.168.1.0 0.0.0.255 192.168.1.0 0.0.0.255
permit ip any any
exit

! ACL for HR VLAN - Restrict to HR resources
ip access-list extended HR-ACL
permit ip 192.168.1.60 0.0.0.31 192.168.1.10 0.0.0.1
permit ip 192.168.1.60 0.0.0.31 192.168.1.81 0.0.0.0
deny ip 192.168.1.60 0.0.0.31 192.168.1.0 0.0.0.255
permit ip any any
exit

! Apply ACLs to interfaces
interface GigabitEthernet0/1
ip access-group HR-ACL in
exit

! Save configuration
end
write memory
```

### Verification

```cisco
show access-lists
show ip interface GigabitEthernet0/1
```

---

## Security Best Practices

### Password Policy
- Enable secret: Minimum 8 characters
- Console password: Changed from default
- VTY password: SSH only, no Telnet
- Local user accounts: Strong passwords required

### Port Security
```cisco
! On switch ports
interface FastEthernet0/2
switchport port-security
switchport port-security maximum 2
switchport port-security violation restrict
switchport port-security mac-address sticky
exit
```

### Disable Unused Services
```cisco
no ip http server
no ip http secure-server
no cdp run
no service pad
```

### Enable Logging
```cisco
logging buffered 51200
logging console warnings
service timestamps log datetime msec
```

---

## Backup and Recovery

### Save Configurations
```cisco
! From privileged EXEC mode
copy running-config startup-config

! Or use shorthand
write memory
```

### Backup to TFTP Server
```cisco
copy running-config tftp:
! Enter TFTP server IP
! Enter filename
```

### Restore from TFTP
```cisco
copy tftp: running-config
! Enter TFTP server IP
! Enter filename
```

---

## Troubleshooting Commands

### Connectivity Testing
```cisco
ping 192.168.1.1
traceroute 192.168.1.10
```

### Interface Status
```cisco
show ip interface brief
show interfaces status
show interfaces GigabitEthernet0/0
```

### Routing Information
```cisco
show ip route
show ip protocols
```

### VLAN Information
```cisco
show vlan brief
show vlan id 2
show interfaces trunk
```

### DHCP Information
```cisco
show ip dhcp binding
show ip dhcp pool
show ip dhcp conflict
```

### Wireless Information (on WLC)
```cisco
show wlan summary
show ap summary
show client summary
```

---

## Additional Resources

- [Cisco Packet Tracer Help](https://www.netacad.com/courses/packet-tracer)
- [Cisco IOS Command Reference](https://www.cisco.com/c/en/us/support/ios-nx-os-software/ios-15-4m-t/products-command-reference-list.html)
- [VLAN Configuration Guide](https://www.cisco.com/c/en/us/td/docs/switches/lan/catalyst2960/software/release/12-2_55_se/configuration/guide/scg_2960/swvlan.html)

---

**Document Version**: 1.0  
**Last Updated**: August 2025  
**Author**: Joseph Posas
