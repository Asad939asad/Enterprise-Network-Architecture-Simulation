# Enterprise Network Architecture Simulation

An enterprise-grade, multifloor network design and simulation built in **Cisco Packet Tracer**. This project showcases a robust, secure, and scalable network architecture designed for an 8-floor corporate headquarters with an integrated remote data center and dynamic IoT-based building automation.

---

## 📌 Project Overview

This project implements a complete network infrastructure for **CMDL Pvt. Ltd.**, featuring:
*   **8 Departemental LANs** (spread across an 8-floor building) mapped to dedicated VLANs.
*   **Core/Distribution Layer Redundancy** using two Multilayer (Layer-3) switches.
*   **Dual ISP WAN Infrastructure** utilizing serial links with OSPF dynamic routing.
*   **Secure Remote Data Center** (abroad) connected via a Site-to-Site IPsec VPN tunnel.
*   **Integrated Network Services**: DHCP, DNS, Web Server, and Email.
*   **IoT & Building Automation** system managed via a local automation server.
*   **Defense-in-Depth Security**: Access Control Lists (ACLs), Port Security, SSH v2 management, and password encryption.

---

## 🗺️ Network Topology

Below is the complete high-resolution logical topology of the enterprise network:

<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-16 at 7.25.17 AM.png" alt="Complete Network Topology" width="100%" />
</p>

---

## 📊 IP Addressing & Subnetting Schema

The internal headquarters network is built on the **`192.168.20.0/24`** block, subnetted using Variable Length Subnet Masking (VLSM) to accommodate department sizes efficiently.

### 🏢 Internal LANs Subnet Allocation

| Floor / Department | VLAN ID | Subnet | Subnet Mask | Usable Host Range | Default Gateway | Usable Hosts |
| :--- | :---: | :--- | :--- | :--- | :--- | :---: |
| **Marketing** | 10 | `192.168.20.0/27` | `255.255.255.224` | `192.168.20.2` - `192.168.20.30` | `192.168.20.1` | 30 |
| **Customer Care** | 20 | `192.168.20.32/27` | `255.255.255.224` | `192.168.20.34` - `192.168.20.62` | `192.168.20.33` | 30 |
| **HR** | 30 | `192.168.20.64/27` | `255.255.255.224` | `192.168.20.66` - `192.168.20.94` | `192.168.20.65` | 30 |
| **Development** | 40 | `192.168.20.96/27` | `255.255.255.224` | `192.168.20.98` - `192.168.20.126` | `192.168.20.97` | 30 |
| **Management** | 50 | `192.168.20.128/27` | `255.255.255.224` | `192.168.20.130` - `192.168.20.158` | `192.168.20.129` | 30 |
| **Media** | 60 | `192.168.20.160/27` | `255.255.255.224` | `192.168.20.162` - `192.168.20.190` | `192.168.20.161` | 30 |
| **Accounts** | 70 | `192.168.20.192/27` | `255.255.255.224` | `192.168.20.194` - `192.168.20.222` | `192.168.20.193` | 30 |
| **Works** | 80 | `192.168.20.224/28` | `255.255.255.240` | `192.168.20.226` - `192.168.20.238` | `192.168.20.225` | 14 |
| **Voice** | 120 | — | — | — | — | — |

### ☁️ Remote Data Center (Abroad) Subnet Allocation

The remote server farm is hosted on a secure **`192.168.21.0/28`** block:

*   **Server Router / Gateway**: `192.168.21.1`
*   **DHCP Server**: `192.168.21.5`
*   **DNS Server**: `192.168.21.6`
*   **Web Server**: `192.168.21.7`
*   **Email Server**: `192.168.21.8`

---

## 🏛️ Architecture Breakdown

### 1. Headquarter Building Network

The HQ LAN features structured access layers on every floor routing through redundant distribution switches. End devices automatically lease IP addresses via DHCP Relay configured on the switches.

<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-16 at 7.26.36 AM.png" alt="Building Network" width="85%" />
</p>

### 2. Dual-ISP WAN Network

To ensure high availability and redundancy, the edge router connects to the remote data center via a Multi-provider WAN (using Safaricom-ISP and NAYATEL_ISP routers) running serial links.

<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-16 at 7.26.28 AM.png" alt="ISP Network" width="70%" />
</p>

### 3. Remote Server Farm (New York Data Center)

Contains centralized servers for DNS resolution, corporate email, public/internal web hosting, and DHCP pool distribution.

<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-16 at 7.26.43 AM.png" alt="New York Server Farm" width="55%" />
</p>

---

## 🚀 Key Configurations & Feature Highlights

### ⚡ Layer-3 Switching & DHCP Relay
Inter-VLAN routing is accomplished via Switched Virtual Interfaces (SVIs) on the Layer-3 switches, offloading traffic from the edge router. Since the DHCP server is located in the remote data center, `ip helper-address 192.168.21.5` is configured on all SVIs to forward local broadcast requests.

<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-15 at 10.09.09 PM.png" alt="DHCP Server Configuration" width="60%" />
</p>

### 🛡️ Network Address Translation (NAT Overload)
Static and dynamic PAT is configured on `HQ_ROUTER` to translate internal subnets to public-facing IP addresses for internet access.

<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-15 at 9.57.06 PM.png" alt="NAT CLI Configuration" width="50%" />
</p>

*Verify translation entries:*
<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-15 at 9.25.20 PM.png" alt="NAT Translations Verification" width="65%" />
</p>

### 🔑 Site-to-Site IPsec VPN
To securely bridge the Headquarter Network (`192.168.20.0/24`) and the Data Center Server subnet (`192.168.21.0/28`), a robust IPsec VPN tunnel is established:
*   **Phase 1 (ISAKMP)**: AES-256 encryption, SHA hash, Diffie-Hellman Group 5 key exchange, pre-shared key `vpnpa55`.
*   **Phase 2 (IPsec)**: Encapsulating Security Payload (ESP) transform set using AES and SHA-HMAC.

<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-14 at 8.21.11 PM.png" alt="VPN Phase 1 ISAKMP" width="45%" />
  <img src="ScreenShots/Screenshot 2025-12-14 at 8.21.31 PM.png" alt="VPN Phase 2 Crypto Map" width="45%" />
</p>

---

## 🌐 Deployed Services

### 📧 Corporate Mail Server
SMTP and POP3 services are enabled on domain `cmdl.org.com`. Client profiles range from `user1` to `user10`.

<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-16 at 6.53.11 AM.png" alt="Mail Server Settings" width="50%" />
</p>

*Client Mail Interface verification:*
<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-16 at 6.39.51 AM.png" alt="Mail Send Success" width="45%" />
  <img src="ScreenShots/Screenshot 2025-12-16 at 6.39.59 AM.png" alt="Mail Receive Success" width="45%" />
</p>

### 🖥️ HTTP Web Hosting
Web server hosts dedicated portals (`index.html`, `cybersecurity.html`, `artificialintelligence.html`, etc.) accessible by all authorized departments across the VPN tunnel.

<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-15 at 10.08.49 PM.png" alt="Web Server Directory" width="48%" />
  <img src="ScreenShots/Screenshot 2025-12-15 at 9.38.28 PM.png" alt="Web Server Index Editing" width="48%" />
</p>

---

## 💡 IoT & Building Automation

A localized smart-grid building automation system is deployed on Floor 8 (Works Department), allowing technicians to monitor and manage smart appliances (Fans, Lights, and Power Mains) through a web-based IoT controller page.

<p align="center">
  <img src="ScreenShots/Screenshot 2025-12-16 at 7.26.54 AM.png" alt="IoT Server Connectivity" width="45%" />
  <img src="ScreenShots/Screenshot 2025-12-16 at 6.53.46 AM.png" alt="IoT Monitor Dashboard" width="45%" />
</p>

---

## 🔒 Security Configuration Highlights

All core switches and routers include strict baseline protection schemes:
1.  **VTY Access Control**: VTY lines only accept encrypted **SSH v2** connections (Telnet is completely disabled).
2.  **Management ACL**: Access to device management terminals (VTY/Console) is restricted via ACL 10 to a specific management subnet.
3.  **Password Security**: Encrypted secrets are enforced via `service password-encryption`.
4.  **Banner (MOTD)**: A stern banner warning prevents unauthorized access attempts.

---

## 🛠️ Requirements & How to Run
1.  Download and install **Cisco Packet Tracer** (version 8.0 or higher recommended).
2.  Clone this repository:
    ```bash
    git clone https://github.com/Asad939asad/Enterprise-Network-Architecture-Simulation.git
    ```
3.  Open the file **`CMDL Private Limited.pkt`** inside Cisco Packet Tracer.
4.  Wait for the converging link-state indicators to turn green.
5.  Double-click any end user device (e.g., PC/Laptop) to test connectivity:
    *   Ping other departmental computers to verify Inter-VLAN routing.
    *   Open the Web Browser app and browse `192.168.21.7` (or resolving domain) to view the web services.
    *   Log in to the IoT monitor from the Works department's PC (`PC4`) to control the floor appliances.
