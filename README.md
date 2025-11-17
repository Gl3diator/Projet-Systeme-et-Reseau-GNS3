
# 🌐 Backbone Full Mesh — GNS3  
### **FastEthernet Links + /30 Public IP Plan**

---

## 📘 1. Topology Overview

The backbone consists of **5 routers** interconnected in a **full mesh** using **FastEthernet links**:

- **RI** (Core Router)  
- **RZ-1**  
- **RZ-2**  
- **RZ-3**  
- **RZ-4**

This topology provides:

- 🔁 High redundancy  
- 🚀 Fast convergence  
- 🔐 Multiple alternative routing paths  

All backbone connections use **/30 public IP networks** taken from `200.200.20.0/24`.

📌 **Full Mesh Formula:**  
`5 × (5 − 1) / 2 = 10 links`

---

## 🔗 2. FastEthernet Wiring (Exact Links)

### 🟦 RI Connections
```
RI Fa0/0 ↔ RZ-1 Fa0/0
RI Fa0/1 ↔ RZ-2 Fa0/0
RI Fa1/0 ↔ RZ-3 Fa0/0
RI Fa2/0 ↔ RZ-4 Fa0/0
```

### 🟥 RZ-1 Extra Mesh Links
```
RZ-1 Fa1/0 ↔ RZ-2 Fa0/1
RZ-1 Fa2/0 ↔ RZ-3 Fa0/1
RZ-1 Fa3/0 ↔ RZ-4 Fa0/1
```

### 🟩 RZ-2 Extra Mesh Links
```
RZ-2 Fa1/0 ↔ RZ-3 Fa2/0
RZ-2 Fa3/0 ↔ RZ-4 Fa2/0
```

### 🟨 RZ-3 Extra Mesh Link
```
RZ-3 Fa1/0 ↔ RZ-4 Fa3/0
```

🔒 **Total FastEthernet Backbone Links:** 10  
🔒 **All routers interconnected in full mesh**

---

## 🌐 3. /30 Public IP Addressing Plan

Each /30 subnet provides:  
- **4 total addresses**  
- **2 usable router IPs**  
- Perfect for backbone point-to-point links  

---

**RI ↔ RZ-1** `200.200.20.0/30` → **.1 -- .2**  
**RI ↔ RZ-2** `200.200.20.4/30` → **.5 -- .6**  
**RI ↔ RZ-3** `200.200.20.8/30` → **.9 -- .10**  
**RI ↔ RZ-4** `200.200.20.12/30` → **.13 -- .14**

**RZ-1 ↔ RZ-2** `200.200.20.16/30` → **.17 -- .18**  
**RZ-1 ↔ RZ-3** `200.200.20.20/30` → **.21 -- .22**  
**RZ-1 ↔ RZ-4** `200.200.20.24/30` → **.25 -- .26**

**RZ-2 ↔ RZ-3** `200.200.20.28/30` → **.29 -- .30**  
**RZ-2 ↔ RZ-4** `200.200.20.32/30` → **.33 -- .34**  
**RZ-3 ↔ RZ-4** `200.200.20.36/30` → **.37 -- .38**

---

## 🖥️ 4. Sample Configuration (RI Example with FastEthernet)

```bash
interface FastEthernet0/0
 description LINK_TO_RZ1
 ip address 200.200.20.1 255.255.255.252
 no shutdown

interface FastEthernet0/1
 description LINK_TO_RZ2
 ip address 200.200.20.5 255.255.255.252
 no shutdown

interface FastEthernet1/0
 description LINK_TO_RZ3
 ip address 200.200.20.9 255.255.255.252
 no shutdown

interface FastEthernet2/0
 description LINK_TO_RZ4
 ip address 200.200.20.13 255.255.255.252
 no shutdown
```

---

## 📌 Future Work

You will later add:

- **OSPF dynamic routing**  
- **VPN**  
- **Firewall or ACL rules**  
- **Default route or NAT (if Internet is added)**

For now, the **FastEthernet backbone** is complete and ready for neighbor testing.
