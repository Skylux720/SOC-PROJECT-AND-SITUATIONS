# WordPress Intrusion Analysis - Compromised Plugin & Web Shell Detection

## Executive Summary
During a routine security review of the Apache web server access logs (access.log), an unauthorized intrusion was detected targeting a WordPress instance. The attacker scanned the infrastructure, identified a vulnerable file-upload component, and successfully deployed a PHP web shell to achieve Remote Code Execution (RCE). 

This report documents the chronological breakdown of the incident, identifies the Indicators of Compromise (IoCs), and outlines the remediation steps taken to secure the environment.

---

## Incident Timeline & Analysis

### 1. Reconnaissance and Vulnerability Identification
The attacker performed automated scanning against the WordPress plugins directory. Although initial attempts targeted Contact Form 7 (referencing historical artifacts like CVE-2020-35489), the scanner successfully identified a high-severity flaw in the Simple File List plugin, specifically version 4.2.2.

* Target Vulnerability: Arbitrary File Upload via insecure input validation in the plugin's upload engine components (ee-upload-engine.php).

### 2. Exploitation and Web Shell Deployment
Leveraging the upload vulnerability, the attacker bypassed initial extension filtering by uploading a malicious script utilizing a .phtml extension, which was subsequently executed or renamed as a standard PHP file within the web-accessible directory.

* Malicious File Name: fr34k.php (initially observed with alternating .phtml extensions).
* Absolute Upload Path: /wp-content/uploads/simple-file-list/fr34k.php

### 3. Remote Code Execution (RCE) & Persistent Access
The attacker established a web shell interface to tunnel system commands directly through the HTTP protocol. Log analysis revealed persistent POST and GET requests interacting with the web shell, carrying payloads containing standard Linux reconnaissance commands (whoami, id, ls).

* Final Access HTTP Status Code: The last successful interaction with the operational shell returned an HTTP 200 OK status code, shortly before subsequent requests resulted in HTTP 404 Not Found errors, indicating either self-destruction by the actor or automated containment.

<img width="1373" height="620" alt="Captura de pantalla 2026-06-16 004414" src="https://github.com/user-attachments/assets/2a3f809c-6987-400c-9904-77bfa73c051f" />


<img width="1358" height="761" alt="Captura de pantalla 2026-06-16 004433" src="https://github.com/user-attachments/assets/fcf331bf-92d2-4cf7-b96d-711959733576" />

---

## Indicators of Compromise (IoCs)

### Network & Application Artifacts
| IoC Type | Value / Path | Description |
| :--- | :--- | :--- |
| Vulnerable Plugin | Simple File List v4.2.2 | Vector used for initial entry |
| Malicious File | fr34k.php / fr34k.phtml | PHP Web Shell backdoor |
| Compromised Directory | /wp-content/uploads/simple-file-list/ | Directory where execution permissions should be disabled |

### Key Log Excerpt (Evidence)
REVISIÓN REQUERIDA ANTES DE PUBLICAR: En la línea de abajo, reemplaza lo que está entre corchetes con los datos reales de la última línea exitosa de tu access.log:
* 14/JAN/2021 06:30:05
* 4266
* Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36

14/Jan/2021:05:43:26 +0000 "POST /wp-content/uploads/simple-file-list/fr34k.php HTTP/1.1" 200 4266 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.88 Safari/537.36"

---

## Remediation & Hardening Report

1. Malware Eradication: Deleted the malicious files fr34k.php, fr34k.phtml, and audited the /emergency.php root file to ensure no persistence scripts remained.
2. Component Patching: Upgraded the Simple File List plugin to the latest secure stable version (or completely removed the component if not business-critical).
3. Directory Hardening: Implemented a configuration within the /wp-content/uploads/ directory via .htaccess to explicitly deny the execution of any PHP scripts (Deny from all para archivos .php).
4. Log Rotation & Centralization: Configured Apache logs to stream to a centralized SIEM/Log Management platform to prevent log tampering by malicious actors.
