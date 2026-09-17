# GNS3 Project: Multi-Router OSPF Configuration Lab

## 📌 Project Overview
This project demonstrates the configuration of **OSPF (Open Shortest Path First)** dynamic routing using **GNS3**. 

The topology consists of three Cisco routers (R1, R2, R3) interconnected via Serial WAN links, each serving a separate LAN. The goal is to configure OSPF (Single Area - Area 0) so that all routers automatically learn routes to each other's networks, enabling full end-to-end connectivity between all VPCS (Virtual PC Simulator) endpoints.

## 🗺️ Network Topology & Addressing Scheme

![Network Topology](https://github.com/PraveenKumarmsk/OSPF-Configuration-Project-Labs/blob/main/OSPF%20Lab%20SS.png)

### Routers & Interfaces
| Device | Interface | IP Address | Subnet Mask | Description |
| :--- | :--- | :--- | :--- | :--- |
| **R1** | f0/0 | 192.168.1.1 | 255.255.255.0 | Gateway for LAN 1 |
| | f1/0 | 10.0.0.1 | 255.255.255.252 | WAN Link to R2 |
| **R2** | f0/0 | 192.168.2.1 | 255.255.255.0 | Gateway for LAN 2 (Central) |
| | f1/0 | 10.0.0.2 | 255.255.255.252 | WAN Link to R1 |
| | f2/0 | 11.0.0.1 | 255.255.255.252 | WAN Link to R3 |
| **R3** | f1/0 | 192.168.3.1 | 255.255.255.0 | Gateway for LAN 3 |
| | f0/0 | 11.0.0.2 | 255.255.255.252 | WAN Link to R2 |

### End Devices (VPCS)
| Device | IP Address | Subnet Mask | Default Gateway |
| :--- | :--- | :--- | :--- |
| **PC1** | 192.168.1.2 | 255.255.255.0 | 192.168.1.1 |
| **PC2** | 192.168.2.2 | 255.255.255.0 | 192.168.2.1 |
| **PC3** | 192.168.3.2 | 255.255.255.0 | 192.168.3.1 |

---

## ⚙️ Configuration Steps

### 1. R1 Configuration (OSPF Area 0)
R1 needs to advertise its LAN network and the WAN link to R2.

```
Router> enable
Router# configure terminal
Router(config)# hostname R1

! Configure LAN Interface
Router(config)# interface FastEthernet0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

! Configure WAN Interface
Router(config)# interface FastEthernet1/0
Router(config-if)# ip address 10.0.0.1 255.255.255.252
Router(config-if)# no shutdown
Router(config-if)# exit

! Configure OSPF
Router(config)# router ospf 1
Router(config-router)# network 192.168.1.0 0.0.0.255 area 0
Router(config-router)# network 10.0.0.0 0.0.0.3 area 0
Router(config-router)# exit
```
2. R2 Configuration (OSPF Area 0 - Central Router)
R2 connects the left and right networks and advertises all three connected networks.
```Router> enable
Router# configure terminal
Router(config)# hostname R2

! Configure LAN Interface
Router(config)# interface FastEthernet0/0
Router(config-if)# ip address 192.168.2.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

! Configure WAN Interface to R1
Router(config)# interface FastEthernet1/0
Router(config-if)# ip address 10.0.0.2 255.255.255.252
Router(config-if)# no shutdown
Router(config-if)# exit

! Configure WAN Interface to R3
Router(config)# interface FastEthernet2/0
Router(config-if)# ip address 11.0.0.1 255.255.255.252
Router(config-if)# no shutdown
Router(config-if)# exit

! Configure OSPF
Router(config)# router ospf 1
Router(config-router)# network 192.168.2.0 0.0.0.255 area 0
Router(config-router)# network 10.0.0.0 0.0.0.3 area 0
Router(config-router)# network 11.0.0.0 0.0.0.3 area 0
Router(config-router)# exit
```
3. R3 Configuration (OSPF Area 0)
R3 needs to advertise its LAN network and the WAN link to R2.
```
Router> enable
Router# configure terminal
Router(config)# hostname R3

! Configure LAN Interface
Router(config)# interface FastEthernet1/0
Router(config-if)# ip address 192.168.3.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit

! Configure WAN Interface
Router(config)# interface FastEthernet0/0
Router(config-if)# ip address 11.0.0.2 255.255.255.252
Router(config-if)# no shutdown
Router(config-if)# exit

! Configure OSPF
Router(config)# router ospf 1
Router(config-router)# network 192.168.3.0 0.0.0.255 area 0
Router(config-router)# network 11.0.0.0 0.0.0.3 area 0
Router(config-router)# exit
```
4. VPCS Configuration (End Devices)
In GNS3, VPCS uses command-line syntax to configure IP addresses. Open the console for each PC and run the following commands:
```
For PC1:
ip 192.168.1.2 255.255.255.0 192.168.1.1

For PC2:
ip 192.168.2.2 255.255.255.0 192.168.2.1

For PC3:
ip 192.168.3.2 255.255.255.0 192.168.3.1
```
### ✅ Verification
Check OSPF Neighbor Adjacencies:

Run show ip ospf neighbor on R1, R2, and R3. You should see "FULL" state between connected routers.

Example on R1: You should see R2 (10.0.0.2) listed as a neighbor.

Check Routing Tables:

Run show ip route on any router.

Look for routes marked with O (OSPF). For example, R1 should have learned the 192.168.2.0/24 and 192.168.3.0/24 networks via OSPF.

End-to-End Ping Test:

Open the console for PC1.

Run the command: ping 192.168.3.2 (PC3).

The first ping might fail due to ARP resolution, but subsequent pings should be successful. This proves that OSPF has successfully routed traffic from the far-left network to the far-right network.

### 🛠️ Tools Used
GNS3 (Graphical Network Simulator-3)

Cisco IOS Router Images

VPCS (Virtual PC Simulator)

OSPF (Open Shortest Path First) - Single Area 0

### 📂 How to Use
Clone this repository.

Open the .gns3 project file in GNS3.

Review the router configurations or use the CLI commands provided above to rebuild the lab from scratch.

Ensure you have the correct Cisco IOS images imported into your GNS3 environment before running the project.



