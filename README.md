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
* Platform: ISC2 Certified in Cybersecurity (CC)
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
