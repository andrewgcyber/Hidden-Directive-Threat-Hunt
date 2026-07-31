# Incident Overview

---

## Incident Summary

On **July 4, 2026**, Greenfield Logistics experienced a Priority 1 (P1) security incident after Microsoft Defender XDR detected suspicious PowerShell activity on workstation **GF-WS01**. The organization's Managed Security Service Provider (MSSP) escalated the alert for immediate investigation due to the potential for malware execution and post-exploitation activity.

The investigation focused on determining the initial compromise vector, reconstructing the sequence of attacker actions, identifying affected systems, and evaluating the overall impact to the Greenfield environment.

---

## Original Incident Brief

The investigation began with an incident briefing provided by the Greenfield Logistics Security Lead. The briefing established the scope of the investigation and identified the systems believed to be involved.

> **Figure 1. Initial incident briefing provided to the investigation team.**

```text
<img width="1241" height="556" alt="image" src="https://github.com/user-attachments/assets/d1aa05f5-cd7c-4841-88e5-ffb12984af0d" />

```

The briefing identified the following hosts as being within the investigation scope:

| Host | Description |
|------|-------------|
| GF-WS01 | Primary compromised workstation |
| GF-SRV01 | Application server |
| GF-DC01 | Domain Controller |

At the beginning of the investigation, only **GF-WS01** was confirmed as compromised. The remaining hosts required validation through endpoint telemetry and log analysis.

---

## Investigation Objectives

The primary objectives of the investigation were to:

- Determine how the compromise occurred.
- Identify malicious processes executed on the affected workstation.
- Analyze recovered malware artifacts.
- Reconstruct the attack timeline.
- Identify command-and-control communications.
- Determine whether persistence or credential theft occurred.
- Produce actionable remediation recommendations.

---

## Investigation Scope

The investigation was limited to activity occurring during the defined incident window.

| Category | Value |
|----------|-------|
| Investigation Type | Threat Hunt / Incident Response |
| Environment | Greenfield Logistics |
| Time Window | July 4–5, 2026 |
| Primary Host | GF-WS01 |
| Supporting Hosts | GF-SRV01, GF-DC01 |

Microsoft Defender XDR served as the primary source of endpoint telemetry, while Microsoft Sentinel was used to correlate security events and validate attacker activity across the environment.

---

## Data Sources

The investigation relied on multiple sources of evidence to reconstruct the attack.

### Microsoft Defender XDR

- DeviceProcessEvents
- DeviceNetworkEvents
- DeviceFileEvents
- DeviceRegistryEvents
- DeviceInfo

### Microsoft Sentinel

- ASIM telemetry
- Authentication events
- Network activity
- Correlated security events

### Static Malware Analysis

Recovered malware artifacts were analyzed within an isolated Windows 11 virtual machine. This analysis confirmed the functionality of the PowerShell loader and the associated encoded shellcode without executing the malware in a production environment.

---

## Investigation Workflow

The investigation followed a structured methodology designed to validate malicious activity while preserving evidence.

1. Review the original incident briefing.
2. Validate affected hosts.
3. Collect endpoint telemetry.
4. Analyze PowerShell execution.
5. Examine recovered malware artifacts.
6. Correlate endpoint and network activity.
7. Reconstruct the attack timeline.
8. Develop remediation recommendations.

---

## Transition

Following the initial scoping process, the investigation proceeded with the collection and analysis of endpoint telemetry using Microsoft Defender XDR and Microsoft Sentinel. The methodology used throughout the investigation is described in the following section.
