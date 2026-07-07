# 🛡️ Building and Securing a Network with a DMZ

**Network Security Lab · Cisco Packet Tracer · 4Geeks Academy**
Student: Raúl Velásquez · 

## 📋 Overview

Implementation of a segmented network architecture with a Demilitarized Zone (DMZ) in Cisco Packet Tracer, applying defensive cybersecurity principles: isolating critical services, controlled exposure via NAT, and traffic control between zones with Access Control Lists (ACLs).

---

## 🎯 Objectives

- ✅ Isolate critical services in a DMZ, separate from the internal LAN
- ✅ Configure static NAT to expose the DMZ web server to the Internet in a controlled way
- ✅ Implement ACLs that simulate a firewall, allowing only legitimate traffic
- ✅ Validate the configuration through connectivity tests, HTTP access, and blocking verification

---

## 🗺️ Network Topology

Central router (`Router_FW`, Cisco ISR 2911) with three interfaces, each connected to a switch (Cisco 2960) representing one zone:

| Zone | Router Interface | End Device |
|---|---|---|
| Internal LAN | GigabitEthernet0/0 | PC_Internal |
| DMZ | GigabitEthernet0/1 | Web_DMZ (Server-PT) |
| External (simulated Internet) | GigabitEthernet0/2 | PC_External |

---

## 🔢 IP Addressing Plan

| Device | IP | Mask | Gateway | Zone |
|---|---|---|---|---|
| Router_FW (GE0/0) | 192.168.1.1 | /24 | — | LAN |
| Router_FW (GE0/1) | 192.168.2.1 | /24 | — | DMZ |
| Router_FW (GE0/2) | 192.168.3.1 | /24 | — | External |
| PC_Internal | 192.168.1.10 | /24 | 192.168.1.1 | LAN |
| Web_DMZ | 192.168.2.10 | /24 | 192.168.2.1 | DMZ |
| PC_External | 192.168.3.10 | /24 | 192.168.3.1 | External |

---

## ⚙️ Configuration Implemented

**Static NAT** — exposes the DMZ server (192.168.2.10) to the external network through the public IP 192.168.3.1, without revealing the real private IP:

```
interface GigabitEthernet0/1
 ip nat inside
interface GigabitEthernet0/2
 ip nat outside
ip nat inside source static 192.168.2.10 192.168.3.1
```

**Extended ACLs** — three access control lists that replicate firewall behavior between zones:

| ACL | Applied On | Main Rule |
|---|---|---|
| `ACL_EXTERNAL_IN` | GE0/2 (external inbound) | Permits TCP to 192.168.3.1:80; denies everything else |
| `BLOQUEAR_DMZ_A_LAN` | GE0/1 (DMZ inbound) | Permits established DMZ→LAN traffic; blocks new DMZ→LAN connections |
| `ACL_LAN_OUT` | GE0/0 (LAN outbound) | Permits outbound traffic from the internal LAN |

---

## 🧪 Validation Tests

| Test | Result |
|---|---|
| Connectivity PC_Internal → LAN Gateway | ✅ Success (0% loss) |
| Connectivity Web_DMZ → DMZ Gateway | ✅ Success (0% loss) |
| Connectivity PC_External → External Gateway | ✅ Success (0% loss) |
| External HTTP access via NAT (`http://192.168.3.1`) | ✅ Success |
| Direct internal HTTP access (`http://192.168.2.10`) | ✅ Success |
| External ICMP ping → 192.168.3.1 | 🚫 Blocked (ACL_EXTERNAL_IN only allows TCP/80) |
| DMZ → LAN ping (Web_DMZ → 192.168.1.10) | 🚫 Blocked (BLOQUEAR_DMZ_A_LAN) |

The most critical test is the DMZ→LAN block: it confirms that even if the DMZ were compromised, it couldn't be used as a bridge into the internal network.

---

## ✅ Conclusions

- **Effective segmentation:** splitting the network into three interfaces (LAN, DMZ, External) reduces the attack surface by isolating public-facing services from the internal network.
- **NAT for controlled exposure:** publishes the web service without revealing the server's real private IP.
- **ACLs as a Layer 3/4 firewall:** enable granular traffic policies between zones.
- **Validated architecture:** all unauthorized traffic (external ICMP, DMZ→LAN) was blocked, while the legitimate service (HTTP via NAT) worked correctly.

---

## 📁 Repository Contents

| File | Description |
|---|---|
| `Informe_DMZ_Raul_Velasquez.pdf` | 📄 Full technical report (15 pages) with step-by-step configuration and evidence |
| `DMZ_PROJECT-hecho.pka` | 🖧 Complete network topology file in Cisco Packet Tracer |
| `assets/` | 📸 Screenshots and lab resources |

---
Academic project — Cybersecurity Program, 4Geeks Academy
