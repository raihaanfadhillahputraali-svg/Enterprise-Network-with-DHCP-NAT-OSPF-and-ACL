# Enterprise Network with DHCP, NAT, OSPF, and ACL

## Overview

This project simulates a small enterprise network using Cisco IOSv and IOSvL2 devices in PNETLab.

The network consists of multiple VLANs connected through a multilayer switch (MLS). Dynamic routing is provided using OSPF, centralized DHCP services are hosted on R1, and NAT overload allows internal networks to reach simulated external networks through an ISP router. ACLs are used to enforce basic security policies.

---

## Objectives

This project demonstrates:

- VLAN Segmentation
- Access Ports
- IEEE 802.1Q Trunking
- Switch Virtual Interfaces (SVI)
- Inter-VLAN Routing
- OSPF Dynamic Routing
- DHCP Server
- DHCP Relay (ip helper-address)
- NAT Overload (PAT)
- Basic Access Control Lists
- Enterprise Network Design

---

# Topology

<img width="864" height="782" alt="image" src="https://github.com/user-attachments/assets/56b6b2b6-6739-4094-afb4-a41c5cda25a0" />


---

## Network Topology

```text
                 ISP
          Lo0: 8.8.8.8/32
                   |
                  R1
          DHCP + NAT Overload
                   |
                  R2
                   |
                 MLS1
                /    \
              SW1    SW2
             /  \    /  \
           PC1 PC2 PC3 PC4
```

---

## VLAN Design

| VLAN | Department | Network |
|--------|------------|---------|
| 10 | IT | 192.168.10.0/24 |
| 20 | HR | 192.168.20.0/24 |
| 30 | Finance | 192.168.30.0/24 |
| 40 | Guest | 192.168.40.0/24 |

---

## Technologies Used

### Cisco IOSv

- OSPF
- DHCP Server
- NAT Overload
- Static Routes

### Cisco IOSvL2

- VLAN
- Trunking
- SVI
- Inter-VLAN Routing

### PNETLab

- Network Emulation Platform

---

## IP Addressing

Detailed IP addressing information can be found in:

```text
Topology/ip-addressing.xlsx
```

---

# Configuration Files

| Device | Configuration |
|----------|-------------|
| ISP | configs/ISP.txt |
| R1 | configs/R1.txt |
| R2 | configs/R2.txt |
| MLS1 | configs/MLS1.txt |
| SW1 | configs/SW1.txt |
| SW2 | configs/SW2.txt |

---

# Verification

## DHCP Verification

### Commands

```bash
show ip dhcp pool
show ip dhcp binding
```

### Result

<img width="626" height="585" alt="image" src="https://github.com/user-attachments/assets/eb8782f1-b449-4e82-81e1-c9b7a83f662a" />

---

## DHCP Relay Verification

### Command

```bash
show run interface vlan10
```

### Result

<img width="330" height="603" alt="image" src="https://github.com/user-attachments/assets/ffd6d376-ce2d-42c2-b7f6-defc2ed59759" />


---

## OSPF Verification

### Command

```bash
show ip ospf neighbor
```

### Result

<img width="682" height="77" alt="image" src="https://github.com/user-attachments/assets/ad13fefb-9c5e-40e1-8b13-c4d3c9f3f059" />
<img width="685" height="81" alt="image" src="https://github.com/user-attachments/assets/1d75c948-b004-48fd-b1e1-a361a1a04b25" />


---

## Routing Table Verification

### Command

```bash
show ip route
```

### Result

<img width="639" height="376" alt="image" src="https://github.com/user-attachments/assets/90ce399f-780a-4d30-849a-63bf0883eca2" />
<img width="630" height="376" alt="image" src="https://github.com/user-attachments/assets/cafd0902-5a36-4ff9-ab25-ecfab30d9129" />


---

## NAT Verification

### Command

```bash
show ip nat translations
```

### Result

<img width="617" height="330" alt="image" src="https://github.com/user-attachments/assets/01fb172e-a361-4d96-8ba1-91432dbd2c45" />
<img width="504" height="107" alt="image" src="https://github.com/user-attachments/assets/37f22b70-1223-4a4c-add1-8fe5e15660f0" />


---

## Connectivity Testing

### Successful Tests

```text
PC1 → PC2
PC1 → PC3
PC1 → PC4
PC1 → 8.8.8.8
```

### Result

<img width="489" height="469" alt="image" src="https://github.com/user-attachments/assets/8d0ae658-abac-44c8-b81e-198f78032da6" />


---

## ACL Verification

### Security Policy

Guest VLAN (40) is denied access to Finance VLAN (30).

### Allowed

```text
IT → Finance
HR → Finance
IT → HR
```
<img width="487" height="105" alt="image" src="https://github.com/user-attachments/assets/922dccc4-2571-4c1a-b80d-1b44359d6787" />
<img width="490" height="112" alt="image" src="https://github.com/user-attachments/assets/33ec6798-f4ae-4e30-9f29-f26f707429eb" />
<img width="493" height="108" alt="image" src="https://github.com/user-attachments/assets/b26150d7-5b10-4740-bc1c-f5df85b3e773" />



### Denied

```text
Guest → Finance
```

<img width="906" height="113" alt="image" src="https://github.com/user-attachments/assets/b8a95c59-db04-48cb-9e04-aa7225e338e4" />


---

# Troubleshooting

## Issue 1 – DHCP Pools Missing After Reload

### Problem

After restarting the routers, DHCP clients were unable to obtain IP addresses.

### Investigation

Checked DHCP configuration:

```bash
show ip dhcp pool
show ip dhcp binding
```

### Root Cause

DHCP pool configurations and excluded addresses were missing from the running configuration.

### Resolution

Reconfigured DHCP pools and excluded addresses:

```bash
ip dhcp excluded-address 192.168.10.1 192.168.10.9

ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
```

Saved the configuration:

```bash
copy running-config startup-config
```

### Verification

```bash
show ip dhcp binding
```

Confirmed that clients successfully obtained IP addresses.

---

# Lessons Learned

Through this project I learned:

- Centralized DHCP configuration
- DHCP relay using ip helper-address
- Dynamic routing with OSPF
- NAT overload configuration
- ACL-based security policies
- Troubleshooting DHCP issues
- Verification using Cisco IOS commands

---

# Future Improvements

Potential enhancements include:

- HSRP
- EtherChannel
- GRE Tunnel
- Site-to-Site VPN
- IPv6
- Cisco + MikroTik Integration
- FortiGate Firewall

---

# Conclusion

This project demonstrates an enterprise network implementation using Cisco IOSv and IOSvL2 devices.

The design incorporates VLAN segmentation, OSPF routing, DHCP services, NAT overload, and ACL-based security controls commonly found in enterprise environments.

This project serves as a foundation for more advanced enterprise and CCNP-level networking projects.
