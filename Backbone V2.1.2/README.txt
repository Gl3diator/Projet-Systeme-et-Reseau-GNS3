📘 Project Description — Backbone V2.1.1


created on 2025-11-26
Author: Ghaith Ben Salah <GhaithBenSalah1999@gmail.com>

This project implements a fully operational enterprise backbone using Cisco routers in GNS3.
The backbone uses a full-mesh FastEthernet topology with /30 public addressing, OSPF area 0, and NAT-enabled Internet access via the RI core router.
It also supports cross-PC department integration through UDP tunnels, allowing external routers (R-WEB, R-IT, R-DB, R-NFS) to connect remotely to the backbone while maintaining full routing and Internet connectivity.
My responsibilities in this project include:


Designing and configuring the full-mesh backbone (RI, RZ-1, RZ-2, RZ-3, RZ-4)


Implementing /30 public IP addressing (200.200.20.0/26)


Deploying OSPF dynamic routing across all backbone links


Configuring NAT + default route on RI for global Internet access


Managing UDP tunnel integration for remote department routers


Ensuring full routing between backbone, departments, and Internet



This exported project represents the completed backbone environment already integrated with each department.