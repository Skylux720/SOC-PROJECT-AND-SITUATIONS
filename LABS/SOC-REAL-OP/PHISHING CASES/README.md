# PHISHING ANALYSIS

## 1. Project overwiew
The objective of this laboratory is to analyze a suspicious .eml file to understand the attacker's strategy and ultimate goals. This analysis focuses on parsing email headers, identifying Key Indicators of Compromise (IoCs), and mapping the adversary's behavior to the MITRE ATT&CK framework.

---

## 2. Environment & Tools Used

* Kali Linux: Utilized as a fully isolated virtual environment (sandbox) to conduct the analysis safely without risking the host system.
* Thunderbird: Used to safely open and inspect the structure, formatting, and raw headers of the .eml file.
* URL2png: A secure web-based tool used to capture screenshots and analyze the malicious landing page without direct browser interaction.

---

## 3. Threat Intelligence & MITRE ATT&CK Mapping

During the investigation, the malicious behavior observed was successfully mapped to the following standard tactics and techniques:

* Tactic: Initial Access (TA0001)

* Technique: Phishing (T1566)

* Sub-technique: Spearphishing Link (T1566.002)

Description: The threat actor utilized a deceptively crafted email containing a malicious link disguised as a standard website contact form submission.

---

## 4. Analysis & Step-by-Step Walkthrough

### Step 1: Environment Setup and Initial Triage
The suspicious file was securely downloaded and moved into the isolated Kali Linux virtual environment to prevent any accidental execution or infrastructure leakage. The .eml file was then opened using Thunderbird for safe inspection.



### Step 2: Parsing the Reporting Structure
Initial analysis revealed that the file was an email submission from an internal employee reporting a spam/phishing attempt. The employee attached the original malicious email as an .eml file for the security team to investigate.

 <img width="950" height="828" alt="Captura de pantalla 2026-06-14 002838" src="https://github.com/user-attachments/assets/4c9599ca-8695-4e8f-9b3d-070483830d8c" />


### Step 3: Inspecting the Core Phishing Payload
Upon opening the attached original email, the true nature of the attack was uncovered:

Target Recipient: kinnar1975@yahoo.co.uk

Social Engineering Theme: The email used a classic financial scam lure, promising an opportunity to "make money" to entice the user into taking immediate action.

Delivery Mechanism: The email did not contain a traditional malicious attached file. Instead, it relied entirely on an embedded hyperlink designed to redirect the victim to an external infrastructure.

<img width="958" height="810" alt="Captura de pantalla 2026-06-14 002932" src="https://github.com/user-attachments/assets/42229537-dc4f-448c-938a-ff7568c862e0" />


### Step 4: Infrastructure and Landing Page Audit
The suspicious URL extracted from the email body was analyzed safely using url2png to prevent direct browser interaction or tracking by the threat actor. The screenshot and analysis revealed that the destination site was already down or deleted, which is highly consistent with short-lived, actor-controlled malicious landing pages or compromised domains used for temporary phishing campaigns.

<img width="1546" height="912" alt="Captura de pantalla 2026-06-14 002804" src="https://github.com/user-attachments/assets/e9cb5657-148b-45b9-a193-bf2a521c48ee" />


---


## 5. Indicators of Compromise (IoCs)

During the analysis, the following technical artifacts were identified as Indicators of Compromise (IoCs) associated with this phishing and financial scam campaign:

### Email Artifacts
* **Sender (Mail Delivery Gateway):** `Mailer-Daemon@se7-syd.hostedmail.net.au`
* **Inbound Notification Recipient:** `johnsmith123@gmail.com`
* **Original Spoofed Sender Name:** `Robertbiolo Robertbiolo`
* **Target Malicious Recipient:** `kinnar1975@yahoo.co.uk`
* **Email Subject:** `Website contact form submission`
* **Delivery Status Notification (Error Code):** `554 30 Sorry, your message to kinnar1975@yahoo.co.uk cannot be delivered. This mailbox is disabled.`

### Network & Infrastructure Artifacts
* **Target Remote Mail Server IP:** `188.125.72.73` (`mx-eu.mail.am0.yahoodns.net`)
* **Malicious Phishing URLs:** * `https://35000usdperwwekpodf.blogspot.sg?p=9swghttps://35000usdperwwekpodf.blogspot.co.il?o=0hnd`
  * `https://35000usdperwwekpodf.blogspot.co.il?o=0hnd`
* **Landing Page Status:** Inactive / Removed ("Blog has been removed" status verified safely via URL2PNG)

---

## 6. Incident Response & Remediation Actions

To mitigate the threat and prevent further exposure across the organization, the following containment and playbooks would be executed:

### Step 1: Automated Sender Blocking (Blocklist)
* **Action:** Deploy an automated tenant-wide block rule within the Email Security Gateway (e.g., Microsoft Defender for Office 365 / Exchange Online Protection).
* **Objective:** Immediately block and reject any future inbound emails originating from the attacker's discovered sending address or domain to prevent further phishing attempts.

### Step 2: Internal Scope Investigation (Message Trace)
* **Action:** Execute a platform-wide **Message Trace** or advanced hunting query using the specific email subject (`Website contact form submission`) and sender artifacts.
* **Objective:** Identify if the same malicious email was delivered to other corporate mailboxes or endpoints within the network, ensuring no other users have interacted with the threat.

### Step 3: Proactive Recipient Remediation
* **Action:** For any additional mailboxes identified in Step 2, initiate an automated **Zap/Purge** action (Zero-hour Auto Purge) to delete the malicious email directly from the users' inboxes before they can open it.
