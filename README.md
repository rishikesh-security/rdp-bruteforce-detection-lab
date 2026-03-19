🛡️ RDP Brute-Force Attack — Detection & Incident Response Lab

 Project Summary
This project simulates a real-world RDP (Remote Desktop Protocol) brute-force attack in a controlled lab environment and demonstrates how to detect, investigate, and respond to it using a SIEM platform.
The attacker machine (Kali Linux) used Hydra with the rockyou.txt wordlist to brute-force RDP credentials on a Windows target. Detection and analysis were performed using Splunk Enterprise with Windows Security logs forwarded via the Splunk Universal Forwarder.

🎯 Objectives

Simulate an RDP brute-force attack using Hydra on Kali Linux
Configure Splunk Universal Forwarder to ship Windows Security logs to SIEM
Detect the attack using Windows Event IDs in Splunk
Reconstruct the full attack timeline through log correlation
Document findings in a professional incident response report


🧰 Tools & Technologies
ToolPurposeKali LinuxAttacker machine (VirtualBox VM)Hydra v9.6RDP brute-force attack toolrockyou.txtPassword wordlist (14M+ passwords)Windows 10Attack target & log sourceSplunk EnterpriseSIEM — log aggregation & analysisSplunk Universal ForwarderShips Windows Security logs to SplunkWindows Event ViewerSecondary log validationOracle VirtualBoxLab hypervisor

🖥️ Lab Environment
Attacker:  Kali Linux (VirtualBox)        → 10.0.2.x
Target:    Windows 10 (LAPTOP-SMMVETKR)  → 10.0.2.15  [RDP Port 3389]
Host 2:    Windows 10 (DESKTOP-HMUS15L)  → Local
SIEM:      Splunk Enterprise             → localhost:8000
Forwarder: Splunk Universal Forwarder    → Installed on Windows target

⚔️ Attack Execution
Command used on Kali Linux:
bashhydra -l admin -P /usr/share/wordlists/rockyou.txt rdp://10.0.2.15
What happened:

Hydra launched a dictionary attack against RDP on port 3389
Tested 14,344,399 password combinations from rockyou.txt
Passwords attempted: password, 123456, iloveyou, princess, rockyou, abc123 and many more
Tasks auto-reduced to 4 threads (RDP limitation)


🔍 Detection — Splunk SIEM
Key Event IDs Monitored
Event IDNameSignificance4625Failed LogonPrimary brute-force indicator4624Successful LogonConfirms attacker gained access4672Special Privileges AssignedElevated/admin access granted4627Group Membership InfoUser group logged at logon5379Credential Manager AccessPost-compromise credential activity
Splunk Queries Used
EventCode=4625
EventCode=4624
Findings
MetricResultFailed logons (4625) — 7 days12 events on DESKTOP-HMUS15LSuccessful logons (4624) — 60 min121 events on LAPTOP-SMMVETKRSuccessful logons (4624) — 5 min window2 events during peak attackElevated access (4672)Confirmed at 02:54:40 AM

⏱️ Attack Timeline
02:02:39 AM  →  Hydra brute-force started against 10.0.2.15:3389
02:02–02:09  →  Multiple Event 4625 (failed logons) generated
02:09:21 AM  →  Event 4624 — SUCCESSFUL LOGON (attacker authenticated)
02:10:33 AM  →  Second successful logon in 5-minute window
02:12:38 AM  →  Most recent successful logon — active session confirmed
02:14:00 AM  →  Splunk UF confirmed running, all events forwarded
02:54:00 AM  →  Continued activity on DESKTOP-HMUS15L
02:54:40 AM  →  Event 4672 — Special privileges assigned (admin access)
02:59:00 AM  →  Post-incident cross-validation in Windows Event Viewer

 Key Findings

✅ RDP brute-force attack successfully simulated using Hydra
✅ Attacker gained successful authentication (Event 4624 confirmed)
✅ 121 successful logon events in 60 minutes — highly anomalous
✅ Elevated privileges granted post-logon (Event 4672)
✅ Credential Manager access detected (Event 5379)
✅ Attack detected and correlated across multiple Windows hosts


🛡️ Recommendations

Account Lockout Policy — Lock after 5 failed attempts in 5 minutes
Disable RDP on systems that don't require it; block port 3389 via firewall
Enable Network Level Authentication (NLA) for all RDP connections
Multi-Factor Authentication (MFA) for all remote access
Splunk Alert — trigger on >5 Event 4625 from same host in 1 minute
Strong Password Policy — minimum 14 characters with complexity rules


📁 Repository Structure
rdp-bruteforce-detection-lab/
│
├── README.md
├── report/
│   └── RDP_BruteForce_Lab_Report.docx
│
└── screenshots/
    ├── 1_hydra_attack.png
    ├── 2_splunk_eventcode4624.png
    ├── 3_splunk_eventcode4624_5min.png
    ├── 4_splunk_eventcode4625_60min.png
    ├── 5_splunk_eventcode4625_7days.png
    ├── 6_splunk_uf_running.png
    └── 7_windows_event_viewer.png

📄 Full Report
The complete incident response report (including detailed analysis, evidence tables, and recommendations) is available in the report/ folder.

 Author
rishikesh-security
Cybersecurity Enthusiast | SOC Analyst | Blue Team
