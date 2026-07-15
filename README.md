# cybersecurity-writeups
Documenting my cybersecurity journey through hands-on labs, TryHackMe rooms, and practical writeups.
## SOC Analyst Level 1 - Alert Classification Lab

In this lab, I practiced my skills in triaging and classifying security alerts from a SIEM environment. The goal was to differentiate between True Positives, False Positives, and Benign activities based on log analysis and context.

### Key Learnings & Findings:

* True Positives (Verified Threats):
    * SSH Brute-Force: Detected 47 failed SSH logins within 3 minutes targeting a production database server—a clear brute-force attempt.
    * Malicious PowerShell: Observed WINWORD.EXE spawning an encoded PowerShell command (-enc). This is a classic indicator of a phishing/malware attack using malicious macros.
    * Persistence Mechanism: Identified a scheduled task named WindowsUpdateHelper using certutil to download a payload.exe from an external IP. This highlights how attackers use legitimate-sounding names to "blend in."
    * C2 Communication: Detected DNS queries to a randomly generated (DGA) domain registered just 2 days ago, indicating active command-and-control traffic.

* False Positives:
    * Failed RDP Login: An alert was triggered for an administrator's failed RDP login. After reviewing the logs, it was identified as a simple user typo, making it a False Positive alert by the monitoring tool.

* Benign Activities:
    * Scheduled Scans: Detected a Nessus vulnerability scan originating from an internal, authorized scanner host. Since this was a scheduled Tuesday scan, it was classified as authorized, benign activity.

### Key Takeaway:
Attackers often attempt to "blend in" by using familiar names (like WindowsUpdateHelper). Analyzing the actual command arguments (like certutil or PowerShell -enc) is more critical than looking at the file names alone.<img width="1031" height="590" alt="brave_screenshot_techmadesimple net" src="https://github.com/user-attachments/assets/8283a4ae-8a23-4e4a-80cd-07b1eeb911ed" />


---

## 📊 Lab 2: SIEM Log Analysis
* Platform: TechMadeSimple
* Objective: Identify anomalous (suspicious) security events from a Windows SIEM log viewer.

### 🔍 Identified Anomalous Events:
* Malicious Process Spawning: WINWORD.EXE launching powershell.exe (indicates a phishing macro execution).
* LOLBin Usage: PowerShell calling certutil.exe with -urlcache to download external files.
* Persistence Mechanism: A suspicious scheduled task named WindowsUpdateHelper was created by SYSTEM.
* Lateral Movement: User j.harris attempting a logon with explicit credentials targeting the Domain Controller (DC-01).
* Privilege Escalation: User account successfully added to the high-privileged Domain Admins group.

### 🏆 Lab Result:
Successfully flagged all 5 anomalies with 100% accuracy (0 missed, 0 false flags).
<img width="985" height="585" alt="brave_screenshot_techmadesimple net2" src="https://github.com/user-attachments/assets/34edba76-19a5-4c52-afe9-9cafa6a60bb0" />


---

## 📊 Lab 2: SIEM Log Analysis — Suspicious encoded PowerShell execution followed by credential dumping
* Platform: TechMadeSimple
* Objective: Investigate a high-severity alert within a simulated corporate network, identify system anomalies using SIEM logs, and execute correct Incident Response (IR) containment actions.

---

### 🔍 Phase 1: Threat Detection & Anomalies Identified
During the log analysis, 5 critical security anomalies were successfully detected and flagged with 100% accuracy:

* Malicious Process Spawning: WINWORD.EXE spawned powershell.exe with an encoded command, indicating a classic phishing/malicious macro execution.
* LOLBin Exploitation (Live off the Land): powershell.exe called certutil.exe with -urlcache to download an external payload (svc32.exe) from a known TOR exit node (185.220.101.5).
* Persistence Mechanism: A suspicious scheduled task named WindowsUpdateHelper was created by SYSTEM using certutil to maintain a foothold on the target machine.
* Lateral Movement: The compromised user account j.harris attempted an explicit credential logon targeting the Domain Controller (DC-01).
* Privilege Escalation: The adversary successfully added the j.harris account to the high-privileged Domain Admins group, gaining full control.
* <img width="636" height="291" alt="brave_screenshot_techmadesimple3 net" src="https://github.com/user-attachments/assets/b129d7f0-bb86-43ac-814a-f3afae195dc2" />


---

### 🛡️ Phase 2: Incident Response & Containment Action
Once the threat timeline was established, the following containment strategy was executed to minimize the blast radius:

* Action Taken: Selected "Isolate FIN-LAPTOP-07, disable j.harris AD account, escalate to Tier 2 with full timeline" as the correct response action.
* Technical Justification:
  1. Host Isolation: Immediately isolating FIN-LAPTOP-07 from the network stops the adversary's C2 communication and prevents further lateral movement.
  2. Account Disabling: Disabling the j.harris Active Directory (AD) account blocks the attacker from utilizing their newly acquired Domain Admins privileges.
  3. Escalation: Handing over a detailed, chronological attack timeline to the Tier 2 Incident Response team ensures rapid and effective remediation.
  4. <img width="637" height="588" alt="brave_screenshot_techmadesimple net3 1" src="https://github.com/user-attachments/assets/d8906065-0f8b-4309-bcc1-34a6df4fe3fd" />


---

### 🏆 Lab Results & Verification
* Detection Accuracy: 100% (5/5 Anomalies Correctly Flagged)
* Response Accuracy: Correct Containment Action Selected
