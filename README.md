# Greenfield Threat Hunt Investigation

> End-to-end threat hunting investigation performed using Microsoft Defender XDR, Microsoft Sentinel, and static malware analysis.

---

## Overview

This project documents the investigation of a simulated enterprise compromise within the Greenfield environment. The objective was to identify the initial access vector, reconstruct the attack timeline, analyze recovered malware artifacts, and determine the overall impact using Microsoft Defender XDR, Microsoft Sentinel, and static analysis.

---

## Objectives

- Identify the initial compromise
- Reconstruct the attack timeline
- Analyze recovered malware artifacts
- Assess attacker activity and impact
- Produce an incident response report

---

## Tools Used

- Microsoft Defender XDR
- Microsoft Sentinel
- Kusto Query Language (KQL)
- PowerShell
- Windows 11 Analysis VM

---

## Investigation Summary

The investigation identified a malicious HTML lure containing hidden prompt injection instructions designed to execute a PowerShell command. Static analysis of the recovered `loader.ps1` confirmed an AMSI bypass, download of an XOR-encoded payload, and in-memory shellcode execution using Windows API functions.

Microsoft Defender XDR telemetry confirmed outbound HTTPS communications between the compromised workstation and attacker-controlled infrastructure. Additional findings included KeePass execution and WMI authentication activity. No evidence of persistence, successful credential theft, or data exfiltration was identified during the investigation.

---

## Key Findings

- Hidden prompt injection embedded within `blog_lure.html`
- Malicious PowerShell loader recovered and analyzed
- AMSI bypass identified
- XOR-encoded shellcode downloaded and executed in memory
- Command-and-control communication to attacker infrastructure confirmed
- WMI activity observed during post-exploitation
- No confirmed persistence or credential theft

---

## MITRE ATT&CK

| Tactic | Technique |
|---------|-----------|
| Execution | T1059.001 – PowerShell |
| Defense Evasion | T1562.001 – Impair Defenses |
| Command & Control | T1071.001 – Web Protocols |
| Discovery | T1047 – Windows Management Instrumentation |

---

## Indicators of Compromise

### Domains

- `cdn.cloud-endpoint.net`
- `api.cloud-endpoint.net`

### Files

- `blog_lure.html`
- `loader.ps1`
- `shellcode_encoded.bin`

### SHA-256

```
523F4C317E03CD1AC811FA7E1C308EFD6DF1E2A61048C1C0BDD5B4D5FFB73C34
```

---

## Skills Demonstrated

- Threat Hunting
- Incident Response
- Microsoft Defender XDR
- Microsoft Sentinel
- Kusto Query Language (KQL)
- Static Malware Analysis
- MITRE ATT&CK Mapping
- IOC Development

---

## Repository Structure

```
Greenfield-Threat-Hunt/
│
├── README.md
├── Incident-Report.md
├── IOC-Table.md
├── MITRE.md
├── Queries.md
└── images/
```

---

> **Note:** This investigation was performed in a controlled lab environment for educational and defensive security purposes.
