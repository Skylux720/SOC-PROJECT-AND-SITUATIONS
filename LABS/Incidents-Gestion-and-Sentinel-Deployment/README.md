# Modern SOC Lab Deployment: Ingestion via AMA and Incident Management in Microsoft Defender XDR

## 📌 Project Overview
This project demonstrates the end-to-end implementation of an enterprise-grade Security Operations Center (SOC) monitoring environment within **Microsoft Azure**. The laboratory simulates an organizational infrastructure utilizing an Active Directory Domain Controller, centralized critical event collection via the modern **Azure Monitor Agent (AMA)**, and alert automation for unified incident management inside the **Microsoft Defender XDR** portal.

The primary objective is to emulate the complete lifecycle of a security incident: from attack generation at the endpoint, through telemetry ingestion and correlation in the SIEM (**Microsoft Sentinel**), to analyst triage and response.

---

## 🛠️ Technologies and Tools Used
* **Cloud Platform:** Microsoft Azure
* **SIEM / SecOps:** Microsoft Sentinel & Kusto Query Language (KQL)
* **XDR / Incident Management:** Microsoft Defender XDR (Unified Security Portal)
* **Infrastructure:** Windows Server 2025 (Active Directory Domain Controller)
* **Monitoring Agents:** Azure Monitor Agent (AMA) & Data Collection Rules (DCR)
* **Automation / Auditing:** PowerShell & Windows Security Log Audit Policies

---

## 📐 Laboratory Architecture
The data flow and monitoring pipeline follow this architecture:
1. **Event Generation:** Failed logon attempts on the local server (`Server-admin`).
2. **Ingestion (AMA + DCR):** The AMA agent filters and extracts Event ID 4625 utilizing specific Data Collection Rules (DCR).
3. **Analytics (Sentinel):** A scheduled rule continuously executes KQL queries against the `SecurityEvent` table.
4. **XDR Synchronization:** Sentinel bi-directionally pushes the alert to the unified Microsoft Defender connector for analyst triage.

---

## 🚀 Deployment Phases

### Phase 1: Infrastructure & Domain Configuration
* Deployed the `Server-admin` Virtual Machine in Azure running Windows Server 2025.
* Configured the local Active Directory forest and domain: `cyberlab.local`.
* Enabled Advanced Security Audit Policies to log both successful and failed logon attempts.

---

### Phase 2: Telemetry Ingestion via Azure Monitor Agent (AMA)
*This section involved advanced cloud troubleshooting and infrastructure alignment:*
* Evaluated legacy monitoring agents (MMA) and opted to implement the current industry standard (**AMA**).
* **Identity Solution:** Activated a *System-assigned Managed Identity* on the VM to allow the agent to securely authenticate against Azure without hardcoded credentials.
* **DCR Configuration:** Designed a clean Data Collection Rule associated directly with the *Windows Security Events via AMA* connector in Sentinel.


<img width="1815" height="896" alt="Captura de pantalla 2026-06-04 170117" src="https://github.com/user-attachments/assets/08f833f0-ca2c-4124-b09b-967f147fe1b4" />

---

### Phase 3: Detection Engineering & KQL Analytics Rules
Implemented a **Kusto Query Language (KQL)** query tailored to identify potential brute-force or lateral movement activities (Logon Type 3 - Network Logon) that failed authentication.


<img width="1737" height="864" alt="Captura de pantalla 2026-06-04 170334" src="https://github.com/user-attachments/assets/1fd2bcfb-b52f-4791-8160-83f3972be142" />



```kusto
SecurityEvent
| where TimeGenerated > ago(1h)
| where EventID == 4625
| project TimeGenerated, Computer, Account, TargetUserName, SubStatus, LogonType, IpAddress
