# Multi-Floor Enterprise Network

## 1. Project Overview

This project demonstrates the design and implementation of a redundant multi-floor enterprise network infrastructure using **Cisco Packet Tracer**.

The network is designed to provide reliable connectivity, efficient traffic management, VLAN-based segmentation, centralized DHCP services, and secure network administration across multiple floors and departments.

The architecture incorporates dual ISP connections, two core multilayer switches, redundant network links, Layer 2 and Layer 3 technologies, and network security mechanisms to improve network availability and fault tolerance.

## 2. Network Topology

The network consists of two ISP routers, two core multilayer switches, and access switches connecting end devices across different floors and departments.

## 3. Key Network Technologies

The following technologies are used in the network architecture:

- **VLANs:** Logical network segmentation across departments.
- **Inter-VLAN Routing:** Communication between different VLANs using multilayer switching.
- **Rapid PVST+:** Spanning Tree Protocol for loop prevention and redundant path management.
- **HSRP:** Default gateway redundancy and failover.
- **LACP EtherChannel:** Link aggregation for increased bandwidth and link redundancy.
- **DHCP:** Dynamic IP address allocation for network clients.
- **SSH:** Secure remote network device management.
- **Access Control Lists (ACLs):** Network traffic filtering and guest network isolation.
- **PortFast and BPDU Guard:** Layer 2 access-port protection.

## 4. Core High Availability and Switching Redundancy

### Dual Core Multilayer Switches

Two multilayer switches, CORE-S1 and CORE-S2, provide the core switching and routing infrastructure.

Both switches support Layer 3 switching and inter-VLAN routing through Switched Virtual Interfaces (SVIs).

The `ip routing` command enables Layer 3 routing functionality on supported multilayer switches.

### Rapid PVST+ Load Balancing

Rapid PVST+ provides loop prevention and redundant link management.

The root bridge roles are distributed between the two core switches:

| Core Switch | Primary Root Bridge | Secondary Root Bridge |
|---|---|---|
| CORE-S1 | VLAN 10, 20, 25 | VLAN 30, 40, 50 |
| CORE-S2 | VLAN 30, 40, 50 | VLAN 10, 20, 25 |

This split-root design distributes spanning-tree root responsibilities across both core switches.

### HSRP Gateway Redundancy

Hot Standby Router Protocol (HSRP) provides default gateway redundancy for the VLANs.

- CORE-S1: Active gateway, priority 110.
- CORE-S2: Standby gateway, priority 100.
- Virtual gateway addresses: `.254` in each VLAN subnet.

If the active core switch becomes unavailable, the standby core switch can take over the virtual gateway role.

### LACP EtherChannel

Link Aggregation Control Protocol (LACP) combines multiple physical links into a logical EtherChannel.

The configuration uses:

`channel-group mode active`

Benefits include:

- Increased aggregate link bandwidth.
- Link-level redundancy.
- Improved network availability.
- Reduced dependence on a single physical connection.

## 5. Dual ISP Connectivity

The enterprise network connects to two separate ISP routers to provide redundant external connectivity.

### ISP Connections

| Connection | Network Address | Connected Devices |
|---|---|---|
| ISP 1 | 203.0.113.0/30 | ISP 1 ↔ CORE-S1 |
| ISP 2 | 203.0.113.4/30 | ISP 2 ↔ CORE-S2 |

Default gateway configurations are implemented on both ISP and core network sides to establish connectivity between the enterprise network and external networks.

This dual-ISP architecture provides an alternative external connectivity path.

## 6. Centralized DHCP Services

The core multilayer switches provide DHCP services for the internal VLANs.

DHCP pools are configured on both CORE-S1 and CORE-S2 to support dynamic IP address allocation.

DHCP configuration provides:

- Automatic IP address allocation.
- Subnet mask distribution.
- Default gateway assignment.
- DNS server information.
- Exclusion of reserved IP address ranges.

The DHCP pools distribute the HSRP virtual gateway addresses as the default routers for network clients.

## 7. VLAN Segmentation and IP Addressing

VLAN segmentation separates network traffic according to departments and organizational requirements.

### VLAN and Subnet Allocation

| VLAN ID | VLAN Name | Network Address | Default Gateway |
|---|---|---|---|
| 10 | Admin | 192.168.10.0/24 | 192.168.10.254 |
| 20 | Engineering | 192.168.20.0/24 | 192.168.20.254 |
| 25 | Staff | 192.168.25.0/24 | 192.168.25.254 |
| 30 | IT | 192.168.30.0/24 | 192.168.30.254 |
| 40 | Management | 192.168.40.0/24 | 192.168.40.254 |
| 50 | Guest | 192.168.50.0/24 | 192.168.50.254 |

### IP Address Reservation

Reserved IP ranges are maintained to prevent conflicts with statically assigned infrastructure devices.

| VLAN | Reserved IP Addresses |
|---|---|
| Admin | 192.168.10.250 – 192.168.10.254 |
| Engineering | 192.168.20.250 – 192.168.20.254 |
| Staff | 192.168.25.250 – 192.168.25.254 |
| IT | 192.168.30.250 – 192.168.30.254 |
| Management | 192.168.40.10 – 192.168.40.15 and 192.168.40.250 – 192.168.40.254 |
| Guest | 192.168.50.250 – 192.168.50.254 |

## 8. Security and Access Control

Network security mechanisms are incorporated to protect network devices, restrict unauthorized access, and improve Layer 2 security.

### Console and Password Security

- Privileged EXEC access protected using `enable secret`.
- Console authentication configured using `line con 0`.
- Console session timeouts configured using `exec-timeout`.
- Local authentication credentials used for device administration.

### Secure SSH Remote Management

Secure Shell (SSH) is used instead of Telnet for encrypted remote device management.

Configuration includes:

- Domain name configuration.
- RSA key generation.
- SSH version 2.
- Local username and password authentication.
- VTY access restricted to SSH using `transport input ssh`.

### PortFast and BPDU Guard

Layer 2 access-port protection is implemented using PortFast and BPDU Guard.

**PortFast:**
- Allows end-device ports to transition quickly into the forwarding state.
- Reduces connection delays for end devices.

**BPDU Guard:**
- Protects access ports against unauthorized switch connections.
- Places a port into an error-disabled state when a BPDU is received on a protected PortFast port.

### Guest Network Isolation

VLAN 50 is designated for guest network access.

Access Control Lists (ACLs) are configured to restrict guest devices from accessing internal enterprise networks while permitting authorized external network access.

### Access Port Configuration

User-facing switch ports are configured using access mode:

- `switchport mode access`
- `switchport access vlan X`

This ensures end devices are assigned to their designated VLANs.

## 9. Project Files

| File | Description |
|---|---|
| `Multi-Floor-Switching-Network.pkt` | Cisco Packet Tracer network project |
| `topology.png` | Logical network topology diagram |
| `configurations.txt` | Network device CLI configurations |
| `README.md` | Project documentation |

## 10. Learning Outcomes

Through this project, practical knowledge and experience are developed in:

- Enterprise network architecture and topology design.
- VLAN creation and network segmentation.
- Trunk configuration and inter-VLAN communication.
- Layer 3 switching and routing.
- Spanning Tree Protocol and redundancy.
- EtherChannel configuration using LACP.
- First-hop gateway redundancy using HSRP.
- DHCP configuration and IP address management.
- Dual ISP connectivity.
- Secure network administration using SSH.
- Network security and access control.
- Network troubleshooting and connectivity verification.

## 11. Tools and Technologies

- Cisco Packet Tracer
- Cisco IOS CLI
- VLAN and IEEE 802.1Q Trunking
- Rapid PVST+
- LACP EtherChannel
- HSRP
- DHCP
- SSH
- Access Control Lists (ACLs)

---

**Project Category:** Enterprise Network Switching and Infrastructure

**Author:** [Shehan Jayakody](linkedin.com/in/shehan-jayakody)
