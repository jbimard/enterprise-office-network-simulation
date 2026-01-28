# 📁 Project File Structure

This document outlines the recommended file structure for the Enterprise Office Network Simulation repository.

## Directory Tree

```
enterprise-office-network-simulation/
│
├── README.md                                  # Main project documentation
├── LICENSE                                    # MIT License file
├── .gitignore                                 # Git ignore file
│
├── enterprise-network.pkt                     # Main Packet Tracer simulation file
│
├── docs/                                      # Documentation directory
│   ├── Network_Infrastructure_Project_Documentation.pdf  # Full technical documentation
│   ├── configuration-guide.md                 # Device configuration guide
│   ├── troubleshooting-guide.md               # Troubleshooting reference
│   ├── quick-start-guide.md                   # Quick setup instructions
│   │
│   └── images/                                # Network diagrams and screenshots
│       ├── network-topology-preview.png       # High-level topology diagram
│       ├── logical-topology.png               # Logical network diagram
│       ├── physical-layout-it.png             # IT office physical layout
│       ├── physical-layout-hr.png             # HR office physical layout
│       ├── wiring-closet-it.png               # IT wiring closet diagram
│       ├── wiring-closet-hr.png               # HR wiring closet diagram
│       ├── vlan-diagram.png                   # VLAN architecture diagram
│       ├── ip-addressing-scheme.png           # IP addressing visual
│       ├── security-architecture.png          # Security implementation diagram
│       └── wireless-coverage-map.png          # Wireless coverage visualization
│
├── configs/                                   # Device configuration files
│   ├── routers/
│   │   └── IT-Router-config.txt               # IT Router running configuration
│   │
│   ├── switches/
│   │   ├── IT-Switch-config.txt               # IT Switch running configuration
│   │   └── HR-Switch-config.txt               # HR Switch running configuration
│   │
│   ├── wireless/
│   │   ├── WLC-config.txt                     # Wireless Controller configuration
│   │   └── ap-settings.txt                    # Access Point settings
│   │
│   └── servers/
│       ├── dhcp-server-config.txt             # DHCP server configuration
│       └── dns-server-config.txt              # DNS server configuration
│
├── scripts/                                   # Configuration scripts and commands
│   ├── initial-setup/
│   │   ├── router-basic-config.txt            # Basic router configuration commands
│   │   ├── switch-basic-config.txt            # Basic switch configuration commands
│   │   └── security-hardening.txt             # Security hardening commands
│   │
│   ├── vlan-setup/
│   │   ├── create-vlans.txt                   # VLAN creation commands
│   │   └── assign-ports.txt                   # Port-to-VLAN assignment commands
│   │
│   ├── dhcp-setup/
│   │   ├── dhcp-pool-config.txt               # DHCP pool configuration
│   │   └── dhcp-exclusions.txt                # DHCP excluded addresses
│   │
│   ├── acl-setup/
│   │   ├── guest-acl.txt                      # Guest VLAN ACL
│   │   ├── hr-acl.txt                         # HR VLAN ACL
│   │   └── management-acl.txt                 # Management access ACL
│   │
│   └── wireless-setup/
│       ├── wlc-initial-config.txt             # WLC initial configuration
│       ├── employee-ssid-config.txt           # Employee SSID setup
│       └── guest-ssid-config.txt              # Guest SSID setup
│
├── testing/                                   # Test plans and results
│   ├── connectivity-tests.md                  # Connectivity testing procedures
│   ├── security-tests.md                      # Security validation tests
│   ├── performance-tests.md                   # Performance testing procedures
│   └── test-results/
│       ├── connectivity-results.txt           # Connectivity test outputs
│       ├── ping-tests.txt                     # Ping test results
│       └── traceroute-outputs.txt             # Traceroute results
│
├── diagrams/                                  # Source files for diagrams (optional)
│   ├── network-topology.drawio                # Draw.io topology diagram
│   ├── vlan-architecture.drawio               # Draw.io VLAN diagram
│   └── logical-design.pptx                    # PowerPoint diagrams
│
└── reference/                                 # Reference materials
    ├── cisco-commands-reference.md            # Quick command reference
    ├── ip-addressing-plan.xlsx                # IP addressing spreadsheet
    ├── device-inventory.md                    # Device inventory list
    └── cable-labeling-scheme.md               # Cable labeling conventions
```

## File Descriptions

### Root Level Files

| File | Purpose |
|------|---------|
| `README.md` | Main project documentation with overview, setup instructions, and links |
| `LICENSE` | MIT License (or your chosen license) |
| `.gitignore` | Specifies files Git should ignore (e.g., .DS_Store, Thumbs.db) |
| `enterprise-network.pkt` | The actual Cisco Packet Tracer simulation file |

### docs/ Directory

Contains all human-readable documentation:

- **PDF Documentation**: Official project documentation converted from Word
- **Markdown Guides**: Configuration and troubleshooting guides
- **images/**: All screenshots, diagrams, and visual assets

### configs/ Directory

Stores exported device configurations organized by device type:

- **routers/**: Router running-config exports
- **switches/**: Switch running-config exports  
- **wireless/**: WLC and AP configurations
- **servers/**: Server configuration files

### scripts/ Directory

Contains reusable configuration command sets:

- Organized by function (initial setup, VLANs, DHCP, ACLs, wireless)
- Copy-paste ready command blocks
- Useful for quick deployment or recovery

### testing/ Directory

Test plans and validation results:

- Test procedures for connectivity, security, performance
- Actual test outputs and results
- Helps demonstrate thoroughness to recruiters

### diagrams/ Directory (Optional)

Source files for network diagrams:

- Editable diagrams (Draw.io, Visio, PowerPoint)
- Allows easy updates and modifications
- Keep separate from final PNG exports in docs/images/

### reference/ Directory (Optional)

Additional reference materials:

- Quick command references
- IP addressing plans
- Device inventories
- Labeling conventions

## .gitignore Template

Create a `.gitignore` file in the root directory:

```gitignore
# Operating System Files
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# Packet Tracer Backup Files
*.pkt~
*.pkz~

# Temporary Files
*.tmp
*.temp
*.bak
*.swp
*~

# Log Files
*.log

# Personal Notes (if you keep any private notes)
personal-notes/
private/

# IDE Files
.vscode/
.idea/
*.sublime-project
*.sublime-workspace
```

## How to Populate This Structure

### 1. Extract Configurations from Packet Tracer

For each device:
1. Open device in Packet Tracer
2. Go to CLI tab
3. Enter: `show running-config`
4. Copy output to appropriate config file

### 2. Export Diagrams as Images

1. In Packet Tracer, use File > Export > Image
2. Export logical and physical views
3. Save to `docs/images/`
4. Ensure consistent naming

### 3. Create Command Scripts

From your configuration files:
1. Extract common command sequences
2. Create thematic scripts (VLANs, DHCP, etc.)
3. Add comments for clarity
4. Save to `scripts/` subdirectories

### 4. Document Testing

As you test the network:
1. Record test procedures in markdown
2. Save outputs (ping, traceroute, show commands)
3. Document results and any issues found
4. Store in `testing/` directory

## Best Practices

### Naming Conventions

- **Use descriptive names**: `IT-Router-config.txt` not `config1.txt`
- **Include dates for versions**: `IT-Router-config-2025-08-17.txt`
- **Use lowercase with hyphens**: `guest-ssid-config.txt` not `Guest_SSID_Config.txt`
- **Be consistent**: Pick a convention and stick to it

### Documentation

- **Keep README.md updated**: Always reflect current project state
- **Link between documents**: Cross-reference related docs
- **Use relative paths**: `[Config Guide](docs/configuration-guide.md)`
- **Include screenshots**: Visual aids help understanding

### Version Control

- **Commit frequently**: Small, logical commits
- **Write clear commit messages**: "Add VLAN configuration script"
- **Use branches for experiments**: Keep main branch stable
- **Tag releases**: Use Git tags for versions (v1.0, v1.1, etc.)

### For Recruiters

To make your repository recruiter-friendly:

1. **Strong README.md**: Clear overview, technologies, skills demonstrated
2. **Visual appeal**: Include diagrams and screenshots
3. **Professional documentation**: Well-formatted, error-free
4. **Complete project**: All files present and organized
5. **Evidence of testing**: Show you validated your work
6. **Clean commits**: Professional commit history

## Example Git Commands

### Initial Setup

```bash
# Initialize repository
git init
git add .
git commit -m "Initial commit: Enterprise network simulation"

# Create GitHub repository and push
git remote add origin https://github.com/jbimard/enterprise-office-network-simulation.git
git branch -M main
git push -u origin main
```

### Adding Files

```bash
# Add new configuration
git add configs/routers/IT-Router-config.txt
git commit -m "Add IT Router configuration"

# Add documentation
git add docs/configuration-guide.md
git commit -m "Add detailed configuration guide"

# Push changes
git push origin main
```

### Creating Releases

```bash
# Tag a release
git tag -a v1.0 -m "Version 1.0: Complete enterprise network"
git push origin v1.0
```

---

**Remember**: A well-organized repository demonstrates professionalism and attention to detail - key qualities recruiters look for!

**Document Version**: 1.0  
**Last Updated**: August 2025  
**Author**: Joseph Posas
