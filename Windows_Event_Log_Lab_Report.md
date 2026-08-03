# Windows Event Log Investigation Lab Report

**Practical Log Analysis Skills for SOC Analysts**

| Field | Details |
|-------|---------|
| **Author** | Thomas Christoforidis |
| **Role Target** | Junior SOC Analyst / Cyber Security Analyst |
| **Date** | August 2026 |
| **Focus** | Windows Security Event Log analysis and investigation |
| **Environment** | Windows 10/11 (standalone or domain-joined) |

---

## 1. Executive Summary

This report documents practical investigation work using **Windows Event Logs**, with primary focus on the Security channel. The lab developed core skills required for SOC triage and incident investigation: locating relevant Event IDs, interpreting logon types, correlating authentication and process activity, and building simple timelines.

Key activities included successful and failed logon analysis, process creation review, account management event examination, and correlation of related events. Where available, Sysmon telemetry and SIEM views were used to enrich the investigation.

**Key outcomes achieved:**
- Confident navigation and filtering of the Windows Security log
- Understanding of critical Event IDs (4624, 4625, 4688, 4720, 4672, etc.)
- Interpretation of Logon Types and their investigative meaning
- Ability to build a basic activity timeline from event records
- Awareness of how native Event Logs complement Sysmon and SIEM data
- Structured documentation suitable for portfolio use

---

## 2. Lab Objectives

| # | Objective | Status |
|---|-----------|--------|
| 1 | Navigate and filter Windows Event Viewer effectively | Achieved |
| 2 | Analyse successful and failed logon events (4624 / 4625) | Achieved |
| 3 | Interpret Logon Type values | Achieved |
| 4 | Examine process creation events (4688) | Achieved |
| 5 | Review account and group management events | Achieved |
| 6 | Build a simple investigation timeline | Achieved |
| 7 | Correlate with Sysmon / SIEM where available | Achieved |
| 8 | Document findings professionally | Achieved |

---

## 3. Lab Environment

- Windows 10/11 virtual machine (domain-joined preferred, using the existing Active Directory lab)
- Standard Windows Event Viewer
- Optional: Sysmon installed for enhanced process telemetry
- Optional: Wazuh agent forwarding Security and Sysmon events

This environment reflects the log sources a Tier-1 or Tier-2 SOC analyst works with daily.

---

## 4. Implementation and Exercises Performed

### 4.1 Logon Investigation

Successful interactive logons and multiple failed logon attempts were generated. Security log filters were applied for Event ID 4624 and 4625. Key fields examined:

- Account Name
- Account Domain
- Logon Type
- Source Network Address
- Logon Process / Authentication Package
- Failure Reason (for 4625)

Logon Type values were interpreted (Type 2 = Interactive, Type 3 = Network, Type 10 = RemoteInteractive/RDP, etc.).

### 4.2 Process Creation Review

Multiple processes were launched (command prompt, PowerShell, browsers, utilities). Event ID 4688 records were located and compared with Sysmon Event ID 1 where available. The difference in command-line visibility between standard 4688 and Sysmon was noted.

### 4.3 Account Management Events

A test user account was created, added to a group, and later disabled/deleted. Corresponding events (4720, 4732, 4725, 4726) were located and reviewed. These events form important audit trails for insider and compromised-account investigations.

### 4.4 Timeline Construction

A short sequence of activity (failed logons → successful logon → process launches) was performed. Events were ordered by timestamp to produce a simple investigation timeline demonstrating how discrete log records combine into a coherent narrative.

### 4.5 SIEM Perspective (where available)

The same Event IDs were searched from the Wazuh dashboard to practise pivoting between host-level Event Viewer and centralised SIEM views.

---

## 5. Results and Evidence

### 5.1 Key Event IDs Practised

| Event ID | Category | Investigative Use |
|----------|----------|-------------------|
| 4624 | Successful Logon | Confirm authentication, identify Logon Type and source |
| 4625 | Failed Logon | Detect brute force, spraying, or misconfiguration |
| 4672 | Special Privileges | Indicator of administrative logon |
| 4688 | Process Creation | Basic process visibility (enhanced by Sysmon) |
| 4720 / 4726 | Account Created / Deleted | Account lifecycle auditing |
| 4732 | Group Membership Change | Privilege escalation or access changes |
| 1102 | Audit Log Cleared | Potential defence evasion |

### 5.2 Practical Observations

- Logon Type is one of the most important fields in 4624 events and immediately narrows the investigation scope.
- Failed logon events (4625) with the same source and target account in a short time window are classic brute-force indicators.
- Standard 4688 events lack the full command line that Sysmon provides — highlighting why both sources are valuable.
- Account management events create a clear audit trail that is often critical in insider or compromised-account cases.
- Building even a short timeline from multiple Event IDs significantly improves investigative clarity.

---

## 6. Investigation Case Study

**Scenario:** Investigate a sequence of failed logons followed by a successful logon and subsequent process activity.

**Steps performed:**

1. Generated several failed logons for a test account, then logged on successfully.
2. Filtered the Security log for Event ID 4625 and 4624 around the relevant time window.
3. Recorded timestamps, account name, Logon Type, and source information.
4. Located related process creation events (4688 / Sysmon ID 1) shortly after the successful logon.
5. Ordered the events chronologically into a simple timeline.
6. Wrote a short summary describing the activity and a classification (expected lab activity / suspicious if seen in production).

**Key Learning:** Individual events have limited meaning in isolation. The value of Event Log investigation comes from correlating multiple related records into a coherent timeline and narrative.

---

## 7. Challenges Encountered and Resolutions

| Challenge | Resolution |
|-----------|------------|
| High volume of events | Used targeted Event ID filters and narrow time ranges |
| Limited command-line detail in 4688 | Cross-referenced with Sysmon Event ID 1 when available |
| Understanding Logon Type numbers | Maintained a quick reference list of common Logon Types |
| Pivoting between host and SIEM views | Practised the same queries in both Event Viewer and Wazuh |

---

## 8. Lessons Learned

1. **Logon Type is critical** — It immediately tells you the nature of the authentication and guides the next investigative steps.
2. **Correlation beats single events** — Timelines built from multiple Event IDs are far more powerful than isolated records.
3. **Native logs + Sysmon is the practical combination** — Security log for authentication and account changes; Sysmon for rich process detail.
4. **Filtering skill is fundamental** — The ability to quickly reduce noise is essential for efficient triage.
5. **Documentation turns activity into evidence** — Clear notes and screenshots make the work usable for reports, handovers, and portfolio demonstration.

---

## 9. Recommendations and Next Steps

- Enable advanced audit policies and PowerShell logging permanently in the lab.
- Practise the same investigations entirely from the SIEM interface.
- Create a personal one-page Event ID cheat sheet for rapid reference.
- Combine with the Sysmon and Wireshark labs for full host + network visibility.
- Write a short standard operating procedure (SOP) for “Windows Logon Investigation”.
- Expand into service installation (7045), scheduled tasks, and persistence-related events.

---

## 10. Conclusion

The Windows Event Log Investigation lab successfully developed practical skills in locating, interpreting, and correlating native Windows security events. Through focused exercises on logon activity, process creation, account management, and timeline construction, core competencies required for SOC triage and investigation were strengthened.

These skills form a direct foundation for daily work in a Security Operations Centre and complement the Sysmon, Active Directory, Wireshark, and Wazuh SIEM labs already completed.

---

## Appendix – Quick Event ID Reference

```
4624  Successful logon
4625  Failed logon
4672  Special privileges assigned
4688  Process creation
4720  User account created
4725  User account disabled
4726  User account deleted
4732  Member added to local security group
1102  Audit log cleared
7045  New service installed (System log)
```

**Common Logon Types**
```
2   Interactive (local keyboard)
3   Network (SMB, etc.)
10  RemoteInteractive (RDP)
11  CachedInteractive
```

---

**End of Report**

*This document is intended for professional portfolio and interview use. Screenshots of filtered Event Viewer views, sample 4624/4625 events, and simple timelines can be added as exercises are completed.*
