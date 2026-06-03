# Cloud Infrastructure Deployment: Secure Active Directory Domain Controller in Azure

## Project Overview
This project demonstrates the deployment of a secure, centralized identity and access management infrastructure within Microsoft Azure. I configured an isolated virtual network, established static networking parameters, and promoted a clean Windows Server instance to a primary Domain Controller ($cyberlab.local$). 

This environment serves as the foundational core for identity management, access control, and security auditing simulations.

---

## Technical Skills & Tools
* **Cloud Platform:** Microsoft Azure (Virtual Machines, Virtual Networks, Network Security Groups).
* **Operating System:** Windows Server 2025.
* **Core Services:** Active Directory Domain Services (AD DS), DNS (Domain Name System).
* **Networking:** Private IP allocation, VNet architecture, Remote Desktop Protocol (RDP) management.

---
Estructure

<img width="1024" height="559" alt="image" src="https://github.com/user-attachments/assets/1fec2e3a-6faf-44c4-a6c2-019a32478945" />

---

### Network Security Group (NSG) Configuration

To secure the environment, inbound traffic was restricted using a zero-trust approach, allowing only administrative access from a verified source:

| Source IP | Destination IP | Destination Port | Protocol | Action | Purpose |
|-----------|----------------|------------------|----------|--------|---------|
| My_Public_IP | 10.0.0.5 (DC)  | 3389             | TCP      | Allow  | Secure Admin RDP Access |
| VirtualNetwork | VirtualNetwork | Any              | Any      | Allow  | Internal Domain Traffic |
| Any       | Any            | Any              | Any      | Deny   | Default Inbound Deny |

---

## Implementation Steps

### 1. Isolated Network Architecture
* Deployed a dedicated Virtual Network (VNet) in Azure to isolate the infrastructure.
* Configured a static private IP address for the server instance to ensure consistent identity and DNS services across the domain.

### 2. Operating System Provisioning
* Initialized a clean Windows Server VM instance via the Azure Portal.
* Hardened basic access by managing network security rules and ensuring RDP access was restricted to administrative parameters.

### 3. Active Directory Promotion
* Installed the **Active Directory Domain Services (AD DS)** and **DNS** roles via Server Manager.
* Promoted the server to the root Domain Controller of a new forest: `cyberlab.local`.
* Verified infrastructure health by confirming local security database migration to the domain database.

---

## Verification & Results
The successful deployment was verified through the following parameters:
* **Domain Status:** Server Manager confirms the active domain role under `cyberlab.local`.
* **Service Health:** AD DS and DNS services are fully operational and listening on the designated network interfaces.
* **Access Control:** Verified administrative domain authentication via Secure Remote Desktop.

Server Manager Active Directory Status 
<img width="1842" height="890" alt="image" src="https://github.com/user-attachments/assets/a57ca262-0a88-4387-9109-899e43c3b8ff" />

