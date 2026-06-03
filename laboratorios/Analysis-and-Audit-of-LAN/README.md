# 📑 [PORTFOLIO] Perimeter Security Audit: Obsolescence Analysis on Edge Devices (ISP)
**Analyst:** Juan Cadena  
**Date of Analysis:** May 31, 2026  
**Project Type:** Vulnerability Management / Infrastructure Audit  
**Environment:** Local Area Network (Home Lab)  

## 1. Objective
Perform a comprehensive discovery and vulnerability assessment within a local area network (LAN), focusing specifically on the edge router. The goal is identify potential security breaches, obsolete firmware versions, and evaluate the overall exposure risk against external cyber threats.

## 2. Methodology & Telemetry Collection
To extract the technical specifications of the device without causing any service disruption, the following reconnaissance procedure was conducted:

1. **Gateway Identification:** The Windows Command Prompt (CMD) was utilized to execute the `ipconfig` command in order to locate the router's private IP address (Default Gateway).

<img width="1043" height="741" alt="image" src="https://github.com/user-attachments/assets/6331b203-1b7a-483c-841e-7d550ba95504" />

2. **Web Console Data Extraction:** The private IP address was accessed via a web browser to log into the device status panel provided by the ISP, allowing the collection of manufacturing and system data.

<img width="1425" height="903" alt="image" src="https://github.com/user-attachments/assets/33fdd0d2-5735-47ea-8a4e-c6d52807b3e3" />

* **Device Type:** Modem/Router (ISP Property).
* **Hardware Version:** `3.35.1`
* **Active Firmware Version:** `9.36.2020`

## 3. Risk & Vulnerability Assessment
After analyzing the active software version identifiers, the following critical security findings were determined:

* **Critical System Obsolescence (Firmware):** The detected firmware version (`9.36.2020`) indicates that the router's internal operating system was last compiled or updated in **2020**. Operating in the year **2026**, this device has accumulated **6 years without receiving security patches**.
* **Hardware Lifecycle Risk:** The physical hardware revision `3.35.1` suggests an estimated device age of 6 to 8 years (Pre-pandemic). There is a high probability that the manufacturer has declared this model as **End of Life (EoL)**, permanently ceasing technical support and vulnerability remediation.

### Potential Impact & Attack Vectors:

1. **Public CVE Exploitation & Remote Code Execution (RCE):** An external threat actor could scan the WAN interface (public IP) of the router. Upon detecting the unpatched 2020 version, they could deploy public exploits to achieve **Remote Code Execution (RCE)** or trigger a Denial of Service (DoS). Due to the lack of memory validation in obsolete firmware, targeted malicious requests could overwhelm the internal CPU, freezing security protocols into a *Fail-Open* state. This could allow unauthorized port forwarding or the execution of a *Reverse Shell* to gain full root control of the device from the internet.

2. **Absence of Modern Encryption Standards:** Legacy hardware often lacks the processing power and software capabilities required to support modern wireless security protocols, such as WPA3. This restricts the local network to WPA2, leaving it exposed to deauthentication attacks and handshake capturing for offline password cracking.

3. **Resource Exhaustion via Brute Force:** Legacy hardware processors are highly susceptible to saturation from automated traffic. A brute-force attack or automated vulnerability scanning would quickly deplete the modem's CPU and RAM, resulting in a total crash of the local network.

## 4. Proposed Mitigation Plan
As a security analyst, the following high-priority actions are recommended to secure the perimeter infrastructure:

1. **Management Interface Hardening:** Verify and completely disable the "WAN Remote Management" feature within the router settings. This ensures that the device's web console is exclusively accessible from the local network (LAN), making it invisible to automated internet-wide port scans.

2. **Enforced Update or Asset Replacement:** * Coordinate with the service provider's (ISP) technical support to verify if there is a newer, signed firmware patch released after version `9.36.2020` that is compatible with hardware revision `3.35.1`.
   * If the manufacturer confirms the device is *End of Life (EoL)*, request an immediate physical replacement of the modem for a current model with active security support.

3. **Network Segmentation & Dedicated Perimeter (Bridge Mode):** Following architectural best practices, it is highly recommended to configure the ISP modem into **Bridge Mode**. This delegates all routing and packet inspection duties to a dedicated, open-source perimeter Firewall (such as *pfSense* or *OPNsense*). This implementation ensures strict control over inbound connections and proactively intercepts unauthorized outbound traffic (*Reverse Shells*).
