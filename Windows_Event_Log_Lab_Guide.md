# Windows Event Log Investigation Home Lab Guide

**Practical Log Analysis for SOC Analyst Training**  
Version 1.0 | August 2026

---

## 1. Overview & Objectives

This guide focuses on **Windows Event Log investigation** — one of the core skills every SOC analyst must develop. Windows Event Logs are the primary native telemetry source on Windows systems and form the foundation of many detections and investigations.

**Primary Goals:**
- Understand the main Windows Event Log channels
- Master the most important Security Event IDs for SOC work
- Practise filtering, searching, and correlating events
- Investigate common scenarios (logons, privilege use, process creation, account changes)
- Combine with Sysmon and SIEM when available
- Document findings in a professional format

**Why this matters for SOC roles:**
- Almost every Windows-related incident investigation starts with Event Logs
- Understanding “normal” vs “suspicious” log patterns is essential for triage
- Skills transfer directly to SIEM queries (Splunk, Elastic, Sentinel, Wazuh, etc.)

---

## 2. Key Windows Event Log Channels

| Channel | Location in Event Viewer | Primary Use |
|---------|--------------------------|-------------|
| **Security** | Windows Logs → Security | Logons, authentication, privilege use, object access, process creation (basic) |
| **System** | Windows Logs → System | Service starts/stops, system errors, driver issues |
| **Application** | Windows Logs → Application | Application-specific errors and information |
| **Sysmon Operational** | Applications and Services Logs → Microsoft → Windows → Sysmon → Operational | High-fidelity process, network, DNS, file events (if Sysmon is installed) |
| **PowerShell** | Applications and Services Logs → Microsoft → Windows → PowerShell | PowerShell script block and module logging (when enabled) |
| **Windows Defender** | Applications and Services Logs → Microsoft → Windows → Windows Defender | Antivirus detections and actions |

---

## 3. Most Important Security Event IDs for SOC Analysts

### Authentication & Logon
| Event ID | Description | Notes |
|----------|-------------|-------|
| **4624** | Successful logon | Look at Logon Type (2=Interactive, 3=Network, 10=RemoteInteractive/RDP) |
| **4625** | Failed logon | Brute force, password spraying, account lockouts |
| **4634** | Logoff | Session end |
| **4648** | Logon with explicit credentials | Often seen with RunAs or lateral movement |
| **4672** | Special privileges assigned | New admin logon indicator |

### Account & Group Management
| Event ID | Description |
|----------|-------------|
| **4720** | User account created |
| **4722** | User account enabled |
| **4723** | Attempt to change password |
| **4724** | Attempt to reset password |
| **4725** | User account disabled |
| **4726** | User account deleted |
| **4732** | Member added to security-enabled local group |
| **4733** | Member removed from security-enabled local group |
| **4756** | Member added to universal security group |

### Process & Privilege
| Event ID | Description | Notes |
|----------|-------------|-------|
| **4688** | New process created | Limited compared with Sysmon Event ID 1 (no full command line by default in older configs) |
| **4673 / 4674** | Sensitive privilege use | |
| **1102** | Audit log cleared | Strong indicator of defence evasion |

### Object Access & Other
| Event ID | Description |
|----------|-------------|
| **4663** | Attempt to access object |
| **5140 / 5145** | Network share access |
| **7045** | New service installed (System log) |

---

## 4. Lab Setup Options

### Minimum
- Any Windows 10/11 or Windows Server VM
- Event Viewer (built-in)
- Ability to generate logons and process activity

### Recommended
- Domain-joined Windows client from your Active Directory lab
- Sysmon installed (from previous lab)
- Wazuh agent forwarding both Security and Sysmon logs
- Optional: enable PowerShell Script Block Logging via GPO or local policy

---

## 5. Enabling Better Logging (Important)

### Audit Policy (via GPO or Local Security Policy)
Recommended advanced audit settings for a lab:
- Audit Logon Events → Success and Failure
- Audit Account Logon Events → Success and Failure
- Audit Process Tracking → Success
- Audit Account Management → Success and Failure
- Audit Object Access → Success and Failure (as needed)
- Audit Policy Change → Success and Failure

### PowerShell Logging
Enable via Group Policy or registry:
- Turn on Module Logging
- Turn on Script Block Logging
- Turn on Transcription (optional)

---

## 6. Practical Investigation Exercises

### Exercise 1 – Successful and Failed Logons
1. Log on interactively with a domain or local account.
2. Attempt several failed logons with a wrong password.
3. Filter Security log for Event ID 4624 and 4625.
4. Note Logon Type, Account Name, Source Network Address, and Failure Reason.

### Exercise 2 – Process Creation
1. Launch several programs (notepad, cmd, powershell, browser).
2. Look for Event ID 4688 (and Sysmon Event ID 1 if available).
3. Compare the level of detail between standard 4688 and Sysmon.

### Exercise 3 – Account Manipulation
1. Create a new local user.
2. Add the user to a group.
3. Disable or delete the account.
4. Locate the corresponding 4720 / 4732 / 4725 / 4726 events.

### Exercise 4 – RDP / Remote Logon (if available)
1. RDP into the machine.
2. Find Event ID 4624 with Logon Type 10.
3. Note the source IP and account used.

### Exercise 5 – Correlation Practice
1. Generate a sequence: failed logons → successful logon → process creation → network activity.
2. Build a simple timeline using timestamps from the Security log (and Sysmon).
3. Write a short investigation summary.

### Exercise 6 – SIEM View (if Wazuh is running)
1. Confirm Security and Sysmon events are arriving.
2. Search for specific Event IDs or account names.
3. Practise the same investigation from the SIEM interface instead of Event Viewer.

---

## 7. Investigation Tips

- Always note **Logon Type** for 4624 events — it tells you how the authentication happened.
- Correlate 4624/4625 with 4672 (special privileges) for admin logons.
- Use the **Record ID** or timestamps to pivot between related events.
- Clear filtering is essential — learn to filter by Event ID, Account Name, and time range quickly.
- When Sysmon is present, prefer it for process-related questions; use Security log for authentication and account management.

---

## 8. Documentation for Portfolio

Capture screenshots of:
- Filtered Event ID 4624 / 4625 views
- A process creation event (4688 or Sysmon ID 1)
- Account creation / group change events
- A simple timeline of a simulated activity chain
- The same events viewed from your SIEM (if available)

---

## 9. Next Steps

- Enable richer audit policies and PowerShell logging permanently in your lab
- Practise the same investigations from the Wazuh SIEM interface
- Combine with Sysmon for process-level detail and with Wireshark for network confirmation
- Build a personal cheat sheet of the Event IDs you use most often
- Write a short “Windows Logon Investigation” playbook

---

## 10. References

- Microsoft Security Event documentation
- Ultimate Windows Security Event ID lists
- SANS posters and logging cheat sheets
- Your existing Sysmon, Active Directory, and Wazuh lab documentation

---

**Document prepared for practical SOC and cyber security skill development.**
