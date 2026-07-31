<div align="center">

# 🛡️ Hidden Directive Threat Hunt

<img width="1672" height="941" alt="Hidden_Directive" src="https://github.com/user-attachments/assets/d264fa4c-3574-489f-b3bb-d29c534e046b" />


### Enterprise Malware Investigation using Microsoft Defender XDR, Microsoft Sentinel, and Static Malware Analysis

![Status](https://img.shields.io/badge/Status-Completed-success)
![Platform](https://img.shields.io/badge/Platform-Microsoft_Defender_XDR-0078D4)
![SIEM](https://img.shields.io/badge/SIEM-Microsoft_Sentinel-00BCF2)
![Language](https://img.shields.io/badge/Language-KQL-blue)
![Framework](https://img.shields.io/badge/Framework-MITRE_ATT%26CK-red)

</div>

---

# Executive Summary

This project documents a complete threat hunting investigation performed within the **Greenfield Logistics** environment following the detection of suspicious PowerShell activity by Microsoft Defender XDR.

The investigation leveraged **Microsoft Defender XDR**, **Microsoft Sentinel**, **Kusto Query Language (KQL)**, and **static malware analysis** to reconstruct the attack chain, identify the initial compromise, analyze recovered malware artifacts, and assess the overall impact of the incident.

Analysis confirmed execution of a malicious PowerShell loader capable of bypassing Microsoft's Antimalware Scan Interface (AMSI), downloading an XOR-encoded payload, executing shellcode directly in memory, and establishing outbound HTTPS communications with attacker-controlled infrastructure.

Although multiple post-exploitation techniques were identified throughout the investigation, no evidence confirmed successful persistence, credential theft, or data exfiltration during the investigation window.

---

# Investigation Overview

## Scenario

Greenfield Logistics experienced a **Priority 1 (P1)** security incident after Microsoft Defender XDR detected suspicious activity originating from workstation **GF-WS01**.

The Managed Security Service Provider (MSSP) requested a complete investigation to determine:

- Initial access vector
- Attack timeline
- Malware functionality
- Command and control communications
- Overall business impact

---

## Objectives

- Identify the initial compromise vector
- Validate malicious activity
- Reconstruct the attack timeline
- Analyze recovered malware artifacts
- Map attacker techniques to MITRE ATT&CK
- Produce remediation recommendations

---

# Environment

| Item | Value |
|------|-------|
| Organization | Greenfield Logistics |
| Investigation Type | Threat Hunt |
| EDR | Microsoft Defender XDR |
| SIEM | Microsoft Sentinel |
| Primary Host | GF-WS01 |
| Additional Hosts | GF-SRV01, GF-DC01 |
| Primary User | sancadmin |
| Investigation Window | July 4–5, 2026 |

---

# Tools Used

- Microsoft Defender XDR
- Microsoft Sentinel
- Microsoft Defender Advanced Hunting
- Kusto Query Language (KQL)
- Windows 11 Analysis VM
- PowerShell
- Static Malware Analysis

---

# Skills Demonstrated

- Threat Hunting
- Incident Response
- Malware Analysis
- KQL Development
- Microsoft Defender XDR
- Microsoft Sentinel
- MITRE ATT&CK Mapping
- IOC Development
- Timeline Reconstruction
- Technical Report Writing

---

# Investigation Workflow

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
MITRE ATT&CK Mapping
      │
      ▼
Recommendations
```

---

# Investigation Report

The complete investigation is organized into dedicated sections.

| Section | Description |
|----------|-------------|
| [01 - Executive Summary](report/01-Executive-Summary.md) | High-level overview |
| [02 - Incident Overview](report/02-Incident-Overview.md) | Investigation scope |
| [03 - Investigation Methodology](report/03-Investigation-Methodology.md) | Hunting methodology |
| [04 - Root Cause Analysis](report/04-Root-Cause-Analysis.md) | Root cause determination |
| [05 - Attack Timeline](report/05-Attack-Timeline.md) | Chronological reconstruction |
| [06 - Technical Findings](report/06-Technical-Findings.md) | Detailed findings |
| [07 - MITRE ATT&CK](report/07-MITRE-ATTACK.md) | ATT&CK mapping |
| [08 - Indicators of Compromise](report/08-Indicators-of-Compromise.md) | IOC summary |
| [09 - Recommendations](report/09-Recommendations.md) | Remediation actions |
| [10 - Lessons Learned](report/10-Lessons-Learned.md) | Investigation summary |

---

# Key Findings

- ✅ Malicious PowerShell execution confirmed
- ✅ AMSI bypass identified
- ✅ XOR-encoded shellcode recovered
- ✅ In-memory malware execution validated
- ✅ HTTPS command-and-control communication confirmed
- ✅ WMI activity observed
- ✅ No confirmed persistence
- ✅ No confirmed credential theft
- ✅ No confirmed data exfiltration

---

# MITRE ATT&CK Summary

| Tactic | Technique |
|---------|-----------|
| Execution | T1059.001 – PowerShell |
| Defense Evasion | T1562.001 – Impair Defenses |
| Command and Control | T1071.001 – Web Protocols |
| Discovery | T1047 – Windows Management Instrumentation |

---

# Repository Structure

```text
Hidden-Directive-Threat-Hunt/
│
├── README.md
├── report/
├── queries/
├── artifacts/
├── images/
└── docs/
```

---

> **Note**
>
> This investigation was performed in a controlled lab environment for educational and defensive security purposes. The analysis and recommendations presented in this repository are based solely on the evidence collected during the investigation.
> [!IMPORTANT]
> **Assessment:** Confirmed outbound communication with attacker-controlled infrastructure.

---
