# Enterprise Network Project 2:SOHO Network Design & Implementation (VLANS, DHCP & WLAN)

This project designs and simulates a small branch office network for **XYZ Company** (a fast-growing food trading business in Eastern Australia) at their new location in **Bonalbo**. The network operates independently from headquarters and meets all specified requirements using only **one Cisco router** and **one Cisco multilayer switch**.

**Departments**:
- Admin/IT → VLAN 10
- Finance/HR → VLAN 20
- Customer Service/Reception → VLAN 30

**Core Features**:
- VLAN segmentation per department
- Wireless access (Cisco Aironet APs) per department
- Automatic IPv4 addressing via **DHCP** (router as DHCP server)
- Inter-VLAN routing using **router-on-a-stick** (subinterfaces)
- Full communication between all departments

## Case Study Requirements (Fully Met)
- One router + one switch (Cisco devices)
- 3 departments, each in a separate VLAN
- Wireless network for each department
- Hosts obtain IPv4 addresses automatically (DHCP)
- Devices in all departments can communicate with each other
- Base network: 192.168.1.0/24 (subnetted into three /26 subnets)

## Network Topology
![Enterprise Network Project 2 - Topology](<img width="1627" height="702" alt="Enterprise Network Project 2 - Toplogy" src="https://github.com/user-attachments/assets/7bff89ba-1582-462d-8e54-cb91f3020073" />)

Color-coded departments in Packet Tracer:
- Pink → Admin/IT (VLAN 10)
- Green → Finance/HR (VLAN 20)
- Blue → Customer Service/Reception (VLAN 30)

**Devices**:
- Router: Router0 (likely 2911 or 4331)
- Switch: Multilayer switch (connected via trunk to router)
- End devices: PCs, laptops, smartphones, printers, access points (one per department)

## IP Addressing & Subnetting
Base network 192.168.1.0/24 divided into three /26 subnets (62 usable hosts each):

| Department              | VLAN | Network Address   | Subnet Mask       | Usable Range             | Broadcast Address | Default Gateway   | Router Subinterface |
|-------------------------|------|-------------------|-------------------|--------------------------|-------------------|-------------------|---------------------|
| Admin/IT               | 10   | 192.168.1.0       | 255.255.255.192   | 192.168.1.1 – .62       | 192.168.1.63     | 192.168.1.1      | G0/0.10            |
| Finance/HR             | 20   | 192.168.1.64      | 255.255.255.192   | 192.168.1.65 – .126     | 192.168.1.127    | 192.168.1.65     | G0/0.20            |
| Customer Service       | 30   | 192.168.1.128     | 255.255.255.192   | 192.168.1.129 – .190    | 192.168.1.191    | 192.168.1.129    | G0/0.30            |

**DHCP Pools** configured on the router exclude the gateway and reserve space for static devices (e.g., APs).

Full detailed plan:  
📊 [Enterprise Network Project 1 - Network Plan.xlsx]([Enterprise Network Project 2 - Network Plan.xlsx](https://github.com/user-attachments/files/25116345/Enterprise.Network.Project.2.-.Network.Plan.xlsx)
)  


## Key Configurations

### Router – Router-on-a-Stick + DHCP Server
```bash
enable
configure terminal

! Subinterfaces for inter-VLAN routing
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.1.1 255.255.255.192
 no shutdown
exit
interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.1.65 255.255.255.192
 no shutdown
exit
interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.1.129 255.255.255.192
 no shutdown
exit

! DHCP Pools (example for VLAN 10 – repeat for 20 & 30)
ip dhcp pool VLAN10
 network 192.168.1.0 255.255.255.192
 default-router 192.168.1.1
 dns-server 8.8.8.8
 exit
ip dhcp excluded-address 192.168.1.1 192.168.1.9   ! Reserve for static devices

end
write memory
```
### Switch – VLANs & Trunk Port
```bash
enable
configure terminal

vlan 10
 name Admin-IT
exit
vlan 20
 name Finance-HR
exit
vlan 30
 name Customer-Service
exit

! Trunk to router
interface GigabitEthernet0/1   ! or Fa0/1 depending on model
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30
 no shutdown
exit

! Example access ports (adjust per your topology)
interface range FastEthernet0/1 - 5
 switchport mode access
 switchport access vlan 10
 no shutdown
exit

end
write memory
```
### Access Points

Static IP in respective VLAN/subnet
SSID configured per department (e.g., "Admin-WiFi", "Finance-WiFi", "Customer-WiFi")
VLAN mapping enabled on APs

## How to Explore the Project

Clone/download this repository.
Open Enterprise-Network-Project-2.pkt in Cisco Packet Tracer.
View CLI configs on router & switch.
Verify:
show vlan brief on switch
show ip dhcp binding on router
Successful pings: PC in VLAN 10 → PC in VLAN 30, wireless client → printer in another department


## Files in This Repository

Enterprise-Network-Project-2.pkt – Main simulation file
XYZ-Branch-Network-Plan.xlsx – IP addressing and device table
topology-screenshot.png – Logical topology view

## Lessons Learned

Router-on-a-stick is efficient for small networks with limited router interfaces.
Proper trunk configuration and VLAN tagging are critical for inter-VLAN traffic.
DHCP simplifies host management but requires careful pool/exclusion planning.
Wireless segmentation via VLAN-mapped SSIDs enhances department isolation.

Feel free to use this as reference, fork, or suggest improvements!

Project completed: 6th February, 2026

