# Projet Système et Réseau GNS3

## 🚀 Project Overview

**Projet Système et Réseau GNS3** is a network infrastructure simulation project built using **GNS3**. The project aims to simulate a complete enterprise network with **inter-departmental connectivity**, **routing protocols**, **VLAN configurations**, **firewall rules**, and **network security policies**.

This project is designed to simulate realistic network environments for **education**, **training**, and **testing**. It demonstrates how to configure network devices such as **routers**, **switches**, and **firewalls** to achieve **secure, scalable, and efficient network topologies**.

---
# 🌐 Backbone Full Mesh -- GNS3

### **Serial Links + /30 Public IP Plan**

------------------------------------------------------------------------

## 📘 1. Topology Overview

Your backbone consists of **5 interconnected routers**:

-   **RI** (Core / Backbone Router)\
-   **RZ-1**\
-   **RZ-2**\
-   **RZ-3**\
-   **RZ-4**

The backbone uses a **full mesh topology** with **serial interfaces**,
providing:

-   High availability\
-   Multiple redundant paths\
-   Improved routing performance

We assign **/30 public IP subnets** (example block: `200.200.20.0/24`)
to each point-to-point connection.

📌 **Full Mesh Formula:**\
`5 × (5 − 1) / 2 = 10 links`

------------------------------------------------------------------------

## 🔗 2. Serial Interface Wiring (Exact Links)

### 🟦 RI Connections

    RI Serial0/0 ↔ RZ-1 Serial0/0
    RI Serial0/1 ↔ RZ-2 Serial0/0
    RI Serial0/2 ↔ RZ-3 Serial0/0
    RI Serial2/0 ↔ RZ-4 Serial0/0

### 🟥 RZ-1 Connections

    RZ-1 Serial2/0 ↔ RZ-2 Serial0/1
    RZ-1 Serial2/1 ↔ RZ-3 Serial0/1
    RZ-1 Serial2/2 ↔ RZ-4 Serial0/1

### 🟩 RZ-2 Connections

    RZ-2 Serial2/0 ↔ RZ-3 Serial0/2
    RZ-2 Serial2/1 ↔ RZ-4 Serial0/2

### 🟨 RZ-3 Connections

    RZ-3 Serial2/0 ↔ RZ-4 Serial2/0

🔒 **Total serial backbone links:** 10\
🔒 **All routers interconnected in full mesh**

------------------------------------------------------------------------

## 🌐 3. /30 Public IP Addressing Plan

Each /30 provides:
- **4 total addresses**
- **2 usable router IPs**
- **Perfect for point-to-point backbone links**

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


## ⚙️ 4. DCE Clock Rate

Any interface that GNS3 marks as **DCE** must include:

    clock rate 64000

If the router is DTE, this command will be ignored.

------------------------------------------------------------------------

## 🖥️ 5. Sample Configuration (RI Example)

``` bash
interface s0/0
 ip address 200.200.20.1 255.255.255.252
 clock rate 64000
 no shutdown

interface s0/1
 ip address 200.200.20.5 255.255.255.252
 clock rate 64000
 no shutdown

interface s0/2
 ip address 200.200.20.9 255.255.255.252
 clock rate 64000
 no shutdown

interface s2/0
 ip address 200.200.20.13 255.255.255.252
 clock rate 64000
 no shutdown
```

------------------------------------------------------------------------

## 📌 Future Work

You will later add:

-   **OSPF dynamic routing**\
-   **LAN networks per department**\
-   **Default routes or NAT if needed**

For now, the backbone is complete and fully functional for neighbor
testing.

------------------------------------------------------------------------



## 🔑 Features

- **Backbone Network Setup**: Core network devices configured to provide inter-department connectivity.
- **Routing Protocols**: Configuration of **OSPF**, **EIGRP**, and **static routing** to manage traffic across departments.
- **VLAN & Security**: **VLAN configuration**, **Access Control Lists (ACLs)**, and **firewall rules** for secure data flow between departments.
- **Network Design**: A comprehensive network topology for testing various **enterprise-level** network scenarios.
- **Documentation & Diagrams**: Detailed configuration files, network diagrams, and project structure to help users understand the setup.

---

## 💻 Installation & Setup

### Prerequisites
Before you begin, make sure you have the following software installed:

- **GNS3**: [Download and install GNS3](https://www.gns3.com/)
- **Cisco Router and Switch Images**: Make sure you have valid **IOS images** for Cisco devices to run the simulation.
- **Operating System**: This setup is compatible with **Windows**, **Linux**, and **MacOS**.

### Cloning the Repository

1. Clone the repository to your local machine:
   ```bash
   git clone https://github.com/Gl3diator/Projet-Systeme-et-Reseau-GNS3.git
