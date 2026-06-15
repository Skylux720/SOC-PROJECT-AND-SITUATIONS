# Phishing Email Analysis - Amazon Brand Spoofing

## 1. Objective
The objective of this laboratory is to analyze a suspicious phishing email masquerading as a security alert from Amazon. The analysis focuses on verifying sender authenticity, identifying social engineering tactics, extracting Indicators of Compromise (IoCs), and mapping the threat to the **MITRE ATT&CK** framework to build defensive playbooks.

---

## 2. Environment & Tools Used
* **Kali Linux:** Utilized as a fully isolated virtual environment (sandbox) to conduct the email triage and header analysis safely.
* **Thunderbird:** Used to render the HTML structure safely, inspect metadata, and extract the raw email headers (`.eml`).
* **Text Editors (Mousepad/Notepadqq):** Used for safe string extraction and checking embedded URLs within the raw source code.

---

## 3. Threat Intelligence & MITRE ATT&CK Mapping

The threat actor's behavior during this campaign aligns with the following matrix tactics and techniques:

* **Tactic:** Initial Access ([TA0001](https://attack.mitre.org/tactics/TA0001/))
* **Technique:** Phishing ([T1566](https://attack.mitre.org/techniques/T1566/))
* **Sub-technique:** Spearphishing Link ([T1566.002](https://attack.mitre.org/techniques/T1566/002/))
  * *Description:* The adversary delivered a malicious link embedded within a high-urgency call-to-action button ("Review Account").
* **Technique:** Impersonation ([T1656](https://attack.mitre.org/techniques/T1656/))
  * *Description:* The email leverages brand spoofing by mimicking Amazon's corporate visual identity, including logos and typical account limitation vocabulary.

---

## 4. Analysis & Step-by-Step Walkthrough

### Step 1: Initial Triage and Header Inspection
The `.eml` file was imported into **Thunderbird** inside the isolated **Kali Linux** workspace. A quick inspection of the top-level headers immediately revealed mismatched identity metrics:

* **Display Name:** Amazn
* **Sender Address (`From:`):** `amazon@zyevantoby.cn`
* **Recipient Mailbox (`To:`):** `saintington73@outlook.com`
* **Subject Line:** `Your Account has been locked`

**Analyst Note:** While the display name attempts to mimic the brand, the actual domain extension `.cn` (China) confirms the email address does not belong to Amazon's legitimate infrastructure (`amazon.com`).

### Step 2: Social Engineering and Payload Analysis
The email body employs psychological coercion by stating that account access, payment processing, and purchasing abilities have been restricted due to "significant changes in account activity." 

To enhance visual credibility, the threat actor linked a legitimate Amazon logo hosted on an external content delivery network (CDN):
* `https://images.squarespace-cdn.com/content/52e2b6d3e4b06446e8bf13ed/1500584238342-OX2L298XVSKF8AO6I3SV/amazon-logo?format=750w&content-type=image%2Fpng`

The core payload consists of a large, high-contrast hyperlinked button labeled **"Review Account"**. This link redirects the victim to an external, actor-controlled phishing landing page designed for **credential harvesting** and financial data theft.

<img width="950" height="827" alt="Captura de pantalla 2026-06-14 223523" src="https://github.com/user-attachments/assets/89bee1a9-2de6-4550-9c62-100378d5ce5b" />


<img width="1390" height="780" alt="Captura de pantalla 2026-06-14 223646" src="https://github.com/user-attachments/assets/4bc94f0d-17d5-4868-a817-21128ee23e8a" />

---

## 5. Indicators of Compromise (IoCs)

The following technical indicators were extracted during the analysis of this incident:

### Email Artifacts
* **Spoofed Sender Name:** `Amazon`
* **Malicious Sender Address:** `amazon@zyevantoby.cn` (Top-level domain: `.cn`)
* **Target Recipient:** `saintington73@outlook.com`
* **Email Subject:** `Your Account has been locked`
* **Timestamp:** `7/13/21, 12:40 PM`

### Network & Infrastructure Artifacts
* **External Image Source (CDN):** `https://images.squarespace-cdn.com/content/52e2b6d3e4b06446e8bf13ed/1500584238342-OX2L298XVSKF8AO6I3SV/amazon-logo?format=750w&content-type=image%2Fpng`
* **Phishing Destination URL:** *https://emea01.safelinks.protection.outlook.com/?url=https%3A%2F%2Famaozn.zzyuchengzhika.cn%2F%3Fmailtoken%3Dsaintington73%40outlook.com&data=04%7C01%7C%7C70072381ba6e49d1d12d08d94632811e%7C84df9e7fe9f640afb435aaaaaaaaaaaa%7C1%7C0%7C637618004988892053%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=oPvTW08ASiViZTLfMECsvwDvguT6ODYKPQZNK3203m0%3D&reserved=0*

---

## 6. Incident Response & Remediation Actions

To mitigate the risk of corporate credentials being compromised by this specific vector, the following defensive actions should be implemented:

### Step 1: Perimeter Blocklist Deployment
* **Action:** Create a strict inbound block rule at the email gateway for the domain `zyevantoby.cn` and any variations of the sending address.
* **Objective:** Prevent any current or future variants of this phishing campaign from reaching employee inboxes.

### Step 2: Proactive Hunting (Message Trace)
* **Action:** Run a tenant-wide query in the SIEM or Exchange Transport Logs using the subject string `Your Account has been locked` and the sending sender pattern.
* **Objective:** Identify if other internal assets or devices received the same malicious message to measure the exact scope of the attack.

### Step 3: Malicious URL Takedown & Endpoint Block
* **Action:** Submit the extracted destination URL behind the "Review Account" button to security vendors (e.g., Google Safe Browsing, Microsoft SmartScreen) and apply a block rule on the corporate Web Proxy / DNS Filter.
* **Objective:** Ensure that even if an internal user inadvertently clicks the button, the network controls will intercept and terminate the connection before the phishing page loads.

---

## 7. Conclusion
This case study highlights the importance of checking the actual sender address domain rather than relying on display names or embedded corporate logos. By utilizing legitimate external CDNs to fetch icons, the adversary attempts to look authentic, but the infrastructure mismatch (`.cn` domain targeting an Outlook account) allows SOC analysts to quickly classify and neutralize the threat.
