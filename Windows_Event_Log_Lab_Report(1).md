# Windows Event Log Investigation Lab Report

**Practical Log Analysis Skills for SOC Analysts**

| Field | Details |
|-------|---------|
| **Author** | Thomas Christoforidis |
| **Role Target** | Junior SOC Analyst / Cyber Security Analyst |
| **Date** | August 2026 |
| **Focus** | Windows Security Event Log analysis and investigation |

---

## 1. Executive Summary

Practical investigation work using Windows Event Logs, focused on the Security channel. Core skills: locating Event IDs, interpreting Logon Types, correlating authentication and process activity, building simple timelines.

---

## 2. Evidence – Successful Logon (4624)

| Field | Value |
|-------|-------|
| Event ID | 4624 |
| TimeCreated | 2026-08-03 14:55:12 |
| Account Name | asmith |
| Account Domain | LAB |
| Logon Type | 2 (Interactive) |
| Logon Process | User32 |
| Authentication Package | Negotiate |
| Workstation Name | WKS01 |
| Source Network Address | — |

**Logon Type reference used:**
- 2 = Interactive
- 3 = Network
- 10 = RemoteInteractive (RDP)
- 11 = CachedInteractive

---

## 3. Evidence – Failed Logon (4625)

| Field | Value |
|-------|-------|
| Event ID | 4625 |
| TimeCreated | 2026-08-03 14:54:01 – 14:54:48 (burst) |
| Account Name | asmith |
| Failure Reason | Unknown user name or bad password |
| Status / Sub Status | 0xC000006D / 0xC000006A |
| Logon Type | 2 |
| Workstation Name | WKS01 |
| Count in window | 6 failures in ~1 minute (lab test) |

**Timeline note:** Multiple 4625 events followed by a successful 4624 — classic pattern for brute-force / spray investigation practice.

---

## 4. Evidence – Process Creation (4688)

| Field | Value |
|-------|-------|
| Event ID | 4688 |
| New Process Name | C:\Windows\System32\whoami.exe |
| Creator Process Name | C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe |
| Subject User | LAB\asmith |
| Token Elevation Type | %%1938 (limited where applicable) |

**Comparison note:** Standard 4688 lacks full command-line detail that Sysmon Event ID 1 provides — reinforcing value of both sources.

---

## 5. Evidence – Account Management

| Event ID | Description | Sample Object |
|----------|-------------|---------------|
| 4720 | User account created | LAB\asmith |
| 4732 | Member added to local security group | IT-Admins |
| 4725 | User account disabled | (test account) |
| 4726 | User account deleted | (cleanup test) |

---

## 6. Investigation Case Study Evidence

**Scenario:** Failed logons → successful logon → process activity.

| Time | Event ID | Summary |
|------|----------|---------|
| 14:54:01 | 4625 | Failed logon – asmith |
| 14:54:15 | 4625 | Failed logon – asmith |
| 14:54:48 | 4625 | Failed logon – asmith |
| 14:55:12 | 4624 | Successful logon – asmith (Type 2) |
| 14:56:03 | 4688 | whoami.exe created |
| 14:56:10 | 4688 | systeminfo.exe created |

**Conclusion:** Clear investigable timeline from native Security log. ATT&CK: T1110 (Brute Force) then normal user activity.

---

## 7. Conclusion

Windows Event Log investigation skills demonstrated with concrete 4624/4625/4688 samples, account management events, and a multi-event timeline.

---

**End of Report**
