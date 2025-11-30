
# 🌐 Enterprise Backbone – Full Mesh (GNS3)
### **FastEthernet Links + /30 Public IP Plan + OSPF + Internet Access + ZeroTier WAN + UDP Tunnel**

---

# 📘 1. Overview
This project implements a **full-mesh enterprise backbone** across multiple PCs using GNS3.  
It includes:

- Cisco routers  
- FastEthernet point‑to‑point links  
- /30 public backbone addressing  
- OSPF dynamic routing  
- Internet access through RI (NAT overload)  
- ZeroTier virtual WAN for inter‑PC connectivity  
- UDP tunnel bridging RZ‑4 ↔ R‑NFS  
- Full integration of department routers into backbone OSPF  

Routers included:

- **RI (Core Gateway)**
- **RZ‑1**
- **RZ‑2**
- **RZ‑3**
- **RZ‑4**
- **R‑NFS (via UDP tunnel)**

---

# 🔗 2. Full Mesh Backbone Cabling (FastEthernet)

![Backbone Diagram](./Backbone%20V2.1.2/Backbone%20V2.1.2.png)

## 🟦 RI Connections
- RI Fa0/0 ↔ RZ‑1 Fa0/0  
- RI Fa0/1 ↔ RZ‑2 Fa0/0  
- RI Fa1/0 ↔ RZ‑3 Fa0/0  
- RI Fa2/0 ↔ RZ‑4 Fa0/0  

## 🟥 RZ‑1 Extra Links
- RZ‑1 Fa1/0 ↔ RZ‑2 Fa0/1  
- RZ‑1 Fa2/0 ↔ RZ‑3 Fa0/1  
- RZ‑1 Fa3/0 ↔ RZ‑4 Fa0/1  

## 🟩 RZ‑2 Extra Links
- RZ‑2 Fa1/0 ↔ RZ‑3 Fa2/0  
- RZ‑2 Fa3/0 ↔ RZ‑4 Fa2/0  

## 🟨 RZ‑3 Extra Link
- RZ‑3 Fa1/0 ↔ RZ‑4 Fa3/0  

---

# 🌐 3. Backbone /30 Public Addressing Plan  
Block: **200.200.20.0/24**  
Used: **200.200.20.0/26**

| Link | Network | Usable IPs |
|------|---------|-------------|
| RI ↔ RZ‑1 | 200.200.20.0/30 | .1 – .2 |
| RI ↔ RZ‑2 | 200.200.20.4/30 | .5 – .6 |
| RI ↔ RZ‑3 | 200.200.20.8/30 | .9 – .10 |
| RI ↔ RZ‑4 | 200.200.20.12/30 | .13 – .14 |
| RZ‑1 ↔ RZ‑2 | 200.200.20.16/30 | .17 – .18 |
| RZ‑1 ↔ RZ‑3 | 200.200.20.20/30 | .21 – .22 |
| RZ‑1 ↔ RZ‑4 | 200.200.20.24/30 | .25 – .26 |
| RZ‑2 ↔ RZ‑3 | 200.200.20.28/30 | .29 – .30 |
| RZ‑2 ↔ RZ‑4 | 200.200.20.32/30 | .33 – .34 |
| RZ‑3 ↔ RZ‑4 | 200.200.20.36/30 | .37 – .38 |

---

# ⚙️ 4. Example RI Backbone Interface Configuration

```
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
```

---

# 🛰️ 5. OSPF Routing (Area 0)
Backbone block: **200.200.20.0/26**  
Wildcard: **0.0.0.63**

### Example (RZ‑4):
```
router ospf 1
 router-id 5.5.5.5
 network 200.200.20.0 0.0.0.63 area 0
```

![OSPF Test](./Backbone%202.1.1%20(static%20UDP%20with%20Zerotier)/OSPF%20test.png)

---

# 🌍 6. Internet Access via RI (NAT)

### Internet Interface (Cloud → RI)
```
interface FastEthernet3/0
 ip address 192.168.40.200 255.255.255.0
 ip nat outside
 no shutdown
```

### NAT Inside
```
interface FastEthernet0/0
 ip nat inside
interface FastEthernet0/1
 ip nat inside
interface FastEthernet1/0
 ip nat inside
interface FastEthernet2/0
 ip nat inside
```

### NAT ACL
```
access-list 1 permit 200.200.20.0 0.0.0.63
ip nat inside source list 1 interface FastEthernet3/0 overload
```

### Default Route
```
ip route 0.0.0.0 0.0.0.0 192.168.40.2
```
---

# 🌐 7. DNS on All Routers

```
ip domain-lookup
ip name-server 8.8.8.8
ip name-server 1.1.1.1
```
![NAT+dns Test](./Backbone%202.1.1%20(static%20UDP%20with%20Zerotier)/Internet%20access%20test.png)

---

# ⭐ 8. ZeroTier WAN Integration (Stable Inter‑PC Connectivity)



[![ZeroTier Logo](https://i.imgur.com/w1wUIAE.jpeg)](https://www.zerotier.com)




ZeroTier creates a **virtual WAN** between all participating PCs.  
Each PC receives a **stable, encrypted, non-changing virtual IP**.

### ZeroTier Assigned IPs


- **Ghaith (Backbone PC):** `10.223.199.xxx`

![ipconfig](./Backbone%202.1.1%20(static%20UDP%20with%20Zerotier)/Zerotier%20Client-side%20SetUp.png)

- **Omar (NFS Department PC):** `10.223.199.yyy`

![zt network config](./Backbone%202.1.1%20(static%20UDP%20with%20Zerotier)/ZeroTier%20Virtual%20Network%20Setup.png)




---

# ⭐ 9. UDP Tunnel Over ZeroTier (RZ‑4 ↔ R‑NFS)

### Why UDP + ZeroTier?
- ZeroTier = stable WAN path  
- UDP Tunnel = L2/L3 bridge for router interfaces  
- Enables OSPF adjacency between routers on different machines  



### UDP Tunnel Configuration

![GNS3 side](./Backbone%202.1.1%20(static%20UDP%20with%20Zerotier)/New%20UDP%20Tunnel.png)

#### RZ‑4 side:
```
Local port: 30000
Remote host: 10.223.199.xxx
Remote port: 30001
```

#### R‑NFS side:
```
Local port: 30001
Remote host: 10.223.199.yyy
Remote port: 30000
```

---

# ⭐ 10. Tunnel Interface Configuration

### RZ‑4:
```
interface FastEthernet1/0
 ip address 10.0.0.1 255.255.255.0
 no shutdown
```

### R‑NFS:
```
interface FastEthernet0/1
 ip address 10.0.0.2 255.255.255.0
 no shutdown
```

---



---

# ⭐ 12. Diagram (Backbone + ZeroTier + UDP Tunnel)

```
           FULL MESH BACKBONE (200.200.20.x /30)
    -----------------------------------------------------
     RI ----- RZ1 ----- RZ2 ----- RZ3 ----- RZ4
                             |            |
                             |            |
                       ZeroTier Virtual WAN
                  10.223.199.xxx ↔ 10.223.199.yyy
                             |            |
                         UDP Tunnel (30000↔30001)
                             |            |
                          10.0.0.1     10.0.0.2
                             |            |
                           RZ-4         R-NFS
                             |            |
                      NFS LAN 172.24.74.0/24
```

---

# 🧪 13. Testing


**show ip ospf neighbor**

![ospf](./Backbone%202.1.1%20(static%20UDP%20with%20Zerotier)/OSPF%20test.png)


show ip route

**ping 10.0.0.2**

![ping1](./Backbone%202.1.1%20(static%20UDP%20with%20Zerotier)/UDP%20ping.png)


ping 200.200.20.13

![ping2](./Backbone%202.1.1%20(static%20UDP%20with%20Zerotier)/RI%20↔%20RZ‑4%20ping.png)


ping google.com

![goog p](./Backbone%202.1.1%20(static%20UDP%20with%20Zerotier)/Internet%20access%20test.png)


---

# ✅ Project Status

✔ Backbone fully operational  
✔ OSPF full mesh  
✔ NAT working  
✔ Internet access  
✔ ZeroTier WAN stable  
✔ UDP tunnel established  
✔ NFS fully integrated  

---

## 📌 Future Work

I will later add:
- **VPN**  
- **Firewall**  
