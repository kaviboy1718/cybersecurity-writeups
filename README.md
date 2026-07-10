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
