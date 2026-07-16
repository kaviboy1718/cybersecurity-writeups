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

# Incident Response Simulation: Encoded PowerShell & Credential Dumping

## Overview
This write-up covers an incident response scenario from a SOC (Security Operations Center) simulation. The alert triggered on a suspicious encoded PowerShell execution followed by potential credential dumping on a financial department asset.

---

## Alert Analysis
* Alert Title: Suspicious encoded PowerShell execution followed by credential dumping
* Severity: High
* Target Entity (User): j.harris (Finance Dept)
* Target Host: FIN-LAPTOP-07
* Suspicious IP: 185.220.101.5 (Identified as a TOR exit node)
* Process: powershell.exe -enc <encoded_payload>
* Dropped File: C:\Users\j.harris\AppData\Local\Temp\svc32.exe

### Attack Timeline (Chronological Flow)
1. 09:15:03 | WINWORD.EXE spawned powershell.exe with an encoded command. (Indicates a malicious macro execution via a Microsoft Word document).
2. 09:15:04 | certutil.exe was used to download a payload from the TOR exit node (185.220.101.5).
3. 09:15:09 | A persistence mechanism was established via a scheduled task named WindowsUpdateHelper.
4. 09:18:11 | An explicit credential dumping attempt was made to the Domain Controller (DC-01).
5. 09:20:02 | The compromised user account j.harris was successfully added to the Domain Admins group (Privilege Escalation & Lateral Movement).

   <img width="636" height="291" alt="brave_screenshot_techmadesimple3 net" src="https://github.com/user-attachments/assets/68c1fb05-e424-461e-821f-f38f452ab28e" />


---

## Incident Response & Containment Strategy

Based on the severity and progress of the attack (which reached Domain Admin compromise), immediate containment was required to prevent further lateral movement.

### Correct Actions Taken:
1. **Isolate the Endpoint (FIN-LAPTOP-07):** Cut off network access to the compromised machine to stop outbound C2 communication and prevent further lateral scanning.
2. **Disable the Compromised Account (j.harris):** Instantly disable the Active Directory account to revoke the newly gained Domain Admin privileEscalate to Tier 2 (Incident Response Team):Team):** Hand over the incident with a fully documented timeline so Tier 2 can scope the full blast radius, perform memory forensics, and execute proper remediation.
   <img width="637" height="588" alt="brave_screenshot_techmadesimple net3 1" src="https://github.com/user-attachments/assets/90433a84-b33e-494e-9cdf-09a931c14864" />


---

## Key TakeMacro Execution Danger:anger:** Financial departments are primary targets for phishing emails containing malicious macros (WINWORD.EXE spawning Powershell).
* **Abuse of Built-in Tools (LolBins):** The attacker abused certutil.exe to bypass traditional download restPrivilege Escalation Speed:ion Speed:** The time between initial access and Domain Admin compromise was less than 10 minutes. Fast response time is crucial.


# TechMadeSimple - Lab 04: Defender Threat Dashboard Analysis
## Objective
Analyze a process tree within a simulated Defender Threat Dashboard to identify the attack chain, map it to MITRE ATT&CK tactics, and determine the correct mitigation verdict.
## Attack Chain Breakdown
The process tree revealed a multi-stage execution flow, starting from a user opening a malicious document to the attacker gaining complete control over the system.

| Process Name | PID | User / Context | Description |
| :--- | :--- | :--- | :--- |
| explorer.exe | - | j.harris | User shell environment. |
| WINWORD.EXE | - | j.harris | User opens a suspicious invoice document (Invoice_Q1_2026.docm). |
| cmd.exe | 5892 | j.harris | Spawned directly by Word Macro (T1566.001). |
| powershell.exe | 6104 | j.harris | Executed with -ExecutionPolicy Bypass and an encoded command (-enc) to evade detection. |
| certutil.exe | 7230 | j.harris | Used as a Living-off-the-Land Binary (LOLBin) to download svc32.exe from http://185.220.101.5/ via -urlcache. |
| svc32.exe | 7891 | j.harris | The dropped executable payload. (SHA256: a1b2c3d4...f9). |
| cmd.exe | 8021 | SYSTEM | Final payload execution resulting in Local Privilege Escalation to SYSTEM level. |

## Key Takeaways & Mitigation
* Verdict: Confirmed malware dropper via Office Macro (MITRE T1566.001 + T1059.001).
* Remediation: Isolated the affected endpoint (FIN-LAPTOP-07) immediately using Defender's device isolation action to prevent lateral movement.
* Defense Strategy: Block WinWord from spawning child processes (using Attack Surface Reduction - ASR rules) and restrict the usage of certutil for downloading external files.
  <img width="635" height="558" alt="brave_screenshot_techmadesimple net" src="https://github.com/user-attachments/assets/b1adeec9-ec1d-49de-a285-1753fad7be9b" />
