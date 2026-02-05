# Enterprise Network Project 1: Multi-Department VLAN with Inter-VLAN Routing

## Overview
This project simulates a small enterprise network in **Cisco Packet Tracer** with two departments:
- **ACCOUNTS** (VLAN 10) – Subnet: 192.168.40.0/25
- **DELIVERY** (VLAN 20) – Subnet: 192.168.40.128/25

The goal was to:
- Properly subnet a /24 network into two usable /25 subnets.
- Assign static IPs to PCs with correct default gateways.
- Configure VLANs and access ports on 2960 switches.
- Implement **multi-arm inter-VLAN routing** on a Cisco ISR 4331 router (separate physical interfaces for each VLAN).
- Achieve full connectivity: intra-VLAN (Layer 2 switching) and inter-VLAN (Layer 3 routing).

No router-on-a-stick (subinterfaces/trunk) was used—instead, dedicated router interfaces per VLAN for simplicity.

## Network Topology
![Topology Screenshot](topology-screenshot.png)

**Devices**:
- Router: ISR4331 (Router0)
  - Gig0/0/0 → Switch0 (VLAN 10 side)
  - Gig0/0/1 → Switch1 (VLAN 20 side)
- Switches: 2960-24TT (Switch0 for VLAN 10, Switch1 for VLAN 20)
- PCs: 2 in ACCOUNTS (PC0, PC1), 2 in DELIVERY (PC2, PC3)

## IP Addressing & Planning
The entire network was carefully planned in an Excel sheet for accuracy and scalability.

- **VLAN 10 (ACCOUNTS)**: 192.168.40.0/25
  - Network: 192.168.40.0
  - Broadcast: 192.168.40.127
  - Usable hosts: 192.168.40.1 – 192.168.40.126 (126 hosts)
  - Router gateway: 192.168.40.1
  - PCs: Static IPs (e.g., 192.168.40.10, 192.168.40.11)

- **VLAN 20 (DELIVERY)**: 192.168.40.128/25
  - Network: 192.168.40.128
  - Broadcast: 192.168.40.255
  - Usable hosts: 192.168.40.129 – 192.168.40.254 (126 hosts)
  - Router gateway: 192.168.40.129
  - PCs: Static IPs (e.g., 192.168.40.130, 192.168.40.131)

**Full IP table available**:  
[ACCOUNTS-Department-IP-Table.xlsx](ACCOUNTS-Department-IP-Table.xlsx)  
(This includes columns for Department, Device Name, Type, Interface, IP Address, Subnet Mask, Gateway, Network/Broadcast Addresses, Notes, and VLAN.)

## Device Configurations
Here are the key configuration steps for each device. Full `show run` outputs are available in the [configs/ folder](configs/) if uploaded.

### 1. Router (ISR4331 – Multi-Arm Routing)
```bash
enable
configure terminal

interface GigabitEthernet0/0/0
 description To Switch0 - VLAN 10
 ip address 192.168.40.1 255.255.255.128
 no shutdown
exit

interface GigabitEthernet0/0/1
 description To Switch1 - VLAN 20
 ip address 192.168.40.129 255.255.255.128
 no shutdown
exit

end
write memory
