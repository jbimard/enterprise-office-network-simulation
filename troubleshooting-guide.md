# 🔧 Troubleshooting Guide

## Overview
This guide provides solutions to common issues encountered in the Enterprise Office Network Simulation project.

---

## Table of Contents
- [Connectivity Issues](#connectivity-issues)
- [DHCP Problems](#dhcp-problems)
- [VLAN Issues](#vlan-issues)
- [Wireless Connectivity Problems](#wireless-connectivity-problems)
- [Routing Issues](#routing-issues)
- [Switch Problems](#switch-problems)
- [Security Issues](#security-issues)
- [Performance Problems](#performance-problems)

---

## Connectivity Issues

### Problem: Cannot ping between IT and HR offices

**Symptoms:**
- IT PC cannot reach HR PC
- Ping times out

**Possible Causes & Solutions:**

1. **Check interface status**
   ```cisco
   show ip interface brief
   ```
   - Ensure all interfaces are "up/up"
   - If down, check physical connections in Packet Tracer
   - Use `no shutdown` on interface if administratively down

2. **Verify IP addressing**
   ```cisco
   show running-config interface GigabitEthernet0/0
   ```
   - Confirm correct IP addresses on router interfaces
   - Check subnet masks match

3. **Check default gateway on PCs**
   - IT PCs should have gateway: 192.168.1.1
   - HR PCs should have gateway: 192.168.1.1 or 192.168.1.254
   - Verify with `ipconfig` on PC

4. **Test routing**
   ```cisco
   show ip route
   ```
   - Ensure connected routes appear
   - Verify static routes if configured

---

### Problem: No internet connectivity from workstations

**Symptoms:**
- Cannot reach external networks
- Can ping internal devices only

**Solutions:**

1. **Check default route on router**
   ```cisco
   show ip route
   0.0.0.0/0 via 10.0.0.2
   ```
   - If missing, add: `ip route 0.0.0.0 0.0.0.0 10.0.0.2`

2. **Verify Cloud connection**
   ```cisco
   ping 10.0.0.2
   ```
   - Check Serial interface status
   - Ensure clock rate is set on DCE side

3. **Check NAT configuration (if applicable)**
   ```cisco
   show ip nat translations
   show ip nat statistics
   ```

---

## DHCP Problems

### Problem: Devices not receiving IP addresses

**Symptoms:**
- PCs show 169.254.x.x (APIPA) address
- "DHCP request failed" message

**Solutions:**

1. **Verify DHCP pool configuration**
   ```cisco
   show ip dhcp pool
   ```
   - Ensure pool network matches interface subnet
   - Check that pool has available addresses

2. **Check excluded addresses**
   ```cisco
   show running-config | section dhcp
   ```
   - Verify excluded range doesn't overlap with DHCP pool

3. **Test DHCP service**
   ```cisco
   show ip dhcp binding
   ```
   - Should show active leases
   - If empty, DHCP may not be running

4. **Check interface DHCP relay (if using separate DHCP server)**
   ```cisco
   show running-config interface GigabitEthernet0/0
   ```
   - Look for `ip helper-address` command

5. **Renew DHCP lease on PC**
   - On PC command prompt:
   ```
   ipconfig /release
   ipconfig /renew
   ```

---

### Problem: DHCP pool exhausted

**Symptoms:**
- Some devices get IP, others don't
- "Address pool exhausted" in logs

**Solutions:**

1. **Check DHCP bindings**
   ```cisco
   show ip dhcp binding
   ```
   - Look for expired or stale leases

2. **Clear conflicted addresses**
   ```cisco
   clear ip dhcp conflict *
   ```

3. **Increase pool size**
   ```cisco
   configure terminal
   ip dhcp excluded-address 192.168.1.1 192.168.1.50
   ip dhcp pool LAN-POOL
   network 192.168.1.0 255.255.255.0
   ```

---

## VLAN Issues

### Problem: Devices on same switch cannot communicate

**Symptoms:**
- PCs on different VLANs cannot ping
- Traffic not crossing VLAN boundary

**Solutions:**

1. **Verify VLAN configuration**
   ```cisco
   show vlan brief
   ```
   - Confirm VLANs exist
   - Check port assignments

2. **Check port VLAN assignment**
   ```cisco
   show interfaces FastEthernet0/2 switchport
   ```
   - Verify "Access Mode VLAN" is correct

3. **Enable inter-VLAN routing**
   - On router, ensure sub-interfaces configured
   - Or use Layer 3 switch with `ip routing`

4. **Check trunk links**
   ```cisco
   show interfaces trunk
   ```
   - Ensure VLANs allowed on trunk
   - Verify native VLAN matches

---

### Problem: VLAN traffic leaking between departments

**Symptoms:**
- HR can access IT resources
- Guests can reach internal servers

**Solutions:**

1. **Verify VLAN assignments**
   ```cisco
   show vlan brief
   show mac address-table
   ```

2. **Check ACL configuration**
   ```cisco
   show access-lists
   show ip interface GigabitEthernet0/1
   ```
   - Ensure ACLs applied to correct interfaces
   - Verify ACL logic permits only intended traffic

3. **Test ACL functionality**
   ```cisco
   show access-lists GUEST-ACL
   ```
   - Check hit counters to see if ACL is matching traffic

---

## Wireless Connectivity Problems

### Problem: Cannot connect to wireless network

**Symptoms:**
- SSID not visible
- Connection authentication fails

**Solutions:**

1. **Check SSID broadcast**
   - On WLC: Wireless > WLANs
   - Verify SSID is enabled
   - Check "Broadcast SSID" is enabled

2. **Verify wireless security settings**
   - Ensure WPA2 is configured correctly
   - Check pre-shared key matches (for WPA2-Personal)
   - Verify RADIUS server (for WPA2-Enterprise)

3. **Check Access Point status**
   ```cisco
   show ap summary
   ```
   - Ensure APs are "Registered" state
   - Check AP is associated with correct WLAN

4. **Verify CAPWAP connectivity**
   - AP must reach WLC on IP 192.168.1.3
   - Check DHCP option 43 or DNS for WLC discovery

5. **Check VLAN assignment**
   - Verify WLAN is tagged with correct VLAN
   - On WLC: WLAN > Edit > Advanced tab

---

### Problem: Wireless clients get IP but no network access

**Symptoms:**
- Device shows connected to WiFi
- Has IP address but cannot reach anything

**Solutions:**

1. **Check default gateway**
   - Wireless clients should receive 192.168.1.1 as gateway
   - Verify DHCP pool configuration

2. **Verify VLAN routing**
   - Ensure router has route to wireless VLAN
   - Check `show ip route`

3. **Test from client**
   ```
   ping 192.168.1.1  (gateway)
   ping 192.168.1.10 (DNS server)
   ping 8.8.8.8      (external)
   ```

4. **Check ACLs**
   - Guest VLAN should block internal access
   - Employee VLAN should allow internal access

---

## Routing Issues

### Problem: Some networks unreachable

**Symptoms:**
- Can ping some subnets but not others
- Traceroute stops at router

**Solutions:**

1. **Check routing table**
   ```cisco
   show ip route
   ```
   - Verify all networks have routes (Connected, Static, or Dynamic)

2. **Add missing static routes**
   ```cisco
   configure terminal
   ip route 192.168.2.0 255.255.255.0 10.0.0.2
   ```

3. **Verify routing protocol** (if using OSPF/EIGRP)
   ```cisco
   show ip protocols
   show ip ospf neighbor
   ```

4. **Check route metrics**
   - Ensure best path is selected
   - Use `traceroute` to verify path

---

### Problem: Routing loops or suboptimal paths

**Symptoms:**
- Packets taking inefficient routes
- Traceroute shows loops

**Solutions:**

1. **Check for duplicate routes**
   ```cisco
   show ip route
   ```
   - Remove redundant static routes

2. **Verify administrative distance**
   - Static routes: AD 1
   - OSPF: AD 110
   - EIGRP: AD 90

3. **Enable routing protocol debugging** (use carefully)
   ```cisco
   debug ip routing
   ```

---

## Switch Problems

### Problem: Ports not forwarding traffic

**Symptoms:**
- Link light green but no traffic
- Port shows "up" but not working

**Solutions:**

1. **Check port status**
   ```cisco
   show interfaces FastEthernet0/2 status
   show interfaces FastEthernet0/2 switchport
   ```

2. **Verify spanning-tree status**
   ```cisco
   show spanning-tree interface FastEthernet0/2
   ```
   - Port should be in "FWD" (forwarding) state
   - If "BLK" (blocking), may be intentional STP behavior

3. **Enable PortFast** (for end-device ports)
   ```cisco
   configure terminal
   interface FastEthernet0/2
   spanning-tree portfast
   ```

4. **Check for err-disabled**
   ```cisco
   show interfaces status err-disabled
   ```
   - If err-disabled, find cause and recover:
   ```cisco
   configure terminal
   interface FastEthernet0/2
   shutdown
   no shutdown
   ```

---

### Problem: Broadcast storms

**Symptoms:**
- Network extremely slow
- High CPU on switches
- Spanning-tree constantly recalculating

**Solutions:**

1. **Check for loops**
   - Verify physical topology in Packet Tracer
   - Ensure no redundant connections without STP

2. **Enable BPDU Guard**
   ```cisco
   configure terminal
   interface FastEthernet0/2
   spanning-tree bpduguard enable
   ```

3. **Check STP status**
   ```cisco
   show spanning-tree summary
   show spanning-tree root
   ```

4. **Enable storm control**
   ```cisco
   interface range FastEthernet0/1-24
   storm-control broadcast level 50
   ```

---

## Security Issues

### Problem: Unauthorized access to management interfaces

**Symptoms:**
- Unknown logins in logs
- Configuration changes without authorization

**Solutions:**

1. **Enable strong authentication**
   ```cisco
   enable secret MyStr0ngP@ssw0rd!
   username admin privilege 15 secret Adm1nP@ss!
   ```

2. **Restrict VTY access**
   ```cisco
   line vty 0 4
   transport input ssh
   access-class 10 in
   ```

3. **Create management ACL**
   ```cisco
   access-list 10 permit 192.168.1.0 0.0.0.255
   access-list 10 deny any log
   ```

4. **Enable logging**
   ```cisco
   logging buffered 51200
   logging console warnings
   service timestamps log datetime msec
   ```

---

### Problem: Guest users accessing internal resources

**Symptoms:**
- Guest devices can ping internal servers
- HR data accessible from guest network

**Solutions:**

1. **Verify VLAN isolation**
   ```cisco
   show vlan brief
   ```
   - Ensure guests on VLAN 20

2. **Check ACL configuration**
   ```cisco
   show access-lists GUEST-ACL
   ```
   - Should deny internal network access
   - Permit only internet-bound traffic

3. **Test from guest device**
   - Try pinging internal server (should fail)
   - Try pinging 8.8.8.8 (should succeed)

4. **Reconfigure ACL if needed**
   ```cisco
   ip access-list extended GUEST-ACL
   deny ip 192.168.1.0 0.0.0.255 192.168.1.0 0.0.0.255
   permit ip any any
   ```

---

## Performance Problems

### Problem: Slow network performance

**Symptoms:**
- High latency
- Packet loss
- Slow file transfers

**Solutions:**

1. **Check interface statistics**
   ```cisco
   show interfaces GigabitEthernet0/0
   ```
   - Look for errors, collisions, or drops

2. **Verify duplex/speed settings**
   ```cisco
   show interfaces GigabitEthernet0/0 status
   ```
   - Ensure auto-negotiation or manually set to 1000/full

3. **Check CPU usage**
   ```cisco
   show processes cpu
   ```
   - High CPU may indicate issues

4. **Monitor bandwidth utilization**
   - Use Packet Tracer's simulation mode
   - Check for saturated links

5. **Implement QoS** (if needed for voice/video)
   ```cisco
   class-map match-all VOICE
   match ip dscp ef
   ```

---

### Problem: Intermittent connectivity

**Symptoms:**
- Connections drop randomly
- Ping works sometimes, fails other times

**Solutions:**

1. **Check for physical issues**
   - In Packet Tracer, verify cable connections
   - Check for "red X" on links

2. **Monitor interface flapping**
   ```cisco
   show logging | include changed state
   ```

3. **Check spanning-tree stability**
   ```cisco
   show spanning-tree summary
   show spanning-tree inconsistentports
   ```

4. **Verify DHCP lease time**
   - Short leases may cause renewals during activity
   - Consider extending lease time

---

## Diagnostic Commands Quick Reference

### Layer 1/2 Diagnostics
```cisco
show interfaces status
show interfaces FastEthernet0/1
show mac address-table
show cdp neighbors
show spanning-tree
```

### Layer 3 Diagnostics
```cisco
show ip interface brief
show ip route
show ip arp
ping 192.168.1.1
traceroute 192.168.1.10
```

### VLAN Diagnostics
```cisco
show vlan brief
show interfaces trunk
show interfaces switchport
```

### DHCP Diagnostics
```cisco
show ip dhcp binding
show ip dhcp pool
show ip dhcp conflict
show ip dhcp server statistics
```

### Wireless Diagnostics (WLC)
```cisco
show wlan summary
show ap summary
show client summary
show mobility summary
```

### Security Diagnostics
```cisco
show access-lists
show ip access-lists
show port-security
show logging
```

---

## Getting Help

If you encounter issues not covered in this guide:

1. **Check Cisco documentation**
   - [Cisco Packet Tracer Help](https://www.netacad.com/courses/packet-tracer)
   - [Cisco IOS Documentation](https://www.cisco.com/c/en/us/support/ios-nx-os-software/index.html)

2. **Review project documentation**
   - [Configuration Guide](configuration-guide.md)
   - [Full Project Documentation](docs/Network_Infrastructure_Project_Documentation.pdf)

3. **Use simulation mode in Packet Tracer**
   - Step through packet flow
   - Identify where traffic stops

4. **Open an issue on GitHub**
   - Provide detailed description
   - Include configuration outputs
   - Describe troubleshooting steps already taken

---

**Document Version**: 1.0  
**Last Updated**: August 2025  
**Author**: Joseph Posas
