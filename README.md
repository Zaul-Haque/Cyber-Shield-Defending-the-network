# 🛡️ Cyber Shield: Defending the Network

**A comprehensive network security and access control simulation project** designed for a modern educational campus.  
Developed using **Cisco Packet Tracer**, this project demonstrates how to build, secure, and manage a hybrid campus network following real-world **network engineering and cybersecurity principles**.

---

## 📘 Project Overview

**Cyber Shield** aims to design and simulate a secure, scalable network infrastructure for  
**ABES Institute of Technology (ABESIT)** — a campus with 500–700 active users including students, faculty, and staff.  

The project focuses on:
- Logical **VLAN segmentation** for access control.
- **Network hardening** using ACLs, DHCP snooping, and device security.
- **Hybrid access** via secure VPN for faculty and isolated guest/student networks.
- **Web access policy enforcement** with DNS and Layer 7 filtering.

---

## 🧩 Project Scope & Environment

| Component | Description |
|------------|-------------|
| **Location** | ABES Institute of Technology (Campus Environment) |
| **Users** | 500–700 (Students, Faculty, Admin Staff, Guests) |
| **Network Types** | Wired LAN + Wireless WLAN |
| **Core Services** | LMS, AD/LDAP, Database, Git, Research Portals |
| **Tools Used** | Cisco Packet Tracer v8.x, Cisco IOS Devices, Syslog, NAT, ACLs |

---

## ⚙️ Tools & Technologies

- **Cisco Packet Tracer v8.x** — Network topology simulation  
- **Cisco IOS Devices** — Routers, L2/L3 switches, and WRT-300N APs  
- **Network Services** — VLANs, ACLs, NAT, Syslog, DHCP  
- **Security Controls** — WPA2/3 Enterprise, AAA, SNMP, Port Security  
- **Optional Add-ons** — VPN (SSL/IPSec), RADIUS, Identity Proxy, IDS/IPS

---

## 🧭 Network Design

### 🔹 Logical Segmentation (VLANs)
| Zone | VLAN | Subnet | Purpose |
|------|-------|--------|----------|
| Admin/IT | 10 | 10.10.10.0/24 | Network management & sysadmin |
| Faculty | 20 | 10.10.20.0/24 | Internal faculty applications |
| Student Labs | 30 | 10.10.30.0/24 | Wired lab desktops |
| Student Wi-Fi | 35 | 10.10.35.0/24 | Student BYOD access |
| Guest Wi-Fi | 40 | 10.10.40.0/24 | Internet-only guest access |
| Servers/DMZ | 50 | 10.10.50.0/24 | AD, LMS, DB, Git services |
| VPN-Faculty | 60 | 10.10.60.0/24 | Secure remote faculty access |
| Mgmt/OOB | 99 | 10.10.99.0/24 | Device management (SSH, SNMP, Syslog) |

**Goal:**  
Each VLAN isolates traffic by user function and enforces the **Principle of Least Privilege**.

---

## 🏗️ Physical Topology

- **Core L3 Switch:** Handles inter-VLAN routing and ACLs.  
- **Edge Router/NGFW:** NAT, VPN, and firewall protection.  
- **Access Switches & APs:** VLAN trunking to wireless SSIDs.  
- **Server/DMZ:** Connected to firewall with strict ACLs.  

---

## 🔒 Security Controls

| Device Type | Location | Security Features |
|--------------|-----------|-------------------|
| Edge Router / NGFW | Edge Rack | NAT, IPS, ACLs, Logging |
| Core Switch | Data Center | ACLs, DHCP Snooping |
| Access Switches | Labs | Port Security, BPDU Guard |
| Wireless APs | Classrooms | WPA2/3-Enterprise, 802.1X |
| Servers | Server Room | MFA, Patch Management |
| Syslog/NTP | Core | Centralized Logging & Time Sync |

---

## 🚨 Risk Assessment

| Issue | Evidence | Risk | Mitigation |
|--------|-----------|------|-------------|
| Flat student VLAN | No ACLs | 🔴 High | Implement Inter-VLAN ACLs |
| Weak Wi-Fi Auth | PSK | 🟠 Medium | Upgrade to 802.1X/RADIUS |
| No Central Logs | None | 🟠 Medium | Deploy Syslog/NTP |
| Default Credentials | cisco/cisco | 🔴 High | Enforce AAA, SSH, Password Policies |

---

## 🧱 Hybrid Access Network Design (Part 2)

### 🎯 Objective
Enable **secure remote access** for faculty while isolating students and guests from internal networks.

| Zone | VLAN/Subnet | Access Type |
|------|--------------|-------------|
| Faculty VPN | 10.10.20.0/24 | Remote VPN (Split Tunneling) |
| Student Wi-Fi | 10.10.35.0/24 | On-campus Wi-Fi only |
| Guest VLAN | 10.10.40.0/24 | Internet-only access |

**Security Features**
- SSL/IPSec VPNs for encrypted faculty connections  
- RADIUS + MFA for user authentication  
- ACLs for micro-segmentation  
- Syslog monitoring for remote sessions  

---

## 🌐 Web Access Policy & Smart Filtering (Part 3)

### 🎯 Problem
Post-deployment misuse: students streaming videos, using proxies, and torrenting during classes.

### ✅ Solution
A **context-aware, layered web filtering** framework based on **user type** and **time of day**.

| Method | Purpose | Recommendation |
|---------|----------|----------------|
| DNS Filtering | Lightweight domain blocking | Quick malicious site blocking |
| L7 Firewall | Application-level content control | Main enforcement layer |
| Proxy Filtering | URL logging & caching | For student VLANs |
| Client Enforcement | Device-level restrictions | Optional for managed systems |

### 🧠 Policy Logic
| Group | Allowed | Restricted | Enforcement |
|--------|----------|-------------|-------------|
| Faculty | Research, SaaS | P2P (optional) | L7 Firewall |
| Students | LMS, Research | Streaming, Torrents | DNS + L7 Firewall |
| Guests | Internet Only | Internal VLANs | NAT Rules |

---

## 🧩 Sample Config Snippets

### Inter-VLAN ACL
ip access-list extended STUDENT_ACL
permit ip 10.10.35.0 0.0.0.255 any
deny ip 10.10.35.0 0.0.0.255 10.10.20.0 0.0.0.255
### VPN Tunnel (ASA)
crypto ipsec transform-set VPN-SET esp-aes 256 esp-sha-hmac
crypto map VPN-MAP 10 match address VPN-ACL
crypto map VPN-MAP 10 set peer 203.0.113.1
crypto map VPN-MAP 10 set transform-set VPN-SET
### DHCP Pool
ip dhcp pool STUDENT_WIFI
network 10.10.35.0 255.255.255.0
default-router 10.10.35.1
dns-server 8.8.8.8
### Syslog Setup
logging 10.10.99.5
logging trap informational
