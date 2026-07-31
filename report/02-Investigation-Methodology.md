# Investigation Methodology

---

## Overview

The investigation followed a structured threat hunting methodology to validate suspicious activity, reconstruct the attack timeline, and determine the scope of the compromise. Multiple evidence sources were correlated to ensure findings were supported by endpoint telemetry, security logs, and malware analysis.

Microsoft Defender XDR served as the primary endpoint detection and response (EDR) platform, while Microsoft Sentinel provided centralized log correlation. Static malware analysis was performed on recovered artifacts to understand malware functionality without executing the payload on production systems.

---

## Investigation Workflow

The investigation was performed using the following workflow:

```text
Incident Brief
      │
      ▼
Environment Validation
      │
      ▼
Endpoint Hunting (Microsoft Defender XDR)
      │
      ▼
Security Event Correlation (Microsoft Sentinel)
      │
      ▼
Static Malware Analysis
      │
      ▼
Timeline Reconstruction
      │
      ▼
MITRE ATT&CK Mapping
      │
      ▼
Recommendations
```

---

## Microsoft Defender XDR

Microsoft Defender XDR was used to collect endpoint telemetry from the affected systems. Advanced Hunting queries were executed to identify malicious process execution, file creation, network connections, registry modifications, and endpoint health.

The following Defender tables were used throughout the investigation:

| Table | Purpose |
|--------|----------|
| DeviceProcessEvents | Process execution analysis |
| DeviceFileEvents | File creation and modification |
| DeviceNetworkEvents | Outbound network connections |
| DeviceRegistryEvents | Registry modifications |
| DeviceInfo | Endpoint validation |

> **Figure 2.** Microsoft Defender XDR Advanced Hunting interface used throughout the investigation.

<img width="1516" height="787" alt="Screenshot 2026-07-31 154612" src="https://github.com/user-attachments/assets/5e8f8700-8d7a-4405-b1f2-8578a8d7318b" />



---

## Microsoft Sentinel

Microsoft Sentinel was used to correlate endpoint telemetry with broader security events occurring during the investigation window. ASIM-normalized logs were reviewed to identify authentication activity, network communications, and additional evidence supporting the Defender XDR findings.

Sentinel provided valuable context for validating suspicious activity across multiple systems while reducing false positives through centralized log correlation.

---

## Kusto Query Language (KQL)

KQL queries were developed and executed to identify attacker activity throughout the investigation.

The investigation focused on:

- Suspicious PowerShell execution
- Command-line arguments
- File creation events
- Network connections
- Registry modifications
- Administrative utilities
- WMI activity
- Malware execution sequence

Each query was refined to isolate activity occurring within the defined investigation window while reducing unrelated system noise.

---

## Static Malware Analysis

Recovered malware artifacts were analyzed within an isolated Windows 11 virtual machine.

Artifacts analyzed included:

| Artifact | Purpose |
|----------|---------|
| blog_lure.html | Initial lure |
| loader.ps1 | PowerShell loader |
| shellcode_encoded.bin | Encoded shellcode payload |

Static analysis identified several malicious capabilities, including:

- AMSI bypass
- XOR payload decoding
- In-memory shellcode execution
- HTTPS communications
- Windows API memory allocation

No malware samples were executed on production systems during the investigation.

---

## Evidence Correlation

Findings were considered validated only after multiple evidence sources supported the same conclusion.

For example:

- Static malware analysis identified an AMSI bypass.
- Defender XDR confirmed PowerShell execution.
- Network telemetry confirmed outbound HTTPS communications.
- File telemetry confirmed creation of recovered malware artifacts.

This correlation process increased confidence in the reconstructed attack chain and reduced the likelihood of false-positive conclusions.

---

## Investigation Outcome

By combining endpoint telemetry, centralized log analysis, KQL hunting, and static malware analysis, the investigation successfully reconstructed the observed attack chain and established a reliable foundation for identifying the root cause and assessing the overall impact of the incident.

The following section presents the root cause analysis and explains how the compromise occurred based on the evidence collected throughout the investigation.
