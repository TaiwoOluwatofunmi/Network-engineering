# Network Technical Documentation
## Enterprise Network Design and VLAN Configuration

**Platform:** Cisco Packet Tracer  
**Project Type:** Enterprise Network Simulation  
**Status:** Core network implementation and connectivity testing completed.

---

## 1. Introduction

This project involves designing and implementing a simulated enterprise network using Cisco Packet Tracer.

The network represents a small office environment with two logical departments: Staff and Guest.

The objective is to separate these departments using Virtual Local Area Networks (VLANs), provide automatic IP address allocation through DHCP, and enable communication between the different networks through inter-VLAN routing.

The project provides practical experience with Cisco networking devices, network configuration, IP addressing, switching, routing and connectivity troubleshooting.

## 2. Project Objectives

The objectives of this project are to:

1. Design a basic enterprise network topology.
2. Create and configure VLANs for network segmentation.
3. Assign switch access ports to the appropriate VLANs.
4. Configure trunk links between networking devices.
5. Understand and verify Spanning Tree Protocol (STP).
6. Implement inter-VLAN routing using Router-on-a-Stick.
7. Configure DHCP for automatic IP address allocation.
8. Perform network connectivity tests.
9. Verify device configurations using Cisco IOS commands.

## 3. Network Topology and Architecture

The simulated network consists of:

| Device | Quantity | Model |
|---|---:|---|
| Router | 1 | Cisco 2911 |
| Switches | 2 | Cisco 2960 |
| End devices | 6 | PCs |

### Physical connections

- R1 GigabitEthernet0/0 connects to SW1 GigabitEthernet0/1.
- SW1 GigabitEthernet0/2 connects to SW2 GigabitEthernet0/1.
- Three Staff PCs connect to SW1 FastEthernet0/1–0/3.
- Three Guest PCs connect to SW2 FastEthernet0/1–0/3.

### Network architecture

The Staff devices are connected to VLAN 10, while Guest devices are connected to VLAN 20.

The switches use trunk links to transport traffic belonging to multiple VLANs.

The router uses subinterfaces to provide routing between the two VLANs.

## 4. IP Addressing Scheme and VLAN Design

Two VLANs were created to provide logical separation between Staff and Guest devices.

| VLAN ID | VLAN Name | Network Address | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| 10 | STAFF | 192.168.10.0 | 255.255.255.0 | 192.168.10.1 |
| 20 | GUEST | 192.168.20.0 | 255.255.255.0 | 192.168.20.1 |

Both networks use a `/24` prefix.

A `/24` subnet provides 254 usable host addresses, excluding the network and broadcast addresses.

### Why VLANs were used

A Virtual Local Area Network (VLAN) allows a physical network to be divided into separate logical broadcast domains.

In this project, Staff and Guest devices are placed in different VLANs even though they share the same physical network infrastructure.

This improves network organisation and provides a foundation for applying different network policies.

## 5. VLAN Configuration

The VLANs were created on both switches.

### Configuration on SW1

```text
enable
configure terminal

vlan 10
name STAFF
exit
```

Staff access ports were assigned to VLAN 10:

```text
interface range fa0/1-3
switchport mode access
switchport access vlan 10
exit
```

### Configuration on SW2

```text
enable
configure terminal

vlan 20
name GUEST
exit
```

Guest access ports were assigned to VLAN 20:

```text
interface range fa0/1-3
switchport mode access
switchport access vlan 20
exit
```

### Explanation

An access port normally belongs to one VLAN and connects to an end device such as a computer or printer.

The `switchport mode access` command configures a port as an access port.

The `switchport access vlan` command assigns that port to the specified VLAN.

### Verification

```text
show vlan brief
```

This command displays VLAN IDs, VLAN names and their associated access ports.

## 6. Trunk Port Configuration

Trunk links were configured to carry traffic from multiple VLANs across a single physical connection.

IEEE 802.1Q was used for VLAN tagging.

### SW1 trunk configuration

```text
interface gigabitEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan 10,20
no shutdown
exit

interface gigabitEthernet 0/2
switchport mode trunk
switchport trunk allowed vlan 10,20
no shutdown
exit
```

### SW2 trunk configuration

```text
interface gigabitEthernet 0/1
switchport mode trunk
switchport trunk allowed vlan 10,20
no shutdown
exit
```

### Explanation

A trunk port carries traffic from multiple VLANs.

IEEE 802.1Q adds VLAN identification information to Ethernet frames so that switches can identify the VLAN to which each frame belongs.

The `switchport trunk allowed vlan 10,20` command specifies which VLANs are permitted across the trunk.

### Verification

```text
show interfaces trunk
```

The verification confirmed that the configured trunk interfaces were operating with 802.1Q encapsulation and allowing VLANs 10 and 20.

## 7. Spanning Tree Protocol (STP)

Spanning Tree Protocol is a Layer 2 protocol used to prevent switching loops in Ethernet networks.

Switching loops can cause problems such as broadcast storms, duplicate frames and unstable MAC address tables.

STP identifies a root bridge and determines which switch ports should forward or block traffic to maintain a loop-free topology.

### Verification commands

```text
show spanning-tree vlan 10
show spanning-tree vlan 20
```

### Observations

The verification output showed SW2 as the root bridge for both VLANs.

On SW1:

- GigabitEthernet0/2 operated as a Root Port in the forwarding state.
- GigabitEthernet0/1 operated as a Designated Port in the forwarding state.
- The Staff access ports were also forwarding.

The switch-to-switch and router-facing links were verified as forwarding for the relevant VLANs.

### Important terminology

- **Root Bridge:** The central reference switch selected by STP.
- **Root Port:** The port on a non-root switch with the best path towards the root bridge.
- **Designated Port:** The forwarding port selected for a network segment.
- **Forwarding State:** A port can forward normal network traffic.

## 8. Inter-VLAN Routing: Router-on-a-Stick

Devices in different VLANs belong to different IP subnets.

Communication between these subnets requires a Layer 3 device.

In this project, a Cisco 2911 router was configured to perform inter-VLAN routing using the Router-on-a-Stick method.

Router-on-a-Stick uses one physical router interface with multiple logical subinterfaces.

### Router configuration

The physical interface was enabled:

```text
interface gigabitEthernet 0/0
no shutdown
exit
```

Subinterface for VLAN 10:

```text
interface gigabitEthernet 0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit
```

Subinterface for VLAN 20:

```text
interface gigabitEthernet 0/0.20
encapsulation dot1Q 20
ip address 192.168.20.1 255.255.255.0
exit
```

### Explanation

The `encapsulation dot1Q` command associates each router subinterface with its corresponding VLAN tag.

Each subinterface receives an IP address that acts as the default gateway for its VLAN.

| Subinterface | VLAN | IP Address |
|---|---|---|
| G0/0.10 | 10 | 192.168.10.1 |
| G0/0.20 | 20 | 192.168.20.1 |

### How inter-VLAN routing works

When a Staff PC wants to communicate with a Guest PC:

1. The Staff PC identifies that the destination belongs to a different subnet.
2. It sends the traffic to its default gateway, 192.168.10.1.
3. The router receives the traffic through its VLAN 10 subinterface.
4. The router examines its routing table.
5. It forwards the packet through the VLAN 20 subinterface.
6. The packet travels through the switch to the destination Guest PC.

The reverse process applies when Guest devices communicate with Staff devices.

### Verification

```text
show ip interface brief
```

The physical router interface and both subinterfaces were verified as `up/up`.

## 9. Dynamic Host Configuration Protocol (DHCP)

DHCP is a network service that automatically assigns IP configuration information to devices.

Instead of manually assigning IP addresses to all six PCs, DHCP was configured on R1.

### DHCP excluded addresses

```text
ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp excluded-address 192.168.10.101 192.168.10.254

ip dhcp excluded-address 192.168.20.1 192.168.20.9
ip dhcp excluded-address 192.168.20.101 192.168.20.254
```

These commands reserve addresses outside the intended dynamic allocation range.

The intended DHCP allocation range is `.10` to `.100` in each subnet.

### Staff DHCP pool

```text
ip dhcp pool STAFF
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
dns-server 8.8.8.8
exit
```

### Guest DHCP pool

```text
ip dhcp pool GUEST
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
dns-server 8.8.8.8
exit
```

### Explanation

- `network` specifies the subnet served by the DHCP pool.
- `default-router` specifies the gateway given to clients.
- `dns-server` specifies the DNS server address given to clients.
- `ip dhcp excluded-address` reserves addresses from dynamic assignment.

### DHCP operation: DORA

DHCP commonly uses a four-stage process known as DORA:

1. **Discover:** The client broadcasts a request to locate a DHCP server.
2. **Offer:** The DHCP server offers an available IP address.
3. **Request:** The client requests the offered address.
4. **Acknowledgement:** The DHCP server confirms the lease.

### Verification

```text
show ip dhcp binding
```

The command displayed six active DHCP leases.

Observed allocations:

| VLAN | Assigned Addresses |
|---|---|
| STAFF | 192.168.10.10–192.168.10.12 |
| GUEST | 192.168.20.11–192.168.20.13 |

All six PCs successfully received IP addresses from their respective DHCP pools.

## 10. Network Testing and Verification

Connectivity tests were conducted to confirm that the network was operating as intended.

### Tests performed

| Test | Expected Result | Observed Result |
|---|---|---|
| Staff PC to Staff gateway | Successful | Passed |
| Staff PC to another Staff PC | Successful | Passed |
| Staff PC to Guest PC | Successful | Passed |
| Guest PC to Guest gateway | Successful | Passed |
| Guest PC to another Guest PC | Successful | Passed |
| Guest PC to Staff PC | Successful | Passed |

### Example ping commands

```text
ping 192.168.10.1
ping 192.168.10.11
ping 192.168.20.11
```

The tests returned successful replies with 0% packet loss in the recorded checks.

### Interpretation

Successful communication within the same VLAN confirms local Layer 2 connectivity.

Successful communication between Staff and Guest VLANs confirms that inter-VLAN routing is functioning.

The successful gateway tests also confirm that the router interfaces are reachable from the respective networks.

## 11. Network Troubleshooting and Verification Commands

The following Cisco IOS commands were used during configuration and verification.

| Command | Purpose |
|---|---|
| `show vlan brief` | View VLANs and access port assignments |
| `show interfaces trunk` | Verify trunk links |
| `show spanning-tree` | Inspect STP operation |
| `show ip interface brief` | Check router interface status and IP addresses |
| `show ip dhcp binding` | View DHCP address leases |
| `show running-config` | Display the active device configuration |
| `ping` | Test IP connectivity |
| `show ip route` | Inspect the router's routing table |

These commands provide useful information when troubleshooting switching, routing and DHCP issues.

## 12. Saving Device Configurations

The running configurations were saved to startup configuration on the router and switches.

```text
copy running-config startup-config
```

This stores the device configuration in NVRAM so that it can be retained after a reload.

The Packet Tracer simulation was also saved as a `.pkt` project file.

Separate text files containing the device configurations were exported for documentation and version-controlled project storage.

## 13. Project Results

The implementation achieved the following:

- A simulated enterprise network with two switches and one router.
- Two successfully configured VLANs.
- Access port and trunk port configuration.
- Verified STP operation.
- Successful Router-on-a-Stick implementation.
- Separate DHCP pools for Staff and Guest.
- Six successful DHCP leases.
- Successful same-VLAN and inter-VLAN connectivity tests.
- Saved device configurations and network simulation files.

The results demonstrate a functioning basic enterprise network with logical VLAN segmentation and inter-VLAN communication.

## 14. Future Improvements

The following enhancements can be explored in a separate project or future version:

- Access Control Lists (ACLs) for traffic filtering.
- Guest network isolation and security policies.
- Network monitoring and performance analysis.
- Network automation using Python.
- More advanced enterprise routing and switching configurations.

## 15. Conclusion

This project provided practical experience in designing, configuring and verifying a small enterprise network using Cisco Packet Tracer.

It demonstrated the relationship between switching, VLAN segmentation, trunking, STP, routing and DHCP.

The project also provided an opportunity to practise Cisco IOS commands and troubleshoot network connectivity.

The completed simulation serves as a foundation for further study in enterprise networking, network security and network automation.