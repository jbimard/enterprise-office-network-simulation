# 🚀 Quick Start Guide

Get up and running with the Enterprise Office Network Simulation in minutes!

---

## Prerequisites

Before you begin, ensure you have:

- ✅ **Cisco Packet Tracer 8.0+** installed ([Download here](https://www.netacad.com/courses/packet-tracer))
- ✅ Basic understanding of networking concepts
- ✅ 10 minutes of time to explore

---

## Installation Steps

### 1. Clone the Repository

```bash
git clone https://github.com/jbimard/enterprise-office-network-simulation.git
cd enterprise-office-network-simulation
```

**Or download ZIP:**
- Click the green "Code" button on GitHub
- Select "Download ZIP"
- Extract to your preferred location

### 2. Open Packet Tracer

1. Launch Cisco Packet Tracer application
2. Go to File → Open
3. Navigate to the cloned repository
4. Open `enterprise-network.pkt`

### 3. Explore the Network

The simulation will load with the complete network topology. You're ready to explore!

---

## Your First 5 Minutes

### 1. Switch Views (30 seconds)

Toggle between different perspectives:

- **Logical View**: Click the "Logical" tab at bottom
  - Shows network connections and IP addresses
  - Best for understanding data flow

- **Physical View**: Click the "Physical" tab
  - Shows actual device placement in offices
  - Navigate between IT Office, HR Office, and Wiring Closets

### 2. Inspect a Device (1 minute)

Click on **IT-Router** (the router in IT office):

1. Select the **CLI** tab
2. Press Enter to activate
3. Type: `show ip interface brief`
4. See all configured interfaces and IP addresses

### 3. Test Connectivity (2 minutes)

Click on an **IT Office PC**:

1. Go to **Desktop** tab
2. Click **Command Prompt**
3. Test connectivity:
   ```
   ping 192.168.1.1      → Ping the router (should succeed)
   ping 192.168.1.60     → Ping HR PC (should succeed)  
   ping 192.168.1.10     → Ping DNS server (should succeed)
   ```

### 4. View Device Configuration (1.5 minutes)

On **IT-Router**, view the complete configuration:

1. Click the device
2. Go to **CLI** tab
3. Type:
   ```
   enable
   show running-config
   ```
4. Scroll through to see VLANs, DHCP, interfaces, etc.

---

## Key Network Information

### 🌐 IP Addressing

| Device/Range | IP Address | Purpose |
|--------------|-----------|---------|
| IT Router | 192.168.1.1 | Default Gateway |
| IT Switch | 192.168.1.2 | Management |
| WLC | 192.168.1.3 | Wireless Controller |
| DNS Server | 192.168.1.10 | Name Resolution |
| File Server | 192.168.1.11 | File Storage |
| HR Switch | 192.168.1.20 | HR Management |
| IT PCs | 192.168.1.50-59 | IT Workstations |
| HR PCs | 192.168.1.60-80 | HR Workstations |
| HR Printer | 192.168.1.81 | Network Printer |
| DHCP Pool | 192.168.1.101-199 | Dynamic IPs |

### 🔐 Default Credentials

| Access Type | Username | Password |
|-------------|----------|----------|
| Enable Mode | - | `class123` |
| Console | - | `cisco123` |
| SSH/VTY | `admin` | `admin123` |

### 📡 Wireless Networks

| SSID | Security | VLAN | Password |
|------|----------|------|----------|
| Intercity-Employee | WPA2-Enterprise | 10 | (RADIUS) |
| Intercity-Guest | WPA2-Personal | 20 | `GuestPass2024!` |

### 🔀 VLANs

| VLAN | Name | Purpose |
|------|------|---------|
| 1 | Default/Management | Infrastructure devices |
| 2 | HR-Department | HR office devices |
| 10 | Employee-Wireless | Employee wireless clients |
| 20 | Guest-Wireless | Guest network (internet only) |

---

## Common Tasks

### Test VLAN Isolation

**Verify HR can access File Server:**
1. Click HR PC
2. Desktop → Command Prompt
3. `ping 192.168.1.11` ✅ Should succeed

**Verify Guest cannot access File Server:**
1. Connect device to Guest WiFi
2. Try: `ping 192.168.1.11` ❌ Should fail (blocked by ACL)

### View DHCP Leases

On **IT-Router**:
```cisco
enable
show ip dhcp binding
```

Shows all dynamically assigned IP addresses.

### Check Wireless Clients

On **WLC** (Wireless LAN Controller):
1. Click the WLC device
2. GUI tab
3. View connected wireless clients and statistics

### Trace Network Path

From any PC:
```
tracert 192.168.1.1
```

Shows the path packets take through the network.

---

## Simulation Mode (Advanced)

Packet Tracer's simulation mode lets you watch packets travel through the network!

### Enable Simulation

1. Click **Simulation** mode button (bottom right, stopwatch icon)
2. Click **Edit Filters** to choose protocol (ICMP, TCP, etc.)
3. Generate traffic (e.g., ping from a PC)
4. Click **Auto Capture/Play** to watch packets move

### What to Watch For

- Packets traveling through switches and routers
- VLAN tagging on trunk links
- ACLs blocking traffic
- DHCP request/reply sequence

---

## Troubleshooting Tips

### Device Shows Red "X"

- Check physical connections
- Ensure interface is not shutdown: `no shutdown`

### Cannot Ping Between Offices

- Verify default gateway on PCs
- Check router interfaces are up: `show ip interface brief`
- Verify routing: `show ip route`

### DHCP Not Working

- Check DHCP pool: `show ip dhcp pool`
- Verify DHCP service running
- On PC: `ipconfig /renew`

### Need More Help?

- 📖 Read the full [Configuration Guide](docs/configuration-guide.md)
- 🔧 Check the [Troubleshooting Guide](docs/troubleshooting-guide.md)
- 📄 Review [Full Documentation PDF](docs/Network_Infrastructure_Project_Documentation.pdf)

---

## Next Steps

Once you're comfortable with the basics:

1. **Modify Configurations**
   - Try changing IP addresses
   - Add new VLANs
   - Configure additional ACLs

2. **Break and Fix**
   - Intentionally misconfigure something
   - Use troubleshooting commands to identify the issue
   - Fix it and verify connectivity

3. **Expand the Network**
   - Add more PCs or servers
   - Create additional office locations
   - Implement redundancy (HSRP, multiple switches)

4. **Study the Security**
   - Review ACL configurations
   - Test wireless security
   - Verify VLAN isolation

---

## Learning Resources

### Cisco Resources
- [Cisco Packet Tracer Tutorials](https://www.netacad.com/courses/packet-tracer)
- [Cisco IOS Command Reference](https://www.cisco.com/c/en/us/support/ios-nx-os-software/index.html)

### Networking Concepts
- Subnetting and CIDR notation
- VLAN configuration and trunking
- Access Control Lists (ACLs)
- Wireless networking fundamentals

### This Project
- [Full README](README.md) - Complete project overview
- [Configuration Guide](docs/configuration-guide.md) - Detailed device configs
- [Troubleshooting Guide](docs/troubleshooting-guide.md) - Problem solving

---

## Questions or Issues?

- Open an issue on [GitHub](https://github.com/jbimard/enterprise-office-network-simulation/issues)
- Check existing documentation
- Review Cisco Packet Tracer help files

---

<div align="center">

**Happy Networking! 🎉**

Ready to dive deeper? Check out the [full documentation](README.md)!

</div>

---

**Document Version**: 1.0  
**Last Updated**: August 2025  
**Author**: Joseph Posas
