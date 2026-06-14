# Malware Analysis & Detection: Follina Vulnerability (CVE-2022-30190)

## Project Overview
This project documents the step-by-step analysis of a malicious Microsoft Word document (sampler.doc) utilizing a Blue Team Labs Online (BTLO) environment. The objective was to inspect the file's internal structure, leverage OSINT threat intelligence via VirusTotal to identify Indicators of Compromise (IOCs), map the attack to the MITRE ATT&CK framework, and build an engineering detection rule for a Security Operations Center (SOC).

---

## Tools Used
* Kali Linux (Analysis Environment / Deconstruction)
* VirusTotal (Threat Intelligence & Behavioral Analysis)
* Microsoft Sentinel / EDR (Detection Logic)

---

## Technical Implementation & Workflow

### 1. Terminal Deconstruction
Modern Microsoft Office documents function as disguised archive containers. To safely inspect the internal XML components and look for artifacts without executing the file, the document were extracted in a VM using Kali Linux:

# Manually exploring the extracted directories revealed the relationship file

<img width="862" height="617" alt="Captura de pantalla 2026-06-12 013021" src="https://github.com/user-attachments/assets/fb61dbc0-e9e4-41a2-93a8-ebca69e98831" />

<img width="753" height="607" alt="Captura de pantalla 2026-06-12 013032" src="https://github.com/user-attachments/assets/814495aa-46d9-4ee6-ac4b-9e2b1190f270" />


### 2. Threat Intelligence & Threat Analysis (VirusTotal)
After isolating the file, it was queried on VirusTotal to analyze the threat feed, detection rates, and high-level behavioral indicators:

* Exploit Target: The document triggers the Follina vulnerability by abusing the Windows HTML rendering engine (mshtml.dll) to fetch an external link.
* Payload Constraints: According to the internal HTML processing functions, the remote payload must be 4,096 bytes or larger to successfully execute. This forces attackers to pad the malicious file with dead data to bypass standard network security filters.
* File Identification: VirusTotal successfully flagged the file's SHA-256 hash as highly malicious, with a steep detection rate across industry vendors confirming it as a known exploit vector.
* High-Priority Threats: The intelligence platform identified the sample as a critical trigger for the Follina vulnerability (CVE-2022-30190), pointing out embedded malicious URLs acting as external relationship templates.
* Execution Behavior: Behavioral flags on VirusTotal highlighted the anomalous spawning of the Windows Support Diagnostic Tool (msdt.exe) by the parent office application, marking it as a critical evasion and execution threat.

<img width="1385" height="763" alt="Captura de pantalla 2026-06-12 013708" src="https://github.com/user-attachments/assets/8ee27871-f7f0-4d9d-a162-6d0841a32a2d" />

---


## MITRE ATT&CK Mapping
* Tactic: Execution (TA0002)
* Technique: Command and Scripting Interpreter (T1059) / Exploitation for Client Execution (T1203)

---

## Recommended SOC Detection Engineering (SIEM/EDR Logic)

To mitigate this threat across an enterprise infrastructure, a behavioral detection rule should be engineered within the SIEM/EDR platform. Rather than relying on easily alterable file hashes, the rule must monitor for abnormal parent-child process relationships.

### Detection Strategy
* Log Source: Windows Security Event Logs (Event ID 4688) or Sysmon (Event ID 1) / EDR Process Telemetry.
* Analytical Logic: Trigger a high-severity alert whenever a Microsoft Office binary spawns the Windows diagnostic utility, as productivity software has no legitimate administrative reason to invoke this process.

### Conceptual Detection Rule
* Parent Process: winword.exe OR excel.exe OR powerpnt.exe
* Child Process: msdt.exe
* MITRE Target: Defense Evasion (TA0005) / Execution (TA0002)

---

## Conclusion
This laboratory highlights why modern SOC teams cannot rely solely on traditional antivirus signatures or file extensions. Combining terminal-based file deconstruction with OSINT tools like VirusTotal allows analysts to trace the root behavior of an exploit, enabling the creation of robust parent-child process detection rules to neutralize threats in real time.
