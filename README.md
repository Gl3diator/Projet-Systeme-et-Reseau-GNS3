# 🌐 Enterprise Backbone – Full Mesh (GNS3)
### **FastEthernet Links + /30 Public IP Plan + OSPF + Internet Access**

## 📘 1. Overview
This project implements a **full-mesh backbone** for a multi-department enterprise network using GNS3 with:
- Cisco routers  
- FastEthernet links  
- /30 public IP addressing  
- OSPF dynamic routing  
- NAT on RI for Internet access  
- GNS3 VM Cloud for external connectivity  

Routers:
- RI (Core Gateway)
- RZ-1
- RZ-2
- RZ-3
- RZ-4

Provides:
- High redundancy  
- Fast convergence  
- Full routing  
- Internet access  

---

## 🔗 2. Full Mesh Cabling (FastEthernet)

### 🟦 RI Connections
RI Fa0/0 ↔ RZ-1 Fa0/0  
RI Fa0/1 ↔ RZ-2 Fa0/0  
RI Fa1/0 ↔ RZ-3 Fa0/0  
RI Fa2/0 ↔ RZ-4 Fa0/0  

### 🟥 RZ-1 Extra Links
RZ-1 Fa1/0 ↔ RZ-2 Fa0/1  
RZ-1 Fa2/0 ↔ RZ-3 Fa0/1  
RZ-1 Fa3/0 ↔ RZ-4 Fa0/1  

### 🟩 RZ-2 Extra Links
RZ-2 Fa1/0 ↔ RZ-3 Fa2/0  
RZ-2 Fa3/0 ↔ RZ-4 Fa2/0  

### 🟨 RZ-3 Extra Link
RZ-3 Fa1/0 ↔ RZ-4 Fa3/0  

---

## 🌐 3. Backbone /30 IP Plan (Public Block)

Allocated from **200.200.20.0/24**  
Used block: **200.200.20.0/26**

RI ↔ RZ-1 → 200.200.20.0/30 (.1–.2)  
RI ↔ RZ-2 → 200.200.20.4/30 (.5–.6)  
RI ↔ RZ-3 → 200.200.20.8/30 (.9–.10)  
RI ↔ RZ-4 → 200.200.20.12/30 (.13–.14)  

RZ1 ↔ RZ2 → 200.200.20.16/30 (.17–.18)  
RZ1 ↔ RZ3 → 200.200.20.20/30 (.21–.22)  
RZ1 ↔ RZ4 → 200.200.20.24/30 (.25–.26)  

RZ2 ↔ RZ3 → 200.200.20.28/30 (.29–.30)  
RZ2 ↔ RZ4 → 200.200.20.32/30 (.33–.34)  
RZ3 ↔ RZ4 → 200.200.20.36/30 (.37–.38)  

---

## ⚙️ 4. Example Config (RI)

interface FastEthernet0/0  
 ip address 200.200.20.1 255.255.255.252  
 no shutdown  

interface FastEthernet0/1  
 ip address 200.200.20.5 255.255.255.252  
 no shutdown  

interface FastEthernet1/0  
 ip address 200.200.20.9 255.255.255.252  
 no shutdown  

interface FastEthernet2/0  
 ip address 200.200.20.13 255.255.255.252  
 no shutdown  

---

## 🛰️ 5. OSPF Routing (Area 0)

Backbone block: **200.200.20.0/26** → wildcard **0.0.0.63**

### RI
router ospf 1  
 router-id 1.1.1.1  
 network 200.200.20.0 0.0.0.63 area 0  

### RZ-1
router ospf 1  
 router-id 2.2.2.2  
 network 200.200.20.0 0.0.0.63 area 0  

### RZ-2
router ospf 1  
 router-id 3.3.3.3  
 network 200.200.20.0 0.0.0.63 area 0  

### RZ-3
router ospf 1  
 router-id 4.4.4.4  
 network 200.200.20.0 0.0.0.63 area 0  

### RZ-4
router ospf 1  
 router-id 5.5.5.5  
 network 200.200.20.0 0.0.0.63 area 0  

---

## 🌍 6. Internet via RI (NAT + Cloud)

Cloud eth1 (Internet): **192.168.40.0/24**  
Gateway: **192.168.40.2**

### 6.1 RI Internet Interface
interface FastEthernet3/0  
 ip address 192.168.40.200 255.255.255.0  
 ip nat outside  
 no shutdown  

### 6.2 NAT Configuration
access-list 1 permit 200.200.20.0 0.0.0.63  

interface FastEthernet0/0  
 ip nat inside  
interface FastEthernet0/1  
 ip nat inside  
interface FastEthernet1/0  
 ip nat inside  
interface FastEthernet2/0  
 ip nat inside  

ip nat inside source list 1 interface FastEthernet3/0 overload  

### 6.3 Default Route
ip route 0.0.0.0 0.0.0.0 192.168.40.2  

### 6.4 OSPF Default Route Advertisement
router ospf 1  
 default-information originate  

---

## 🌐 7. DNS (Required on All Routers)

ip domain-lookup  
ip name-server 8.8.8.8  
ip name-server 1.1.1.1  

---

## 🧪 8. Testing

show ip ospf neighbor  
show ip route  
show ip nat translations  
show ip nat statistics  

ping 8.8.8.8  
ping google.com  

---

# UDP Tunnel + OSPF Setup (RZ-4 ↔ R-NFS)  
### With Clean Steps + ASCII Diagrams

---

# ⭐ 1. Overview

This document explains **ONLY the steps starting from the UDP TUNNEL creation**, including:

- UDP Tunnel between **RZ‑4** and **R‑NFS**  
- Tunnel IP configuration  
- OSPF adjacency over the tunnel  
- NFS LAN advertisement  
- Routing verification  
- ASCII diagrams

No NAT, no DB department here.

---

# ⭐ 2. ASCII Network Diagram (Tunnel + Backbone)

```
                 +-------------------------------------+
                 |     FULL-MESH BACKBONE               |
                 |  (200.200.20.x /30 links)            |
                 +------------------------------------+
                   |         |         |             |    
                   |         |         |             |
               +-------+  +-------+  +-------+   +-------+
               | RZ-1  |  | RZ-2  |  | RZ-3  |   | RZ-4  |
               +-------+  +-------+  +-------+   +-------+
                                                     |
                                                     |
                                UDP Tunnel (L2 over Internet)
                                             |
                                             |
                           WiFi Backbone IP ↔ WiFi NFS-Dep IP
                                             |
                                             |
                                       +-----------+
                                       |  R-NFS    |
                                       +-----------+
                                             |
                                             |
                                   NFS LAN 172.24.74.0/24
```


---

# ⭐ 3. Required Information

| Component | Value |
|----------|--------|
| Your WiFi IP | **Backbone wifi ip** |
| Friend WiFi IP | **NFS wifi ip** |
| Backbone Tunnel Port | 30000 |
| NFS Tunnel Port | 30001 |
| RZ‑4 Tunnel IP | 10.0.0.1/24 |
| R‑NFS Tunnel IP | 10.0.0.2/24 |
| NFS LAN | 172.24.74.0/24 |

---

# ⭐ 4. Test Basic Tunnel Connectivity

### From RZ‑4:
```
ping 10.0.0.2
```

### From R‑NFS:
```
ping 10.0.0.1
```


---

# ⭐ 5. Diagram (Tunnel Interfaces Only)

```
               RZ-4                                R-NFS
      +-----------------+                       +-----------------+
      | Fa1/0           |                       | Fa0/1           |
      | 10.0.0.1/24     | ← UDP Tunnel →        | 10.0.0.2/24     |
      +-----------------+                       +-----------------+
                ^                                          ^
                |                                          |
     UDP 30000 →●                                          ●← UDP 30001
                |                                          |
        Local: Backbone WIFI ip                   Local: NFS WIFI ip
```




---
## ✅  Status

✔ Backbone complete  
✔ OSPF operational  
✔ NAT working  
✔ ACL  
✔ Internet access confirmed  
✔ Working **cross‑PC UDP tunnel**  :

        - Layer‑2 connectivity between routers  
        - Tunnel IPs (10.0.0.1 ↔ 10.0.0.2)  
        - OSPF adjacency across the tunnel  
        - NFS LAN advertised dynamically  
        - Backbone + department integration complete  

---

## 📌 Future Work

I will later add:
- **VPN**  
- **Firewall**  
