# GF-INC-2026-0704 — Greenfield Threat Hunt Investigation

> End-to-end threat hunting investigation performed against the Greenfield training environment using Microsoft Defender XDR, Microsoft Sentinel, and static malware analysis.

---

## Overview

This investigation documents the analysis of a simulated enterprise intrusion within the Greenfield environment. The objective of the hunt was to identify the initial compromise, reconstruct the attack timeline, determine the scope of the incident, analyze recovered malware artifacts, and assess the overall impact to the environment.

During the investigation, telemetry from Microsoft Defender XDR and Microsoft Sentinel was correlated with recovered evidence bag artifacts to reconstruct the attack chain. Static analysis of the recovered PowerShell loader revealed an in-memory malware execution framework that bypassed Microsoft Antimalware Scan Interface (AMSI), downloaded an obfuscated payload, and executed shellcode directly in memory.

The investigation identified communications between compromised systems and attacker-controlled infrastructure, evidence of malicious PowerShell execution, credential-related activity, Windows Management Instrumentation (WMI) usage, and multiple indicators of command-and-control activity.

---

## Objectives

- Confirm incident scope
- Triage security alerts
- Identify the initial access vector
- Reconstruct the attack timeline
- Perform static malware analysis
- Correlate Defender and Sentinel telemetry
- Assess impact to the Greenfield environment
- Produce a professional incident report

---

## Environment

### Platforms

- Microsoft Defender XDR
- Microsoft Sentinel
- Windows Event Telemetry
- PowerShell
- Windows 11 Analysis VM

### Primary Log Sources

- DeviceProcessEvents
- DeviceNetworkEvents
- DeviceLogonEvents
- DeviceFileEvents
- DeviceRegistryEvents
- WindowsPowerShell_CL
- WindowsProcess_CL

---

## Investigation Highlights

- Identified malicious HTML prompt injection used as the initial delivery mechanism.
- Confirmed outbound HTTPS communications to attacker infrastructure.
- Recovered and statically analyzed a malicious PowerShell loader.
- Identified AMSI bypass functionality.
- Confirmed XOR decoding of an encrypted payload.
- Confirmed in-memory shellcode execution using Windows API calls.
- Correlated network telemetry with recovered malware artifacts.
- Assessed potential credential access and lateral movement activity.
- Produced IOC and MITRE ATT&CK mappings.

---

## Attack Summary

```text
Malicious HTML
        │
        ▼
PowerShell Execution
        │
        ▼
loader.ps1
        │
        ▼
AMSI Bypass
        │
        ▼
Download XOR Payload
        │
        ▼
Shellcode Injection
        │
        ▼
HTTPS C2 Communication
        │
        ▼
Post-Exploitation Activity
```

---

## MITRE ATT&CK

| Tactic | Technique |
|----------|-----------|
| Initial Access | T1189 Drive-by Compromise *(assessed)* |
| Execution | T1059.001 PowerShell |
| Defense Evasion | T1562.001 Impair Defenses |
| Command and Control | T1071.001 Web Protocols |
| Discovery | T1047 Windows Management Instrumentation |
| Credential Access | T1555 *(assessment only)* |

---

## Indicators of Compromise

### Domains

- cdn.cloud-endpoint.net
- api.cloud-endpoint.net

### Files

- blog_lure.html
- loader.ps1
- shellcode_encoded.bin

### SHA-256

```
523F4C317E03CD1AC811FA7E1C308EFD6DF1E2A61048C1C0BDD5B4D5FFB73C34
```

---

## Repository Structure

```
GF-INC-2026-0704
│
├── README.md
├── Incident-Report.md
├── Timeline.md
├── IOC-Table.md
├── MITRE.md
├── Artifact-Analysis.md
├── Defender-KQL.md
├── Sentinel-KQL.md
└── images
```

---

## Report

The complete investigation report is available in:

**Incident-Report.md**

This report contains:

- Executive Summary
- Investigation Scope
- Timeline
- Technical Findings
- MITRE ATT&CK Mapping
- IOC Analysis
- Static Malware Analysis
- Containment Recommendations
- Lessons Learned

---

## Skills Demonstrated

- Threat Hunting
- Incident Response
- Microsoft Defender XDR
- Microsoft Sentinel
- Kusto Query Language (KQL)
- Static Malware Analysis
- MITRE ATT&CK Mapping
- Digital Forensics
- IOC Development
- Technical Report Writing
