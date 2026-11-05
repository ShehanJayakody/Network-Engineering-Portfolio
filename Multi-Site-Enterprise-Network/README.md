# Multi-Site Enterprise Network – RIP, EIGRP & Route Redistribution

## 1. Project Overview

This project demonstrates the design and implementation of a multi-site enterprise network using Cisco Packet Tracer.

The network consists of two separate routing domains, Site A and Site B, connected through a central core router and an ISP network.

- **Site A:** Uses Routing Information Protocol Version 2 (RIPv2).
- **Site B:** Uses Enhanced Interior Gateway Routing Protocol (EIGRP).
- **Core Network:** Performs bidirectional route redistribution between RIP and EIGRP.
- **ISP Network:** Provides connectivity to a centralized server hosting DNS, HTTP and TFTP services.

The main objective is to establish communication between different routing domains while providing redundant paths within each site.

## 2. Network Topology

![Multi-Site Enterprise Network Topology](topology.png)

The network contains:

| Component | Quantity |
|---|---:|
| Core Router | 1 |
| ISP Router | 1 |
| RIP Routers | 4 |
| EIGRP Routers | 4 |
| PCs | 2 |
| Server | 1 |
| **Total Routers** | **10** |

## 3. Network Architecture

### Site A – RIPv2 Routing Domain

Site A consists of four interconnected routers operating with RIPv2.

**Routers:**
- R-RIP1
- R-RIP2
- R-RIP3
- R-RIP4

**Network characteristics:**
- RIPv2 dynamic routing.
- Disabled automatic route summarization.
- Redundant routing paths between routers.
- /30 subnets for point-to-point router connections.
- /24 LAN connected to R-RIP4.
- Passive interface configured on the LAN interface of R-RIP4.

**LAN Network:** `172.16.10.0/24`

**Default Gateway:** `172.16.10.254`

### Site B – EIGRP Routing Domain

Site B consists of four interconnected routers operating with EIGRP.

**Routers:**
- R-EIGRP1
- R-EIGRP2
- R-EIGRP3
- R-EIGRP4

**Network characteristics:**
- EIGRP dynamic routing.
- Autonomous System Number 100.
- Wildcard masks used in network statements.
- Redundant routing paths between routers.
- /30 subnets for point-to-point router connections.
- /24 LAN connected to R-EIGRP4.
- Passive interface configured on the LAN interface of R-EIGRP4.

**LAN Network:** `10.10.10.0/24`

**Default Gateway:** `10.10.10.254`

## 4. IP Addressing Scheme

### Site A – RIP

| Network | Device | Interface IP |
|---|---|---|
| 172.16.12.0/30 | R-RIP1 | 172.16.12.1 |
| 172.16.12.0/30 | R-RIP2 | 172.16.12.2 |
| 172.16.13.0/30 | R-RIP1 | 172.16.13.1 |
| 172.16.13.0/30 | R-RIP3 | 172.16.13.2 |
| 172.16.24.0/30 | R-RIP2 | 172.16.24.1 |
| 172.16.24.0/30 | R-RIP4 | 172.16.24.2 |
| 172.16.34.0/30 | R-RIP3 | 172.16.34.1 |
| 172.16.34.0/30 | R-RIP4 | 172.16.34.2 |

**Site A LAN – 172.16.10.0/24**

| Device | IP Address |
|---|---|
| R-RIP4 | 172.16.10.254 |
| PC-RIP | 172.16.10.36 |

### Site B – EIGRP

| Network | Device | Interface IP |
|---|---|---|
| 10.1.2.0/30 | R-EIGRP1 | 10.1.2.1 |
| 10.1.2.0/30 | R-EIGRP2 | 10.1.2.2 |
| 10.1.3.0/30 | R-EIGRP1 | 10.1.3.1 |
| 10.1.3.0/30 | R-EIGRP3 | 10.1.3.2 |
| 10.2.4.0/30 | R-EIGRP2 | 10.2.4.1 |
| 10.2.4.0/30 | R-EIGRP4 | 10.2.4.2 |
| 10.3.4.0/30 | R-EIGRP3 | 10.3.4.1 |
| 10.3.4.0/30 | R-EIGRP4 | 10.3.4.2 |

**Site B LAN – 10.10.10.0/24**

| Device | IP Address |
|---|---|
| R-EIGRP4 | 10.10.10.254 |
| PC-EIGRP | 10.10.10.36 |

### Core and ISP Networks

| Network | Device | IP Address |
|---|---|---|
| 192.168.1.0/30 | R-RIP1 | 192.168.1.1 |
| 192.168.1.0/30 | R-CORE | 192.168.1.2 |
| 192.168.2.0/30 | R-CORE | 192.168.2.1 |
| 192.168.2.0/30 | R-EIGRP1 | 192.168.2.2 |
| 8.8.8.0/30 | R-CORE | 8.8.8.1 |
| 8.8.8.0/30 | R-ISP | 8.8.8.2 |

### Server Network – 172.31.100.0/24

| Device | IP Address |
|---|---|
| R-ISP | 172.31.100.254 |
| Server0 | 172.31.100.36 |

## 5. Routing Configuration

### RIPv2

RIPv2 is configured throughout Site A and on the core router's connection to Site A.

Key configurations:

```cisco
router rip
 version 2
 network 172.16.0.0
 no auto-summary
```

R-RIP4 uses a passive interface on its LAN connection:

```cisco
router rip
 version 2
 passive-interface GigabitEthernet0/2
```

### EIGRP

EIGRP is configured throughout Site B and on the core router's connection to Site B.

**Autonomous System Number: 100**

Example configuration:

```cisco
router eigrp 100
 network 10.1.2.0 0.0.0.3
 network 10.1.3.0 0.0.0.3
```

R-EIGRP4 uses a passive interface on its LAN connection:

```cisco
router eigrp 100
 passive-interface GigabitEthernet0/0
```

## 6. Route Redistribution – R-CORE

The central core router enables communication between the RIP and EIGRP routing domains through bidirectional route redistribution.

### RIP to EIGRP

```cisco
router eigrp 100
 redistribute rip metric 10000 0 255 1 1500
```

This allows RIP-learned routes to be advertised into the EIGRP routing domain.

### EIGRP to RIP

```cisco
router rip
 version 2
 redistribute eigrp 100 metric 3
```

This allows EIGRP-learned routes to be advertised into the RIP routing domain.

### Static Route Redistribution

```cisco
router eigrp 100
 redistribute static
```

This enables static routes configured on the core router to be redistributed into EIGRP.

### Default Route Configuration

```cisco
ip route 0.0.0.0 0.0.0.0 8.8.8.2
```

The core router uses the ISP router as its default next hop.

The default route is also originated into RIP:

```cisco
router rip
 default-information originate
```

## 7. ISP Configuration

The ISP router provides connectivity between the core network and the server network.

**Core-facing interface:** 8.8.8.2/30

**Server-facing interface:** 172.31.100.254/24

Default route:

```cisco
ip route 0.0.0.0 0.0.0.0 8.8.8.1
```

## 8. Server Services

The centralized server uses the following configuration:

| Parameter | Value |
|---|---|
| IP Address | 172.31.100.36 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 172.31.100.254 |
| DNS Name | www.apex-corp.com |
| DNS A Record | 172.31.100.36 |

**Enabled Services:**

- DNS – Resolves `www.apex-corp.com` to the server IP address.
- HTTP – Provides web server functionality.
- TFTP – Provides Trivial File Transfer Protocol service.

Both client PCs use `172.31.100.36` as their DNS server.

## 9. Technologies Used

- Cisco Packet Tracer
- Cisco IOS
- IPv4 Addressing and Subnetting
- RIPv2
- EIGRP
- Bidirectional Route Redistribution
- Static Routing
- Default Route Propagation
- Passive Interfaces
- DNS
- HTTP
- TFTP

## 10. Verification Commands

The following commands can be used to inspect and verify the network configuration and routing operation.

**Interface Verification**

```cisco
show ip interface brief
```

**Routing Table**

```cisco
show ip route
```

**RIP Verification**

```cisco
show ip protocols
```

**EIGRP Neighbors**

```cisco
show ip eigrp neighbors
```

**EIGRP Topology**

```cisco
show ip eigrp topology
```

**Connectivity Testing**

```cisco
ping 172.16.10.36
ping 10.10.10.36
ping 172.31.100.36
```

**DNS Testing**

```text
ping www.apex-corp.com
```

*Note: These are suggested verification commands. Actual successful connectivity and routing results should be documented using Packet Tracer test results.*

## 11. Learning Outcomes

Through this project, I practised:

- Designing a multi-site enterprise network.
- Configuring and implementing RIPv2 and EIGRP.
- Implementing bidirectional route redistribution.
- Configuring static and default routes.
- Working with redundant routing paths.
- Implementing IP addressing and subnetting.
- Configuring centralized DNS, HTTP and TFTP services.
- Understanding communication between different dynamic routing protocols.

---

**Project Type:** Enterprise Network Design and Implementation

**Platform:** Cisco Packet Tracer

**Routing Protocols:** RIPv2 and EIGRP

**Core Feature:** Bidirectional Route Redistribution
