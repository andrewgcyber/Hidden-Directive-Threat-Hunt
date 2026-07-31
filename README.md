# Greenfield Threat Hunt Investigation

> **Enterprise Malware Investigation using Microsoft Defender XDR, Microsoft Sentinel, and Static Malware Analysis**

![Banner](images/cover.png)

---

## Executive Summary

On **July 4, 2026**, Greenfield Logistics experienced a Priority 1 (P1) security incident after Microsoft Defender XDR detected suspicious PowerShell activity originating from workstation **GF-WS01**. The Managed Security Service Provider (MSSP) escalated the incident after identifying abnormal process execution involving the **sancadmin** account, prompting a full threat hunting investigation to determine the source of the compromise, identify affected systems, and assess the overall impact.

Using Microsoft Defender XDR, Microsoft Sentinel, Kusto Query Language (KQL), and static malware analysis, the investigation reconstructed the complete attack chain. Endpoint telemetry confirmed execution of an unrestricted PowerShell script, outbound HTTPS communications with attacker-controlled infrastructure, administrative command execution, and Windows Management Instrumentation (WMI) activity. Recovered malware artifacts further revealed that the attack relied on a staged PowerShell loader capable of bypassing Microsoft's Antimalware Scan Interface (AMSI), downloading an XOR-encoded payload, and executing shellcode directly in memory.

Although several post-exploitation techniques were observed throughout the investigation, no evidence confirmed successful persistence, credential theft, lateral movement, or data exfiltration during the investigation window. The findings indicate that the attack successfully achieved code execution and command-and-control communication, but the available evidence did not demonstrate a complete compromise of the Greenfield environment.

This repository documents the complete investigation, including the threat hunting methodology, timeline reconstruction, malware analysis, indicators of compromise (IOCs), MITRE ATT&CK mapping, and remediation recommendations.

---

## Investigation Objectives

The primary objectives of this investigation were to:

- Identify the initial compromise vector.
- Determine the scope of affected hosts and user accounts.
- Reconstruct the complete attack timeline using Microsoft Defender XDR and Microsoft Sentinel telemetry.
- Perform static analysis of recovered malware artifacts.
- Identify attacker techniques and map them to the MITRE ATT&CK framework.
- Assess the overall impact of the incident.
- Develop evidence-based remediation recommendations.

---

## Environment

| Component | Details |
|-----------|---------|
| Organization | Greenfield Logistics |
| Investigation Type | Threat Hunt / Incident Response |
| Primary Platform | Microsoft Defender XDR |
| Secondary Platform | Microsoft Sentinel |
| Primary Host | GF-WS01 |
| Additional Hosts | GF-SRV01, GF-DC01 |
| Primary User | sancadmin |
| Investigation Window | July 4–5, 2026 |

---

## Tools Used

- Microsoft Defender XDR
- Microsoft Sentinel
- Microsoft Defender Advanced Hunting
- Kusto Query Language (KQL)
- Windows 11 Analysis Virtual Machine
- PowerShell
- Static Malware Analysis

---

## Investigation Methodology

The investigation followed a structured threat hunting methodology to validate the incident, reconstruct attacker activity, and correlate endpoint telemetry with recovered malware artifacts.

The investigation consisted of the following phases:

1. Confirm the investigation scope.
2. Validate affected systems.
3. Collect endpoint telemetry.
4. Reconstruct process execution.
5. Analyze network communications.
6. Perform static malware analysis.
7. Correlate evidence.
8. Produce an incident report.

---

## Investigation Scope

The investigation focused on the three systems identified within the original incident briefing.

| Host | Purpose |
|------|---------|
| **GF-WS01** | Primary compromised workstation |
| **GF-SRV01** | Investigated for lateral movement |
| **GF-DC01** | Investigated for authentication activity |

The investigation window was limited to **July 4, 2026 through July 5, 2026** in order to isolate attacker activity from normal administrative operations.

---

## Investigation Workflow

The following workflow was used throughout the investigation.

```text
Incident Brief
        │
        ▼
Environment Validation
        │
        ▼
Microsoft Defender XDR Hunting
        │
        ▼
Microsoft Sentinel Correlation
        │
        ▼
Timeline Reconstruction
        │
        ▼
Static Malware Analysis
        │
        ▼
Root Cause Analysis
        │
        ▼
IOC Development
        │
        ▼
MITRE ATT&CK Mapping
        │
        ▼
Incident Report
```

---

## Initial Investigation

The investigation began by reviewing the incident brief provided by Greenfield Logistics. The briefing identified **GF-WS01** as the primary workstation involved in the incident and instructed investigators to determine the source of the compromise using Microsoft Defender XDR and Microsoft Sentinel telemetry.

The first phase of the investigation focused on validating the environment, confirming that all systems were actively reporting telemetry, and identifying the primary user account associated with the incident.

![Figure 1](images/figure01-brief.png)

**Figure 1.** Initial incident briefing outlining the investigation objectives, affected systems, and required data sources.

Following the initial briefing, Microsoft Defender XDR Advanced Hunting was used to confirm the investigation scope and identify the hosts included in the threat hunt.

![Figure 2](images/figure02-deviceinfo.png)

**Figure 2.** Microsoft Defender XDR environment validation confirming the systems included within the investigation scope.

---

➡️ **Continue to Part 2: Root Cause Analysis and Timeline**
