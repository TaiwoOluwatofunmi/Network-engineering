# Enterprise Network Design and VLAN Configuration

![Cisco Packet Tracer](https://img.shields.io/badge/Tool-Cisco%20Packet%20Tracer-049FD9?style=for-the-badge&logo=cisco&logoColor=white)
![Networking](https://img.shields.io/badge/Domain-Computer%20Networking-blue?style=for-the-badge)
![VLAN](https://img.shields.io/badge/Technology-VLAN-orange?style=for-the-badge)
![DHCP](https://img.shields.io/badge/Service-DHCP-green?style=for-the-badge)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=for-the-badge)

## 📌 Project Overview

This project demonstrates the design and configuration of a small enterprise network using Cisco Packet Tracer.

The simulated network represents an office environment with two departments: Staff and Guest. Each department is assigned a separate Virtual Local Area Network (VLAN) to organise network traffic and create logical network segmentation.

A Cisco router provides inter-VLAN routing, while DHCP automatically assigns IP addresses to connected devices.

## 🎯 Project Objectives

- Design a basic enterprise network topology.
- Configure VLANs to separate Staff and Guest devices.
- Configure access and trunk ports on Cisco switches.
- Implement inter-VLAN routing using Router-on-a-Stick.
- Configure DHCP for automatic IP address assignment.
- Understand and verify Spanning Tree Protocol (STP).
- Test connectivity within and between VLANs.
- Document network configurations and verification results.

## 🖥️ Network Topology

The simulated network consists of:

| Device | Quantity | Model |
|---|---:|---|
| Router | 1 | Cisco 2911 |
| Switches | 2 | Cisco 2960 |
| End devices | 6 | PCs |

The Staff devices connect to SW1, while Guest devices connect to SW2. The switches are interconnected using a trunk link. SW1 also connects to the router through a trunk link.

## 🌐 IP Addressing and VLAN Design

| VLAN | Name | Network | Default Gateway |
|---|---|---|---|
| 10 | STAFF | 192.168.10.0/24 | 192.168.10.1 |
| 20 | GUEST | 192.168.20.0/24 | 192.168.20.1 |

Each VLAN operates in a separate IP subnet.

## ⚙️ Technologies and Configurations

### 1. VLAN Configuration
Created two VLANs:

- VLAN 10 — STAFF
- VLAN 20 — GUEST

Assigned switch access ports to their respective VLANs.

### 2. Trunking

Configured IEEE 802.1Q trunk links to carry traffic from multiple VLANs between switches and between the switch and router.

### 3. Spanning Tree Protocol (STP)

Verified STP operation for VLAN 10 and VLAN 20 to understand root bridge selection and forwarding port roles.

### 4. Inter-VLAN Routing

Implemented Router-on-a-Stick using router subinterfaces:

- GigabitEthernet0/0.10
- GigabitEthernet0/0.20

Each subinterface was assigned the appropriate VLAN encapsulation and default gateway address.

### 5. DHCP Configuration

Configured separate DHCP pools for Staff and Guest networks.

DHCP automatically provides connected PCs with IP addresses, subnet masks, default gateways and DNS server information.

## 🧪 Testing and Verification

The following tests were successfully performed:

- Verified VLAN membership using `show vlan brief`.
- Verified trunk links using `show interfaces trunk`.
- Inspected STP operation using `show spanning-tree`.
- Verified router interfaces using `show ip interface brief`.
- Confirmed six DHCP leases using `show ip dhcp binding`.
- Tested default gateway connectivity.
- Tested communication between devices within the same VLAN.
- Tested communication between Staff and Guest VLANs.

**Result:** All six PCs successfully received DHCP addresses, and the tested intra-VLAN and inter-VLAN connectivity checks passed.

## 🛠️ Tools Used

- Cisco Packet Tracer
- Cisco IOS CLI
- GitHub
- Markdown


## 📚 Learning Outcomes

Through this project, I developed practical experience with:

- Basic enterprise network design.
- VLAN segmentation and switch port configuration.
- Trunk links and 802.1Q encapsulation.
- STP fundamentals.
- Router-on-a-Stick configuration.
- DHCP services and IP addressing.
- Network troubleshooting and connectivity verification.

## 🚀 Future Improvements

Potential extensions include:

- Access Control Lists (ACLs) and network security.
- Network monitoring and troubleshooting.
- Network automation using Python.

---

**Project:** Enterprise Network Design and VLAN Configuration  
**Platform:** Cisco Packet Tracer