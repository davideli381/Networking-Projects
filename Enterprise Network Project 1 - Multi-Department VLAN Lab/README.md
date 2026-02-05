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
![Topology Screenshot]
<img width="1235" height="701" alt="Enterprise Network Project 1 - Topology" src="https://github.com/user-attachments/assets/aba56eb8-909d-47f2-946d-46bd12df6869" />

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

**Full IP table available**:  ([Enterprise Network Project 1 - Network Plan.xlsx](https://github.com/user-attachments/files/25090744/Enterprise.Network.Project.1.-.Network.Plan.xlsx)
)  
(This includes columns for Department, Device Name, Type, Interface, IP Address, Subnet Mask, Gateway, Network/Broadcast Addresses, Notes, and VLAN.)

## Device Configurations

### 1. Router (ISR4331) - Multi-Arm Inter-VLAN Routing
```bash
enable
configure terminal

! Interface to Switch0 (VLAN 10 - ACCOUNTS)
interface GigabitEthernet0/0/0
 description Connected to Switch0 Fa0/1 - VLAN 10
 ip address 192.168.40.1 255.255.255.128
 no shutdown
exit

! Interface to Switch1 (VLAN 20 - DELIVERY)
interface GigabitEthernet0/0/1
 description Connected to Switch1 Fa0/1 - VLAN 20
 ip address 192.168.40.129 255.255.255.128
 no shutdown
exit

end
write memory
```
### 2. Switch0 (2960 - VLAN 10 / ACCOUNTS)
```bash
enable
configure terminal

! Create VLAN
vlan 10
 name ACCOUNTS
exit

! Router-facing port (must be access in VLAN 10)
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 10
 no shutdown
exit

! PC ports (example for PC0 and PC1)
interface range FastEthernet0/2 - 3
 switchport mode access
 switchport access vlan 10
 no shutdown
exit

end
write memory
```
Verification on Switch0:
```bash 
show vlan brief 
```
Fa0/1, Fa0/2, Fa0/3 in VLAN 10.

### 3. Switch1 (2960 - VLAN 20 / DELIVERY)
```bash 
enable
configure terminal

! Create VLAN
vlan 20
 name DELIVERY
exit

! Router-facing port (must be access in VLAN 20)
interface FastEthernet0/1
 switchport mode access
 switchport access vlan 20
 no shutdown
exit

! PC ports (example for PC2 and PC3)
interface range FastEthernet0/2 - 3
 switchport mode access
 switchport access vlan 20
 no shutdown
exit

end
write memory
```
### 4. PCs (Static IP Setup in Packet Tracer)

Click PC → Desktop tab → IP Configuration → Static.
Example for VLAN 10 PCs:
IP Address: 192.168.40.10 / 192.168.40.11
Subnet Mask: 255.255.255.128
Default Gateway: 192.168.40.1

Example for VLAN 20 PCs:
IP Address: 192.168.40.130 / 192.168.40.131
Subnet Mask: 255.255.255.128
Default Gateway: 192.168.40.129

## How to Use This Project

Download the Enterprise-Network-Project-1.pkt file.
Open in Cisco Packet Tracer.
Explore the topology, configs, and test pings.
Refer to the Excel sheet for IP planning inspiration.

## Lessons Learned & Challenges

Matching switch access ports to the correct VLAN (e.g., Fa0/1 on Switch0 in VLAN 10) was critical—mismatch caused all inter-VLAN pings to fail.
Default gateways on PCs must exactly match the router interface in each subnet.
Multi-arm routing is simpler for small setups but less scalable than router-on-a-stick
