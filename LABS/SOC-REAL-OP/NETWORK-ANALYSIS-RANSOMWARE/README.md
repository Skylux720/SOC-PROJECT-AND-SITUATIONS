# Blue Team Lab Report: Network Analysis - Ransomware (BTLO)

## 📝 Incident Overview
This repository contains the technical analysis and investigation of a ransomware incident affecting **ABC Industries**. A critical corporate tender document was encrypted during the attack. By analyzing network traffic (PCAP), leveraging threat intelligence, and mapping behavioral patterns, the attack vector was isolated, the malware family was identified, and strategic mitigation steps were established.

---

## 🎯 Investigation Objectives & Findings

* **1. Host Operating System:** 32-bit Windows 7 Service Pack 1, build 7601
  *(Significance: An obsolete, unpatched operating system highly vulnerable to legacy exploits).*

* **2. Ransomware Download URL:** http://10.0.2.15:8000/safecrypt.exe
  *(Significance: Delivery mechanism utilizing a raw destination IP and an unauthorized non-standard port).*

* **3. Executable Filename:** safecrypt.exe
  *(Significance: Defensive evasion technique using a deceptive name to appear as a legitimate utility).*

* **4. Malware MD5 Hash:** 4a1d88603b1007825a9c6b36d1e5de44
  *(Significance: The unique digital fingerprint used to pivot and match the threat against global databases).*

* **5. Ransomware Family:** TeslaCrypt
  *(Significance: A well-documented ransomware strain known for historical flaws in its cryptographic implementation).*

* **6. Stated Encryption Algorithm:** RSA-4096
  *(Significance: Asymmetric encryption advertised by the attackers in the ransom note to induce panic and force payment).*

* **7. Suspicious C2 Domain ('d...'):** dunyamuzelerimuzesi.com
  *(Significance: Rogue infrastructure used for automated beaconing and encryption key management).*

* **8. Decrypted Document Flag:** BTLO-T3nd3r-Fl@g
  *(Significance: The final validation string recovered after successfully reverse-engineering the incident).*

---

## 🕵️‍♂️ Step-by-Step Incident Methodology

### Step 1: Environment & Context Discovery
The investigation initiated by parsing the packet capture metadata within Wireshark's Frame Properties. The target endpoint was identified as a legacy Windows 7 machine. This immediate baseline context flagged the asset as a soft target prone to remote code execution and older exploit vectors.

<img width="872" height="387" alt="image" src="https://github.com/user-attachments/assets/0dd22d59-c07e-43b7-bd4b-bcb0241c3090" />


### Step 2: Isolating the Delivery Vector
To track how the ransomware entered the perimeter, the network stream was filtered for active file transfers. Inspection of HTTP web requests revealed a highly anomalous transaction where the host requested an executable payload directly from a raw internal/external IP on port 8000. The binary was delivered under the masqueraded name 'safecrypt.exe'.

<img width="1327" height="677" alt="image" src="https://github.com/user-attachments/assets/27f87020-802b-4683-9a73-40cac5eb391a" />

### Step 3: Malware Carving & Threat Intel Alignment
The suspicious binary object was carved directly out of the PCAP file. Once isolated in a safe directory, its MD5 cryptographic hash was generated. Querying this unique footprint against global threat reputation engines like VirusTotal immediately stripped away its disguise, confirming it as the notorious TeslaCrypt ransomware.

<img width="747" height="55" alt="image" src="https://github.com/user-attachments/assets/9c520c48-98ef-462a-85c9-c560f8c221ba" />

<img width="1737" height="486" alt="image" src="https://github.com/user-attachments/assets/e55597b6-b1b7-42e3-8292-6c2adb88dc7e" />

### Step 4: Mapping Command & Control (C2) Traffic
Malware typically rings home to sync up with its operators. By filtering for outbound DNS resolution queries right after the file execution timestamp, a massive spike was detected targeting 'dunyamuzelerimuzesi.com'. This confirmed the exact rogue domain being utilized for malicious C2 beaconing.

<img width="1360" height="682" alt="image" src="https://github.com/user-attachments/assets/f2e40dfe-5fc4-46b2-8bec-32b151adf4f0" />


### Step 5: Cryptographic Recovery Logic
Although the ransom note threatened the deployment of unbreakable asymmetric RSA-4096 keys, active threat intelligence on early TeslaCrypt operations proved otherwise. Leveraging public master decryption keys released by the security community, the 'Tender Document' was successfully recovered, exposing the hidden validation flag.

---

## 🛡️ Defensible Architecture Recommendations
* **Decommission Legacy Assets:** Immediately migrate all Windows 7 systems to modern, actively supported operating systems with continuous patch deployment cycles.
* **Egress Filtering:** Implement strict firewall rules to drop outbound traffic originating from standard workstations toward non-standard high ports (e.g., port 8000).
* **Endpoint Detection & Response (EDR):** Deploy an EDR solution capable of killing anomalous mass-file modification behavior and untrusted executable path operations.
